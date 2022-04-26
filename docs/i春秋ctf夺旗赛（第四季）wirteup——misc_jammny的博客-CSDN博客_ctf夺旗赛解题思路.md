<!--yml
category: 未分类
date: 2022-04-26 14:49:54
-->

# i春秋ctf夺旗赛（第四季）wirteup——misc_jammny的博客-CSDN博客_ctf夺旗赛解题思路

> 来源：[https://blog.csdn.net/qq_41832837/article/details/103821095](https://blog.csdn.net/qq_41832837/article/details/103821095)

*前言：**这次参加了i春秋ctf夺旗赛，misc类型一共有两道题。在这里跟大家分享一下解题步骤以及思路。*

第一道题，XImg

下载文件解压后，得到一张png图片

![](img/b665c8f1d89dc2d112235bba252cded2.png)

这道题考察是图片隐写。图片隐写是misc常见的题型之一，接下来我会详细讲解一下解题步骤。

工具：stegsovle

1、把图片放到stegsovle分析，程序最下面有两个小按钮，不断点击直到得到一个被隐藏的二维码。

![](img/e8b57726d379793c7570fecc15261922.png)

2、按照以往的题型，扫这个二维码就可以得到flag。但是这里有个坑，解码后的flag是假的。真的flag通过LBS隐写藏起来了。那怎么看是否存在LBS隐写呢？还是使用我们stegsovle隐写神器。点击程序最上面的Analyse，选择Data Extract 。然后如图所示勾选功能，Preview一下。得到flag。

![](img/f5512ef518dc196ffbb045d6e7a14178.png)

第二道题，pypi

首先我们要知道PyPI是什么？PyPI(*Python Package Index*)是python官方的第三方库的仓库

1、下载文件解压，我们会得到一个图片，还有一个需要输入密码才能解压的txt文件。遇到这种题型，图片中可能会找到我们需要的密码。一开始的解题步骤和上一题类似，通过stegsovle找到被隐写的信息。只不过，这里被隐写的信息不是明文，而是通过base85加密后的信息。

![](img/b9df666527f6cc5e5035ea5c987166d2.png)

2、点击SaveBin保存下来。然后把密文拿到在线base85解密网站去破译：

![](img/1064381cb232417d15585e288d4a87fe.png)

可以得到：

eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiIxMjM0NTY3ODkwIiwiaGludCI6IlRoZSBTaWduYXR1cmUncyBNZDUgaXMgWmlwJ3MgUGFzc3dvcmQiLCJpYXQiOjE1MTYyMzkwMjJ9.wvVICsoUEmVkDxWAfBgDR9Xp32x88OB1h08UmTTenxA

3、我们发现解密后的信息，是ey开头的。如果对jwt有所了解的，就该知道这里其实是加密后的jwt信息。不了解的也没关系，把它再进行一次base64解码。会得到以下结果：

![](img/f7d18ece5a13ddf49bd979b6676bd491.png)

4、可以看到“JWT”，所以我们JWT官网网站分析一下：

![](img/95e45298c178e65f6269b1480b24bc40.png)

这里有个提示：签名的MD5就是解压缩的密码

5、对JWT进行爆破攻击，得到我们secret。使用的工具：c-jwt-cracker；

使用c-jwt-cracker需要我们在我们linux里安装好openssl头文件。

在linux的配置命令是：apt-get install libssl-dev

下载好c-jwt-cracker，还需要我么在工具所在目录执行make命令。目的是让文件makefile运行起来。编译完后会生成一个jwtcrack文件。

6、我们把解密后的base85信息放进去就可以得到签名了，如下图所示：

![](img/815f9cb882dd56412fa72bdd51fcd218.png)

7、再把Zac1进行MD5加密：

![](img/5eb7a8a974281e0f763886b0b1f33230.png)

得到密码：f228128e1042ad0affd8c04a3fbd90c5

8、打开文本输入密码得到信息：Gameforflag，结合题目“pypi”，需要我们再pypi网站找到”Gameforflag“这个项目

![](img/7ff1fd337dafc0f11466f819b1bdaf6d.png)

9、下载下来，里面有个flag.py。打开得到flag{a70483c2fa93f9bd54c4fcf3cca0b484}

END：

           笔者也是个小白，在不断学习和成长中。以后会不断更新各类CTF的writeup，以供更多的读者学习和积累知识，共同学习和探讨。