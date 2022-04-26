<!--yml
category: 未分类
date: 2022-04-26 14:29:56
-->

# CTF 题型了解_秦罗敷写代码的博客-CSDN博客

> 来源：[https://blog.csdn.net/qq_58148144/article/details/123440357](https://blog.csdn.net/qq_58148144/article/details/123440357)

![watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA56em572X5pW35YaZ5Luj56CB,size_15,color_FFFFFF,t_70,g_se,x_16](img/9856feb3d0137b74d272c11a18a44d07.png)

 下一个成功的就是你

Web

Web类题目大部分情况下和网、Web、HTTP等相关技能有关。主要考察选手对于Web攻防的一些知识技巧。诸如SQL注入、XSS、代码执行、代码审计等等都是很常见的考点。一般情况下Web题目只会给出一个能够访问的URL。部分题目会给出附件

Pwn

Pwn类题目重点考察选手对于二进制漏洞的挖掘和利用能力，其考点也通常在堆栈溢出、格式化漏洞、UAF、Double Free等常见二进制漏洞上。选手需要根据题目中给出的二进制可执行文件进行逆向分析，找出其中的漏洞并进行利用，编写对应的漏洞攻击脚本(Exploit)，进而对主办方给出的远程服务器进行攻击并获取flag通常来说Pwn类题目给出的远程服务器信息为nc IP_ADDRESS PORT，例如nc 1.2.3.4 4567这种形式，表示在1.2.3.4这个IP的4567端口上运行了该题目ReverseRe类题目考察选手逆向工程能力。题目会给出一个可执行二进制文件，有些时候也可能是Android的APK安装包。选手需要逆向给出的程序，分析其程序工作原理。最终根据程序行为等获得flag

Crypto

Crypto类题目考察选手对密码学相关知识的了解程度，诸如RSA、AES、DES等都是密码学题目的常客。有些时候也会给出一个加密脚本和密文，根据加密流程逆推出明文。

Misc

Misc意为杂项，即不包含在以上分类的题目都会放到这个分类。题目会给出一个附件。选手下载该附件进行分析，最终得出flag