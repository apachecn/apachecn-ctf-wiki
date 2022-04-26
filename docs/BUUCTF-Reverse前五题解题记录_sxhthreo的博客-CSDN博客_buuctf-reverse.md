<!--yml
category: 未分类
date: 2022-04-26 14:49:03
-->

# BUUCTF Reverse前五题解题记录_sxhthreo的博客-CSDN博客_buuctf reverse

> 来源：[https://blog.csdn.net/sxH3O/article/details/123220088](https://blog.csdn.net/sxH3O/article/details/123220088)

# 第一题：easyre

直接找到字符串即可。

# 第二题：reverse1

这题进入ida找不到main函数，但可以通过shift+F12（我的电脑还要同时按住Fn），查找此程序的string集。如图：

![](img/84d88f71453813967279ecb91090bacc.png)

通过该图可以看到this is the right flag!字符串，点进去如下图所示：

![](img/8ba77332432e7976c21446757d00991d.png)
DATA XREF是交叉引用的意思，我点入DATA XREF: sub_1400118C0:loc_140011996↑o，找到了程序的“主函数”，如图：

![](img/1bbf34dbfc581127ec6760641f4aa58a.png) 在这里找到了flag，即“{hello_world}”。

# 第三题：reverse2

![](img/5a6f192eb9592b07433114aa45d1c5c7.png) 这题比较简单，点击"flag"即可获取flag：

![](img/f74fd3e421d0b6f80535f2b4e92ea7c6.png)

 然后将ascii为105（i）和114（r）进行代替操作（1）即可。

# 第四题：内涵的软件

简单，点shift+F5获取反汇编，直接获得flag。

![](img/5900672115485e728f90351db54f6d2a.png)

#  第五题：新年快乐

本题有点难度。由于ida左边只有两个函数，考虑本程序加壳。

![](img/eb960e628d8fbbe0d3164ec0fc2f61d4.png)

进入exeinfope分析，发现是upx壳，上网查找到了脱壳工具，它的操作是这样的：

打开工具文件夹，输入.\upx -d a.exe，成功脱壳。

![](img/7d4913d78327be3fed716ad6d1fd8870.png)

脱壳之后就很好办了。进入main函数，F5反汇编，直接获取flag。

![](img/1164dfa8a1f8ddc71ed180492350620f.png) 2022.3.2 0:49