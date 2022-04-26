<!--yml
category: 未分类
date: 2022-04-26 14:19:59
-->

# BugkuCTF web题解析_qq_37078651的博客-CSDN博客_bugkuctfweb题解

> 来源：[https://blog.csdn.net/qq_37078651/article/details/88423588](https://blog.csdn.net/qq_37078651/article/details/88423588)

1、简单

 ![](img/da7ba1bc3749fbc87d7c74cd722ca241.png)

查看源码F12：![](img/da4aa9e390ea6fe40f337d09fde3a3c5.png)

2、计算器

![](img/61ca2356569596e05c2c1ecce1adfebd.png)

输入发现只能输入一个字符，遂查看源码修改maxlength值或者删掉。

![](img/56fef7b7b7c7d64eb8e9fb9a43b733fb.png)![](img/b1af7ea0bca9685fa61db772edb2f376.png)

3、web基础$_GET

![](img/b67eac0cfcc848270ea337182a3ada14.png)

GET请求参数what==flag即可.

4、web基础$_POST

![](img/71ecbf89208aa8761958e86c883992b0.png)

使用工具（我用的是hackbar）发送post请求

参数what=flag。

![](img/d7112fa841b4e3b3216d297580f4f121.png)

5、矛盾

```
$num=$_GET['num'];
if(!is_numeric($num))
{
echo $num;
if($num==1)
echo 'flag{**********}';
}
```

**is_numeric()** 函数用于检测变量是否为数字或数字字符串。

题意是get传参num不能为数字或数字字符串但值却等于1。

当num用科学计数法表示1：1*e*0

或者传入1x，x为任意字母串即可。

6、web3

![](img/339e1608377b59b9965c887bf23922a6.png)

一直在弹窗，查看源码。

![](img/0db3f4bbe715f70f2f21fed5f5c802f2.png)

发现一串编码，这是Unicode编码，扔到在线编码[http://tool.chinaz.com/tools/unicode.aspx](http://tool.chinaz.com/tools/unicode.aspx)即可得到：![](img/99668e65423748ca97f88e425ab98d28.png)

7、域名解析

[https://www.cnblogs.com/ECJTUACM-873284962/p/8995801.html](https://www.cnblogs.com/ECJTUACM-873284962/p/8995801.html)转自别人的。

8、你必须让他停下

![](img/5c5e1ab9666e0e073cacd599cfe6f0c5.png)

通过控制JS，关闭即可得到flag。