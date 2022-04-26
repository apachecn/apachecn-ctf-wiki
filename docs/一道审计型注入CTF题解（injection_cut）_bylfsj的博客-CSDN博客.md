<!--yml
category: 未分类
date: 2022-04-26 14:33:12
-->

# 一道审计型注入CTF题解（injection_cut）_bylfsj的博客-CSDN博客

> 来源：[https://blog.csdn.net/bylfsj/article/details/102816762](https://blog.csdn.net/bylfsj/article/details/102816762)

0ops给的一道基础题，搞了半天没搞出来，后来想起了雨牛几年前审计的一则案例：就是利用数据库的字段长度来截取得要的反斜杠
进而吃单引号 结合二次注入再在第二个点进行注入

首先index.php中进行了全局GPC

```
if (!get_magic_quotes_gpc()){
foreach($_POST as $key => $value){
    $_POST[$key] = addslashes($value);
}
foreach($_GET as $key => $value){
    $_GET[$key] = addslashes($value);
}
} 
```

本来到这里，秒想到能不能二次注入，单引号被转义通过insert语句插入数据库，然后出库的时候被触发
但是它在每段执行SQL语句前都mysql_real_escape_string了，例如下面这段

```
if($_POST['username'] && $_POST['password']){
    $username = mysql_real_escape_string($_POST['username']);
    $password = md5($_POST['password']);
    $sql = "SELECT * FROM users WHERE username = '$username' and password = '$password'"; 
```

导致正常的二次注入无法利用，例如输入单引号’，会首先被GPC转义为’,然后经过mysql_real_escape_string变成了\’，然后插到数据库变成了’,还是单引号字符，无法闭合语句，无法二次注入。输入反斜杠,会首先被转义为\，经过mysql_real_escape_string变成\\,数据库里面还是\，还是反斜杠字符，没办法吃掉单引号。

后来想到了雨牛的一则截断案例，如果username的数据库字段长度是64位，那么我们输入63位数据再加一个反斜杠,这时经过转义变成了63位数据加两道反斜杠，经过mysql_real_escape_string变成了63位数据加四道反斜杠，但是字段长度只是64位，所以插到数据库里的数据被截断了，是63位数据加一个反斜杠,这个反斜杠不是字符，是反斜杠，是可以吃掉单引号的

然后寻找二次注入的触发点，需要有两个可控点，第一个可控点负责闭合单引号，第二个可控点才进行注入。二次注入的触发点在

```
if($_GET['search']){
$username = $_SESSION['username'];
$title = mysql_real_escape_string($_GET['search']);
$sql = "select * from posts where username='$username' and title like '$title'";
$result = mysql_query($sql);
$sql = "select * from posts where username='$username' and title like '$title'"; 
```

在 u s e r n a m e 处 可 以 引 入 反 斜 杠 吃 掉 单 引 号 ， 然 后 在 username处可以引入反斜杠吃掉单引号，然后在 username处可以引入反斜杠吃掉单引号，然后在title处进行注入，进行时间盲注即可