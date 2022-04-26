<!--yml
category: 未分类
date: 2022-04-26 14:53:16
-->

# BUUCTF-Crypto-变异凯撒解题思路_Georgeiweb的博客-CSDN博客_buuctf 变异凯撒

> 来源：[https://blog.csdn.net/Georgeiweb/article/details/108928448](https://blog.csdn.net/Georgeiweb/article/details/108928448)

## BUUCTF-Crypto-变异凯撒解题思路

## 题目

加密密文：afZ_r9VYfScOeO_UL^RWUc
格式：flag{}

## 解答思路

由密文可以看出，有大小写字母，并且还有下划线和阿拉伯数字，所以我们基本可以看出需要使用ASCII码表。
![在这里插入图片描述](img/cf65ffbcc076bbe21bc7fe6794634dd8.png)
又因为明文flag对应afZ_,所以寻找明文和密文的规律
f-102 a-97 相差5
l-108 f-102 相差6
a-97 Z-90 相差7
g-103 _-95 相差8
可以看出每个字符的偏移量为n+4
所以依次算出各密文字符对应的明文字符求得明文为
flag{Caesar_variation}