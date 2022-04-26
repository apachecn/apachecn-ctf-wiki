<!--yml
category: 未分类
date: 2022-04-26 14:21:20
-->

# BUUCTF__[ACTF2020 新生赛]BackupFile_题解_风过江南乱的博客-CSDN博客

> 来源：[https://blog.csdn.net/TM_1024/article/details/107160915](https://blog.csdn.net/TM_1024/article/details/107160915)

## 前言

## 读题

*   打开直接提示你找源码，不在f12。
*   尝试了robots.txt。没有
*   猜测www.zip、.tar.gz、rar，也没有
*   最后说是index.php.bak。。。。可能用字典可以扫出来。
*   可以看看[常见源码泄露](https://blog.csdn.net/wy_97/article/details/78165051)的地方。
*   得到源码

```
<?php
include_once "flag.php";

if(isset($_GET['key'])) {
    $key = $_GET['key'];
    if(!is_numeric($key)) {
        exit("Just num!");
    }
    $key = intval($key);
    $str = "123ffwsfwefwf24r2f32ir23jrw923rskfjwtsw54w3";
    if($key == $str) {
        echo $flag;
    }
}
else {
    echo "Try to find out source file!";
} 
```

*   就很简单了，get传入key，与一串开头为123的字符串比较。`==` 为弱比较，直接令`key=123`就可以。直接出flag。

## 最后

*   还是PHP的弱类型。`==`和`===` 的差别。
*   附上[题目链接](https://buuoj.cn/challenges#%5BACTF2020%20%E6%96%B0%E7%94%9F%E8%B5%9B%5DBackupFile)
*   持续更新BUUCTF题解，写的不是很好，欢迎指正。
*   最后欢迎来踩[个人博客](http://ctf-web.zm996.cloud/)