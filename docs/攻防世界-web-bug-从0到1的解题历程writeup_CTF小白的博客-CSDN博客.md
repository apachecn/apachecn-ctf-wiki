<!--yml
category: 未分类
date: 2022-04-26 14:54:23
-->

# 攻防世界-web-bug-从0到1的解题历程writeup_CTF小白的博客-CSDN博客

> 来源：[https://blog.csdn.net/qq_41429081/article/details/105534291](https://blog.csdn.net/qq_41429081/article/details/105534291)

## 题目分析

拿到题目首先注册了一下admin账号发现账号存在。然后后登陆后发现Manage需要admin权限，那么大概就是要获取到admin账号或者admin权限了。

![图片](img/654005a6228e16e094983a5275e9641b.png)

尝试了一下发现

![图片](img/ddd36a87c9e0921e4220cad4bcf76250.png)

先输入自己注册的正确账号令其跳转到修改密码界面，然后修改username直接找回admin的密码即可

成功登陆admin账号

![图片](img/d55803b29573a52df5b5b011f82dd445.png)

![图片](img/61574eddcc21da180c4be5114d2b9aa3.png)

发现进入admin模块提示IP NOT allowed。

尝试修改IP添加请求头X-Forwarded-For: 127.0.0.1

![图片](img/1991bdf9b9dab0d8dedd5350f75c21ad.png)

得到提示`<!-- index.php?module=filemanage&do=???-->`

u1s1这个do后面猜一个我是真的没有猜到，找了一下wp才知道，原来是do=upload，根据前面的filemanege猜出的。。。。。

![图片](img/ecfa9edc3a07d5875fdad4f5ee6b85a8.png)

发现是一个上传页面，估计就是上传图片马了。

随意上传了一下发现并不会给上传后的路径。。看了下wp才知道。。咋都是脑洞，只要成功上传了一个php文件似乎就给flag了。。

找了以前的一个图片马，修改后缀为php4或者php5都可以得到flag

![图片](img/724761180e55017074a2dd5bd9e6ce2d.png)