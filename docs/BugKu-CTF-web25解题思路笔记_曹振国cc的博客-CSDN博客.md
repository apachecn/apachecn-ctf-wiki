<!--yml
category: 未分类
date: 2022-04-26 14:54:54
-->

# BugKu CTF web25解题思路笔记_曹振国cc的博客-CSDN博客

> 来源：[https://blog.csdn.net/weixin_45736958/article/details/117403714](https://blog.csdn.net/weixin_45736958/article/details/117403714)

<mark>注:关于SQL约束攻击可以查看这篇文章</mark>：[sql注入学习总结](https://www.cnblogs.com/vincy99/p/9642941.html)

* * *

#### 一、打开环境

> 提示：SQL约束攻击

![image-20210530192758022](img/07d1bf653f20644b6a79ee7794c0b028.png)

#### 二、注册账号登录

> 发现应该是需要管理员登录才能查看到flag

![image-20210530193307762](img/ac7b89e66dba378bcf86b08fec98378e.png)

#### 三、注册admin

> admin +一个或多个空格可以注册

![image-20210530193512607](img/58f2a89cfa98f83bad1c058ce4175419.png)

#### 四、登录查看到flag

![image-20210530193540404](img/73242de61abc9b14801b7dc76e040a39.png)