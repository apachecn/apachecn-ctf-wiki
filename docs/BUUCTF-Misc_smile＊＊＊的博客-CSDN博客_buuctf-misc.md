<!--yml
category: 未分类
date: 2022-04-26 14:52:37
-->

# BUUCTF Misc_smile***的博客-CSDN博客_buuctf misc

> 来源：[https://blog.csdn.net/qq_45784859/article/details/105769753](https://blog.csdn.net/qq_45784859/article/details/105769753)

# 基础破解

## 题目

给你一个压缩包，你并不能获得什么，因为他是四位数字加密的哈哈哈哈哈哈哈。。。不对= =我说了什么了不得的东西。里面还有一个压缩包。

## 思路

很明显里面那个rar压缩包需要暴力破解，密码是四位的数字，用i相关工具直接破解就可以得到flag，解密后里面是一个base64加密的字符串，解密即可得到flag。

# 你竟然赶我走

## 题目

里面是一张图片，用Winhex打开后搜索flag就可以看到。