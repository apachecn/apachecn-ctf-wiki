<!--yml
category: 未分类
date: 2022-04-26 14:51:05
-->

# BUUCTF-MISC刷题记录-1_L.o.W的博客-CSDN博客

> 来源：[https://blog.csdn.net/weixin_44145820/article/details/104701824](https://blog.csdn.net/weixin_44145820/article/details/104701824)

## 二维码

用winhex打开图片，在末尾发现如下，怀疑图片中包含压缩包
![在这里插入图片描述](img/616ead91eb5136b62c9c55b9104fae8b.png)
在Linux下用binwalk查看，并用foremost导出至output文件夹
![在这里插入图片描述](img/1e12e93d7214777db2db9ef3c812b71a.png)
根据提示，密码为4位数字，直接爆破即可
![在这里插入图片描述](img/a56b21a2e126aa1ca4903bc2993d38a7.png)
得到密码 7639
![在这里插入图片描述](img/ccfc5f7f6750e6147bd29ee3044c00df.png)
解压压缩包即可获得flag

## N种方法解决

根据题目提示是base64编码的PNG
![在这里插入图片描述](img/8b9bfb12e0eb31cdfdaad28ad2cd7178.png)
使用下面的脚本解码

```
import base64

f = open("a.txt", 'r')
s = f.read()
f.close()
content = base64.b64decode(s)
f = open("a.png", "wb")
f.write(content)
f.close() 
```

获得一个二维码，在线扫码:[https://online-barcode-reader.inliteresearch.com/](https://online-barcode-reader.inliteresearch.com/)
即可得到flag
![在这里插入图片描述](img/dc71342dcbf1fc3745c301006a4d96ff.png)

## 大白

根据题目提示怀疑题目大小被修改，放入Linux查看，无法打开，证明的确被修改
![在这里插入图片描述](img/41e746ab90eea0f45a99da01c35b3416.png)
这里用了一下大佬的脚本：

```
import os
import binascii
import struct

misc = open("dabai.png","rb").read()

for i in range(1024):
    data = misc[12:20] +struct.pack('>i',i)+ misc[24:29]
    crc32 = binascii.crc32(data) & 0xffffffff
    if crc32 == 0x6d7c7135:
        print i 
```

得到高度为479（0x1DF），使用winhex修改高度，得到flag
![在这里插入图片描述](img/bf46f09dcaae83ccb3eeb3ab0bb6f72d.png)
![在这里插入图片描述](img/3f6d41750331a9a7e4b89fb77f50f245.png)

## 基础破解

题目提示密码为4位数字，直接爆破
得到密码 2563
![在这里插入图片描述](img/e6cb142cb13fe35b50607f95c1b07292.png)
解压之后，得到：ZmxhZ3s3MDM1NDMwMGE1MTAwYmE3ODA2ODgwNTY2MWI5M2E1Y30=
使用base64解密得到flag

## 你竟然赶我走

使用winhex打开，拉到最后就是flag
![在这里插入图片描述](img/c465e31391075ec1e6ebc339da5f41ba.png)

## LSB

使用stegsolve的data extract，发现是一张图片，保存二进制数据
![在这里插入图片描述](img/bf9d8bbb118cd89ea639dda47b25977f.png)
![在这里插入图片描述](img/f890f2713a077e8bc9f47bba5edf4fd8.png)
得到一个二维码，在线解码：[https://online-barcode-reader.inliteresearch.com/](https://online-barcode-reader.inliteresearch.com/)
![在这里插入图片描述](img/336893ec2034aef6e6651166a15dedf8.png)

## 乌镇峰会种图

用winhex打开，拉到最下面就是flag
![在这里插入图片描述](img/64033233ebc575ee2f0e844a64a1131f.png)

## rar

和前面一样暴力破解，密码是四位数字

![在这里插入图片描述](img/2b07849b5da2aa5808c14651311cfa30.png)

## ningen

根据提示，查看图片中隐藏文件
![在这里插入图片描述](img/50b4feac770a675f202d10be6689190a.png)
暴力破解密码
![在这里插入图片描述](img/54d37d8473574c9d0a09264cd4d15fe5.png)

## qr

得到一个二维码，在线解码：[https://online-barcode-reader.inliteresearch.com/](https://online-barcode-reader.inliteresearch.com/)
得到flag
![在这里插入图片描述](img/bcdc8d2eb29818ae99058dd58b718868.png)

## 文件中的秘密

winhex打开发现flag
![在这里插入图片描述](img/9de8e0295381c7430efd19d41ddf4dea.png)

* * *

最后给大家分享一下本人整理的MISC各种编码以及密码学的在线工具网站：
[CTF Crypto/MISC 在线工具网站](https://blog.csdn.net/weixin_44145820/article/details/104635169)