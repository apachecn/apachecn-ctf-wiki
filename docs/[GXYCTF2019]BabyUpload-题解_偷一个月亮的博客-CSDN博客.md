<!--yml
category: 未分类
date: 2022-04-26 14:35:44
-->

# [GXYCTF2019]BabyUpload 题解_偷一个月亮的博客-CSDN博客

> 来源：[https://blog.csdn.net/yiqiushi4748/article/details/108343263](https://blog.csdn.net/yiqiushi4748/article/details/108343263)

方法一：

<FilesMatch  "wen">

SetHandler  application/x-httpd-php

</FilesMatch>

 ![](img/f1abfb722b96c3116ada5e96582dcc5a.png)

 ![](img/539e4d98de98ba4dd3593cca53d1a3d2.png)

 ![](img/4e52c4772bbaa12e34fbf309021ceae2.png)

方法二：

首先上传随意后缀名木马

 ![](img/7f2ff790c10515289952c2f6a976d378.png)

更改.htaccess文件

 ![](img/d51827ef4a3e8388b2d7c5853d9543e1.png)

访问测试

虽然未解码，其实已经附着，可以使用一句话木马

 ![](img/6f9280f7d1c1095dc6261c18a2721041.png)

 ![](img/f5283667b089b361fc419e54cb2955e5.png)

三个获取文件内容得函数