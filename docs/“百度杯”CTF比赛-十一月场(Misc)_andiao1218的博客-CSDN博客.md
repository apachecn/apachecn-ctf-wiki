<!--yml
category: 未分类
date: 2022-04-26 14:52:59
-->

# “百度杯”CTF比赛 十一月场(Misc)_andiao1218的博客-CSDN博客

> 来源：[https://blog.csdn.net/andiao1218/article/details/101192779](https://blog.csdn.net/andiao1218/article/details/101192779)

题目提示：

![](img/e049e556fd447632004d51a4212eae7e.png)

文件在i春秋的ctf2群里，加群下载文件

![](img/c2d5d162f60379d16f3a94b78a81f93a.png)

下载下来之后发现有压缩密码

题目提示有提示解压密码：key：ichunqiumemeda

![](img/57e82ac448977c4049435e9aa5999b0e.png)

打开文件，得到flag

![](img/b21dd35d282fd5b16c0061db3059bf2e.png)

![](img/9c4fbefba3dcb90316634f22c22a1205.png)

点击下载附件

![](img/395b8be9c5ff1bb3b773f861893ad562.png)

给出的却是一串unicode码，转换一下编码

![](img/286d06c7b5f00fea05e025064114f3c9.png)

得到flag

![](img/a10c6775b5c34062f2c028519aae525e.png)

一串md5：21232f297a57a5a743894a0e4a801fc3

拿去解一下，得到admin

![](img/ba12cdabdc16a415f70578e4a5f01a08.png)

加上flag{}格式就是flag了

![](img/c1c1897234bd69ec71f8e728895f6965.png)

按照字母跟着在键盘上连，可得到字母x，z，v，o，c

加上flag{}格式就得到flag

![](img/522a47cf68c27bba8b628aa12626820a.png)

根据题目提示，可能是base32编码

解码一下

![](img/2c1d920ce967dd5d5d9c24fccb59e8f2.png)

得到flag

![](img/04f782a04f2ab2bea71f4d832b84633a.png)

根据题目提示，可能是XXencode编码，解码一下

![](img/0c5018ff533ed9aca1141c7cef4007ea.png)

得到flag

密文：f5-lf5aa9gc9{-8648cbfb4f979c-c2a851d6e5-c}

栅栏密码在线解密一下

![](img/201e6d3da0920de4eb57c7eb1ea71859.png)

得到flag

题目内容：synt{mur_VF_syn9_svtug1at}

用rot13解码，http://www.mxcz.net/tools/rot13.aspx

![](img/1957edf2b1c6edd6a2a1f4f7e2e21bf8.png)

得到flag

![](img/ecdc0cde316ee399bdd7289ffa283b34.png)

把94从十六进制转为十进制，把4664从十六进制转为十进制，用逗号隔开，然后加上flag格式，即得到flag