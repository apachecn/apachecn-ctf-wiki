<!--yml
category: 未分类
date: 2022-04-26 14:41:22
-->

# CTF|BugkuCTF-WEB解题思路_「已注销」的博客-CSDN博客_bugkuctfweb题解

> 来源：[https://blog.csdn.net/m0_46387542/article/details/106148855](https://blog.csdn.net/m0_46387542/article/details/106148855)

> 截止至练习题【login4】，仅提供解题思路。
> 【本地包含】、【点击一百万次】、【welcome to bugkuctf】、【过狗一句话】、【INSERT INTO注入】、【这是一个神奇的登陆框】由于个人做题时网址已挂，故略过。
> 还没做完，导出整理博客这是唯一一篇草稿，拿来测试发布文章功能用的。

## WEB2

按F12直接可得flag

## 计算器：

直接按F12，将maxlength修改，输入正确结果即可得到flag

## web基础$_GET

URL输入：http://123.206.87.240:8002/get/?what=flag

## web基础$_POST

使用hackbar工具，按F12，打开Post data，传递what=flag。

## 矛盾

这里是使用is_numeric遇到%00截断的漏洞,这里构造
http://120.24.86.145:8002/get/index1.php?num=1%00
得到flag。

## web3

直接查看网页源代码，最后一行采用html解码获取flag。

## 域名解析

使用burpsuite截包修改host。

## 你必须让他停下

js自动刷新页面，某个页面内存在着flag。使用burpsuite截包，右键选择Send to Repeater，跳转到Repeater界面，点击Go，其中一个页面代码存在flag。

## 变量1

构造URL：http://123.206.87.240:8004/index1.php?args=GLOBALS

## web5

将网页源代码中的jsfuck代码在F12的控制台中输入，可得到flag，将flag字母大写即为答案。

## 头等舱

方法同【你必须让他停下】

## 网站被黑

使用网站扫描工具（如：dirbuster）扫描网站网页，获得http://123.206.87.240:8002/webshell/shell.php网址后利用burpsuite的intruder功能字典破解密码。

## 管理员系统

用户名为admin，右键查看网页源代码，其中存在base64加密的密码，解密后得到密码。
使用burpsuite截包，右键选择Send to Repeater，跳转到Repeater界面，添加键值X-Forwarded-For 127.0.0.1，伪装成本地连接，点击Go，获取flag。

## web4

查看网页源代码，进行url解码即可。

## flag在index里

访问http://123.206.87.240:8005/post/index.php?file=php://filter/read=convert.base64-encode/resource=./index.php，而后base64解密获得flag。

## 输入密码查看flag

使用burpsuite进行五位数密码穷举破解获得密码13579。

## 备份是个好习惯

利用御剑扫描网站发现http://123.206.87.240:8002/web16/index.php.bak网站，查看源码发现其主要实现两个功能，一个是将输入的key参数用’'替代，一个是要求输入的key1和key2不相等，但md5值相等，可以用以下两种方法绕过。
1.md5()函数无法处理数组，如果传入的为数组，会返回NULL，所以两个数组经过加密后得到的都是NULL,也就是相等的。
即http://123.206.87.240:8002/web16/index.php?kekeyy1[]=1&kkeyey2[]=2
2.利用==比较漏洞
如果两个字符经MD5加密后的值为 0exxxxx形式，就会被认为是科学计数法，且表示的是0*10的xxxx次方，还是零，都是相等的。
下列的字符串的MD5值都是0e开头的：
QNKCDZO
240610708
s878926199a
s155964671a
s214587387a
s214587387a
即http://123.206.87.240:8002/web16/index.php?kekeyy1[]=QNKCDZO&kkeyey2[]=s878926199a

## 成绩单

SQL手注

## 秋名山老司机

多次刷新后发现其要求post计算数据且其存在时间限制，因此采用代码。

```
import requests
import re
s = requests.Session()
r = s.get("http://123.206.87.240:8002/qiumingshan/")
searchObj = re.search(r'(\d+[+\-*])+(\d+)', r.text)
d = {
    "value": eval(searchObj.group(0))
    }
r = s.post("http://123.206.87.240:8002/qiumingshan/", data=d)
print(r.text) 
```

## 速度要快

抓包发现其头部存在flag，其采用base64编码，但解码后发现其无效，且时刻在变，因此通过post当前的flag值获取真正的flag。

```
import requests
import base64
url = 'http://123.206.87.240:8002/web6/'
s = requests.session()
flag = s.get(url).headers['flag']
flag = base64.b64decode(flag)
flag = base64.b64decode(flag.decode().split(": ")[1])
print(flag)
payload = {'margin': flag}
print(s.post(url, data=payload).text) 
```

## cookies欺骗

观察URL发现base64编码，解码后发现keys.txt。修改参数line发现不同的代码行数，利用代码获取源代码。

```
import requests
s = requests.session()
for line in range(1, 19):
    url = 'http://123.206.87.240:8002/web11/index.php?line={}&filename=aW5kZXgucGhw'.format(line)
    print(s.get(url).text) 
```

将filename修改为keys.php的base64编码，cookie修改为margin=margin。

## never give up

查看源代码发现1p.html，构造URL访问后跳转到Bugku论坛，利用view-source:http://120.24.86.145:8006/test/1p.html方式查看源码。依次进行url解码-base64解码-url解码，查看源代码，发现f4l2a3g.txt。访问获得flag。

## 字符？正则？

正则匹配

## 前女友(SKCTF)

查看源代码发现code.txt，访问查看源码发现其要求与【备份是个好习惯】相似，构造http://123.206.31.85:49162/?v1=QNKCDZO&v2=s878926199a&v3[]=1访问，其中strcmp函数如果出错，其返回也是0。

## login1(SKCTF)

在处理SQL中的字符串时，字符串末尾的空格字符都会被删除。换句话说，“vampire”与“vampire ”几乎是等效的，这在大多数情况下是正确的，例如WHERE子句中的字符串或INSERT语句中的字符串。例如，以下语句的查询结果，与使用用户名“vampire”进行查询时的结果是一样的。

```
SELECT * FROM users WHERE username='vampire ';
SELECT * FROM users WHERE username='vampire'; 
```

究其原因是因为在字符串比较的过程中，内部在比较之前先进行了补充，使其长度一致再进行比较。
SQL注入攻击约束条件：
1.mysql处于ANSI模式。如果是TRADITIONAL模式或者STRICT_TRANS_TABLES模式会报错data too long for column。
2.服务端没有对用户名长度进行限制。如果服务端限制了用户名长度就自然就不客能导致数据库截断，也就没有利用条件。
3.登陆验证的SQL语句必须是用户名和密码一起验证。如果是验证流程是先根据用户名查找出对应的密码然后再比对密码，当使用vampire为用户名来查询密码的话，数据库此时就会返回两条记录，而一般取第一条即目标用户的记录，那么传输的密码肯定和目标用户密码匹配不上的。
4.验证成功后返回的必须是用户传递进来的用户名，而不是从数据库取出的用户名。因为当我们以用户vampire和密码random_pass登陆时，其实数据库返回的是我们自己的用户信息，而我们的用户名其实是vampire+若干个空格，如果此后的业务逻辑以该用户名为准，那么就不能达到越权的目的了。
注册账号admin (带空格),输入符合的密码，之后再用该账号密码进行登录即可。

## 你从哪里来

修改http referer头即可，简而言之，HTTP Referer是header的一部分，当浏览器向web服务器发送请求的时候，
一般会带上Referer，告诉服务器我是从哪个页面链接过来的，服务器藉此可以获得一些信息用于处理。
比如从我主页上链接到一个朋友那里，他的服务器就能够从HTTP Referer中统计出每天有多少用户点击我主页上的链接访问他的网站。
Referer的正确英语拼法是referrer。
由于早期HTTP规范的拼写错误，为了保持向后兼容就将错就错了。
其它网络技术的规范企图修正此问题，使用正确拼法，所以目前拼法不统一。

## md5 collision(NUPT_CTF)

注意0 == 字符串成立，找一个md5是0e开头的值，因为 php 在处理 == 的时候当碰到的字符串有一边为 0e 开头的就把这串字符串认为是科学计数法, 所以就是 0，类似【备份是个好习惯】

## 程序员本地网站

修改X-Forwarded-For:127.0.0.1，类似【管理员系统】
X-Forwarded-For: 简称XFF头，它代表客户端，也就是HTTP的请求端真实的IP，只有在通过了HTTP 代理或者负载均衡服务器时才会添加该项。

## 各种绕过

老套路，构造URL：123.206.87.240:8002/web7/?uname[]=1&id=margin，post数据passwd[]=2

## web8

根据给的txt提示先访问http://123.206.87.240:8002/web8/flag.txt，发现其内容为flags，根据源代码访问123.206.87.240:8002/web8/?ac=flags&fn=flag.txt

## 细心

利用御剑扫描到robots.txt协议，访问发现resusl.php文件。根据代码和题目admin的提示构造URL：http://123.206.87.240:8002/web13/resusl.php?x=admin

## 求getshell

文件上传漏洞，根据提示只能上传image而不能上传php文件。上传一句话木马php文件，Burpsuite代理，根据头部，修改三个地方：
1、扩展名filename，试验过php2, php3, php4, php5，phps, pht, phtm, phtml之后，发现只有php5可以绕过；
2、filename下面一行的Content-Type:image/jpeg；
3、请求头里的Content-Type字段，进行大小写绕过，也就是把multipart/form-data中任意一个字母改成大写即可。

## 多次

两次SQL注入攻击，涉及异或注入、双写注入等

## PHP_encrypt_1(ISCCCTF)

根据提供的密文和加密方式写解密，得到flag。

## 文件包含2

查看源码发现upload.php