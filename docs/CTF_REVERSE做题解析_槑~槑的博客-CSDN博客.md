<!--yml
category: 未分类
date: 2022-04-26 14:33:20
-->

# CTF_REVERSE做题解析_槑~槑的博客-CSDN博客

> 来源：[https://blog.csdn.net/qq_63676207/article/details/122757522](https://blog.csdn.net/qq_63676207/article/details/122757522)

1

game

这个题刚开始点进去是这种不管如何输入都是死循环![](img/37ce46c6404d88e62fec78098ccc850f.png)

怎莫搞直接拖入IDA中注意是32位的

![](img/d311e09782ebee5331f4d59b8ff39c91.png)

然后显示这个页面点击F5查看伪代码是这个

![](img/9536399b14a6cb4b6ad56969a3eafad0.png)

然后直接跟进进入后 

![](img/b069e87cd78de14db19c6624e004a58c.png)

 是这个其他的看不懂这个也应该没啥问题当这八个都为一时进入下面这个地址猜测这个为末端

![](img/7f6238f54072fee38c1a953f9de9f254.png)

 ![](img/941c061aaedc10919625b2e1066b1fa6.png)是个异或然后再与十六进制13进行异或

由上面的数字自己写个脚本进行尝试运行

 ![](img/61e8b8bf4d134cb5d12f5a6512c17409.png)

 得出flag   注意：IDA中函数用sub_地址编号    那个v后面的数字啥的别抄错本人生有体会