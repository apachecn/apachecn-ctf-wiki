<!--yml
category: 未分类
date: 2022-04-26 14:44:20
-->

# 结合order by 解CTF某题_aiquan9342的博客-CSDN博客

> 来源：[https://blog.csdn.net/aiquan9342/article/details/102075651](https://blog.csdn.net/aiquan9342/article/details/102075651)

真tmd不容易

```
<?php
error_reporting(0);

if (!isset($_POST['uname']) || !isset($_POST['pwd'])) {
	echo '<form action="" method="post">'."<br/>";
	echo '<input name="uname" type="text"/>'."<br/>";
	echo '<input name="pwd" type="text"/>'."<br/>";
	echo '<input type="submit" />'."<br/>";
	echo '</form>'."<br/>";
	echo '<!--source: source.txt-->'."<br/>";
    die;
}

function AttackFilter($StrKey,$StrValue,$ArrReq){  
    if (is_array($StrValue)){
        $StrValue=implode($StrValue);
    }
    if (preg_match("/".$ArrReq."/is",$StrValue)==1){   
        print "水可载舟，亦可赛艇！";
        exit();
    }
}

$filter = "and|select|from|where|union|join|sleep|benchmark|,|\(|\)";
foreach($_POST as $key=>$value){ 
    AttackFilter($key,$value,$filter);
}

$con = mysql_connect("XXXXXX","XXXXXX","XXXXXX");
if (!$con){
	die('Could not connect: ' . mysql_error());
}
$db="XXXXXX";
mysql_select_db($db, $con);
$sql="SELECT * FROM interest WHERE uname = '{$_POST['uname']}'";
$query = mysql_query($sql); 
if (mysql_num_rows($query) == 1) { 
    $key = mysql_fetch_array($query);
    if($key['pwd'] == $_POST['pwd']) {
        print "CTF{XXXXXX}";
    }else{
        print "亦可赛艇！";
    }
}else{
	print "一颗赛艇！";
}
mysql_close($con);
?> 
```

本来直接跳到最后看print "CTF{xxxx}";哪一行了，看到上面一位是弱类型。结果tmd才发现在16行就已经把数组给处理了，也就是很明白的说这不是弱类型。

后来想了想也只有注入了。可AttackFilter这个函数把该过滤的都过滤了。后来记得有个order by注入：http://www.cnblogs.com/xishaonian/p/7703486.html

等会儿再写 快下班了