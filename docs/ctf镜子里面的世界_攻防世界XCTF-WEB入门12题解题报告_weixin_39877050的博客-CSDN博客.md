<!--yml
category: 未分类
date: 2022-04-26 14:40:08
-->

# ctf镜子里面的世界_攻防世界XCTF-WEB入门12题解题报告_weixin_39877050的博客-CSDN博客

> 来源：[https://blog.csdn.net/weixin_39877050/article/details/110158235](https://blog.csdn.net/weixin_39877050/article/details/110158235)

WEB入门题比较适合信息安全专业大一学生，难度低上手快，套路基本都一样

需要掌握：

*   基本的PHP、Python、JS语法
*   基本的代理BurpSuite使用
*   基本的HTTP请求交互过程
*   基本的安全知识（Owasp top10）

先人一步，掌握WEB安全入门诀窍，相信我，你不会后悔滴～

**攻防世界XCFT刷题信息汇总如下：攻防世界XCTF黑客笔记刷题记录**，收藏一下哈～

## 第一题：考察浏览器控制台的查看

解题报告：

右键不管用，那就打开控制台咯～

提交flag：`cyberpeace{2d7647b131421b597501c7e63ddaa879}`

## 第二题：考察网站robots页面的查看

解题报告：

进入robots.txt找到了线索

顺着线索找到了flag：`cyberpeace{3b9d4fcca0aaba7f46f09e6403019a80}`

## 第三题：考察备份文件名的后缀

解题报告：

index.php加个bak不就是备份文件嘛，自动下载了

从文件中找到了flag：`Cyberpeace{855A1C4B3401294CB6604CCC98BDE334}`

## 第四题：考察HTTP请求和应答报文

解题报告：

从控制台可以看到cookie中的值为cookie.php，访问这个页面就是咯

从HTTP的应答头里面找到flag：`cyberpeace{46907c4246480ad4f5b356e1e2f1817a}`

## 第五题：考察在线编辑页面能力

解题报告：

查看源代码，发现有个disable字段，把它改成able不就可以了嘛

浏览器右键选择，检查，就可以在线修改啦

现在按下这个按钮就可以得到flag：`cyberpeace{6ac76b64319ed77f47fe1479f6e25c6f}`

## 第六题：考察弱口令

解题报告：

弱口令我试了2次，第一次试了admin admin错了，第二次试了admin 123456对了，直接就拿到flag：`cyberpeace{286d648347709bbd11ac479ee0a75f2b}`

## 第七题：考察PHP类型比较

解题报告：

*   要求a等于0，并且a是true，那么a可以是字符串："ailx10"
*   要求b不是数字，并且b大于1234，数字后面加空格就可以了

构造一个合理的请求就拿到flag：`Cyberpeace{647E37C7627CC3E4019EC69324F66C7C}`

## 第八题：考察GET请求和POST请求

解题报告：

代理走起来，修改GET为POST，添加一个body，key是b，value是2即可

走你，拿到flag：`cyberpeace{c6d40b542f6325b7330e9cbc9f094dbd}`

## 第九题：考察代理修改请求头的字段

解题报告：

*   添加X-Forwarded-For字段
*   添加Referer字段

拿到flag：`cyberpeace{4079533fd7bdb1611a30d037f70983bc}`

## 第十题：考察一句话木马的利用

解题报告：

在index.php的页面下，通过POST请求一个shell字段，值为`system("ls");`，试图看看当前页面下有哪些文件，找到了flag.txt

访问这个文件，拿到flag：`cyberpeace{cccd7f5bd0cf3b90460309eaf001d1ea}`

## 第十一题：考察命令注入

解题报告：

试了一下几个命令，`127.0.0.1;ls /home` ，在这个里面发现了线索

打印这个文件`127.0.0.1;cat /home/flag.txt` ，拿到flag：`cyberpeace{0b95886087d98c05d0773266fde8b347}`

## 第十二题：考察JS代码审计

解题报告：

这题是真的坑，浪费时间的东西，这也太无聊了。

这个dechiffre()函数不管输入什么结果都是一样的，也就是说这是个干扰项，那密码还能在哪呢？下面一串16进制编码。

```
x35x35x2cx35x36x2cx35x34x2cx37x39x2cx31x31x35x2cx36x39x2cx31x31x34x2cx31x31x36x2cx31x30x37x2cx34x39x2cx35x30
```

第一步：将16进制转为10进制数字

```
b="x35x35x2cx35x36x2cx35x34x2cx37x39x2cx31x31x35x2cx36x39x2cx31x31x34x2cx31x31x36x2cx31x30x37x2cx34x39x2cx35x30"

print(bytes(b).decode('ascii'))

#输出：55,56,54,79,115,69,114,116,107,49,50
```

第二步：ascii码数字转字符串

```
b="55,56,54,79,115,69,114,116,107,49,50"
b=b.split(",")
passwd=""
for bb in b:
  passwd += chr(int(bb))
print(passwd)
#输出：786OsErtk12
```

于是得到flag：`cyberpeace{786OsErtk12}`

至此，CTF-WEB入门题通过了～