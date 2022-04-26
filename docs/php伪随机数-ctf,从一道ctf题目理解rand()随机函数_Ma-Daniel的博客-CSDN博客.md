<!--yml
category: 未分类
date: 2022-04-26 14:49:25
-->

# php伪随机数 ctf,从一道ctf题目理解rand()随机函数_Ma Daniel的博客-CSDN博客

> 来源：[https://blog.csdn.net/weixin_42097508/article/details/115239283](https://blog.csdn.net/weixin_42097508/article/details/115239283)

本帖最后由 yechen123 于 2018-12-16 12:43 编辑

先上传附件

![c557529e2ea4bb8d5cb558b7b5575102.gif](img/12261cfa459b61e9de1a1ce96d0db462.png)

random.rar

(2.76 KB, 下载次数: 19)

2018-9-25 21:23 上传

点击文件名下载附件

下载积分: 吾爱币 -1 CB

没有cb的看这里 链接: https://pan.baidu.com/s/19O8WkgqHt7HhsuXTht85Zw 提取码: vsy7

先说一下rand()和srand()函数

一般用法是给srand传给一个种子  种子是系统时间

然后用rand产生随机数

看一下rand和srand的代码

[Asm] 纯文本查看 复制代码void srand(int seed)

{

holdrand=seed;

}

int rand()

{

holdrand=holdrand*0x000343FD+0x00269EC3;

return(holdrand>>0x10)&0x7FFF;

}

可以看出这只是一个伪随机函数 由于一般传给srand的是系统时间 而用户打开软件的时间不确定 所以可以看成随机

但是如果传给srand的并不是系统时间 而是一个确切的数 那么就有可能 能预测rand给出的值

举例

[Asm] 纯文本查看 复制代码int main()

{

srand(1);

printf("%d\n", rand());

printf("%d\n", rand());

printf("%d\n", rand());

printf("%d\n", rand());

printf("%d\n", rand());

}

这个得出的结果是

![55fd2b2273b5a8b4531f72773c469d6e.gif](img/3246218cdc0a1060a37ca03f50080532.png)

11.png (11.83 KB, 下载次数: 0)

2018-9-25 21:33 上传

然后我们自己仿写一个srand函数和rand函数

[Asm] 纯文本查看 复制代码int main()

{

int hold = 1;

hold = hold*0x000343FD+0x00269EC3;

printf("%d\n", (hold>>0x10)&0x7fff);

hold = hold*0x000343FD+0x00269EC3;

printf("%d\n", (hold>>0x10)&0x7fff);

hold = hold*0x000343FD+0x00269EC3;

printf("%d\n", (hold>>0x10)&0x7fff);

hold = hold*0x000343FD+0x00269EC3;

printf("%d\n", (hold>>0x10)&0x7fff);

hold = hold*0x000343FD+0x00269EC3;

printf("%d\n", (hold>>0x10)&0x7fff);

return 0;

}

结果

![55fd2b2273b5a8b4531f72773c469d6e.gif](img/3246218cdc0a1060a37ca03f50080532.png)

22.png (16.99 KB, 下载次数: 0)

2018-9-25 21:38 上传

可以看出结果是一样的

那么就表明 如果我们知道seed的值

那么就能预测rand产生的值

开始解析题目

这是一道64位系统下linux的题目

用IDA打开

![55fd2b2273b5a8b4531f72773c469d6e.gif](img/3246218cdc0a1060a37ca03f50080532.png)

33.png (81.69 KB, 下载次数: 0)

2018-9-25 21:40 上传

可以看到有两个循环

这两个循环中里面有sleep函数

主要是想影响你分析时间

接下来看重点

先输入username和password

在经过五个函数

[Asm] 纯文本查看 复制代码sub_4008C1(username); // 限制username长度 只能为8或者12

sub_400901(username);

sub_4009B2(username); // 只能为小写或者_

sub_400A33(username, passwrod);

return sub_400C23(username, passwrod);

先分析第一个

[Asm] 纯文本查看 复制代码__int64 __fastcall sub_4008C1(__int64 username)

{

int i; // [rsp+1Ch] [rbp-4h]

for ( i = 0; i <= 100 && *(i + username); ++i )

;

return sub_400866(i);

}

__int64 __fastcall sub_400866(int i)

{

__int64 result; // rax

if ( 4 * (i >> 2) != i || 4 * (i >> 4) == i >> 2 || !(i >> 3) || (result = (i >> 4), result) )

{

puts("Wrong username or password!!!\n");

exit(0);

}

return result;

}

不断循环  一直循环到username结尾 也就是 i就是username长度

在进入sub_400866 这个函数

这段判断代码可以写一个脚本解密一下

[Asm] 纯文本查看 复制代码int result;

for (int i=0; i<100; ++i)

{

if (4 * (i >> 2) != i || 4 * (i >> 4) == i >> 2 || !(i >> 3) || (result = i >> 4, result))

{

continue;

}

printf("%d\n", i);

}

![55fd2b2273b5a8b4531f72773c469d6e.gif](img/3246218cdc0a1060a37ca03f50080532.png)

44.png (9.19 KB, 下载次数: 0)

2018-9-25 21:45 上传

也就是说 username长度只能为8 或者12

再看第二个函数

[Asm] 纯文本查看 复制代码signed __int64 __fastcall sub_400901(signed int *username)

{

signed __int64 result; // rax

__int64 v2; // [rsp+18h] [rbp-18h]

__int64 v3; // [rsp+20h] [rbp-10h]

__int64 v4; // [rsp+28h] [rbp-8h]

v2 = *username; // 第一个

v3 = username[1];

v4 = username[2];

if ( v2 - v3 + v4 != 0x70667A78

|| v3 + 2 * (v4 + v2) != 0x22F241C1FLL

|| (result = 0x31CD156AC3A69DC4LL, v3 * v4 != 0x31CD156AC3A69DC4LL) )

{

puts("Wrong username or password!!!\n");

exit(0);

}

return result;

}

这里主要是解一个三元一次方程组

解方程组在这里不解释

需要注意的是形参声明是signed int *username

是四个字节的 所以在

v2 = *username;                               // 第一个

v3 = username[1];

v4 = username[2];

中 v2是username前面四个字节

v3是中间四个字节

v4是后面四个字节

可以预测 username长度为8

还有一点需要注意 就是linux下大端小端的问题

比如abcd(61626364)

在内存中存储是64636261

最后解出来

v2 = 0x6d737469  换成字母就是 msti  倒序过来就是itsm

v3 = 0x6f726265  orbe  ebro

v4 = 0x72656874 reht  ther

那么username就是 itsmebrother

再看下一个函数

[Asm] 纯文本查看 复制代码__int64 __fastcall sub_4009B2(__int64 username)

{

__int64 result; // rax

int i; // [rsp+1Ch] [rbp-4h]

for ( i = 0; ; ++i )

{

result = *(i + username); // 找一遍字母

if ( !result )

break;

if ( (*(i + username) <= 96 || *(i + username) > 122) && *(i + username) != 95 )

{

puts("Wrong username or password!!!\n");

exit(0);

}

}

return result;

}

这个倒是比较简单

主要是判断输入是不是小写和_

再看下一个

[Asm] 纯文本查看 复制代码__int64 __fastcall sub_400A33(_DWORD *username, _DWORD *password)

{

int v2; // ST1C_4

int v3; // ST20_4

int v4; // ST24_4

int v5; // ST28_4

int v6; // ST2C_4

__int64 result; // rax

int i; // [rsp+18h] [rbp-28h]

for ( i = 0; *(password + i); ++i )

{

if ( (*(password + i) <= 96 || *(password + i) > 122)

&& (*(password + i) <= 64 || *(password + i) > 90)

&& (*(password + i) <= 47 || *(password + i) > 57) )// 只能大写 小写 数字

{

puts("Wrong username or password!!!\n");

exit(0);

}

}

srand(username[1] + *username + username[2]);

v2 = *password;

if ( v2 - rand() != 0x16F48AF6 )

{

puts("Wrong username or password!!!\n");

exit(0);

}

v3 = password[1];

if ( v3 - rand() != 0x200ADA1C )

{

puts("Wrong username or password!!!\n");

exit(0);

}

v4 = password[2];

if ( v4 - rand() != 0x37774C4 )

{

puts("Wrong username or password!!!\n");

exit(0);

}

v5 = password[3];

if ( v5 - rand() != 0x5FC38E35 )

{

puts("Wrong username or password!!!\n");

exit(0);

}

v6 = password[4];

result = (v6 - rand());

if ( result != 0xAECBAF5 )

{

puts("Wrong username or password!!!\n");

exit(0);

}

return result;

}

第一个循环判断password大小写和数字

然后调用srand函数 seed是username[1] + *username + username[2]

前面已经得知username的值

所以可以预测rand的值

好了  昨晚的问题解决了

这个是linux下的程序  所以在windows下调用rand和linux可能有些不同

直接写代码预测rand产生的值

![55fd2b2273b5a8b4531f72773c469d6e.gif](img/3246218cdc0a1060a37ca03f50080532.png)

2333.png (79.49 KB, 下载次数: 0)

2018-9-26 17:35 上传

爆了个警告 不理他

直接运行

最后rand产生的值时

0x000000005664AD74

0x00000000283E9D1A

0x000000006EE9F96C

0x00000000036FC1FF

0x00000000665B8A42

可以得到password的值为

6d59386a

48497736

72616e30

63335034

71484537

写脚本解一下

[Asm] 纯文本查看 复制代码>>> i = "6d59386a4849773672616e306333503471484537"

>>> for q in range(0, len(i), 2):

... flag += chr(int(i[q:q+2], 16))

...

得到

mY8jHIw6ran0c3P4qHE7

在拼一下 得到的passw3ord为

j8Ym6wIH0nar4P3c7EHq