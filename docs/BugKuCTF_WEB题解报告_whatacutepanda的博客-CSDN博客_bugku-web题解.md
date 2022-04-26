<!--yml
category: 未分类
date: 2022-04-26 14:37:33
-->

# BugKuCTF_WEB题解报告_whatacutepanda的博客-CSDN博客_bugku web题解

> 来源：[https://blog.csdn.net/whatacutepanda/article/details/79607030](https://blog.csdn.net/whatacutepanda/article/details/79607030)

在学习CTF中，写点东西记录自己的学习，也算是一个反思吧。

http://ctf.bugku.com/challenges

web2：

![](img/7200ea83e2e73570773793bb84ea5ea2.png)

这个只用简单的按F12就能看到

![](img/1aa877bbfeefec7dfc9030288661f0db.png)

#### 文件上传测试：

![](img/943bb26ffd5f7b69d54624a521226c16.png)

![](img/4807a993544f0ab194e8c56a4a84af44.png)

我们首先随便传入一个PHP文件尝试一下

![](img/d27e11a8334fb5259689d12d52f12f6c.png)

![](img/b9d6aa8ed25237531cd2baf5de8458fe.png)

发现提示说非图片文件

那么我们再尝试传入一个图片

![](img/4d0a5ce53362de07a4955894e5eefa1b.png)

![](img/dba9faac2ecf89e4463e319f17d4df55.png)

然后我们思考一下

题目要求说上传一个PHP文件

但是上传PHP时提示非图片，上传图片时提示非PHP

那我们先尝试上传一个PHP然后用burp抓包将其修改为图片

但我觉得我的想法天真了PHP文件在burp中并没有文件类型所以单纯改变后缀无法欺骗

因此我们改为上传一个图片用burp抓包改为PHP文件

![](img/220a6e8bb3d4b8b14f6b4b0088980d50.png)

![](img/26b77e69488595c33cbd413f98e1c730.png)

![](img/3c48803299d18f068ac2c6b6fdb1ee04.png)

#### 计算器：

![](img/511096c8330627f48ff5c9c635c49684.png)

![](img/32cb2f478152d35970269ae8eac35993.png)

（此题记得允许JS）

验证码计算 发现无法输入结果 只能输入一个数字

所以我们按F12查看源代码

![](img/68d195331780a244181d484692f9102f.png)

我们发现对输入的长度有了限制

所以我们直接把长度改了 把结果输入即可

![](img/66e4fe8e8ee5105f645b785e130ad9f1.png)

#### web基础$_GET：

我们直接在URL中GET即可

![](img/a3a52f31536c3bccf8bdc61cc22d4b58.png)

![](img/94998968e50aefd14ec10485e66cf7ca.png)

#### web基础$_POST：

![](img/92e440d9daa620af18f01e3f4cedf809.png)

这个简单的POST 我们可以直接利用firefox里面的hackbar附件

![](img/459ab144a3c2ca68790f38de703b8397.png)

![](img/29a59503e6b4b5e6cd7b296c0bc43120.png)

#### 矛盾：

![](img/ec308ac07b8889e0e0acdad48d3f4e34.png)

题目要求我们GET一个is_numeric()不是数字而又==1

因此我们想到利用PHP中构造一个1e0

发现并不成功 然后就尝试了1e0.1就可以了

并不清楚为什么 有明白的大佬如果看到求教原理

![](img/686c9e3d537dd5e9a6684e547cd3a976.png)

![](img/16961832f44455e19ea951237aa0d9ca.png)

#### web3：

![](img/18592bf00957f5b36a5d8cd611aee7ea.png)

这个题进去之后你会发现一直点确定会一直重复出现

![](img/99f37e8f378886e1322ae4073efbdf5c.png)![](img/7e529643252d32f883d55bc1b686874d.png)

于是我们直接“阻止此页面创建更多的对话框”or禁止JS后再打开

然后我们F12查看源代码

发现里面重复弹窗多次 所以我们直接翻到最下面

![](img/02f917966a10483110eeabb1130115d0.png)

发现其中有&#xxxx格式的“编码”

实际上这是一些转义序列而不是编码（之际上这里我也不懂。。。有懂的大佬请指教）

注：&#后接十进制数字 &#x后面接十六进制数字

直接把这一串序列找个在线转码扔进去就好

![](img/25eaec6b10db4281711eea25b1b9925c.png)