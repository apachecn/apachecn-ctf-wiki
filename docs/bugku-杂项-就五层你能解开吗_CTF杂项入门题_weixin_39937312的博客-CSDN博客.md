<!--yml
category: 未分类
date: 2022-04-26 14:43:53
-->

# bugku 杂项 就五层你能解开吗_CTF杂项入门题_weixin_39937312的博客-CSDN博客

> 来源：[https://blog.csdn.net/weixin_39937312/article/details/110136214](https://blog.csdn.net/weixin_39937312/article/details/110136214)

## 杂项题: 磁盘镜像

磁盘里藏着flag

7z解压，挂载，分离都可以

![5675c707d81633f6897509ed3e506902.png](img/7124350a2d4015dc0af79f3e471ec130.png)

## 杂项题: 神奇的图片

可能是二维码也可能是图形，要根据生成的图片进行判断。

#!/usr/bin/env python2

# -*- coding: UTF-8 -*-

from PIL import Image

x = 150

y = 900

pic = Image.new("RGB",(x, y))

f =open("basic.txt","r")

flag_s = []

for i in f.readlines():

    j=i.strip("\n").replace(")","").replace("(","")

   flag_s.append(j)

f.close()

s = ""

k = 0

for i in range(x):

   for j in range(y):

       s = flag_s[k].split(",")

       pic.putpixel([i,j],(int(s[0]),int(s[1]),int(s[2])))

       k += 1

pic.show()

pic.save("flag.png")

![a048e7bcadefb8deafa71170cc9b2387.png](img/50ebba48aa173333255e44dfa4795f28.png)

更改x，y值可以看到里面不是二维码而是数字，继续修改到合适的位置

得到的图片用ps翻转即可得到下面的图

![d4e3b81aaf2e2bf21bfb89ceb0de1dfb.png](img/9dd939ed3a1c0ba92e2221bc30a3cb57.png)

## 杂项题：怀疑人生

![90acb76bc7ab3583b027b4ee7d18bf42.png](img/57b36dd8dfce1b1bf054e39ad9748b38.png)

下载压缩包

https://ctf.bugku.com/files/8d61ff10962c756f9b1d8cd048e9f3c6/zip

这个链接不能直接打开，大概是因为没有注册bugku账户登录的话无法直接访问。

更改后缀名，解压

![432fa8609c5a3b37852f23f2fe42a038.png](img/d042a1ba8d912c11f6f5e3a5fec225c1.png)

就发现了这么三个东西  直接解压哈哈哈

第一部分，压缩包解压，发现有密码，那就爆破吧

![deb20e6eb2e1e6b380bb0c635b1f1014.png](img/a66b9501ede77d16d93b3097afa7c0a4.png)

得到密码解压 文本文件

![c5b93e172e0b2404d032bfcda857c528.png](img/6ebbba8444b0fafac5bba7da1583f1d9.png)

这应该是base64把  解密看看

![8b7a5f8a21df7e7b7623b57d0daa88cb.png](img/d7389d3e30c7139174d70200fcf5b5dc.png)

得到 Unicode编码  

\u66\u6c\u61\u67\u7b\u68\u61\u63\u6b\u65\u72  

之后在解码

![e18323d4fae041256a89639ac7ef8cb4.png](img/8d173f8b3718d6c711a22eac6792ec76.png)

![4c3e990db74edea287d33311f53d4091.png](img/dc2993fe0037cee5b79b5f9ed0a9a20a.png)

接着看第二个图片ctf2  我还是用360压缩打开发现有东西 但是需要密码

![aa084e86ac4e213291d7c6d180c67e2f.png](img/913b573eebe20336c29b26da19fd37db.png)

用之前的密码是错误的

那么在十六进制编译器里打开看看

![ab921820b81bd86f1c66471e2b93da67.png](img/f5ceb21992f53a51205f99f5f3d3f186.png)

发现有 .zip的文件头和文件尾 ：

(https://blog.csdn.net/qq_42777804/article/details/98876791)

![be533a623bb995cf069b069dc88915df.png](img/706bf0730486041729067a4e3038a433.png)

那么我们直接将他后缀改为  .zip 发现可以解压  

解压后得到文本文件 

![706a81307d9c66b57c74c6ef0a0f50fb.png](img/b1986e03d252b4ff1024ef6dae8cad3f.png)

发现这个是 ook编码 

那么在线 解码 https://www.splitbrain.org/services/ook

![0b81b9ffb7730d5025c7f957baa6e0c5.png](img/d1029d9b893752151b5517d155c1c941.png)

得到   3oD54e

查了好久这个居然是base58   

还是base58在线解码  https://www.jisuan.mobi/pbHzbBHbzHB6uSJx.html

![a06a201ef2f40930fa377e932a1d1c3e.png](img/8f83def8350b8483762eac073ea1cd9b.png)

第三张图片是一张二维码 扫描 得到：

我用qq直接扫描出来了！！！！！！

![4cf7c4b7a09f136c4bd451b314147e2e.png](img/057f81308ef670ee0ca750e2dd477e48.png)

最后部分为     12580}

flag{hackermisc12580}

但是我看其他大佬都是用这个软件扫出来的

![fe8766aa184d2602ddc27000c9028b48.png](img/ca66d84d5f8f83f60d4908e3b72d0ed1.png)

## CTF加密篇之ok(Ook！)

![a54f23e01ca8e5ee6e45a211a7fb6b06.png](img/582012a0a6f411838b4dd0112e162d97.png)

**ok**

Ook. Ook. Ook. Ook. Ook. Ook. Ook. Ook.Ook. Ook. Ook. Ook. Ook. Ook. Ook.

Ook. Ook. Ook. Ook. Ook. Ook! Ook? Ook!Ook! Ook. Ook? Ook. Ook. Ook. Ook.

Ook. Ook. Ook. Ook. Ook. Ook. Ook. Ook.Ook. Ook. Ook. Ook. Ook. Ook. Ook.

Ook. Ook? Ook. Ook? Ook! Ook. Ook? Ook.Ook. Ook. Ook. Ook! Ook. Ook. Ook.

**本题要点：****Ook!****编码**

首先看到题目~

哇！

好长的Ook！啊~~~

这个就只能依靠日常积累和万能的搜索引擎了~~~

有一个非常好用的在线网站~

https://www.splitbrain.org/services/ook

来吧~ 复制粘贴

![92e624d8640d2eb81111f1076626c7a5.png](img/da36be9ec8a90a5f55695a60639a55cd.png)

在线解码

bingo~

![c1316ddf871c498a78e319c3c8f12045.png](img/e20545adbbd0ede5a7965ea8923ca9d0.png)

![e44938ab1053d148559e0044138cb264.png](img/43f1bb916f15df26e9133cdb3c219e6d.png)

## 杂项题：红绿灯

![66858103dd704383e3a65d973fafcd34.png](img/f31766795bc66c8f2ad899fecc0f4198.png)

https://ctf.bugku.com/files/65beb9db8419bd99c0d97068959d2b3e/Traffic_Light.gif

打开图片然后另存到桌面

是个红绿灯的动图

![c819956eb2ab70d40b127a21e0e72a2a.png](img/8bd51e74ccd76e24116b09106abe96a9.png)

题目内容为一个gif的文件，然后使用2345看图王打开，并将每一帧图片都保存下来

![957b05f29ddd8793d3239f0d3a9a1f90.png](img/51f322697706add91256c1a467a1361c.png)

![ae5a2e0227858cfc107ca996e6fbcad7.png](img/183d7ef11a5686c7949197548efe3ce8.png)

![c7d8a304a7f6a18884d46cb24f8c37f9.png](img/834f3b438c032880aac6cd8ef888a450.png)

1168个图

其中有大概一半左右的图片为没有颜色的红绿灯，将其全部删除。

![08a1e062db833c4ac67542cea5820087.png](img/315aee8bbed6f0518eea3d938f4c05f5.png)

删除完之后，在文件夹里面调节大小的时候，突然发现最左侧和最右侧的一列颜色都是一样的，推测最右侧的一列为空格，因为ascii128位，最左侧的为0，得出绿色为0，红色为1，留下中间的7列。

然后把图片的大小调节为合适的大小，7个一行。

一共455张图片，65组7位二进制数，红灯为1，绿灯为0，将图片信息记录下来

11001101101100110000111001111111011101000011011000110011011010011100110110011101111111100000110100111100110111110110100111010011101000110011110111011101000110001011000011011101011111111010001100001011111111010011100100110100110011011001100110001110001110111111110011011010011001100110011111010011110011011111111011111010000110011110111010111111111001011000011101011011111011010011100100110011101111101100001110101111010011100110110001110010001100111111101

然后利用二进制转换字符串的python的脚本来转换得出flag

def fun1():#二进制字符串转换字符串

    #需要转换的字符串

    f ='11001101101100110000111001111111011101000011011000110011011010011100110110011101111111100000110100111100110111110110100111010011101000110011110111011101000110001011000011011101011111111010001100001011111111010011100100110100110011011001100110001110001110111111110011011010011001100110011111010011110011011111111011111010000110011110111010111111111001011000011101011011111011010011100100110011101111101100001110101111010011100110110001110010001100111111101'

    b = ''

    i = 0

    j = 7

    while j <= len(f):

        a = '0' + f[i:j]

        b += chr(int(a,2))

        i = j

        j += 7

    print(b)

def fun2():#字符串转换二进制字符串

    #需要转换的字符串

    f = ' '

    b = ''

    c = ''

    for i in f:

        a = str(bin(ord(i)))

        b = a[2:].zfill(7)

        c += b

    print(c)

fun1()

#fun2()

![87ec2ef4caf1a6c6348d709ad26bc790.png](img/3dba568c5981059e40a3501de2703388.png)

![13cb22fff5c554fb5f9092035c6ee54f.png](img/97ad17d850f320e3da3ebb68fa9d97d7.png)

flag{Pl34s3_p4y_4tt3nt10n_t0_tr4ff1c_s4f3ty_wh3n_y0u_4r3_0uts1d3}

## 杂项题：不简单的压缩包

![2e55d535b62682cf0c9e4c13ba7fcc10.png](img/7423ca34fcc280b3b7ba5c4804678f3b.png)

下载压缩包

https://ctf.bugku.com/files/e5a937a3985f5264a723bcbd0e062b0f/zip

更改后缀名后，解压

需要密码

![52307aae1ac8bafb3d6fc07eb7a3c03c.png](img/116af03414a4db5848190aee07ca01c4.png)

打开010

在末尾发现第二个压缩包

zip文件头是504B0304，文件尾504B

![7006769a72cb743fd8db1524c7225b55.png](img/33dd09bfcd178205b12ba9cc7ad6d9e8.png)

手动分离出去或者使用binwalk分离

![02b3070bd52e46ed70c57dd1b97a900e.png](img/5bdc7e1a42d3ca178add277f66b2f6ee.png)

另存为第二个zip

![35e8c195fa0890573c90e9055be2e762.png](img/af6544af4407d2a099b249ddfd9f87ec.png)

打开，有密码，以为是伪加密，把140009改成140000，进压缩包，提示解压文件损坏。。。

那行吧，不是伪加密，只能放ARCHPR暴力破解，说不定破解几个小时就出来了。。。
然鹅。。。。。事情并不是这样

![8239efc8b20b7f09cd2966567cd5f0b2.png](img/41508f6158911a589b8ea8e22ad38151.png)

![5ec60e5980ff851d5d0922284e417afb.png](img/6d2cc64dcaf9e54c18dd1d2675c1e9cf.png)

![460ec7fb7a8f0ea257fc623b5c85e1ce.png](img/a11e615308c436689d56475fc072981a.png)

居然一运行就跑出了密码，密码是0。。。。

接下来，你们懂的，解压出tingshuo.txt，发现一段谜之日语

![58b2d301129a4ca6c954fa5cbb46ed2f.png](img/78477628d102134e1ecdb33d9bc20277.png)

翻译一下

![b563db1efe466b62a0aca2a9e4f89973.png](img/d56eecbd23b9ae6536e197e8d8346ca3.png)

密码50位，对应的应该是zip.zip的，但是密码它有50位？？？暴力破解能解到地老天荒，密码会不会在属性里？看了一下属性好像没什么问题(之前还试过流隐写破解，还是没什么发现)

那，猜猜密码？？50位密码，如果混合组合，肯定没法解开，要不然试试相同符号密码？于是我做了一个这样的字典。

![ba4725a678fdc7e67e9a9df65a7ae528.png](img/0a25077ea5ef66cbbef8a473307da75c.png)

每行50个相同字符

打开ARCHPR，跑一下字典

![7d869d90b88310a29da7c9e356135938.png](img/ce1ef53dbde827300915456cbe781f99.png)

50个a

好了，打开zip.zip，输入密码，解压出flag.swf文件

![ad1b3ac4de30df8bd5a717bbbe5281d4.png](img/1586b585c060c4914a6d7007ba25f96f.png)

一个谜之游戏，我试了好几次，通不了关，可能是我太菜了吧
我想着。。。。是一个游戏，通关之后可能会有flag，试试改存档？

但是并没有存档文件出现在文件夹里。。。

调整一下思路
在百度搜索.swf

![5c7cfe2c04b996994c838ce93a55d21f.png](img/b2e025ca19207fb601b186288f2a6d06.png)

既然是应用程序那就试试反编译看源码喽，也许修改能直接通关吧。
然后，我在网上找到一个名叫JPEXS Free Flash Decompiler的软件。

用它打开flag.swf

![4819466a4ff20b5d09b39bc906ef72a8.png](img/a72ec0b25088f21b1d5be95ece1ca0ec.png)

一个一个看源码，在scripts中有一个名叫DefineSprite (171)的项目，点开之后发现一段奇怪的字符串。

![02c149692ca178a77386923d698de15c.png](img/8547b87c82fbd471dcaa2e607ae79278.png)

![e12ceae5adf229b714c7ec9b8859d458.png](img/76a2a9fb0005dc3b43b16b08e77668d8.png)

看上去像是16进制，转换一下试试，

![cdf2336f06846ad1693b616a5450ab5c.png](img/31e8d1f59ef9c463726d9007c9642ae7.png)

密码居然在这里
填进flag，居然通过了！！！！

flag{jpexs7reeflash}

看了大佬的解题思路，脑洞太多了，自己解不出来。。。

整理了下最近做题常用到的工具，自行考虑下载，毕竟这些工具也是来源于网络！