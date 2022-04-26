<!--yml
category: 未分类
date: 2022-04-26 14:42:12
-->

# python 重定向 ctf_CTF web题型解题技巧 第四课 web总结_weixin_39615984的博客-CSDN博客

> 来源：[https://blog.csdn.net/weixin_39615984/article/details/111008515](https://blog.csdn.net/weixin_39615984/article/details/111008515)

以下内容大多是我在学习过程中，借鉴前人经验总结而来，部分来源于网络，我只是在前人的基础上，对CET WEB进行一个总结；

-----------------------------------------------------------

-----------------------------------------------------------

工具集：

基础工具：Burpsuite，python，firefox(hackbar，foxyproxy，user-agent，swither等)

***了解Burpsuite的使用方式、firefox(hackbar，foxyproxy，user-agent，swither等)插件的使用给漏洞挖掘带来便利。

扫描工具：nmap，nessus，openvas

***了解nmap等扫描工具的使用。

sql注入工具：sqlmap等

***注入在CTF WEB中比较常见，通过暴库找到flag

xss平台：xssplatfrom，beef

***利用xss弹cookie的方式弹出flag

文件上传工具：cknife

文件包含工具：LFlsuite

暴力破解工具：burp暴力破解模块，md5Crack，hydra

基础篇

1.直接查看源代码

http://lab1.xseclab.com/base1_4a4d993ed7bd7d467b27af52d2aaa800/index.php

2.修改或添加HTTP请求头

常见的有：

Referer来源伪造

X-Forwarded-For：ip伪造

User-Agent：用户代理(就是用什么浏览器什么的)

http://lab1.xseclab.com/base6_6082c908819e105c378eb93b6631c4d3/index.php

抓取数据包，修改User-Agent：为HAHA

//.net的版本修改，后面添加，如版本9

.NET CLR 9

Accept-Language：语言

http://lab1.xseclab.com/base1_0ef337f3afbe42d5619d7a36c19c20ab/index.php

http://ctf1.shiyanbar.com/basic/header/

Cookie的修改

http://lab1.xseclab.com/base9_ab629d778e3a29540dfd60f2e548a5eb/index.php

3.查看HTTP请求头或响应头

http://lab1.xseclab.com/base7_eb68bd2f0d762faf70c89799b3c1cc52/index.php

http://ctf1.shiyanbar.com/basic/catch/

4.302跳转的中转网页有信息

http://lab1.xseclab.com/base8_0abd63aa54bef0464289d6a42465f354/index.php

5.查看开发者工具控制台

6.javascript代码绕过

通过删除或修改代码或者本地代理改包绕过

http://lab1.xseclab.com/base10_0b4e4866096913ac9c3a2272dde27215/index.php

输入框，我随便输入了111，提示数字太小。

然后我通过用burp抓包，更改数据包数据长度

7.使用burp的repeater查看整个HTTP包

http://lab1.xseclab.com/xss1_30ac8668cd453e7e387c76b132b140bb/index.php

8.阅读javascript代码，直接控制台获取正确密码

http://ctf1.shiyanbar.com/basic/js/index.asp

9.robots.txt文件获取信息

这本来是给搜索引擎看的信息，很可能暴露网站结构目录

http://lab1.xseclab.com/base12_44f0d8a96eed21afdc4823a0bf1a316b/index.php

you find me,but I am not the login page. keep search.

这里有个提示：login page

http://lab1.xseclab.com/base12_44f0d8a96eed21afdc4823a0bf1a316b/9fb97531fe95594603aff7e794ab2f5f//login.php

10..bash_history

这个应该说看到过吧，就是记录用户输入过的linux命令的

前端脚本类

1.js加解密

http://ctf5.shiyanbar.com/DUTCTF/1.html //直接在F12控制台粘贴就有了

2.XSS

http://lab1.xseclab.com/realxss1_f123c17dd9c363334670101779193998/index.php

这题题目就有漏洞，直接在命令行输入下面的就有了

当然简单的直接输入

这样也可以

这题也差不多

http://lab1.xseclab.com/realxss2_bcedaba7e8618cdfb51178765060fc7d/index.php

可以直接输入上题的那个jquery，也可以乖乖下面的

http://lab1.xseclab.com/realxss3_9b28b0ff93d0b0099f5ac7f8bad3f368/index.php

ctf中php常见的考点

本博文转自：大佬博客(侵权删)

总结的很详细，忍不住转载

1.系统变量

2.错误控制运算符

PHP 支持一个错误控制运算符：@。当将其放置在一个PHP表达式之前，该表达式可能产生的任何错误信息都被忽略掉。

3.变量默认值

当定义一个变量，如果没有设置值，默认为0

4.$_GET 和 $_POST

http://ctf4.shiyanbar.com/web/false.php?name[]=a&password[]=b

如果GET 参数中设置name[]=a，那么$_GET['name'] = [a]，php会把[]=a当成数组传入，$_GET会自动对参数调用urldecode。

$_POST同样存在此漏洞，提交的表单数据，user[]=admin，$_POST['user']得到的是['admin']是一个数组。

5.内置函数的松散性

strcmp

strcmp 函数的输出含义如下:

如果str1 小于str2返回< 0；

如果str1 大于str2返回> 0；

如果两者相等，返回0。

•5.2 中是将两个参数先转换成string类型。

•5.3.3 以后，当比较数组和字符串的时候，返回是0。

•5.5 中如果参数不是string类型，直接return了

$array=[1, 2, 3];

// 数组跟字符串比较会返回0

//这里会输出null，在某种意义上null也就是相当于false，也就是判断为相等

var_dump(strcmp($array, 'abc'));

sha1 和 md5 函数

md5 和sha1无法处理数组，但是php没有抛出异常，直接返回fasle

sha1([]) ===false

md5([]) ===false

弱类型

当一个整形和一个其他类型行比较的时候，会先把其他类型intval 再比较

intval

intval() 在转换的时候，会从字符串的开始进行转换直到遇到一个非数字的字符。即使出现无法转换的字符串，intval()不会报错而是返回0。

这个时候$a 的值有可能是1002 union…

is_numeric

PHP提供了is_numeric函数，用来变量判断是否为数字。但是函数的范围比较广泛，不仅仅是十进制的数字。

in_array

in_array函数用来判断一个值是否在某一个数组列表里面，通常判断方式如下：

in_array('b', array('a', 'b', 'c');

这段代码的作用是过滤GET 参数typeid在不在1，2，3，4这个数组里面。但是，in_array函数存在自动类型转换。如果请求，typeid=1’ union select..也能通过in_array的验证。

== 和 ===

•== 是弱类型的比较

•=== 比较符则可以避免这种隐式转换，除了检查值还检查类型。

以下比较的结果都为true

hash 比较的问题

0e 开头且后面都是数字会被当作科学计数法，也就是等于0*10^xxx=0。如果md5是以0e开头，在做比较的时候，可以用这种方法绕过。

如果要找出0e 开头的hash碰撞，可以用如下代码。

switch

如果switch 是数字类型的case的判断时，switch会将其中的参数转换为int

类型。

这个时候程序输出的是i is less than 3 but not negative，是由于switch()函数将$i进行了类型转换，转换结果为2。

正则表达式

preg_match

如果在进行正则表达式匹配的时候，没有限制字符串的开始和结束(^ 和$)，则可以存在绕过的问题

ereg %00 截断

ereg 读到%00的时候，就截止了

这里a=abcd%001234，可以绕过

变量覆盖

extract

extract() 函数从数组中把变量导入到当前的符号表中。对于数组中的每个元素，键名用于变量名，键值用于变量值。

parse_str

parse_str() 的作用是解析字符串，并注册成变量。与parse_str()类似的函数还有mb_parse_str()，parse_str将字符串解析成多个变量，如果参数str是URL传递入的查询字符串(query string)，则将它解析为变量并设置到当前作用域。

$$ 变量覆盖

如果把变量本身的key 也当变量，也就是使用了$$，就可能存在问题。

例子

unset

unset($bar); 用来销毁指定的变量，如果变量$bar包含在请求参数中，可能出现销毁一些变量而实现程序逻辑绕过。

特殊的PHP 代码格式

以这种后缀结尾的php 文件也能被解析，这是在fast-cgi里面配置的

•.php2

•.php3

•.php4

•.php5

•.php7

•.phtml

正则检测文件内容中包含 就异常退出，通常的PHP代码就不行了，可以使用这种方式绕过

效果等于echo ‘a’;

如果在php.ini 文件中配置允许ASP风格的标签

则可以使用该方式

伪随机数

mt_rand()

mt_rand() 函数是一个伪随机发生器，即如果知道随机数种子是可以预测的。

linux 64 位系统中，rand()和mt_rand()产生的最大随机数都是2147483647，正好是2^31-1，也就是说随机播种的种子也是在这个范围中的，0 – 2147483647的这个范围是可以爆破的。

但是用php 爆破比较慢，有一个C的版本，可以根据随机数，爆破出种子php_mt_seed。

在php > 4.2.0 的版本中，不再需要用srand()或mt_srand()函数给随机数发生器播种，现已由PHP自动完成。php中产生一系列的随机数时，只进行了一次播种，而不是每次调用mt_rand()都进行播种。

rand()

rand() 函数在产生随机数的时候没有调用srand()，则产生的随机数是有规律可询的。具体的说明请看这里。产生的随机数可以用下面这个公式预测:

可以用下面的代码验证一下

反序列化

•__construct()：构造函数，当对象创建(new)时会自动调用。但在unserialize()时是不会自动调用的。

•__destruct()：析构函数，当对象被销毁时会自动调用。

•__wakeup() ：如前所提，unserialize()时会自动调用。

PHP unserialize() 后会导致__wakeup()或__destruct()的直接调用，中间无需其他过程。因此最理想的情况就是一些漏洞/危害代码在__wakeup()或__destruct()中。

__wakeup 函数绕过

PHP 有个Bug，如果反序列化出现问题，会不去执行__wakeup函数，例如：

使用这个payload 绕过__wakeup函数

在字符串中，前面的数字代表的是后面字符串中字符的个数，如果数字与字符个数不匹配的话，就会报错，因此将1改成2就会产生报错，导致不会去执行__wakeup函数，从而绕过该函数。

文件包含

http://10.2.1.1:20770/index.php?page=upload这种url很容易就能想到可能是文件包含或者伪协议读取http://10.2.1.1:20770/index.php?page=php://filter/read=convert.base64-encode/resource=upload

命令执行

反引号`

反引号` 可以调用shell_exec正常执行代码

preg_replace()

触发条件：

1.第一个参数需要e标识符，有了它可以执行第二个参数的命令

2.第一个参数需要在第三个参数中的中有匹配，不然echo会返回第三个参数而不执行命令，举个例子：

我们可以构造这样的后门代码

当访问这样这样的链接时就可以被触发

伪协议

php://filter

读取文件

php://input

写入文件，数据利用 POST 传过去

data://

将include 的文件流重定向到用户控制的输入流

可以用于控制file_get_contents 的内容为用户输入的流

phar://

发现有一个文件上传功能，无法绕过，仅能上传jpg后缀的文件。与此同时，无法进行文件包含截断。allow_url_include=on的状态下，就可以考虑phar伪协议绕过。

写一个shell.php文件，里面包含一句话木马。然后，压缩成xxx.zip。然后改名为xxx.jpg进行上传。最后使用phar进行包含

这里的路径为上传的jpg 文件在服务器的路径

zip://

上述phar:// 的方法也可以使用zip://

然后吧1.php文件压缩成zip，再把zip的后缀改为png，上传上去，并且可以获得上传上去的png的地址。

1.zip文件内仅有1.php这个文件

文件上传漏洞

正常的文件上传流程是这样的，首先接收POST 的文件，在tmp目录下生成临时文件，文件名是php[A-Za-z0-9]{6}，在php处理后删除临时文件，虽然没有文件上传，但是只要文件上传开启了就一定会创建临时文件，在这中途如果php意外退出则临时文件不会被删除，造成/tmp目录下可以留下任何内容。 内容构造好后，单纯爆破/tmp/phpxxxxxx文件名是不太现实但是也可行的。

通过文件包含，让其包含本身，造成无限循环后发出SIGSEGV 信号，可以导致php意外退出。

本文作者：Lemon