<!--yml
category: 未分类
date: 2022-04-26 14:40:52
-->

# 南邮一道ctf题目关于e的解释_软柿子捏捏的博客-CSDN博客

> 来源：[https://blog.csdn.net/anzhuangguai/article/details/70049960](https://blog.csdn.net/anzhuangguai/article/details/70049960)

源题目：

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

#### writeup：

#### md5 collision

这个题目如果知道MD5碰撞的概念，同时知道了在PHP中的MD5中的0e的比较，这道题目就十分的简单。

如果md的值是以0e开头的，那么就与其他的0e开头的Md5值是相等的。例子如下：

```
md5('s878926199a')=0e545993274517709034328855841020
md5('s155964671a')=0e342768416822451524974117254469
//可以看到两者的md5值都是以0e开头的，则
md5('s878926199a')==md5('s155964671a') //就是True
```

详细解释：

php关于==号是这样处理的，如果一边是整型，另一边也需要是整型。

```
0e545993274517709034328855841020
```

这是一个整数，在php里是理解为0*10^4549...20的意思，那么其值是0

同样

```
0e342768416822451524974117254469
```

这是一个整数，在php里是理解为0*10^34..69的意思，那么其值是0

举一个反面的例子

1e1和1e2

1e1 == 1e2 这个结果是对是错？

这里1e1=1*10^1=10

1e2=1*10^2=100

所以1e1 == 1e2这是false，但是

100 == 1e2 这是true，为什么1e2先转为整型，是100

注意，对于e是指幂次。而其他26字符并不具有此能力。

参考：

http://php.net/manual/en/language.operators.comparison.php

`<?php
var_dump(0 == "a"); // 0 == 0 -> true var_dump("1" == "01"); // 1 == 1 -> true var_dump("10" == "1e1"); // 10 == 10 -> true var_dump(100 == "1e2"); // 100 == 100 -> true switch ("a") {
case 0:
    echo "0";
    break;
case "a": // never reached because "a" is already matched with 0
    echo "a";
    break;
} ?>`