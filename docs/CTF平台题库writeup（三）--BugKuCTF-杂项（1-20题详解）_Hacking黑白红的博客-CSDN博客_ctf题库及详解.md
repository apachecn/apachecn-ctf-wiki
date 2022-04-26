<!--yml
category: 未分类
date: 2022-04-26 14:18:48
-->

# CTF平台题库writeup（三）--BugKuCTF-杂项（1-20题详解）_Hacking黑白红的博客-CSDN博客_ctf题库及详解

> 来源：[https://blog.csdn.net/zsw15841822890/article/details/107013549](https://blog.csdn.net/zsw15841822890/article/details/107013549)

## 一、BugKuCTF-杂项(1-20)

### ![](img/c9932af5f404e3f7fda86816e5e82fff.png)

### **1****、签到题**

扫描二维码直接获取flag

### 2、这是一张单纯的图片

<u>[http://120.24.86.145:8002/misc/1.jpg](http://120.24.86.145:8002/misc/1.jpg)</u>

![](img/f1612a1459c910e463296ef5e6ca6ef9.png)

（1）.启蒙级隐写术

         linux下保存到本地当作文本文件cat一下（或记事本、winhex等打开）

　　cat 1.jpg

（2）.在末尾发现&#107;&#101;&#121;&#123;&#121;&#111;&#117;&#32;&#97;&#114;&#101;&#32;&#114;&#105;&#103;&#104;&#116;&#125;

　unicode解码得到flag

利用网页上搜索的unicode转ascii进行转换即得FLAG！（工具可以用小葵花）

key{you are right}

![](img/c0dea3afc08810e641ddf0caad724f8e.png)

### 3、隐写(文件头)

Linux下提示

![](img/6e36611dd5348f4d31dbde5c980ba31b.png)

下载2.rar，解压得到一张图片，首先放在winhex里看看

![](img/0af49380fd9201945de393bf02dfb37f.png)

89 50 4E 47PE头是png照片的，就是说没有可能照片中嵌入了Exif信息

在查看PNG文件格式时，IHDR后面的八个字节就是宽高的值

![](img/59dfcd6cc92a8e1c7521b3a060b59e23.png)

将图片放在Linux下，发现是打不开的，说明图片被截了

![](img/06bb9732738572b448b469eaf4bc5a5f.png)

将图片的高改成和宽一样,即将A4改成F4，然后另存为

![](img/4da2042249fd49881dd72df4df9d8725.png)

打开刚存的图片就可以得到FLAG了

![](img/3de969da9b2c5034fb86b5896ec04b72.png)

BUGKU{a1e5aSA}

### 4.telnet

将1.zip下载解压得到一个流量包文件，放到Wireshark中走一遍，因为提示的是telnet,所以使用规则显示telnet的包，然后追踪tcp流

![https://img-blog.csdn.net/20180607163954168?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM5NjI5MzQz/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70](img/5263380e25dac55d4de606cdcb664434.png)

在tcp流中就能直接看到flag,在telnet协议下任意数据流都能看到flag

![](img/77d0e274463a0c709efbb3fb99d79f7b.png)

### 5、眼见非实(ISCCCTF)

下载下来是一个文件的格式，放到winhex中，发现有`50 4B 03 04`这是压缩文件的头，还有`.docx`格式文件，应该压缩包里有一个文档，改文件后缀为`.zip`,解压得到文档

![](img/f3b2844354901f2ca52b5c2d54e31d3e.png)

得到`眼见非实``.docx`是打不开的，放到winhex中发现还是zip格式的文件

![](img/23ee0d4972144cee0269690a4261e00e.png)

继续改后缀为.zip，然后解压得到一个文件夹

![](img/d0c0f5a20f3328c8dd940dad488edab5.png)

检索flag, 然后在`word->document.xml`中找到了flag

![](img/73e59dcc181a752c865cf7013220db73.png)

![](img/30d6680b4a3c03764a2127029a6f3fe0.png)

【注】：

方法二

Kaili 下执行binwalk, 和foremost命令 可以不用修改文件扩展名

![](img/dc1cdcd832f41580005b61a55b923566.png)

![](img/271ca47d6814d556dc11087123e9923a.png)

### 6、**啊哒**

![](img/7713bf42cac22844f1fbc1174d3faa44.png)

拿到压缩包，解压后是一个图片，放到winhex中寻找flag，发现有flag.txt字样，但是不知道怎么处理。

![](img/ee9b51575edf76a568c275ff94de337a.png)

linux下执行：   binwalk ada.jpg

![](img/38347a18e61f78b754cfae48b846c5ab.png)

内含一个zip压缩包，分离文件：

![](img/f78bbe653690034af32a6efe26bb8ef8.png)

压缩包需要密码：

![](img/52d78b86d95b3e1ddeaca205afbdc9d6.png)

回去看看源图片文件ada.jpg的详细信息

![https://img-blog.csdnimg.cn/20181220142203630.png](img/fa864c360167eb2669fd132d54faee82.png)

16进制，转化成字符串（Notepad++中的插件，直接16进制转ASCII）：

73646E6973635F32303138

![](img/41fb26f848429bca9408a99023649cb3.png)

有疑问，为什么在小葵花里解密密码是 dnisc_2018，网上wp上是sdnisc_2018

得到解压密码：    sdnisc_2018

![](img/5bcd9a15e47bd98a82280ef4bb1a5239.png)

分离文件

（1）使用dd命令分离(linux/unix下)

我们可以使用dd命令分离出隐藏文件：

# dd if=carter.jpg of=carter-1.jpg skip=140147 bs=1

if是指定输入文件，of是指定输出文件，skip是指定从输入文件开头跳过140147个块后再开始复制，bs设置每次读写块的大小为1字节 。

dd命令：http://www.cnblogs.com/qq78292959/archive/2012/02/23/2364760.html

（2）使用foremost工具分离

foremost是一个基于文件文件头和尾部信息以及文件的内建数据结构恢复文件的命令行工具，直接将文件拆解

### 7\. 又一张图片，还单纯吗

![](img/cf026ccffdfa2026556186f3c64ffecd.png)

图片隐写，套路化，notepad++、winhex、右键属性、binwalk、foremost打一遍

Binwalk和foremost问题解决

![](img/bee7a184e845900a637a2e6b606c926a.png)

Foremost后out文件夹下看到

![](img/75b1366192205f093506d1180dcf859b.png)

falg{NSCTF_e6532a34928a3d1dadd0b049d5a3cc57}

### 8、**猜**

flag格式key{某人名字全拼}

![](img/31281685b6d7b1c90cbe0e36fbcc401b.png)

百度识图：

![](img/777b7788448c990419a992a6ee5904c5.png)

Key{liuyifei}

### 9、宽带信息泄露

flag格式：flag{宽带用户名}

使用RouterPassView工具查看,

大多数现代路由器都可以让您备份一个文件路由器的配置文件，然后在需要的时候从文件中恢复配置。路由器的备份文件通常包含了像您的ISP的用户名重要数据/密码，路由器的登录密码，无线网络的KEY。

如果你忘记了这些密码，但你仍然有你的路由器配置的备份文件，那么RouterPassView可以帮助你从路由器配置文件中恢复您的密码。该软件可以读取这个路由配置文件。

运行软件，打开"文件"菜单->"打开路由器配置文件"，打开保存的路由器配置文件。
或者Grab Password From IE Window。

![](img/df392702f27b51ed7acd72a1d127e132.png)

Flag{ 053700357621}

注：秘钥破解工具

Aircrack-ng  kaili下自带

命令：Aircrack-ng miyao.ivs （2017年9月部科信局在线测试题）

### 10、**隐写****2**

Welcome_.jpg

![https://ctf.bugku.com/files/af49803469dfdabb80acf562f9381335/Welcome_.jpg](img/477b98b043b2982bd6f998870bfdcc39.png)

图片隐写，套路化，notepad++、winhex、右键属性、binwalk、foremost打一遍

Notepad++

搜索flag发现文件 ![](img/c79ce4bd0dab886ad66136f778f80431.png)  ![](img/242754c85c74c4c4d91a27eb426e264e.png)

Winhex打开

![](img/d61522a16b3b7fbd1e935b4ff02f0eab.png)

可以看到图片嵌入了Exif信息，但是看属性没看到什么有用的提示，老方法放到kali里找

使用binwalk提取

![](img/87e874f953c5f365ced5cd69b8d34220.png)

foremost后output文件夹

![](img/dd7b16ea163427987bd26e4b8cfdd622.png)

提示.jpg  (提示密码为3个数字)

![](img/89305fa5f83e7fd20a9ba37fc0237dd3.png)

爆破flag.zip,工具 Advanced Archive Password Recovery

![](img/ecbce5d2ae5c25019e6b2727924fa132.png)

压缩包密码：871

解压后图片

![](img/31d701242711f93a78d02002de6a030a.png)

Winhex打开

![](img/3cf6fcbe5550acc146c810c6f0c7d5d0.png)

密文eTB1IEFyZSBhIGhAY2tlciE=

带“=”

base64解密，工具小葵花

![](img/e8948b1ca99dfad32fa2902d70f87c69.png)

fl@g{ y0u Are a h@cker!}

### 11、多种方法解决

提示：在做题过程中你会得到一个二维码图片

使用winhex打开，发现是一个base64转图片，所以先将后缀改为.txt,然后[将base64编码为图片](http://imgbase64.duoshitong.com/)（base64转换为图片工具）

![https://img-blog.csdn.net/20180607164433654?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM5NjI5MzQz/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70](img/95fc04fa0d16e7b4c4badd53912bf245.png)

改扩展名为txt

![](img/1c949230fae4b98b0765ddd71677c8e0.png)

![](img/79e13ed9bf3138546d0ed9c1208f0794.png)

KEY{dca57f966e4e4e31fd5b15417da63269}

### 12、闪的好快

这是二维码吗？嗯。。。是二维码了，我靠，闪的好快。。。

题目来源：第七季极客大挑战

![](img/7de24a75b6783535450fc942481390c7.png)

动态的gif图片

1.  发现下载下来是一个gif图片，并且是会动的二维码，我们可以猜测flag很有可能是藏在这些动的二维码里面，这个时候我们可以使用工具尝试分解gif里面的数据

![](img/042a71f8680eb67eb83c00467b453d25.png)

1.  分离出来发现是18张不完整的gif图片，如果尝试每张去修复，工作量太大了，这个时候我们尝试去使用

![](img/28928ad25e01cf85f13fc373ae25e1e9.png)

1.  保存图片祭出神器StegSolve。
    然后Analysis->Frame Browser。这里发现是18张图。也就是18张图片。

![](img/bf22b92a5e5278cd0a6e340e48a2201a.png)

我拿手机一个挨着一个扫的。
扫出来的结果是SYC{F1aSh-so-f4sT}
但是提交不正确。
最后更改为SYC{F1aSh_so_f4sT}

### 13、come_game

听说游戏通关就有flag
题目来源：第七季极客大挑战

下载好题目，解压打开文件，打开可执行文件

![](img/77d37409847354029ad5717e914fe51a.png)

![](img/9b82a50c50253744b5380a345fc5921d.png)

进行通关玩耍，会发现生成一个文件，用记事本打开，你会发现发现里面记录了通关数，考虑更为为最后一关

![](img/c5b036e58e4f29281bc61488d64bd950.png)

![](img/b3aefeee1870ce159915fdbc7ee59e99.png)

顺着玩是保存通关记录，而逆着玩，则是在读取通关记录，则会发现flag。

![](img/0f715e0587ce1b3f267e0a3fccd9191c.png)

然后提交flag即可。
但是这里有一个坑需要注意就是这里并不是flag的格式而是SYC{6E23F259D98DF153}这种格式。

### 14、白哥的鸽子

图片隐写，套路化，notepad++、winhex、010edit、右键属性、binwalk、foremost打一遍

拿到文件binwalk,一切正常

![](img/ebee1892426247ada8c953cd05167651.png)

丢进010editor和winhex（后者不清晰）

![](img/92bc051806c0c8ba85a25bed86749ca7.png)  ![](img/730699f56afa939bb66b42add20a8235.png)

发现文件末位有fg2ivyo}l{2s3_o@aw__rcl@字符串（含flag字样，推断出是栅栏密码，栅栏密码：就是把要加密的明文分成N个一组，形成一段无规律的话）

![](img/92bc051806c0c8ba85a25bed86749ca7.png)

尝试栅栏密码得出flag{w22_is_v3ry_cool}@@，栏数为3

flag{w22_is_v3ry_cool}

### 15、linux

提示：linux基础问题

放在linux下解压（或者Windows下解压），然后得到一个flag二进制文件，使用linux命令查找关键字grep ‘key’ –a flag

![](img/70adff42a7edbcb74337b9da6487406a.png)

key{feb81d3834e2423c9903f4755464060b}

### 16、**隐写3**

### ![](img/85855a2ebf2a9748ebf3ea156bf81286.png)

Windows下打开正常（感觉高度少了） ，kaili下打开报错，IHDR:CRC error

![](img/a02044fa033d51c75fd184cf6fe878ef.png)

IHDR后的4字节是宽，再后4字节是高,00改成FF

![](img/f52a96aaf66490a9e189d3ec3241c2f3.png)

![](img/3646bd32e5734cbf7519eef3310fc2f6.png)

![](img/3c3b996ccd43767f7c6598ca8ddf180f.png)

flag{He1l0_d4_ba1}

### 17、做个游戏(08067CTF)

坚持60秒，Java程序heiheihei.jar

![](img/fd429e297f8676d3fc51f5547713d364.png)

拿到题目，下载jar。直接解压，然后Java反编译.class文件

![](img/e54435fbce3812a8ca192d2330ef77f6.png)

![](img/3753da87946477f1c0969571475c7b2c.png)

![](img/62bf26d2bdd74329e0b89cd9be28a34e.png)

flag{RGFqaURhbGlfSmlud2FuQ2hpamk=} base64解密

![](img/9b1b4842ffb199cfd7f54c42ec6d313d.png)

### 18、想蹭网先解开密码

提示：flag格式：flag{你破解的WiFi密码}

tips：密码为手机号，为了不为难你，大佬特地让我悄悄地把前七位告诉你
1391040****
Goodluck!!

下载cap包，WIFI连接认证的重点在WPA的四次握手包，也就是eapol协议的包，过滤一下

![](img/292d0363e73555996bb9199312718e6f.png)

使用crunch生成密码字典

crunch 11 11 -t 1391040%%%% >>wifipass.txt

或者：crunch 11 11 -t 1391040%%%% -o password.txt

![](img/09aa5b7c615daadb9e8bacc7b9b66cc8.png)

利用aircrack 进行爆破

aircrack-ng w wifipass.txt wifi.cap

或者aircrack-ng -a2 wifi.cap -w wifipass.txt

![](img/679faf29d98bc07e02ba09795329b6a9.png)

第三个存在握手包，就是他。

秒出答案

![](img/afc5f3c37b7fc8b1b209e20806c47740.png)

flag{13910407686}

### 19、Linux2

给你点提示吧：key的格式是KEY{}

压缩包brave.zip 解压，产生brave文件，固定的binwalk 和foremost执行

![](img/aa2de2fd16f5e8000be567263d955a5b.png)

产生图片文件

![](img/5e5b2339a48c88ccdb09514b68611cc3.png)

flag{f0rens!cs!sC00L}

???出错???

flag格式是“KEY{}”

尝试直接搜索brave文件！

grep ‘KEY’ –a brave

![](img/10ee9e65e103d54fda1c727c669289a7.png)

KEY{24f3627a86fc740a7f36ee2c7a1c124a}

### 20、BUGku 账号被盗了

http://123.206.87.240:9001/cookieflag.php

![](img/6cf217ae80ffd447a5211df2effc20b5.png)

文字说我们不是管理员

没什么提示，抓个包看看；

![](img/87b0b010bdc0dbed6c31eb7e63cb587e.png)

显示false，改成true ，然后repeater下试试看

![](img/ad228dbf4d7bca9165298e6c7c664796.png)

发现出来个网页，打开后自动下载一个叫123.exe的文件![](img/4901ed8c585a831be90c83f06660f7bb.png)

跟游戏有关的，利用wireshark分析一下；

找到User和pass

![](img/c3c1930d35ebbb71342e40b6feeda02a.png)

选一个右键点击追踪流就行了，这两行用base64解密就是163邮箱的账号和密码。登陆之后找flag。

![](img/6c2a6dc4221f16d1a97cbc3aeae8acbc.png)  ![](img/a3d5023a5a4ecee2134fb660293b6401.png)

里面有人把flag修改了，不过我发了一份真flag上去了，去收件箱找就行了！![](img/077ad4fa1b9c2823ade95006416c07da.png)

flag{182100518+725593795416}

【注】未完待续