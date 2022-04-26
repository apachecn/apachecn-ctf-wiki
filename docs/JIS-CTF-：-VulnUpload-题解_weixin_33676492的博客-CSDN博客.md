<!--yml
category: 未分类
date: 2022-04-26 14:36:29
-->

# JIS-CTF : VulnUpload 题解_weixin_33676492的博客-CSDN博客

> 来源：[https://blog.csdn.net/weixin_33676492/article/details/91709905](https://blog.csdn.net/weixin_33676492/article/details/91709905)

### 思路

```
1\. nmap扫端口
2\. WEB先查 robots.txt ,然后目录爆破
3\. 留意文件中隐藏的内容
4\. 查看/etc/中程序中的配置，找登陆凭证
```

### Flag 1

爆破目录与文件，发现/flag/目录，访问获得第一个flag

```
The 1st flag is : {8734509128730458630012095}
```

### Flag 2

访问爆破的目录/admin_area/，发现第二个flag和一对用户名密码

```
username : admin
password : 3v1l_H@ck3r
The 2nd flag is : {7412574125871236547895214}
```

### Flag 3

1.  分别使用上面的登陆凭证登陆WEB应用与ssh，最终登陆WEB并发现上传功能
2.  上传一句话***，上菜刀，得到第三个flag以及下一步的提示

    ```
    The 3rd flag is : {7645110034526579012345670}
    try to find user technawi password to read the flag.txt file, you can find it in 
    a hidden file ;)
    ```

### Flag 4

找隐藏文件这块用来很长的时间，最后通过暴力搜索的方式找到：`grep -ri technawi /etc/`

```
The 4th flag is : {7845658974123568974185412}
username : technawi
password : 3vilH@ksor
```

### Flag 5

用technawi登陆ssh，打开/var/www/html/flag.txt

```
The 5th flag is : {5473215946785213456975249}
```

[传送门](https://www.vulnhub.com/entry/jis-ctf-vulnupload,228/)

转载于:https://blog.51cto.com/executer/2091159