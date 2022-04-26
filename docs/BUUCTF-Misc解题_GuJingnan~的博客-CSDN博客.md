<!--yml
category: 未分类
date: 2022-04-26 14:47:47
-->

# BUUCTF Misc解题_GuJingnan~的博客-CSDN博客

> 来源：[https://blog.csdn.net/weixin_54438700/article/details/122174422](https://blog.csdn.net/weixin_54438700/article/details/122174422)

## ******金三胖******

拿到题目，看到是一个动图，其中有红色的帧闪来闪去，可能是flag，用过使用Stegsolve工具中的帧浏览器，得到flag{he11ohongke}。

![](img/0e951ba0b022e40eb4ab25a6f90beb1d.png)

## ******二维码1******

在010中可以看到有一个PK开头的，猜测有一个压缩包，使用kali中自带的工具binwalk（用于搜索二进制图像的嵌入文件和可执行代码的工具）确定存在一个压缩包。把后缀改成zip进行解压，通过工具爆破获得密码（7639），得到flag.

![](img/b9a28ddf403ef660cc4eb29225ea8cf2.png)

 ![](img/7a4dac0c3b64d9e64b1b5030fa116b98.png)

## ******你竟然赶我走******

下载压缩包后解压发现是一张图片，丢到010在最后看到flag{stego_is_s0_bor1ing}

![](img/141b584ebed1932366723ff3e4e10499.png)

![](img/383344189a39a9aa234364155fe61b64.png)

## ******N种解决办法******

压缩包打开是一个key.exe，丢到010 ==> 可以看到是一个base64（base64可以表示图片），拿去解密，

 ![](img/66230c884aba10e23712ed78399b7251.png)

通过在线解密方式，可以得到一张二维码图片。

![](img/4f2ea766f6ca461ce37d7d23614d58c8.png)

使用在线扫描工具扫一扫，可以得到KEY{dca57f966e4e4e31fd5b15417da63269}，完成。 

## ******大白******

打开后是一张大白的图片，根据题目提示，可以得到如片太小，推测图片是可以更改的，丢到010中，结合大白图片的样子，猜测要改宽度，得到flag。![](img/2db63ae61a0e7e1fbf767f416ccf69f9.png)

 ![](img/33963bbd372a0b3df35af2f6ab900ed9.png)

 ![](img/e8dea006facf6ee612e581773faf8b5e.png)![](img/baa5509d7c00ec5dbfde6937db5a2823.png)

## ******基础破解1******

根据提示可知，压缩包是一个四位数字的密码，通过爆破得到口令2563，解压后得到加密的flag，进行bas64解密flag{70354300a5100ba78068805661b93a5c}。

![](img/fa7d88d0f751d5508500b207610b76b1.png)![](img/ac27279a76bb3807f6ceb74283c6ddc9.png)

## ******乌镇峰会种图********1******

把图片丢到010，在最后看到flag{97314e7864a8f62627b26f3f998c37f1}。

![](img/374ef9740245df17fd4e505dfec56a7d.png)

## ******文件中的秘密********-1******

一张海贼王的图片丢到010中可以看到flag。

![](img/5d2578f19e076f0c93a4a47f13aac9fa.png)

![](img/9a5b394ff8d2d9c0504261fe6fa3dd81.png)

## ******LSB-1******

考虑是图片隐写，当red，green，blue为0时候，可以看到图片上方有些不同。保存得到一张二维码。扫描二维码得到flag{1sb_i4_s0_Ea4y}。

![](img/baa6ad652293b2f6f3d5362565062d5b.png)

![](img/23f998f857c3b4c302d126d5fbd73ce0.png)

 ![](img/c541008ecccaf4ffa8910ea54f9569de.png)

## ******Wireshark******

根据题目可以知道密码即是flag，从包中可以看到明文密码。

![](img/2a0a8e418202fceb111c1cf212cc4b12.png)

## ******RAR-1******

根据题目提示四位数字密码，通过爆破得到密码8795，解压后得到flag{1773c5da790bd3caff38e3decd180eb7}

 ![](img/2a77bcc6de549204ee9785821eb592aa.png)![](img/e3817ef5b4b6f58c90a4c7b55d7fb004.png)

##  ******ZIP伪加密-1******

![](img/d44d45d161e73e0b35374f9492decd10.png)

题目中把09改成00，保存再解压可以得到flag{Adm1N-B2G-kU-SZIP}。

![](img/80edda8392a7cab136c8dc895dc279a3.png)

##  Qr-1

直接扫描二维码得到flag。

## ******被嗅探的流量******

使用wireshark打开题目中的包，筛选过滤包http.request.mothod==POST

拉到最后可以看到flag。

![](img/e96d0fa530ef96f3a4f206566e1dc500.png)

## ******镜子中的世界-1******

把图片丢进StegSolve中，找到key。

![](img/b636554ad81ec1dfebcb2235fe8a2621.png)![](img/ebc0a0c2769c5da2946235a9b9328075.png)

## ******Ningen******

使用binwalk分析包，可以看到有个zip，提取出来的压缩包进行口令爆破获得解压密码。解压后得到txt文档获得flag{b025fc9ca797a67d2103bfbc407a6d5f}。

![](img/79e8f626cd0b88a0cc1aa6979f5e4e5e.png)

![](img/c4597969fb8c234849581e26847ee1e9.png)