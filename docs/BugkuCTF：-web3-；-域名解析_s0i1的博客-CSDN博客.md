<!--yml
category: 未分类
date: 2022-04-26 14:53:10
-->

# BugkuCTF: web3 ； 域名解析_s0i1的博客-CSDN博客

> 来源：[https://blog.csdn.net/changer_WE/article/details/85090023](https://blog.csdn.net/changer_WE/article/details/85090023)

## web3

打开题目查看源码：下面的注释部分给出flag：

![](img/921a4214fcd1f8fb6975a0b217731fbc.png)

```
str = "&#75;&#69;&#89;&#123;&#74;&#50;&#115;&#97;&#52;&#50;&#97;&#104;&#74;&#75;&#45;&#72;&#83;&#49;&#49;&#73;&#73;&#73;&#125"

str2 = ""
dict = []
str3 = ""

str2 += str.replace( '&#', '' )
dict = str2.split(';')
for elem in dict:
    str3 += chr(int(elem))

print(str3) 
```

脚本提取字符串中的数字后转化为char类型得出flag

## 域名解析

域名解析是部署在世界各地的DNS服务器干的事（域名到IP地址的转换）

但是本地机的host文件：

Hosts是一个没有扩展名的系统文件，其作用就是将一些常用的网址域名与其对应的IP地址建立一个关联“数据库”，当用户在浏览器中输入一个需要登录的网址时，系统会首先自动从Hosts文件中寻找对应的IP地址，如果没有找到，系统会再将网址提交DNS服务器进行IP地址的解析。
但是，Hosts文件配置的映射是静态的，如果网络上的计算机更改了IP地址，会造成不能访问的错误。

hosts文件位置：

  Linux下：

```
/etc/hosts        
```

windows下： 

```
C:\Windows\System32\drivers\etc
```

                                   ![](img/a3fa146f77e816b15d3c1566f7bfad89.png)

所以找到hosts文件，最后一行加入：

                                        123.206.87.240 flag.baidu.com

访问        [http://flag.baidu.com/](http://flag.baidu.com/)

得到：    KEY{DSAHDSJ82HDS2211}

也可以修改头信息：用burpsuit抓包修改host头信息为`flag.bugku.com`

### 补充

http协议中Host头的作用：

linux   curl 命令：

```
curl [option] [url]
```

常用选项：

```
-A/--user-agent <string>            设置用户代理发送给服务器
-b/--cookie <name=string/file>      cookie字符串或文件读取位置
-c/--cookie-jar <file>              操作结束后把cookie写入到这个文件中
-C/--continue-at <offset>           断点续转
-d/--data/--data-ascii <data>       指定POST的内容
-D/--dump-header <file>             把header信息写入到该文件中
-e/--referer                        来源网址
-f/--fail                           连接失败时不显示http错误
-o/--output                         把输出写到该文件中
-O/--remote-name                    把输出写到该文件中，保留远程文件的文件名
-r/--range <range>                  检索来自HTTP/1.1或FTP服务器字节范围
-s/--silent                         静音模式。不输出任何东西
-T/--upload-file <file>             上传文件
-u/--user <user[:password]>         设置服务器的用户和密码
-w/--write-out [format]             什么输出完成后
-x/--proxy <host[:port]>            在给定的端口上使用HTTP代理
-#/--progress-bar                   进度条显示当前的传送状态
-v/--verbose                        用于打印更多信息，操作失败时看到警告信息
-H/--header <header>                指定请求头参数
--retry <num>                       指定重试次数
-I/--head                           仅返回头部信息，使用HEAD请求
-e/--referer <URL>                  指定引用地址
```

使用：

```
curl -I "XXX" -H "host: 域名" -v
```

它与不指定host有时访问的并不是同一个ip，同一个url根据不同的Host将同一个请求定向到不同主机（host），从而达到负载均衡的效果。