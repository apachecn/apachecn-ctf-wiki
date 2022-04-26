<!--yml
category: 未分类
date: 2022-04-26 14:30:24
-->

# ctf做题小技巧_撸代码的憨憨的博客-CSDN博客_ctf解题思路

> 来源：[https://blog.csdn.net/wangxinru1/article/details/79775756](https://blog.csdn.net/wangxinru1/article/details/79775756)

1 先打开一张图片

![](img/a08e5ed62afcba2ca8649da87930c410.png)

这是一道简单的题，猜猜图片中的人是谁。

直接用百度搜索 上传照片就可以知道她是刘亦菲了。

2 点击链接打开一张图片，比如

[点击打开链接](http://http//120.24.86.145:8002/misc/1.jpg) 

![](img/2e6afa750d95bbc98cdf219251500f42.png)

就会得到一张图片，然后用notepad++打开

![](img/10f4ed8ce7df404e389e432c2c601764.png)

可以看到下面的一段转义序列，用unicode转换器解码就能得到key{you are right}

3 再看一张图片

![](img/6de350606aea70dbbf209aaad208b161.png)

用winhex和照片查看器分别打开它

![](img/d9fc74da64d0f5efc4a4b454b186f603.png)

![](img/858ae542e9a5d828593d22f3ac7dd9a6.png)

可以发现它的高度明显不一样，在winhex里把高度01 A4改为01 F4保存下来就会发现图片已经改变

![](img/010d57c22cc98aee6e5d46da48158836.png)

4 当上面的方法都不行时就直接在notepad++里打开直接搜索key或flag。

比如说telent这道题，这是一个PCAP文件，在notepad++里打开后搜索flag就会发现![](img/f38245881cf6b68d5002317d9cd34cce.png)

5 有的题直接是一个word文档，比如说眼见非实.。用notepad++打开

![](img/2645825a1cb47b9b22a7c80294f612b0.png)

可以看到开头的PK标志，这是一个压缩文件的标志，然后把文档后缀改为zip解压，就会在解压后的文件中的word>document中找到

![](img/b4c2834fafa266327b3264b9a012c57f.png)

6 除此之外还有在图片里隐藏压缩文件的，如：

![](img/ba9ad927d17031117667b24238db4c28.png)

一张图片居然有6.11MB,就很有嫌疑，用010editor查看一下果然发现隐藏了一个压缩包

![](img/81d5e4e5f7c963cca1d6ff64e04f3f21.png)

把从rar开始到最后拷贝为一个rar文件，然后解压这个文件发现是要密码的

![](img/ebd77b889a4d16cb222712a0640644f5.png)

接下来我们打开图片查看器

![](img/ff09f459072c825c08240343e7c4e75a.png)

发现备注后面的一串代码，使用base64解码就能得到密码，解压后就得到了和3中一样的图片，然后，用3中介绍的方法去解就行了。