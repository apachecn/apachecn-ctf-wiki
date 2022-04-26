<!--yml
category: 未分类
date: 2022-04-26 14:50:21
-->

# php string ctf,PHP字符解析特性绕WAF【buuctf题 easy calc】_weixin_39939601的博客-CSDN博客

> 来源：[https://blog.csdn.net/weixin_39939601/article/details/115575821](https://blog.csdn.net/weixin_39939601/article/details/115575821)

一、PHP字符串解析特性

PHP需要将所有参数转换为有效的变量名，因此在解析查询字符串时，它会：

1.删除空白符

2.将某些字符转换为下划线(包括空格)

例如，/?%20news[id%00=42会转换为Array([news_id] => 42)，然后参数%20news[id%00的值将存储到$_GET["news_id"]中。(URL编码%20是空格，%00是0)

例如：

User input

Decoded PHP

variable name

%20foo_bar%00

foo_bar

foo_bar

foo%20bar%00

foo bar

foo_bar

foo%5bbar

foo[bar

foo_bar

二、parse_str函数

parse_str函数通常被自动应用于get、post请求和cookie中。那么解析函数到底是如何处理这些特殊字符的，假设特殊字符在3个位置：

1.[1st]foo_bar

2.foo[2nd]bar

3.foo_bar[3rd]

下面粘贴两张大佬的测试结果：

特殊字符在最前端：(空格)、(+)  可被删掉；

特殊字符在中间：(空格)、(+)、(.)、([)、(_)被转换成下划线；

特殊字符在末尾：%00()空白字符会被删掉；

![d94dbddc0a45a49bb399c0615192112f.png](img/9ce08465322fea3000beafd453f45764.png)

![30fec000f9a953bdd8065710742932dc.png](img/2b2457508f99b8b0ad1a5f0e0e67873b.png)

三、下面是一道CTF题

1、主页面只是个计算器，右键查看源代码，可以看到有两个信息点：一是上了WAF，二是被发送到calc.php进行计算了。

![ff63f4d04dcca1738e12d2682c7212fe.png](img/65818d11e1a5a05511aad8aa0aab8d5e.png)

2、访问calc.php看到源码,获取一个num的变量，并进行了黑名单过滤

3、发现num只要传入了字符就会报错，应该是WAF

![f06c243f99ee30b00f22e40471e0e847.png](img/8ee93595e689c95eaf1e74e4e4d9846f.png)

![805ef79496b9a10d0b7e427304bc5794.png](img/000e744415fa03528f49854bc2d73bbc.png)

4、发现利用PHP的字符串解析特性就能够绕过waf，WAF检测的可能是num这个变量，我们在前面加个空格变成%20num，WAF就检测不到，而PHP解析的时候把空格删掉了正确解析成num,所以可以绕过。

![55c5777899874553392981b9721a7626.png](img/434e8f03863c257d2f4aebd75dda00a7.png)

5、可以用  ?%20num=var_dump(scandir(/)) 来查看根目录下的文件和目录,但是/在黑名单里，所以我们把它换成chr(47)，

payload:   ?%20num=var_dump(scandir(chr(47)))

得到数组![b69fb108cb63c94d10eb326adfaf604d.png](img/7a09313e7fd6efa09b0a23b6bfcc41ef.png)

6、用file_get_contents()函数读取f1agg文件的内容，

payload: ?%20num=file_get_contents(chr(47).f1agg)

![4e42a49451079b3beb27f66580434027.png](img/79306ffcda69edfcc9b16932211d48e2.png)

scandir函数：返回指定目录中的文件和目录的数组。

chr()函数：chr() 用一个范围在 range(256)内的(就是0～255)整数作参数，返回值是当前整数对应的 ASCII 字符。

file_get_contents()函数：file_get_contents() 把整个文件读入一个字符串中。

题目要点：

1、上了WAF，用PHP字符串解析特性绕过；

2、黑名单中的字符用chr函数来绕过；

3、用scandir函数读取文件和目录；

4、用file_get_contents函数读取具体文件内容；

参考文章：https://www.freebuf.com/articles/web/213359.html