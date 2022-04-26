<!--yml
category: 未分类
date: 2022-04-26 14:21:53
-->

# CTF解题-Bugku_Web_WriteUp (上）_Tr0e的博客-CSDN博客_bugku web writeup

> 来源：[https://blog.csdn.net/weixin_39190897/article/details/108014567](https://blog.csdn.net/weixin_39190897/article/details/108014567)

BugkuCTF平台是免费的CTF训练平台,题目数量多网上解析全面对新手入门友好。

![在这里插入图片描述](img/2ccdd07c1c7d954f8663a005ea591f09.png)
为了划水“强网杯”练习一下，先从Web部分下手……

![在这里插入图片描述](img/615063739a671bdd63f73063c3a5e162.png)

# N0.1 前端信息泄露

1、访问靶场链接，花里胡哨：

![在这里插入图片描述](img/f4335079cf03152117f16207572366e7.png)
2、入门题，直接查看搜索源码：

![在这里插入图片描述](img/8abd72b65fc605fa26729d43abfc920a.png)
3、解决：

![在这里插入图片描述](img/fb253c5591e6c83104c54f23e0f3d6ab.png)

# No.2 绕过前端限制

额，这题……没啥好所说的，原先限制前端字段输入长度为1，修改前端字段长度的限制即可：

![在这里插入图片描述](img/f9c1b0add8cc5a5ef2061d6b88a78dba.png)

# No.3 GET传参

1、看看解题链接：

![在这里插入图片描述](img/3fcc7ef158922d077896e29b5eea8243.png)

2、看到这没啥好说的……该提示的都提示了，直接获取 flag：

![在这里插入图片描述](img/ec9d5e19f726f62a776ff4556324470c.png)

# No.4 POST传参

1、看看解题链接：

![在这里插入图片描述](img/7d221167584abef750d445f67f09c86f.png)

2、使用 HackBar 插件发送 Post 请求传递 flag 即可：

![在这里插入图片描述](img/92111ffd461554ef68d0acf2c90b5d5e.png)

# No.5 科学计数法

1、看下解题链接：

![在这里插入图片描述](img/4c22f7a379a16e37979610a6153bfee4.png)

2、题目的要求即num既不能是数字字符，但是要等于1。我们可以想到用科学计数法表示数字1，既不是纯数字，其值又等于1。因此，构造payload：`num=1*e*0.1` 即可：

![在这里插入图片描述](img/c5449702bb64e3daf52724c8d754307d.png)

# No.6 Unicode转码

1、打开解题链接，不断反复弹框……

![在这里插入图片描述](img/1a5306ef35da6899c7f5fe3a2a0195a4.png)

2、禁止弹框后查看源码：

![在这里插入图片描述](img/e1cfc486a1151549a0f9ec49166d6d9a.png)

3、复制以上红框内容，解码得 Flag：

![在这里插入图片描述](img/ec25b8afe72257d03a297431d9ac68bc.png)

# No.7 本地域名解析

1、看看题目：

![在这里插入图片描述](img/17c0fb1049b3d677efe5c42a9c75f919.png)

2、简单，修改本地 host 文件，将域名解析到指定 IP 即可：

![在这里插入图片描述](img/3cee1dc86f78473ced75330ed3648bf3.png)

3、访问域名获得 Flag：

![在这里插入图片描述](img/c3e3f08bfd136911c81d570a63435688.png)

# No.8 数据包重放

1、查看解题链接，可以看到页面一直在抖动变换，时而会出现图片：

![在这里插入图片描述](img/9f6051c10be0891fa0fa142963be78b7.png)

2、使用 BP 抓包并 GO 重放多次，发现后台总共有15个jpg，后台会随机返回一个图片，如果 jpg 为10的时候就能得到flag：

![在这里插入图片描述](img/e0fa9edea85685001b0f6cde24db754b.png)

# No.9 本地包含

题目有问题……

# No.10 PHP全局变量

1、查看解题链接：

![在这里插入图片描述](img/cfc03aadeafb54c47262c418324362a4.png)
2、解题分析过程如下：

![在这里插入图片描述](img/68cb554bb88221a1f83451ec2c33a032.png)

3、获得 Flag：

![在这里插入图片描述](img/1f38401f6538049e89a17fa7a6b6800f.png)

# No.11 JSFuck编码

1、查看解题链接：

![在这里插入图片描述](img/ef77f5b07d234f5eb6219a44cf91d441.png)
2、随便输入字符提交试试：

![在这里插入图片描述](img/062b1eeae296fd5d57b8b7e31b12a5cc.png)
3、查看源码试试：

![在这里插入图片描述](img/24343f8900bf46d65b851967241b8aa0.png)

上面有一行非常奇怪的由`+[]()!`组成的代码，查了一下，这种东西似乎叫做jspfuck（呼应题目）。

> JSFuck（或为了避讳脏话写作 JSF*ck ）是一种深奥的 JavaScript编程风格。以这种风格写成的代码中仅使用 [、]、(、)、! 和 + 六种字符。此编程风格的名字派生自仅使用较少符号写代码的Brainfuck语言。与其他深奥的编程语言不同，以JSFuck风格写出的代码不需要另外的编译器或解释器来执行，无论浏览器或JavaScript引擎中的原生 JavaScript 解释器皆可直接运行。鉴于 JavaScript 是弱类型语言，编写者可以用数量有限的字符重写 JavaScript 中的所有功能，且可以用这种方式执行任何类型的表达式。

简单地说，就是有人不想让自己的代码被别人认出来，用6种字符改造了自己的 js代码，浏览器居然还能识别（惊了）！所以说直接把这段奇怪的代码扔进浏览器控制台，就可以得到 flag 了（记得要全变成大写）：

![在这里插入图片描述](img/f752d4c14acaa2a636dae2658118b49c.png)

# No.12 观察数据包

1、访问解题链接：

![在这里插入图片描述](img/761e32ee7bb31d34e932c306b9ae686c.png)

2、查看网页源码，发现也什么都没有……

![在这里插入图片描述](img/3e6ce2239b31f86e72e82b3700d3bd7e.png)

3、BurpSuite 抓包看看，发现响应包的头信息包含了Flag……

![在这里插入图片描述](img/b177e398fa1cd0c4e70901e2c35aecb2.png)

# No.13 Webshell暴破

1、查看解题链接：

![在这里插入图片描述](img/19e70765e8a453a55c10e989362676e6.png)
2、御剑扫描网站，获得Webshell地址：

![在这里插入图片描述](img/9cb883ec39ca546fd56718a8d2dcd0ae.png)

3、访问需要密码：

![在这里插入图片描述](img/ca7c13771b713a1ab9b1fe5a79cd445e.png)

4、BP暴力破解，获得密码hack：

![在这里插入图片描述](img/41b972e358c5421d02a48b9b318cb47a.png)

5、输入密码获得Flag：

![在这里插入图片描述](img/502f75c5c219fab5c317bed50801d4c2.png)

# No.14 本地IP伪造

1、查看解题链接，随意输入测试数据：

![在这里插入图片描述](img/945c14e894c246a036e2a6cfeba4aa02.png)2、查看源码，获得一串疑似 Base 64 字符串：

![在这里插入图片描述](img/b6feaf4a49fc8148d81fd9efb82ff3ce.png)
3、Base 64 转换获得 “test123”：

![在这里插入图片描述](img/022b0dc535f32ce8ab6c274bc7d4b9a0.png)
4、猜测是密码，故输入账户 admin 密码 test123，进行测试：

![在这里插入图片描述](img/7a5fad7321f82e98896e9e04ad26a033.png)
5、试着添加HTTP请求头：`X-Forwarded-For：127.0.0.1`，伪造成本地登录，获得 Flag：

![在这里插入图片描述](img/b84cb7ea6ceb8f07534049bb8400c996.png)

# No.15 前端源码转码

1、查看解题链接：

![在这里插入图片描述](img/11e1a46f6665223c96dcc816ca3491b5.png)
2、对以上字符串进行URL转码：

![在这里插入图片描述](img/fc863a621069f5263fdeeff5b6572d41.png)
3、源码中有这么一句：eval(unescape(p1) + unescape(’%35%34%61%61%32’ + p2))；含义是：`p1串的编码+‘%35%34%61%61%32’的编码+p2串的编码`。这是一个拼接的字符串，解码之后，拼接完成，回到网页中提交，网页直接爆出了flag：

![在这里插入图片描述](img/84e3013a300bf2a9d9dd527c51e6daa8.png)

# No.16 PHP文件包含

1、查看解题链接：

![在这里插入图片描述](img/14aaf3ddb710358945fb495f92581126.png)

点击链接，注意到 URL 地址：`index.php?file=show.php`，这是一个典型的文件包含漏洞：

![在这里插入图片描述](img/67f473a19384ccb30163bbbd79854de4.png)
2、下面会用到 [php的封装协议](http://php.net/manual/zh/wrappers.php.php)，具体怎么用呢，先说结果：

![在这里插入图片描述](img/041254dcda06637f7bab81ab0c61640c.png)
对得到的字符串进行 Base64 转码，获得 Flag：

![在这里插入图片描述](img/7f5e03ff0ea637c15b5236827ef2d234.png)
完整的转换结果如下：

```
<html>
    <title>Bugku-ctf</title>

<?php
	error_reporting(0);
	if(!$_GET[file]){echo '<a href="./index.php?file=show.php">click me? no</a>';}
	$file=$_GET['file'];
	if(strstr($file,"../")||stristr($file, "tp")||stristr($file,"input")||stristr($file,"data")){
		echo "Oh no!";
		exit();
	}
	include($file); 

?>
</html>
<þfl> 
```

现在具体说说`file=php://filter/read=convert.base64-encode/resource=index.php`的含义：

*   首先这是一个file关键字的get参数传递，php://是一种协议名称，`php://filter/`是一种访问本地文件的协议，`/read=convert.base64-encode/`表示读取的方式是base64编码后，resource=index.php表示目标文件为index.php。
*   通过传递这个参数可以得到index.php的源码，下面说说为什么，看到源码中的include函数，这个表示从外部引入php文件并执行，如果执行不成功，就返回文件的源码。
*   而include的内容是由用户控制的，所以通过我们传递的file参数，是include(）函数引入了index.php的base64编码格式，因为是base64编码格式，所以执行不成功，返回源码，所以我们得到了源码的base64格式，解码即可。

如果不进行base64编码传入，就会直接执行，而flag的信息在注释中，是得不到的。

# No.17 暴力破解……

1、查看解题链接：
![在这里插入图片描述](img/4780b55282d18b737d5a7825f29c7d4c.png)

2、题目提示了密码是5位数，暴力破解获得密码：

![在这里插入图片描述](img/a8ad996f957ddf42eed7821432e40a69.png)

3、获得Flag：

![在这里插入图片描述](img/4f8223182bf97400a13d9b32a5108b90.png)

# No.18 点击一百万次

1、查看解题地址（要是真的点击一百万次，怕是点到手抽筋）：

![在这里插入图片描述](img/082891c5d83d91d9079eb3e7380f46ff.png)
2、查看源码，发现了clicks变量，当它为1000000应该能得到 flag：

![在这里插入图片描述](img/2a5910823772ef7a724f078536317a04.png)
3、直接F12，选择控制台，然后输入clicks=1000000：
![在这里插入图片描述](img/da2516fc9573e6504b31f8498b5c25f7.png)
然后回车，再点击一下网站那个图案，发现得到了flag：

![在这里插入图片描述](img/46e27ed4d98dae06397d01a349731fe5.png)
另外的方法是BP拦截响应包修改clicks=1000000。

# No.19 备份文件泄露

1、查看题目链接：

![在这里插入图片描述](img/3c349305957644aadf19a834871ac43c.png)先解密字符串：

![在这里插入图片描述](img/1cc84f5e4dc520ef6929eac0e5285e35.png)
2、既然是备份文件的题，御剑扫描看看是否有备份文件（备份文件一般都是`.bak`或者`.swp`）：

![在这里插入图片描述](img/afbfddd9f54a4d431ce27a337a4ee23f.png)
下载下来：

![在这里插入图片描述](img/c4d08b2fb89a8d74f3795ff4b2af6885.png)
查看 index.php.bak 文件：

![在这里插入图片描述](img/31f393b343cadd1f4521d1df71ccd792.png)
3、解释下源码：

```
<?php
   include_once "flag.php";   
   ini_set("display_errors", 0);   
   $str = strstr($_SERVER['REQUEST_URI'], '?');     
   $str = substr($str,1);   
   $str = str_replace('key','',$str);       
   parse_str($str);

   echo md5($key2);    
   echo $flag."取得flag";  
}
?> 
```

整段代码的意思是将 get 的两个参数中的 key 替换为空（这里可以用kekeyy 绕过），然后对key1、key2的值进行md5加密，并进行比较，如果md5 加密的值一样而未加密的值不同，就输出flag。

4、构造以下 payload 获得 Flag：

![在这里插入图片描述](img/bfe49e82593d800efdff9846258910a2.png)

# No.20 成绩单(SQL注入)

1、查看解题链接：

![在这里插入图片描述](img/0a9bad8c5710469698eeefe9913b71d2.png)
2、抓包测试，发现存在SQL注入：

![在这里插入图片描述](img/7f28423417f36ac3bcecbabfe9c6e982.png)![在这里插入图片描述](img/3b5737f866675e6d11ead16139eefe1d.png)

3、SQLMap进行测试：

![在这里插入图片描述](img/7d1b54b76bf319d4972e9d84fc1729fd.png)
执行`sqlmap.py -r 111.txt --dbs`查看数据库名称：

![在这里插入图片描述](img/7613e12b9aa7f93589734cbc818b1d2f.png)
执行`sqlmap.py -r 111.txt -D skctf_flag --tables`查看数据库 skctf_flag 的表：

![在这里插入图片描述](img/285d86cb9661d8f5db6f5294972f229a.png)
执行命令`sqlmap.py -r 111.txt -D skctf_flag -T fl4g --columns`查看 fl4g 表的字段：

![在这里插入图片描述](img/d30551e83a981c507366940822ae9f9c.png)
执行命令`sqlmap.py -r 111.txt -D skctf_flag -T fl4g -C “skctf_flag” --dump`获取最终的 Flag 值：

![在这里插入图片描述](img/75c8f83541031aa4743879a378db408f.png)

# No.21 多重编码转换解读

1、查看解题链接：

![在这里插入图片描述](img/693e6794731ea65f977e7ccb44509c60.png)
2、访问`http://123.206.87.240:8006/test/1p.html` ，发现页面自动跳转到 http://www.bugku.com/，应该是有 `window.location.href` 之类的重定向，那就直接查看 1p.html 的源码，在链接前面加 view-source：

![在这里插入图片描述](img/5425e19df7c36dc54824d1a5be52ff5e.png)3、有发现！根据`%3C`来看Words变量应该是 url 编码，解码后发现注释部分还进行了base64编码：

![在这里插入图片描述](img/11500437238950d762a4f16e4779138d.png)
4、继续base64解码后，还有一层url编码：

```
<!--%22%3Bif%28%21%24_GET%5B%27id%27%5D%29%0A%7B%0A%09header%28%27Location%3A%20hello.php%3Fid%3D1%27%29%3B%0A%09exit%28%29%3B%0A%7D%0A%24id%3D%24_GET%5B%27id%27%5D%3B%0A%24a%3D%24_GET%5B%27a%27%5D%3B%0A%24b%3D%24_GET%5B%27b%27%5D%3B%0Aif%28stripos%28%24a%2C%27.%27%29%29%0A%7B%0A%09echo%20%27no%20no%20no%20no%20no%20no%20no%27%3B%0A%09return%20%3B%0A%7D%0A%24data%20%3D%20@file_get_contents%28%24a%2C%27r%27%29%3B%0Aif%28%24data%3D%3D%22bugku%20is%20a%20nice%20plateform%21%22%20and%20%24id%3D%3D0%20and%20strlen%28%24b%29%3E5%20and%20eregi%28%22111%22.substr%28%24b%2C0%2C1%29%2C%221114%22%29%20and%20substr%28%24b%2C0%2C1%29%21%3D4%29%0A%7B%0A%09require%28%22f4l2a3g.txt%22%29%3B%0A%7D%0Aelse%0A%7B%0A%09print%20%22never%20never%20never%20give%20up%20%21%21%21%22%3B%0A%7D%0A%0A%0A%3F%3E--> 
```

5、继续解码，获得：

```
<script>window.location.href='http://www.bugku.com';</script> 
<!--";if(!$_GET['id'])
{
	header('Location: hello.php?id=1');
	exit();
}
$id=$_GET['id'];
$a=$_GET['a'];
$b=$_GET['b'];
if(stripos($a,'.'))
{
	echo 'no no no no no no no';
	return ;
}
$data = @file_get_contents($a,'r');
if($data=="bugku is a nice plateform!" and $id==0 and strlen($b)>5 and eregi("111".substr($b,0,1),"1114") and substr($b,0,1)!=4)
{
	require("f4l2a3g.txt");
}
else
{
	print "never never never give up !!!";
}
?>--> 
```

6、访问 f4l2a3g.txt，获得 flag：

![在这里插入图片描述](img/0ca6cc62274fad99e8a6ec762ee0769b.png)

# No.22 正则表达式

1、访问解题链接：

![在这里插入图片描述](img/d8ee4cfedd22a3220579516bebba3823.png)
具体代码如下：

```
 <?php 
    highlight_file('2.php');
    $key='KEY{********************************}';
    $IM= preg_match("/key.*key.{4,7}key:\/.\/(.*key)[a-z][[:punct:]]/i", trim($_GET["id"]), $match);
    if( $IM ){ 
       die('key is: '.$key);
    }
?> 
```

**代码分析：**

*   highlight_file（filename，return）：对文件进行语法高亮显示；
*   preg_mach（string a, atring b，array matches）：执行匹配正则表达式（a是正则表达式，b是输入的字符串，matches是被填充为搜索结果）；
*   trim() 函数移除字符串两侧的空白字符或其他预定义字符；
*   die函数：输出一条消息，并退出当前脚本。

根据正则表达式`/key.*key.{4,7}key:/./(.key)[a-z][[:punct:]]/i`
构造参数：

| 正则表达式 | 释义 |
| --- | --- |
| . | 代表匹配除\n外的任意单字符 |
| {4，7} | 代表最少匹配4次，最多匹配7次 |
| / | 代表匹配“/” （注意\是转义符号） |
| (.key) | 代表匹配任意单字符和key |
| [a-z] | 代表匹配任意一个小写字母 |
| [[:punct:]] | 代表匹配任意一个标点符号 |

2、构造参数 `id=keykeykeykeykey:/ /keya@i`，获得 Flag：

![在这里插入图片描述](img/3fb7820f3f77ff7641da912c56b37064.png)**解析：**

```
 key        .      *        key      .      {4,7}  key:\/             \/  (       .     *        key)     [a-z]                 [[:punct:]]
‘key’+任意单个字符+零个或多个+‘key’+任意单个字符+长度4-7+‘key:/’+任意单个字符+ / +（任意单个字符+零个或多个+‘key’)+英文小写字母一个+匹配‘!" 
```

# No.23 Referer请求头构造

1、查看题目链接：

![在这里插入图片描述](img/262e1ba323ba19b0bdd01d04fda93092.png)
2、抓包，添加 referer 请求头，伪造来源，获得 flag：

![在这里插入图片描述](img/dfbda9f2c32e76b689fd5c068b9e04a9.png)

# No.24 PHP中的MD5碰撞

1、查看解题链接：

![在这里插入图片描述](img/3b3e2180e03519ba481365befc0289c5.png)![在这里插入图片描述](img/83e6ecc4bfd76a04e791ef3473082169.png)

2、此处附上此题的服务器代码：

```
<?php
$md51 = md5('QNKCDZO');
$a = @$_GET['a'];
$md52 = @md5($a);
if(isset($a)){
if ($a != 'QNKCDZO' && $md51 == $md52) {
    echo "nctf{*****************}";
} else {
    echo "false!!!";
}}
else{echo "please input a";}
?> 
```

**题目解析：**

> PHP在处理哈希字符串时，会利用`”!=”`或`”==”`来对哈希值进行比较，它把每一个以`”0E”`开头的哈希值都解释为0，所以**如果两个不同的密码经过哈希以后，其哈希值都是以`”0E”`开头的，那么PHP将会认为他们相同，都是0**。攻击者可以利用这一漏洞，通过输入一个经过哈希后以”0E”开头的字符串，即会被PHP解释为0，如果数据库中存在这种哈希值以”0E”开头的密码的话，他就可以以这个用户的身份登录进去，尽管并没有真正的密码。

3、所以随意输入md5值为0e开头的的原值即可获得 flag{md5_collision_is_easy}：

![在这里插入图片描述](img/c4fcde48030b1b737ca7845585ca0482.png)附上一些0e开头的md5和原值：

```
s878926199a
0e545993274517709034328855841020
s155964671a
0e342768416822451524974117254469
s214587387a
0e848240448830537924465865611904
s214587387a
0e848240448830537924465865611904
s878926199a
0e545993274517709034328855841020
s1091221200a
0e940624217856561557816327384675
s1885207154a
0e509367213418206700842008763514 
```

# No.25 Sha1哈希函数缺陷

1、查看题目链接：

![在这里插入图片描述](img/8694cde2726916e4453559b3e78cb52c.png)
2、PHP代码审计发现，只要使 uname 的 sha1 的值与 passwd 的sha1的值相等（但是同时他们两个的值又不能相等）即可获得Flag，我们**可以利用sha1 函数无法处理数组的特性即可**（当对sha1()函数传入数组时会返回null，由此只需要传入两个不同的数组即可成功绕过）：

![在这里插入图片描述](img/93f9081623030c3e5c97c56497b6abe8.png)

# No.26 PHP代码审计

1、查看解题链接：

![在这里插入图片描述](img/685de6307a833afefc46cfbc8625f6a4.png)![在这里插入图片描述](img/5c1265f5db15de8df9daee4adb74d8b3.png)
2、先根据 题目提示 txt??? 访问 flag.txt，发现其中内容 flags：

![在这里插入图片描述](img/e5b9040f853204a0f06cec503fa7026f.png)3、`$ac`是指flag.txt中的内容 flags，`$fn`指的是 flag.txt 这个文件，故可推导出 Payload：`?ac=flags&fn=flag.txt`，如下图：

![在这里插入图片描述](img/937a43b47d62f6e63190000cb7919ee2.png)4、另外一种方法，想得到flag，要达到下面三个条件：

*   就要让ac的值不为空
*   f的值从文件fn中获取
*   ac的值要恒等于f的值

故构造Payload：

```
?ac=123&fn=php:
[POST]123 
```

如下图所示：

![在这里插入图片描述](img/b561f8f44bb01e9013232c380e9945d9.png)

# No.27 robots.txt信息泄露

1、查看题目链接：

![在这里插入图片描述](img/178290b67575b728497726d1dfaba905.png)![在这里插入图片描述](img/9d22afe90ca5aa24186e742a2dbf4703.png)
2、御剑扫描网站看看，发现 robots.txt 文件：

![在这里插入图片描述](img/8b84ffa9f2c543da1577c5e09a79d8b9.png)

3、访问 robots.txt 文件发现 /resusl.php 路径：

![在这里插入图片描述](img/ef19f82f18dad2ec73cbbd875e0781f9.png)4、访问 /resusl.php 路径：

![在这里插入图片描述](img/b40812867a95206d06660e16d3de28a5.png)
5、根据题目一开始给的提示，想办法变成admin ，故传递参数 x=admin，即可获得 flag：

![在这里插入图片描述](img/59f5749808ed7cd2d13c2597a3ac69ec.png)

# No.28 PHP文件上传绕过

1、查看解题链接：

![在这里插入图片描述](img/6833be9c16aae4b8196e5db6faa0c6de.png)![在这里插入图片描述](img/3656d0d7e53ba8b3bfbaf7af6118ab3a.png)
2、尝试直接上传木马失败：

![在这里插入图片描述](img/d0d5435c12df33b1343e254d0fcc6cfb.png)

3、经测试发现一共三个过滤：

*   请求头部的 Content-Type；
*   文件后缀；
*   请求数据的Content-Type。

这里是黑名单过滤来判断文件后缀，依次尝试 php4，phtml，phtm，phps，php5（包括一些字母改变大小写），最终发现，php5 可以绕过；接下来，请求数据的Content-Type字段改为 image/jpeg；但是一开始没注意到，上面还有一个请求头 Content-Type 字段，大小写绕过： mULtipart/form-data；最终的 Payload 如下：

![在这里插入图片描述](img/960a55a5568df398c52408e712335f72.png)

# No.29 php反序列化审计

1、查看解题链接：

![在这里插入图片描述](img/165db5f0753c3f51fae8f32e5c57ec14.png)![在这里插入图片描述](img/82464f82423a4ad16cbf9f0f838d1e8a.png)
2、根据题目提示访问传递 hint 参数，参数值任意，获取到PHP代码：

![在这里插入图片描述](img/653b576ee027d3bd39be5baf366108a6.png)
代码逻辑是传入的 Cookie 参数的值反序列化后等于 KEY 就输出 Flag，一开始以为KEY的值是最下面的 `ISecer:www.isecer.com`。

结果忙活了半天发现这里其实上面KEY的值还没有被定义，上面代码中 $KEY 的值应该是NULL，而不是下面的值，所以此处我们应该是要使得反序列化的值为NULL。

3、使用PHP在线运行工具，得知空值 KEY (KEY取值应该是`‘’`而非 NULL）的序列化数值`serialize($KEY)`为 `s:0:"";`：

![在这里插入图片描述](img/589277ef94ebbb59b06829fc2417c6c7.png)
4、最后，BP抓包并构造Cookie发送payload即可获得Flag：

![在这里插入图片描述](img/53f8a429bda3cf05ecf23191d87fef31.png)