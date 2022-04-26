<!--yml
category: 未分类
date: 2022-04-26 14:41:46
-->

# php安全挑战赛,BiliBili1024安全挑战赛题目及解答(关联ctf)_Deep Yao的博客-CSDN博客

> 来源：[https://blog.csdn.net/weixin_31727797/article/details/115735752](https://blog.csdn.net/weixin_31727797/article/details/115735752)

![bilibililogo.jpg](img/381d5def8198e337818887f5824e4a50.png)

哔哩哔哩在 1024 程序员节这天上线了一个安全挑战赛模块，里面的内容类似于 CTF 挑战赛，目前共有 10 道题目供用户参与解答。

题目与解答过程/答案

1.页面的背后是什么

地址： http://45.113.201.36/index.html

直接访问，提示需要使用bilibili Security Browser浏览器访问～。

查看源代码，有两段 js 代码：

$.ajax({

url: "api/admin",

type: "get",

success:function (data) {

//console.log(data);

if (data.code == 200){

// 如果有值：前端跳转

var input = document.getElementById("flag1");

input.value = String(data.data);

} else {

// 如果没值

$('#flag1').html("接口异常，请稍后再试～");

}

}

})

$.ajax({

url: "api/ctf/2",

type: "get",

success:function (data) {

//console.log(data);

if (data.code == 200){

// 如果有值：前端跳转

$('#flag2').html("flag2: " + data.data);

} else {

// 如果没值

$('#flag2').html("需要使用bilibili Security Browser浏览器访问～");

}

}

})

尝试访问 /api/admin，返回 json 中的 data 即为 flag1。

2.真正的秘密只有特殊的设备才能看到

将浏览器 UA 改成 bilibili Security Browser，访问页面即可看到 flag2。

Firefox 可以商店安装 User-Agent Switcher and Manager 扩展。

3.密码是啥

地址： http://45.113.201.36/login.html

猜解密码，最终得：

用户名：admin

密码：bilibili

登录即可见。

4.对不起，权限不足

地址：http://45.113.201.36/superadmin.html

访问后提示：有些秘密只有超级管理员才能看见哦~

查看 Cookies，发现 role 一项的值为 user 的 md5。

经过多方查询，得知 role 应该为 Administrator(windows 平台默认管理员)。

浏览器直接改 Cookies 中的 role 为 7b7bc2512ee1fedcd76bdc68926d4f7b，刷新页面即可。

5.别人的秘密

地址： http://45.113.201.36/user.html

访问提示：这里没有你想要的答案。

根据代码得知一个神奇的 uid 100336889，同时有一个接口api/ctf/5?uid=uid。

从 100336889 往后遍历请求该接口，当返回不是 403 时，即为答案。

6.结束亦是开始

地址： http://45.113.201.36/blog/single.php?id=1

群里大佬说的，先访问 /blog/test.php，得到一串 jsfuck，解密后得两个字符串：

"\u7a0b\u5e8f\u5458\u6700\u591a\u7684\u5730\u65b9" ->程序员最多的地方，即 Github

"bilibili1024havefun"

Github 搜字符串 2 找到这个仓库：

https://github.com/interesting-1024/end

根据该文件内容，拼接请求：

http://45.113.201.36/blog/end.php?id[]=1

访问提示：还差一点点啦

php 文件内还涉及到一个 Get 参数 url，填入 url=”/flag.txt”

http://45.113.201.36/blog/end.php?id[]=1&url=/flag.txt

得到一张图片，该图片以文本方式打开，搜索 flag，得到如下字符串：

flag10:2ebd3b08-47ffc478-b49a5f9d-f6099d65

然而这是第 10 题的答案。

7.需要少年自己去探索啦

8.需要少年自己去探索啦

服务器开放了 Redis 端口，没密码：

服务器开启了6379端口，也就是Redis默认端口，直接上

redis-cli -h 45.113.201.36 -p 6379

查询：

keys *

返回结果：

1) "flag8"

查询：

get flag8

得到 flag8 答案。

另外，我尝试往里面添加一个自定义 key，结果被服务器拉黑了。

9.需要少年自己去探索啦

无资料。

10.需要少年自己去探索啦

在第六题的图片中拿到了 flag10。

其他

现在好多人在直接爆破 679 题。

120.92.151.189 貌似挂了，后面的题目应该是迁移到了 45.113.201.36。