<!--yml
category: 未分类
date: 2022-04-26 14:46:19
-->

# 静默开水的博客_CSDN博客-CTF,web题解题思路,Misc 图片隐写领域博主

> 来源：[https://blog.csdn.net/cadandme/article/list/1](https://blog.csdn.net/cadandme/article/list/1)

记录模块注入学习过程题目：Web_python_template_injection提醒是模块注入在Jinja2模板引擎中，{{}}是变量包裹标识符。{{}}并不仅仅可以传递变量，还可以执行一些简单的表达式。模块注入测试：网址输入“{{1*21}}”页面显示21，说面存在“SSTI”在其他大佬的博客中收集的相关知识：SSTI也是获取了一个输入，然后再后端的渲染处理上进行了语句的拼接，然后执行。当然还是和sql注入有所不同的，SSTI利用的是现在的网站模板引擎(下面会提到)，主要针对pyt

2022-02-15 15:47:28 ![](img/c435921c498fd8cf48f9f07527be548a.png)3046

BUUCTF加固题 Ezsql

2022-02-14 14:44:16 ![](img/c435921c498fd8cf48f9f07527be548a.png)2718

粗心大意要不得

2022-02-13 21:07:33 ![](img/c435921c498fd8cf48f9f07527be548a.png)355

信息隐写的三个姿势

2022-01-26 19:48:17 ![](img/c435921c498fd8cf48f9f07527be548a.png)2220

文件包含打开页面，典型的文件包含，文件包含的读取格式如下：?file=php://filter/read=convert.base64-encode/resource=flag读取到base64编码再解码，就得到因为解码后还有url编码存在，谨慎起见，放到URL解码，得到真正的flag...

2022-01-22 13:10:31 ![](img/c435921c498fd8cf48f9f07527be548a.png)1765

php黑魔法代码审计题型ord() 函数返回字符串的首个字符的 ASCII 值。知道以上函数的使用方法，就可以知道这道题应该怎么解要传入一个没有1到9数字的字符串，但值要等于54975581388，可以使用进制转换来代替将得到的十六进制GET传入得flag...

2022-01-20 10:27:01 ![](img/c435921c498fd8cf48f9f07527be548a.png)1616

ping打开页面提示用GET方式提交ip尝试管道符分隔命令查看目录，没回显，被禁用在这里我尝试了好久，忘记有哪些命令分隔符可以代替了，查找后才知道Linux下一般的命令分隔符有这几个| || & && . ; - <> $ %0a %0d ` 逐个尝试，%0a可以使用到这一步，我们就可以看出flag就在fL4g_1s_He4r_______中了，直接查看，得到flag...

2022-01-19 10:34:16 ![](img/c435921c498fd8cf48f9f07527be548a.png)788

云演 exec提示命令执行exec要求post传输一个ip值，尝试127.0.0.1，其回显为：尝试进行管道命令执行因为有长度限制，所以直接输入*.php，查看源码查看路径，发现flag，查看flag

2022-01-18 11:15:06 ![](img/c435921c498fd8cf48f9f07527be548a.png)364

倒立屋

2022-01-14 17:35:41 ![](img/c435921c498fd8cf48f9f07527be548a.png)46

云演CTF中的一道misc题

2022-01-14 11:25:56 ![](img/c435921c498fd8cf48f9f07527be548a.png)32

今天进行的CTF比赛，跟队友一起做出了几道CTF题，挑几道签到题记录一下，主要记录一下自己参加比赛的点滴，讲解不全，请见谅。第一题：小猪佩奇，看的我眼花用stegsolve打开查看，眼神好的立马就可以发现背景有二维码碎片，一共有25张图，25张碎片需要拼接。大佬们都是脚本跑出来的，但小白的我只能苦哈哈的抠图。在队友的帮忙下，漫长的扣图后产品如下：扫出来后是密文，base解码两次后为猪圈密码，小猪佩奇也是一个提示吧。第二题 ：misc签到题流量分析，给了一个流量包，在kali中文件分离后在一个压

2021-05-23 23:10:16 ![](img/c435921c498fd8cf48f9f07527be548a.png)392

PHP 变量PHP 变量规则：• 变量以 $ 符号开始，后面跟着变量的名称• 变量名必须以字母或者下划线字符开始• 变量名只能包含字母数字字符以及下划线（A-z、0-9 和 _ ）• 变量名不能包含空格• 变量名是区分大小写的（$y 和 $Y 是两个不同的变量）• 当赋一个文本值给变量时，需要在文本值两侧加上引号。PHP 是一门弱类型语言PHP 会根据变量的值，自动把变量转换为正确的数据类型。在强类型的编程语言中，我们必须在使用变量前先声明（定义）变量的类型和名称。PHP 变量作用域

2021-05-11 23:48:13 ![](img/c435921c498fd8cf48f9f07527be548a.png)19

刷了一道web题，记录做题过程。打开页面看到打开源码，尝试解码。试了UTF-8，尝试把得到的数据导成文档。结果是乱码，走入了死胡同。数据库，数据库，又想到扫描目录，在kali扫描，发现东西先看txt，再看phpinfo.php到这里彻底不懂了，瞎搞了没看出东西后，看了大佬的wp，说是phpmyadmin 4.8.1 远程文件包含漏洞（CVE-2018-12613）后面跟着wp来，可惜在构造Pyaload使用时显示index.php?target=db_sql.php%253f/

2021-05-11 23:38:44 ![](img/c435921c498fd8cf48f9f07527be548a.png)44 ![](img/9274138a3dcedc599be983ca211bfd87.png)1

BUU 乌镇峰会种图刷web题刷到头晕，回过头来做misc题，做的好爽！！！打开看到一张图片用stegsolve打开，看了一下有没有夹着什么信息，看了一会后在File Format直接找到flag复制粘贴提交，结果出错，可能是格式有问题，打开16进制编译器再看一下重新复制粘贴提交，一气呵成，真的好爽，被web题虐了那么久，misc签到题又给了我安慰，接下来继续疯斗web题。...

2021-05-08 16:51:18 ![](img/c435921c498fd8cf48f9f07527be548a.png)37

BUU–[ZJCTF 2019]NiZhuanSiWei刷了一道题，思路是有一些了，但还是看大佬的wp才做出来，记录一下。看了很久才知道要get传三个参数，第一个if是限制text，传入的内容在读取文件后必须是后边的一段，这个可以使用伪协议，但 php://input 使用这个协议时失败了，与之相似的写入文件的协议还有data协议，使用这个协议?text=data://text/plain;base64,[d2VsY29tZSB0byB0aGUgempjdGY=]第二个if这个来查看文件的，

2021-05-06 23:05:58 ![](img/c435921c498fd8cf48f9f07527be548a.png)115 ![](img/9274138a3dcedc599be983ca211bfd87.png)4

GIF格式不依靠特定工具找出flag内容：今天做了道web题，跟大家分享一下解题方法。打开后是一个gif，然后我就尝试用各种方式去看gif的内容，在用记事本打开后看到一大堆乱码的最后有这么一串字符串#我发现跟asc解加密有关，但asc解加密还需要一个密码，而在gif显示出来有几个字母，符合密码条件。从而找出flag。主要：在这里主要分享：在不用特定软件的情况如何在一个gif里面找出我们需要的画面，我们可以打开gif然后右击，选择编辑并创建里面的视频制作。进入后的画面如下，我们可以很轻松的找到

2021-02-27 17:03:01 ![](img/c435921c498fd8cf48f9f07527be548a.png)351

图片隐写，撩妹小技巧所谓图片隐写就是在一张图片或文件里面隐藏上一些不想让一般人看到的内容，图片隐写可以分为隐藏和解读两部分。一、常见的文件头二、图片隐写例子前言在平时生活中，有一些不想让所有人都看见的内容，或在谈恋爱中，为了追求一些小浪漫，在朋友圈秀恩爱又不行太明目张胆，可以用上图片隐写知识一、常见的文件头在这之前我们得知道一些基本的文件头知识，只有这样我们才知道要在一张图片或者文件里面隐藏文字图片应该如何下手。JPEG(jpg),文件头：开头 FFD8 , 结尾 FFD9PNG(p

2021-02-23 11:45:02 ![](img/c435921c498fd8cf48f9f07527be548a.png)133

Nmap探测端口命令对某个端口进行探测： nmap -p80 scanme.nmap.org 【80为端口号 ； scanme.nmap.org为nmap官方网站】这里以探测Nmap官网80端口为例。对某几个端口进行探测格式： nmap -p80,135 scanme.nmap.org3.对某个范围端口进行探测格式： nmap -p1-100 scnme.nmap.org4.对所有端口进行探测格式： nmap -p- ip地址【或者域名】5.指定协议探测端口

2021-02-08 17:03:10 ![](img/c435921c498fd8cf48f9f07527be548a.png)154

Nmap安装在nmap官网https://nmap.org下载了Windows版本的nmap在下载界面中找到Windows版本下载最新版nmap双击安装点击 I Agree点击Next自行选择安装在那个位置，点击 install 完成安装。

2021-02-08 16:09:02 ![](img/c435921c498fd8cf48f9f07527be548a.png)876