<!--yml
category: 未分类
date: 2022-04-26 14:49:14
-->

# 南邮CTF逆向题第一道Hello,RE!解题思路_iqiqiya的博客-CSDN博客

> 来源：[https://blog.csdn.net/xiangshangbashaonian/article/details/78878876](https://blog.csdn.net/xiangshangbashaonian/article/details/78878876)

首先可以看到提示如下

![](img/6634ba4bab94262d1a3940860c35f927.png)

我还是查了一下 无壳

提示用IDA

那我们就载入 shift+f12查找字符串

![](img/96d42a307aec22a49fdfbdfaef08fc60.png)

双击进入 在右侧窗口接着双击 然后f5看到了伪代码 于是点击字符串全按R即可

![](img/ff6f21ec3802e15bbfcfed13fee35808.png)

![](img/c950c56e2bf243e3e33a966eef9d69dd.png)

我再用OD试一下

![](img/4a3dac040f6bca091332f8f023ce1828.png)

段首F2下断

![](img/8752f151d37be82208ea0d8232a1b49c.png)

F9运行 段下来后接着单步

走到这个call处

![](img/e32d57e74da28a23e23fdd417e20cbe2.png)

输入假码 回车

![](img/117b52ab4f2ddec1dd578957de2de33f.png)

接着慢慢向下单步走

没走两步 就发现flag

![](img/46e587124baa5a2ae8cfcd61448b2fe3.png)