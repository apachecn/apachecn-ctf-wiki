<!--yml
category: 未分类
date: 2022-04-26 14:34:37
-->

# bugkuCTF平台逆向题第二道Easy_Re题解_iqiqiya的博客-CSDN博客_easy_re

> 来源：[https://blog.csdn.net/xiangshangbashaonian/article/details/78915909](https://blog.csdn.net/xiangshangbashaonian/article/details/78915909)

题目链接：

http://123.206.31.85/files/1b62f97dce25f61c3a43ee953e7467c6/re1.exe

tips:

![](img/4de5ebc6de09cefc0586ce31122f5111.png)

下载后打开

![](img/d71c8e3d137111a2f305d22df892eac8.png)

查壳 无壳

![](img/4eb4cc271a8f3978cb78006e2c5829d1.png)

OD载入直接右键选择中文搜索引擎-->智能s搜索   就可以看到字符串flag

![](img/18fef9bc454d6300466749d55ca69cf7.png)

IDA载入 将这两处转为字符即可 但发现flag是逆序的 反过来就好

![](img/053c4a923cd47a42eb799dd28b2c8bdb.png)

最后验证下

![](img/31977ffe8d0783ae04cd7315714bf8a0.png)