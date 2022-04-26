<!--yml
category: 未分类
date: 2022-04-26 14:41:15
-->

# BUUCTF misc 解题记录 一（超级详细）_Vayn3的博客-CSDN博客_buuctf答案

> 来源：[https://blog.csdn.net/qq_51090016/article/details/114906829](https://blog.csdn.net/qq_51090016/article/details/114906829)

## N种方法解决(运行不了的文件尝试改成txt文件)

附件下载是一个exe文件，我还运行不了。。这咋办

改成txt文件看看

![在这里插入图片描述](img/e19ce8fd9cf44723af2f63315a7b8fd7.png)
好像有什么东西藏在里面 前面有个jpg 还有base，用winhex打开看看
![在这里插入图片描述](img/cdcff749edc8432386962748cd98894c.png)
到这没思路了，看wp

**base64可以表示图片**

这个让我这个菜鸡人傻了

转换网站[点这](https://the-x.cn/base64/)

转换完还能帮你另存为，真不错

![在这里插入图片描述](img/057294557b21aecc57ad95b88f13a3f1.png)
![在这里插入图片描述](img/736e00d7bd19bdc1ba0d5d56ccdfac81.png)
扫一下就OK了
![在这里插入图片描述](img/ebb4f8e251c568de7223f359bdb18e36.png)

## LSB（图片通道上方隐藏信息用save bin）

因为提示lsb，所以先用stegsolve打开看看

这样设置就行了
![在这里插入图片描述](img/fa096a37d11210bd2ce2fb51cce19229.png)
开始我以为这就完事了，傻傻的把{}里面的当成flag了

输了发现不是，然后又更换了几个选项，还是不行，只好看看wp

原来要查看图片通道：![在这里插入图片描述](img/7b19f36e38937dc5cfbdbf4fa8c18591.png)
发现这上面好像隐藏着东西，这样就要用到save bin的操作
![在这里插入图片描述](img/6632d239c23e00c7a98c1078f5f3e22d.png)
保存后用哦010打开看看

![在这里插入图片描述](img/0f2e16c4c923c3945ccd3e2b18e0b831.png)
发现是png文件

![在这里插入图片描述](img/23b8018d6a5665bbb365985bef511e84.png)
这样就有了

## zip伪加密

刚好借着这题把我没搞懂的伪加密弄懂

首先肯定是用010打开
![在这里插入图片描述](img/f77b2705d1a0e053c20578214a0e3e4a.png)
我记得伪加密应该是把什么09改成00吧，既然不记得了，来系统的学习一下，转载大佬的[博客](https://blog.csdn.net/MikeCoke/article/details/105877451)

来了解一下ZIP文件的组成
一个 ZIP 文件由三个部分组成：
　　压缩源文件数据区+压缩源文件目录区+压缩源文件目录结束标志

**a.压缩源文件数据区：**

50 4B 03 04：这是头文件标记（0x04034b50）
14 00：解压文件所需 pkware 版本
00 00：全局方式位标记（有无加密，奇数加密，偶数无加密）
08 00：压缩方式
5A 7E：最后修改文件时间
F7 46：最后修改文件日期
16 B5 80 14：CRC-32校验（1480B516）
19 00 00 00：压缩后尺寸（25）
17 00 00 00：未压缩尺寸（23）
07 00：文件名长度
00 00：扩展记录长度
6B65792E7478740BCECC750E71ABCE48CDC9C95728CECC2DC849AD284DAD0500

**b.压缩源文件目录区:**
1
50 4B 01 02：目录中文件文件头标记(0x02014b50)
3F 00：压缩使用的 pkware 版本
14 00：解压文件所需 pkware 版本
00 00：全局方式位标记（有无加密，奇数加密，偶数无加密）
08 00：压缩方式
5A 7E：最后修改文件时间
F7 46：最后修改文件日期
16 B5 80 14：CRC-32校验（1480B516）
19 00 00 00：压缩后尺寸（25）
17 00 00 00：未压缩尺寸（23）
07 00：文件名长度
24 00：扩展字段长度
00 00：文件注释长度
00 00：磁盘开始号
00 00：内部文件属性
20 00 00 00：外部文件属性
00 00 00 00：局部头部偏移量
6B65792E7478740A00200000000000010018006558F04A1CC5D001BDEBDD3B1CC5D001BDEBDD3B1CC5D001

**c.压缩源文件目录结束标志:**
1
50 4B 05 06：目录结束标记
00 00：当前磁盘编号
00 00：目录区开始磁盘编号
01 00：本磁盘上纪录总数
01 00：目录区中纪录总数
59 00 00 00：目录区尺寸大小
3E 00 00 00：目录区对第一张磁盘的偏移量
00 00 1A：ZIP 文件注释长度
————————————————
版权声明：本文为CSDN博主「宁嘉」的原创文章，遵循CC 4.0 BY-SA版权协议，转载请附上原文出处链接及本声明。

也就是说，我们主要看的就是全局方式位标记，分别在这两个地方：
![在这里插入图片描述](img/4fa26ed363dc1710d4faebd1bf1bbc23.png)
记住它就行了

把这两个都改成00
![在这里插入图片描述](img/65b52540916f48017cbf6be41c04812f.png)

## 另外一个世界（二进制转字符：ASCII码）

图片用010打开看到：
![在这里插入图片描述](img/ed6a5d3a074663b55275f38b7fe1e088.png)
这么一串二进制肯定有问题，我开始的想法是转成16进制再转字符
得到这个![在这里插入图片描述](img/9ac81075ec1568d60104976beb6c0b7b.png)
然后一直不对，看wp，答案是这个
![在这里插入图片描述](img/acaeed0284bf2b0eb7415be2c1bc10e6.png)
wp是将每八位二进制数字分开，用ASCII码得到的

![在这里插入图片描述](img/c8f657e1698c2352ebb7024f763fb202.png)
为什么就差一个字母不一样？不懂，有大佬解释一下嘛

## FLAG（save bin 分析文件类型）

这题是因为写了一半卡住了没思路。。看了wp才知道可惜了

其他方法都试过了，010打开看的时候也没发现里面藏了文件，所以最后用stegsolve打开看看是不是lsb

![在这里插入图片描述](img/59872ef8646bc56d0ca998332ac4eef8.png)
因为前面那题save bin 的先例，所以这题我也试了试 ，用010打开
![在这里插入图片描述](img/ec885608204eba90c79479f886a03f1c.png)
但是我不知道这就是压缩包文件，所以就没做出来，后缀改成zip，解压后用010打开就有了

![在这里插入图片描述](img/c7b5f7ec51a3407d2aa9c372de1a367a.png)

## 假如给我三天光明（盲文）

实在没想到下面这个是盲文（不应该）
![在这里插入图片描述](img/ea4d28c0168b2c3f2a19cc3fa75ce407.png)
搜盲文对照一下

![在这里插入图片描述](img/0bff52e685002dabbb89454714e38ee4.png)
也就是：kmdonowg

这样就能解压出音频文件了

![在这里插入图片描述](img/1d7901b0e34dda426fb3957479948723.png)
应该是摩斯电码：-.-. - …-. .-- .–. . … ----- —… --… …-- …— …–… …— …-- -… --…
按粗细分-和.
这样就得到了：
![在这里插入图片描述](img/ac828d7b27fcce5ed815d4618e04b64e.png)
但是最后提交的时候还要改成小写，这是为什么。。

## 后门查杀（用d盾查杀木马）

差点以为点错地方了，还以为是web题呢

这里需要用到一个工具会比较简单：d盾

直接扫描大文件夹（文件太多了一个个看太麻烦）

![在这里插入图片描述](img/08deae862fa3a5d9ca5785acdd5f4c70.png)
webshell应该就是一句话木马吧，那查看一下下面的这个
![在这里插入图片描述](img/f7a82cb8a4553b2b5f9c63c9dc854021.png)
果然发现了

## 面具下的flag(用7z解压缩vmdk文件)

先试用010打开图片，发现里面藏着文件，然后就改成zip后缀试试

得到了这个
![在这里插入图片描述](img/a92301600704c0b51999b26b81dcdb2b.png)
到这我就没思路了。。看wp做的后面

再kali用7z解压缩这个文件（我之前也不知道7z是什么），反正就用这个命令就能解压缩：

![在这里插入图片描述](img/f82d9e74e476cc21fe17dd12cc6d85cd.png)
然后就得到一堆文件，每个都看看，看到两个关键文件

![在这里插入图片描述](img/c03df8c76f7d47123eab2910c12d0283.png)
第一个长这样
![在这里插入图片描述](img/40ebdb950d677174f42d1491392a1f90.png)
肯定是brainfuck

第二个长这样：
![在这里插入图片描述](img/6a150742360bc430e66ed81472bde4f7.png)
![在这里插入图片描述](img/3c9c65d1f673f3eca59763b417b60cab.png)

## 九连环（steghide）

显示010里面发现有flag。txt文件，然后使用binwalk foremos大法分离出一个压缩包和一个图片
![在这里插入图片描述](img/ffe93ac93f4ac211c7b2a77d359716ba.png)
图片损坏了解压不了，压缩包点开需要密码，先看看是不是伪加密

果然，
![在这里插入图片描述](img/abaa24460355b36480a88da67c034b5d.png)

这样就能正常解压了
![在这里插入图片描述](img/235b0025b4224dd8b0ee6d6359088eb9.png)
但是另一个压缩包也需要密码，这肯定不是伪加密了，关键应该在图片上：这里涉及到steghide的使用

使用`steghide extract -sf good.jpg`，空密码即可

![在这里插入图片描述](img/51e94fe5b6b3d277cfd1a8ff83e5bd02.png)
解压得到的txt文件
![在这里插入图片描述](img/3923a95c703d1edd43d3c076961ce980.png)

## snake（serpent解密）

附件用010打开看看，搜索下flag txt pk等等
![在这里插入图片描述](img/ef390f4b8048eacfbeccbe975c8f268a.png)
里面应该是有压缩文件，改成zip看看

![在这里插入图片描述](img/9046e0a13a865aa0d42500099b5a7b24.png)
果然有，先看key
![在这里插入图片描述](img/235f5b309aaff6bb8606cfb3e87bff9e.png)
base64解密：
![在这里插入图片描述](img/882fbffa4a025b4d5ffcd1624754c4a7.png)
这我哪知道，百度一下是anaconda，然后就是解密了，是什么解密呢？题目是snake，解密应该和这个有关，搜了一下才知道还有serpent解密，网址[贴这](http://serpent.online-domain-tools.com/)

![在这里插入图片描述](img/c0eef16bf982a547829c1b583ca0e829.png)
这样就有了

## 菜刀666（流量分析）

首先是很常规的套路，用winshark打开，搜索flag，发现有个txt文件藏在里面

![在这里插入图片描述](img/838635c273d9dd9680bd7ad027f002e6.png)
后缀改成zip打开看看![在这里插入图片描述](img/ea38fe5457ffe8f3ecb7ce3e08332c66.png)
需要密码，这时候就要想到从本身的文件入手了，搜素一下password。。。没有，那tcp追踪流看看

看到7流的时候发现一串16进制字符![在这里插入图片描述](img/2a8b9f0f743e9164fe3dbc411e6b00af.png)
又看到有个z1 z2, z1我还以为是base，结果不是，先看z2吧 FF D8开头FF D9结尾，判断为jpg图片，将这些十六进制复制出来，粘贴到010里面，注意具体操作如图，要粘贴自16进制文件

![在这里插入图片描述](img/9260e4bca1733699507d423f294629bb.png)
文件类型也是16进制：
![在这里插入图片描述](img/a162fcc214bec091ba456344a59f107d.png)
![在这里插入图片描述](img/5be7979bb30756cdd9ec94d2709b82be.png)
密码有了
![在这里插入图片描述](img/0c64d44922b09ecf155b392723e11a39.png)

## [SWPU2019]神奇的二维码（binwalk -e分离）

正常010打开，发现有rar文件，先试试改成rar文件，解压缩得到
![在这里插入图片描述](img/acb824df21590e65e2b30bb50adaf3cc.png)

解码得到
![在这里插入图片描述](img/6d52694e27d565b04872024546c6eef6.png)

还以为这就是flag。。没思路了，直接看wp

放到虚拟机里binwalk一下 再foremost一下
![在这里插入图片描述](img/1f68e687a93c817e6a25e78af11e3569.png)
用binwalk -e试一下
![在这里插入图片描述](img/e38efc59cd03385f0fb60d5f6f1f8d59.png)

分离出了四个压缩包，发现有一个需要密码。。试了下发现其实asdfghjkl1234567890是看看flag在不在里面<sup>_</sup>.rar的密码
其实这里面什么都没有，就是单纯的图片，什么都没有隐写…浪费我时间…

![在这里插入图片描述](img/bba5168d03b715582633299bd84f02f9.png)

一大串。。应该被编码了很多次，用脚本
![在这里插入图片描述](img/65ba2f873aaaca1c811119b5f51e18b9.png)
解压得到音频文件，使用Audacity打开
![在这里插入图片描述](img/ce9bb8b19ebaabce9ad92b9aca56f02f.png)
摩斯电码
– — .-. … . … … …- . .-. -.-- …- . .-. -.-- . .- … -.–

![在这里插入图片描述](img/451f28fc00e7ae36a14b8078a0661672.png)

## [ACTF新生赛2020]outguess（outguess的用法）

解压后发现一堆东西，重点应该还是在图片上，根据提示猜测是outguess的使用，用法贴[这里](https://ywnz.com/linuxjc/5774.html)

kali下面输入outguess -k ‘abc’ -r mmm.jpg flag.txt

得到flag.txt

![在这里插入图片描述](img/7cf75b373588c485b8fdc35487acb91b.png)

## 谁赢了比赛？

正常流程，丢进kali
![在这里插入图片描述](img/7387b4384b4fa9db82f8ffc5056fd5bc.png)
binwalk -e 分离获得压缩包，爆破得到
![在这里插入图片描述](img/6288f0bc58720c4198ac691b099cd67e.png)
flag.txt假的。。。
分离gif吧：一直看到三百多张发现了点东西
![在这里插入图片描述](img/3b29ec7a6488f188686c9874e38387b7.png)
stegsolve打开。。换通道，终于有了
![在这里插入图片描述](img/8615675659294148bd4dc6625e2cdb4f.png)

## [HBNIS2018]excel破解（修改后缀）

修改zip后缀，然后用010打开。。。就有了？？![在这里插入图片描述](img/cd110fefae5cf034824ece23a913080b.png)

## 喵喵喵（奇怪的lsb NTFS文件流隐写）

这题感觉好难

题目提示有二维码，但是010打开并没有发现隐藏压缩包，应该想到lsb隐写
但是谁能想到要设置成这样才行？？![在这里插入图片描述](img/9f66d66905fb26918869ebfbac7ea5e1.png)

另存为png文件发现打不开。。改一下文件头就好了
![在这里插入图片描述](img/5a622958da82deddaa2db3d2d424f04f.png)
这明显是高的问题了，加个高吧
![在这里插入图片描述](img/578563727283dd3299b7a0430ab88d61.png)
扫了之后是个网盘地址。。下载flag.rar打开
![在这里插入图片描述](img/f820f3c79d09c3d9310cccb4f6ed18a5.png)
？？？看wp
**这里猜测有NTFS文件流隐写，将flag.txt解压到一个新建的文件夹内，利用NtfsStreamsEditor**
![](img/3a807f70a9a7d5816675efaea61651ac.png)
导出就有了

## [HBNIS2018]来题中等的吧（条形码表示摩斯电码）

![在这里插入图片描述](img/11c9a9a1d382457ff95c7ef14a37a77f.png)
开始我以为是条形码，扫了半天没扫出啥。。。看wp，原来是摩斯电码，依次为:

```
.- .-.. .--. .... .- .-.. .- -... 
```

![在这里插入图片描述](img/586829a6a0cdc5cd08ebc62268fff2f5.png)
改成小写字母就行了

## 弱口令（这也能藏莫斯代码？ 没遇到过的lsb隐写）

先是在解压缩的右边有提示，复制粘贴到vs code，会发现隐藏着摩斯电码，翻译完是

```
HELL0FORUM 
```

这样就能解压图片了，然后是奇怪的lsb隐写，需要用到cloacked-pixel。。但是我的脚本一直报错，很烦

## [SWPU2019]你有没有好好看网课?（敲击码）

先是爆破zip3
![在这里插入图片描述](img/635ccb6fdb3e8db3c80b5dbe340a9585.png)
得到：
![在这里插入图片描述](img/facc2fb8f97255d929114a08bf1ee956.png)
提示 520 711

使用Kinovea打开影流之主.mp4

![在这里插入图片描述](img/b2de146d6e7540ada19196439cb34c2e.png)
![在这里插入图片描述](img/677cc3489c8bb8dd49e1d32e41ee8ebb.png)
在第7.36s发现第二段信息：dXBfdXBfdXA=

base解码 `up_up_up`

合起来：`wllmup_up_up`

使用这个密码解压flag2.zip，解压得到Real flag,jpg，使用010 Ediotr打开搜索关键词ctf即可得到flag

## john-in-the-middle(导出http)

wireshark打开文件后，查看数据量并没有线索，然后我就麻了，看wp

需要导出http，具体步骤：文件 →导出对象→http →save all

![在这里插入图片描述](img/2731b19712226edfe8e0682dae8f62bc.png)

得到了好多东西，从6张png下手，一个个在stegsolve中打开，终于在logo.png里面找到了（好狗）

![在这里插入图片描述](img/dd22ce9c368f49a31fd86a643b33157c.png)

## 低个头（键盘加密？？）

![在这里插入图片描述](img/b14812f7e0839a1af53c5b6aaf9e5597.png)

用键盘连线组成字母
![在这里插入图片描述](img/5d873344204fd90030ad7118da621d74.png)

那就是`flag{CTF}`

## zip

参考大佬的wp ： https://blog.csdn.net/destiny1507/article/details/102133268/

不过那个暴破脚本为什么运行的那么慢。。。害的我以为出错了试了好多次

## 我吃三明治(两张图拼接之间隐藏信息)

binwalk的时候发现这张图片是由两张图片拼接起来的：![在这里插入图片描述](img/e54f3c27e6b3ce3e9855f4da3a02a454.png)
找到中间点：搜索jpg文件尾ffd9![在这里插入图片描述](img/b43ce6659f5a83c71e134220ca233cba.png)
看到一串base加密，base64不行，后面又没等号，试试base32：

![在这里插入图片描述](img/3b82679c5d5a5dbeb14b5f7aa627db56.png)

## 间谍启示录（奇怪的题目）

解压后看到一堆文件，只有这个exe有点东西

![在这里插入图片描述](img/9d1946d73bd8adeb57ad6b5ac8c045c1.png)
直接点开就被销毁了
![在这里插入图片描述](img/236565bdb7a44cf91f0cac4dcf12dede.png)
想到解压或者foremost也行

点那个flag.exe就会生成机密文件

![在这里插入图片描述](img/e84149f4b50f2eadb35362c96bdfcad8.png)

## [SUCTF2018]single dog(AAEncode解密)

用010打开发现里面有其他文件，改成zip后缀打开
![在这里插入图片描述](img/6beb6275c72b8bd2a351bd7ba8a3b1f1.png)
里面是一大段表情。。。这就涉及到我的知识盲区了，百度吧

![在这里插入图片描述](img/6f3b09832f172975800908587b64d278.png)

## [安洵杯 2019]吹着贝斯扫二维码

这题需要用到改后缀的脚本，但我运行一直出错啊啊啊啊啊啊。。。

## 从娃娃抓起（中文电码）

两个txt文件，第一个：
![在这里插入图片描述](img/6ceb60214d1e103c3a2adab7f601e60f.png)
中文电码和五笔编码

![在这里插入图片描述](img/5c771116b7e28c58fbfb51e9d78ed260.png)
第二个是提示
![在这里插入图片描述](img/94056eed3625e26c3f3ff2cfd32a7df6.png)
md5加密：

```
3b4b5dccd2c008fe7e2664bd1bc19292 
```

## 小易的U盘（ida的使用）

这个 本来ida一直出错。。百度知道ida安装路径不能有中文，改了之后就好了

## [ACTF新生赛2020]swp（导出http）

这题主要是根据swp的提示想到把那个pcapng文件导出http，获得了一堆文件，里面有一个压缩包，解压后搜索ctf就有了

![在这里插入图片描述](img/12ad6cd67024b4170df9522f3384c27e.png)

## 百里挑一

## [WUSTCTF2020]alison_likes_jojo（outguess隐写）

这一题只做了一半，outguess遇到的太少了，还是太菜了

## [GUET-CTF2019]zips（伪加密 python时间戳？）

zip伪加密还是没搞透，先是第一层的爆破，得到密码723456

然后是第二层的伪加密：把这个位置的09改成00就行了
![在这里插入图片描述](img/cf4275bebd6387f6a28cd50cca3fcdc1.png)
解压得到![在这里插入图片描述](img/d0237e94533f014387c72eb0af9d3902.png)
这是要执行这段代码么
![在这里插入图片描述](img/f52953bdd0f5c58f7dd53b4ab285b754.png)
执行完了。。怎么不是密码,看 WP吧

```
 因为时间戳和出题当时不太一样所以往前推，利用ARCHPR设置掩码15???.?? 掩码符号为**?** 
```

不太懂这个意思

算了，照着wp做

![在这里插入图片描述](img/1fce21f5b98e5de6dfc9f6dfbf6d91fe.png)
得到了这个，解压
![在这里插入图片描述](img/07d458cbab3380b29b343a0082c8bc2c.png)
ok了，主要就是那个时间戳不知道啥意思

## [安洵杯 2019]Attack（mimikatz的使用）

又是一道涉及知识盲区的题

先是用010打开看了看，发现里面藏了txt文件，懒得开虚拟机，改成zip后缀试试
![在这里插入图片描述](img/37f8757c813b9277157a0e4ff100021c.png)
密码肯定要在原文件里搞到了，我先是看tcp流。。真的没发现啥东西，再导出http
![在这里插入图片描述](img/4bb6c3cc4c5e175d7f60fdf60e5d2fd1.png)
这一堆实在让我不知道干啥。。看wp吧

```
lsass.exe是一个系统重要进程，用于微软Windows系统的安全机制。它用于本地安全和登陆策略。 
```

**这里利用mimikatz获取明文密码，保存lsass下来放入mimikatz同目录下（64位）
用x64位右键管理员运行打开
mimikatz（内网渗透工具，可在lsass.exe进程中获取windows的账号明文密码）**

这里的minikatz我在github上下载的没有exe文件。。。可能是被直接删掉了，最后从下面这个大佬的网盘上下的

https://blog.csdn.net/weixin_45663905/article/details/108013149

用管理员权限才能打开：
![在这里插入图片描述](img/adc0289c0840a86b09fe66133c450ed8.png)
具体步骤：

```
//提升权限
privilege::debug
//载入dmp文件
sekurlsa::minidump lsass.dmp
//读取登陆密码
sekurlsa::logonpasswords full 
```

搜索一下password
![在这里插入图片描述](img/0ff904d90616b0994ba3d6106aee8908.png)
有了![在这里插入图片描述](img/2b3305ce0cc549903a75a015c93398b8.png)
输入密码解压就行了

## [SUCTF 2019]Game

先是用vs code打开发现一串base32
![在这里插入图片描述](img/f76c36c52170ae154f5d095bf21f1022.png)
得到 `suctf{hAHaha_Fak3_F1ag}`
假的flag。。。

再看另一张图片，LSB发现一串base64

**这串base64解码后头部是Salted，应该是AES或者3DES**
![在这里插入图片描述](img/d5f18d7295b0237555f3df897e14c25b.png)

## [WUSTCTF2020]girlfriend（DTMF拨号音 手机键盘密码）

先是把音频用audacity打开，结果并不是摩斯电码，麻了，不知道啥东西，看wp

**猜测DTMF拨号音识别，有个程序可以识别一下dtmf2num.exe**
[下载地址](https://lanzous.com/id4e4hc)
放在同一个文件夹下打开cmd
![在这里插入图片描述](img/f2f7831c84c5989c529d3b7d34326ed8.png)

然后就是手机键盘密码
![在这里插入图片描述](img/eacab73b44e1664a3fc2fe4ba6046881.png)

```
999     --->   y
666     --->   o
88      --->   u
2       --->   a
777     --->   r
33      --->   e
6       --->   m
999     --->   y
4       --->   g
4444    --->   i
777     --->   r
555     --->   l
333     --->   f
777     --->   r
444     --->   i
33      --->   e
66      --->   n
3       --->   d
7777    --->   s

youaremygirlfriends 
```

## [MRCTF2020]CyberPunk

又是一道脑洞题

![在这里插入图片描述](img/6ece7d35a364a08ca488ea03272a7a8d.png)
需要我们把电脑时间调到2020年9月17日就有了
![在这里插入图片描述](img/f6ce6990da0a6b8c698ece4ed41b83b3.png)

## USB（USB数据包 维吉尼亚解密）

010打开后发现里面有很多rar，改成rar后缀,解压得到一个pcapng文件

根据wp，pcapng文件里面是USB数据包：
![在这里插入图片描述](img/8f6f1c72379c6932cc93f651a07b9883.png)
直接使用UsbKeyboardDataHacker脚本提取内容

```
https://github.com/WangYihang/UsbKeyboardDataHacker 
```

下载后放到kali里，把pcap文件和脚本放在一个文件夹，用命令行打开，然后输入python UsbKeyboardDataHacker.py key.pcap
![在这里插入图片描述](img/4424063ab46d2243fdfa61017dbf3ee1.png)

```
key{xinan} 
```

再回过头看另一个rar文件，里面的txt文件显然并没有flag
![在这里插入图片描述](img/651e6f1887503da6162c851c493548af.png)
用WinRAR打开显示文件头损坏
![在这里插入图片描述](img/e8518c93a50ba7b5672e0f7ea977a1d6.png)

010打开看看
![在这里插入图片描述](img/664a3ee2879975c166ef03b0f31623dc.png)

crc错误，而且里面有个png图片，修复一下将70A0改为74A0

再用stegsolve打开图片，在一个通道里发现了二维码
![在这里插入图片描述](img/fca9476b72ac8b8f7c79b605f159b8e8.png)

得到`ci{v3erf_0tygidv2_fc0}`

维吉尼亚解密
![在这里插入图片描述](img/1fd8d183fb1ddbafd0ac14a1541c1e63.png)
前面没有flag，但整个字符串里可以拼到，应该是栅栏密码，再进行栅栏解密

![在这里插入图片描述](img/15f20f4a97845e5f7772ebeb3db54d58.png)