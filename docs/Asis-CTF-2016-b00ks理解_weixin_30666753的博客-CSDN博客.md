<!--yml
category: 未分类
date: 2022-04-26 14:54:58
-->

# Asis CTF 2016 b00ks理解_weixin_30666753的博客-CSDN博客

> 来源：[https://blog.csdn.net/weixin_30666753/article/details/98239651](https://blog.csdn.net/weixin_30666753/article/details/98239651)

---恢复内容开始---

最近在学习堆的off by one，其中遇到这道题，萌新的我弄了大半天才搞懂，网上的很多wp都不是特别详细，都得自己好好调试。

首先，这题目是一个常见的图书馆管理系统，虽然我没见过。

```
1. Create a book 2. Delete a book 3. Edit a book 4. Print book detail 5. Change current author name 6. Exit
```

漏洞：

```
signed __int64 __fastcall sub_9F5(_BYTE *a1, int a2)
{ int i; // [rsp+14h] [rbp-Ch]
  _BYTE *buf; // [rsp+18h] [rbp-8h]

  if ( a2 <= 0 ) return 0LL;
  buf = a1; for ( i = 0; ; ++i )
  { if ( read(0, buf, 1uLL) != 1 ) return 1LL; if ( *buf == 10 ) break; ++buf; if ( i == a2 ) break;
  } *buf = 0;#漏洞所在。 return 0LL;
}
```

```
signed __int64 sub_B6D()
{
  printf("Enter author name: "); if ( !sub_9F5(off_202018, 32) ) return 0LL;
  printf("fail to read author_name", 32LL); return 1LL;
}
```

可以看到 book_name的size最大是32位，如果输入32个字符，那么'\x00'会被下一个下面的指针数组覆盖了。而且这程序也有修改author_name的机制，所以也可能修改下面的指针数组。

```
pwndbg> x/20xg 0x555555756040
0x555555756040:    0x6161616161616161    0x6161616161616161
0x555555756050:    0x6161616161616161    0x6161616161616161
0x555555756060:    0x0000555555757790    0x00005555557577c0   指针数组指向book的一个结构，该结构保存了下面结构的信息。
0x555555756070:    0x0000000000000000    0x0000000000000000
```

```
struct book
{ int id; char *name; char *description; int size;
}
```

```
pwndbg> x/20xg 0x0000555555757790
0x555555757790:    0x0000000000000001    0x0000555555757670
0x5555557577a0:    0x0000555555757700    0x0000000000000080
0x5555557577b0:    0x0000000000000000    0x0000000000000031
0x5555557577c0:    0x0000000000000002    0x00007ffff7fae010
0x5555557577d0:    0x00007ffff7dba010    0x0000000000021000
0x5555557577e0:    0x0000000000000000    0x0000000000020821
0x5555557577f0:    0x0000000000000000    0x0000000000000000
0x555555757800:    0x0000000000000000    0x0000000000000000
0x555555757810:    0x0000000000000000    0x0000000000000000
0x555555757820:    0x0000000000000000    0x0000000000000000
```

```
x/50xg 0x0000555555757670
0x555555757670:    0x6262626262626262    0x0000000000000000
0x555555757680:    0x0000000000000000    0x0000000000000000
0x555555757690:    0x0000000000000000    0x0000000000000000
0x5555557576a0:    0x0000000000000000    0x0000000000000000
0x5555557576b0:    0x0000000000000000    0x0000000000000000
0x5555557576c0:    0x0000000000000000    0x0000000000000000
0x5555557576d0:    0x0000000000000000    0x0000000000000000
0x5555557576e0:    0x0000000000000000    0x0000000000000000
0x5555557576f0:    0x0000000000000000    0x0000000000000091
0x555555757700:    0x6363636363636363    0x0000000000006363
```

首先，指针数组保存了指向每个结构的地址，然后在结构体中，name和des是两个地址，保存输入的name和description。

**大概的解题思路：**

首先，利用先输入32位的字符，然后新建book1，这样会导致'\x00'给第一个数组指针给覆盖了，在printbook的时候会将第一个数组指针book1_addr 泄露。

　　然后再利用修改author_name的时候，会将第一个数组的最低位给覆盖成'\x00'，使第一个数组指针越界访问。

　　恰好最低位覆盖为零的时候是指向0x0000555555757700，而0x0000555555757700

```
pwndbg> x/20xg 0x0000555555757700
0x555555757700:    0x6363636363636363    0x0000000000006363
0x555555757710:    0x0000000000000000    0x0000000000000000
0x555555757720:    0x0000000000000000    0x0000000000000000
0x555555757730:    0x0000000000000000    0x0000000000000000
0x555555757740:    0x0000000000000000    0x0000000000000000
0x555555757750:    0x0000000000000000    0x0000000000000000
0x555555757760:    0x0000000000000000    0x0000000000000000
0x555555757770:    0x0000000000000000    0x0000000000000000
0x555555757780:    0x0000000000000000    0x0000000000000031
0x555555757790:    0x0000000000000001    0x0000555555757670
```

就是book1的description。这样我们可以提前伪造一个fake_book在book1.这个book1伪造成指向book2，book2和book1的偏移从上面可以看到是0x30

这样在进行修改author_name的时候就可以覆盖了第一个指针，然后再print_book会泄露book2_name和book2_description的地址.因为在print的时候会从原来的description中解地址，但是现在给修改成了fake_book，所以解地址后获得的是book2_name and book2_description的地址。

伪造的payload:      payload = p64(1) + p64(book1_addr + 0x38) + p64(book1_addr + 0x40) + p64(0xffff)

到这一步我们已经获得了任意地址的读写能力了，

接下来的其他的wp也很详细，但我还是得补充一下，就是free_hook，在构造exp时，可以通过修改book1和book2来使得free_hook指向system,`__free_hook`里面的内容不为`NULL`, 遂执行内容指向的指令.

这个我实在是脑子不太好，这样的题都看wp想了那么久，调试还是蛮重要的，最后多谢大佬们的wp还有萌新的我写的差，大佬们勿喷哦！