<!--yml
category: 未分类
date: 2022-04-26 14:53:44
-->

# 实验吧——安全杂项之“Snake”详解_Geekingdom的博客-CSDN博客_ctf snake

> 来源：[https://blog.csdn.net/qq_41870552/article/details/83956843](https://blog.csdn.net/qq_41870552/article/details/83956843)

# ****Snake****

链接：

题目链接：[http://www.shiyanbar.com/ctf/1851](http://www.shiyanbar.com/ctf/1851)

附件链接：[http://ctf5.shiyanbar.com/misc/snake.jpg](http://ctf5.shiyanbar.com/misc/snake.jpg)

工具：WinHex,kaili(binwalk),360压缩，base64解码，serpent解码

思路及解法：

打开题目链接发现题目没有任何介绍，就一个flag格式要求

![](img/78f79dff2fe0e5303a472a19ca717d2f.png)

点击解题链接发现是一张蛇的图片，将其下载下来

![](img/51953082cc17bcd5d28be023200f596e.png)

发现是jpg格式的图片，如果是一张正常的jpg图片，那么它的结尾应该是FF D9结尾，所以用WinHex打开该图片，发现这个并不是以FF D9结尾，其后面还有内容

![](img/39723025fc29c20a6ba6359523af4fd1.png)

所以知道这并不是一个简单的图片，可能其中含有其他的文件被隐藏起来，所以我打开虚拟机的kali系统，使用里面的binwalk分析该图片，发现里面果然含有一个zip格式的文件

![](img/7bc883b617907725c46754046024b984.png)

所以用360压缩打开这个图片，发现里面还有两个文件，一个是cipher，另一个是key

![](img/aa8de495cd979686a8f40a3c8732e20a.png)

我首先想到的是key文件里应该就是我想要找的flag，所以我首先用记事本打开了key文件，发现是一串经过加密的字符串
![](img/0f9f94a9282169f2e928152516e8c3d3.png)

猜想应该是base加密方法，所以用在线base解码解码，解出一句英文“What is Nicki Minaj’s favorite song that refers to snakes?”

[![](img/5632b3e0d3c9a7df2da0ec9ff79e72bb.png)g](http://bk.jiuzuifusheng.com/wp-content/uploads/2018/07/b6-750x205.png)

好吧，虽然我英文不是太好，还是能看懂一点，发现这并不是我想要的flag，想要得到flag还要根据这句英语继续往下解密，看来是我想的太简单，所以我就去网上百度这句话（不得不吐槽出题人的折磨人程度），经过在网上百度，发现英文中Nicki Minaj是一个歌手，这句话指的是她的一个视频变成的歌曲，名字叫“Anaconda”，这个单词的意思就是蟒蛇

![](img/d22775f6a1ab4f8a3df48d7e00a6fa9d.png)

联系到360压缩打开jpg图片后有两个文件，而key文件已经被我走到无路可走了，最终的解题之路肯定是在另一个文件cipher里，因此猜想cipher文件被一种加密方式加密，此加密方式还需要密钥，而秘钥就是刚才从key文件解出来的单词。 
并且这题如果是有秘钥的而且是解出来的单词，那么应该就是公钥加密的的一种，所以我网上百度了一下共要公钥的算法，找了好多，最后在AES中在百度里看到这个算法

![](img/3fd7b864fc5ee6427ef18bcd4f17b080.png)

 这个单词的英文意思就有蛇的意思，与本道题直接相关

![](img/ac22a3eba0f0283818d8c06751b8e742.png)

所以我在线找了一下这个算法的解密网站，最后根据从key中得到的秘钥解出了flag

![](img/6faaa1798c03b48a75e68a0426f45e03.png)

结束！（吐槽：出题人脑洞真大！）

### <****************注：此文为博主所写，转载请注明出处！************************>