<!--yml
category: 未分类
date: 2022-04-26 14:53:03
-->

# [BUUCTF misc]神秘龙卷风_棠亭_️的博客-CSDN博客_神秘龙卷风

> 来源：[https://blog.csdn.net/qq_50665826/article/details/118937249](https://blog.csdn.net/qq_50665826/article/details/118937249)

如题：

![在这里插入图片描述](img/8a0d297a01f27f50c766211fb4b0d1d9.png)

下载压缩包并解压缩，会得到另一个压缩包，需要输入密码
![在这里插入图片描述](img/8523c406b4729920357a4f9f8e3eeb91.png)

根据题意，压缩包密码为四位数字，用工具ARCHPR暴力破解即可，得到密码为5463

![在这里插入图片描述](img/5d9c9ff91658c89079000d0e6b87d9f4.png)

解压文件，得到一个txt文件，发现里面确实是神秘的外星文，可以判断其要用Brainfuck解密，解密可得到flag（[在线解密网站](https://www.splitbrain.org/services/ook)）

![在这里插入图片描述](img/721e42d1a713ddf24bb01868424a18e7.png)

![在这里插入图片描述](img/8d876270f29bb2f9b1fa0db07bde83aa.png)

flag为flag{e4bbef8bdf9743f8bf5b727a9f6332a8}