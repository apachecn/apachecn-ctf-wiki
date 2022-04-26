<!--yml
category: 未分类
date: 2022-04-26 14:51:47
-->

# CTF中Web找Flag题目（1）_「已注销」的博客-CSDN博客_web找flag

> 来源：[https://blog.csdn.net/TLXX1126/article/details/75050130](https://blog.csdn.net/TLXX1126/article/details/75050130)

今天做的一个题，题目上给了一个链接，链接是一个网页，网页上让输入一个pass key，

做题步骤是先用BP抓包，然后repeter看返回包

其中Content-Row是一个用base64加密过的一串字符，然后将其解码便得到了pass key

听火种CTF中胖虎大佬讲解是这么说的：

这个功能就是抓包和看返回包的过程

就跟你要看一个网站，你就得把你的IP地址各种请求告诉网站，网站知道后说我知道啦，给你返回一下你要看的内容

如果你的各种请求有危险，网站知道后就说不给看

大概就是这个意思吧

PS：好像Content-Row这个东西就是一个网络头吧

网安小白，欢迎大家批评指正，谢谢！