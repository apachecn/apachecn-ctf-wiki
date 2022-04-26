<!--yml
category: 未分类
date: 2022-04-26 14:48:10
-->

# ctf xor题_详解两道CTF逆向题_weixin_34324618的博客-CSDN博客

> 来源：[https://blog.csdn.net/weixin_34324618/article/details/112015680](https://blog.csdn.net/weixin_34324618/article/details/112015680)

0x01:confused_flxg

这是Hackergame 2018的一道题目

拿到题目，是一个压缩包，进行解压后发现是一个.exe可执行程序，双击可以正常运行

随意输入，总是会出来一个倒序的base64编码后的字符串

我们用python进行解码

得到一个假的flag 不用管他

Exeinfo PE载入可以发现是VC++写的64位程序，并且没有加壳

我们直接使用IDA x64载入

shift + F12可以看到程序中的一些字符串

我们双击正确的引用

可以看到有好多跳转

我们可以从下向上进行分析

在最近的jnz跳转处 按F5 查看伪代码

如下所示：

void __usercall sub_7FF7EBA1498F(__int64 a1@)

{

unsigned __int8 *v1; // rax

unsigned __int8 v2; // dl

int v3; // eax

*(a1 + 112) = a1 + 384;

*(a1 + 40) = -1i64;

do

++*(a1 + 40);

while ( *(*(a1 + 112) + *(a1 + 40)) );

*(a1 + 64) = *(a1 + 40);

qmemcpy((a1 + 800), a9eetw4DFh4xu, 0x39ui64); // qmemcpy将内存中的字符串进行拷贝

memset((a1 + 857), 0, 0x8Fui64); // memset()函数初始化了一块内存空间 类似于char getflxg[200] = { 0 }

sub_7FF7EBA11590(a1 + 144, (a1 + 384), *(a1 + 64));// 进行base64加密

memset((a1 + 176), 0, 0xC8ui64); // 再初始化一块空间

*(a1 + 80) = sub_7FF7EBA11A40(a1 + 144);

*(a1 + 48) = a1 + 176;

*(a1 + 136) = *(a1 + 48);

do

{

*(a1 + 32) = **(a1 + 80);

**(a1 + 48) = *(a1 + 32);

++*(a1 + 80);

++*(a1 + 48);

}

while ( *(a1 + 32) );

strrev((a1 + 176)); // strrev用于反转字符串

memset((a1 + 592), 0, 0xC8ui64); // 又初始化了一个新的字符数组

for ( *(a1 + 36) = 0; ; ++*(a1 + 36) )

{

*(a1 + 120) = a1 + 176;

*(a1 + 56) = -1i64;

do

++*(a1 + 56);

while ( *(*(a1 + 120) + *(a1 + 56)) );

if ( *(a1 + 36) >= *(a1 + 56) )

break;

*(a1 + *(a1 + 36) + 592) = *(a1 + 36) ^ *(a1 + *(a1 + 36) + 176);// 这个for循环用于将反转后的str与len(str)进行异或操作

}

v1 = (a1 + 592);

while ( 1 ) // 这个while循环将得到的串与内存中的进行比较 若相等,则v3等于0并且跳转到label_15

{

v2 = *v1;

if ( *v1 != v1[208] )

break;

++v1;

if ( !v2 )

{

v3 = 0;

goto LABEL_15;

}

}

v3 = -(v2 < v1[208]) | 1;

LABEL_15:

if ( v3 )

{

sub_7FF7EBA124B0(std::cout, "\n你的flxg不正确！");

*(a1 + 'D') = 0;

}

else // v3=0,输出成功提示信息

{

sub_7FF7EBA124B0(std::cout, "\n祝贺你，你输入的flxg是正确的！");

*(a1 + 'H') = 0;

}

sub_7FF7EBA11AE0(a1 + 144);

JUMPOUT(&loc_7FF7EBA119F4);

}

可以看到 重要的部分就在这段代码中

这个程序主要流程很简单，先将我们的输入进行标准的base64编码，接着进行翻转，将a[i]与i分别异或，最后与内存中的flag进行比较看是否一致

那我们就可以逆出来flag

双击这个字符串数组 接着shift + e 复制出来得到加密后的串

得到之后，用python解即可

import base64

enc = [0x39, 0x65, 0x45, 0x54, 0x77, 0x5F, 0x34, 0x5F, 0x64, 0x5F,

0x66, 0x68, 0x3C, 0x34, 0x58, 0x55, 0x7F, 0x43, 0x21, 0x4B,

0x7F, 0x20, 0x43, 0x76, 0x5F, 0x20, 0x4C, 0x4D, 0x7A, 0x53,

0x70, 0x7D, 0x56, 0x4D, 0x65, 0x47, 0x4C, 0x5D, 0x71, 0x43,

0x18, 0x6F, 0x47, 0x48, 0x42, 0x18, 0x1C, 0x4D, 0x74, 0x45,

0x01, 0x69, 0x00, 0x4D, 0x5B, 0x6D]

aa = ''

flag = ''

#先求出xor之前的

for i in range(len(enc)):

aa += chr(enc[i] ^ i)

#print aa

#9dGWsZ2XlVlc09VZoR3Xk5UaG9VVfNnbvlGdhxWd0Fmcn52bDt3Z4xmZ

#翻转并base64解码

print base64.b64decode(aa[::-1])

#flxg{Congratulations_U_FiNd_the_trUe_flXg}

总结：

做这道题目时 因为是C++的 伪代码看着也会很乱

直接看不容易理解 这时候可以开启IDA动态调试 下断点进行分析

0x02:easyCpp

2019安恒二月赛的一道题目

这道题目运行，输入11111111111 回车，程序直接关闭，没有任何提示信息

Exeinfo PE查壳发现是一个C++程序并且没有加壳

IDA x32载入程序 shift + F12查看有没有什么可以字符串

发现一个'Please input:'

双击进去 接着双击引用

程序流程图貌似很简单的样子

F5查看伪代码

进行简单分析 Src是一个字符串数组 unsigned int是错误的

我们双击Src看下在程序中的位置

发现是在.data段也就是数据段(data segment),通常是指用来存放程序中已初始化的全局变量的一块内存区域,那Src是一个32位的全局字符串数组变量

双击sub_411302()进去 发现最后返回的是中间的参数 也就是result

双击sub_411352() 进行分析

接着双击sub_413910()

int __usercall sub_413910@(int a1@, char *Str)

{

char v2; // cl

size_t i; // [esp+D0h] [ebp-8h]

sub_4112F8((int)&unk_421008);

for ( i = 0; i < j_strlen(Str); ++i ) // 将Str这个字符串分为三组 分别与0x1f,0x20,0x21进行异或

{

if ( i % 3 )

{

if ( i % 3 == 1 )

v2 = Str[i] ^ 0x20;

else

v2 = Str[i] ^ 0x21;

Str[i] = v2;

}

else

{

Str[i] ^= 0x1Fu;

}

}

return sub_411302(1, (int)Str, a1); // 最后返回a1

}

总的分析

int __usercall sub_413E30@(int a1@)

{

int v1; // eax

int v3; // [esp+0h] [ebp-CCh]

sub_4112F8((int)&unk_421008);

sub_411276(std::cout, "Please input: "); // cout输出提示信息

scanf((const char *)&my_input, (unsigned int)Src, 32);// scanf输入 保存在Src

if ( j_strlen(Src) >= 14 && j_strlen(Src) <= 32 )// 判断Src的长度是否满足大于等于14，小于等于32 若满足，就继续

{

v1 = strcpy_s(Str, 32u, Src); // 用strcpy_s()函数拷贝Src到Str

sub_411302(&v3 == &v3, v1, a1);

sub_411352(a1, Str); // 对Str进行异或操作 返回a1

}

return sub_411302(1, 0, a1); // 返回0 相当于return 0

}

感觉程序没有结束但就是找不到下面的代码

看了官方WP

了解到可以查看Str的引用

因为后面一直是对Str进行处理 所以可以猜测后续还会对他进行处理

点击任意一个Str 点击X 可以查看到第三条是还未分析的

我们双击进去 可以发现有两个Str

这时候我们就是该找到新Str的来源 但是我的IDA好像找不到其他引用

所以换个思路

我们可以看 unk_421008 所在区域 一般都是在一块的 点击它 按X

可以发现向上游好多引用

一个一个看 最后可以发现最上面这个 就是我们要找的

双击可以看到

那么我们就可以编写脚本解出flag

str1 = ''

str2 = 'access denieda'

flag = ''

for i in range(len(str2)):

str1 += chr(ord(str2[i])^i)

# print str1

#abafwv&cmgcnhl

for i in range(len(str2)):

if i % 3 == 0:

flag+= chr(ord(str1[i])^0x1f)

elif i % 3 == 1:

flag+= chr(ord(str1[i])^0x20)

elif i % 3 == 2:

flag+= chr(ord(str1[i])^0x21)

print flag

#~B@yWW9CLxCOwL

总结：当没有头绪的时候，可以查看变量或字符串的引用