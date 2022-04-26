<!--yml
category: 未分类
date: 2022-04-26 14:34:16
-->

# CTF杂项-BUUCTF竞赛真题WriteUp(2）_Tr0e的博客-CSDN博客_ctf竞赛试题及答案

> 来源：[https://blog.csdn.net/weixin_39190897/article/details/116430110](https://blog.csdn.net/weixin_39190897/article/details/116430110)

# 前言

为了划水过几天的“ 红帽杯 ” 网络安全大赛，学习并记录下 BUUCTF 平台的杂项部分题目，因为去年参加“ 强网杯 ”网络安全大赛发现杂项类型的题目还是可以争取得分的，也希望过几天运气好点吧…

# No.1 Git动图分解提取信息

![在这里插入图片描述](img/4dabddf2c6f3734a69b7fc7830efd77a.png)

下载附件发现是一个动图，闪烁着红色 flag 字体。该题需要用到隐写图片查看的神器------**stegsolve**，[下载地址](http://www.caesum.com/handbook/Stegsolve.jar)。

Stegsolve 功能简介：
![在这里插入图片描述](img/964919cec24b103a61450a34bdafa4ef.png)

上面是软件打开的界面，界面简单。主要供能为 analyse，下面对 Analyse 下面几个功能键作简单介绍：

| 功能键 | 作用 |
| --- | --- |
| File Format | 文件格式，这个主要是查看图片的具体信息 |
| Data Extract | 数据抽取，图片中隐藏数据的抽取 |
| Frame Browser | 帧浏览器，主要是对GIF之类的动图进行分解，动图变成一张张图片，便于查看 |
| Image Combiner | 拼图，图片拼接 |

此题是动图中闪烁着 flag，故使用 Frame Browser 功能对 git 动图进行分解，获得 89 张分解后的静图，查阅获得 flag：
![在这里插入图片描述](img/eadf5376f4c8bc4ee1ccde7c91a66e68.png)

![在这里插入图片描述](img/fc5d6d3d5928d9fe32b9b2063aefabcb.png)![在这里插入图片描述](img/eb14da58f81ec9583fe6a61d4dea8a9c.png)
获得 flag：`flag{he11ohongke}`，本入门题完。

# No.2 隐藏文件提取与爆破

![在这里插入图片描述](img/91a4660fc0967ef7f6c561452637d1f6.png)
1、下载完打开是个二维码，用微信扫描只能看见一句话——“secret is here”。
![在这里插入图片描述](img/1abafe6b0d6b9f1344807362dbc000fc.png)
2、猜测二维码图片中隐藏了别的文件，可以使用 Binwalk 工具进行文件分析和分离。
![在这里插入图片描述](img/ccaaee7e00c53c42ae983018b8c35b05.png)
Kali Linux 自带了 Binwalk 工具，但我不想开虚拟机……所以在 Windows 下安装，[Github下载源码](https://github.com/ReFirmLabs/binwalk)，本地安装好 python 并进入 Binwalk 所在文件夹，执行命令如下，程序将会自动安装：
![在这里插入图片描述](img/0aafa894a8beb2a05259b5846fb8c7d7.png)

安装完成后在 python 安转目录下的 Script 目录里有个名字叫做 binwalk 的文件，然后在该文件夹启动命令行就可以使用 Binwalk 了：

![在这里插入图片描述](img/65afe20d3101b719f882e3d4f7b2580c.png)
3、使用 Binwalk 分析目标图片，可以看到隐藏着几个文件：
![在这里插入图片描述](img/6daf4f4b39a6e3808d519a7e3cdecb65.png)4、使用 Binwalk 进一步分离出二维码图片中隐藏的文件：

![在这里插入图片描述](img/48fd5d24589d29c3a792b2856be36fe6.png)5、打开分离出来的压缩文件，提示需要进行密码才能查看文件：
![在这里插入图片描述](img/93f4b52c37659aecec008d4e60b3c46e.png)6、根据提示是 4 位数的密码，直接使用 Ziperello 工具对 ZIP 文件进行暴力破解（下载链接：https://pan.baidu.com/s/17TxQVXE8oZZA_dsyJglaqQ；提取码：2333）：
![在这里插入图片描述](img/e0e236558aaf3e2d86f73b35e12d900b.png)![在这里插入图片描述](img/e397260fe6b0a3826f147724e42a3b26.png)

获得密码为 7639：
![在这里插入图片描述](img/1430b79c0ec5d0de6e5608826b56d005.png)7、打开加密文件，获得 Flag：
![在这里插入图片描述](img/9b23ddb70917cc38fb81e574f944cb14.png)

# N0.3 Base64编码还原图片

![在这里插入图片描述](img/94e07b2b9076c716cd36a57a8ff8b68e.png)1、下载后是一个 KEY.exe 文件：
![在这里插入图片描述](img/478a9b46f6e74de0ef4c8eb4161470bd.png)
2、使用 Sublime 打开，发现是图片转换成 Baase 64编码：
![在这里插入图片描述](img/ad7f09a6fa11a7107afa563fcee4a478.png)3、使用站长之家在线转换该 base 64 编码并还原成图片：
![在这里插入图片描述](img/09e0c10f207571ddc451e06811be8384.png)4、接着使用微信扫描二维码或者在线识别二维码图片，可获得 flag：
![在这里插入图片描述](img/846fce49eef5cd4d7a8013ff25750b78.png)

# No.4 winhex修改图片大小

![在这里插入图片描述](img/47842e1cf4a296c7d58f4aab4a816c4b.png)1、下载后打开图片如下：
![在这里插入图片描述](img/8c08aa67c99f8cb5a33ad1bc63113d97.png)
2、根据题目提示，需要将图片变大，可使用 winhex 打开图片并修改图片的宽、高。先来介绍下相关知识：

![在这里插入图片描述](img/a74345a0831e16b13e74448aa25abaeb.png)![在这里插入图片描述](img/539f9f18af941402369fa351b7cdbff8.png)
3、故使用 winhex 工具打开图片，找到宽、高位置，修改其值：
![在这里插入图片描述](img/e80087059392ae9592fc114db4a0c89f.png)第 18、19 位是长，22、23是宽，我们把宽度设置大一点，就设置和长度一样好了：
![在这里插入图片描述](img/bcef98ae579855361f96ca0bec166b5c.png)

4、保存后重新打开图片，可获得 flag：
![在这里插入图片描述](img/0cb6547e49cb5469041814fe4ada127f.png)

# No.5 编辑器查看图片隐写

![在这里插入图片描述](img/dbe2091193d56fffa39475ad6e8c0e60.png)1、下载后打开是一张图片：
![在这里插入图片描述](img/f45076abe0feade0b5729bf7ff47e8fc.png)
2、使用 Notepad 编辑器打开查看图片，搜索 flag，可在末尾获得 flag：
![在这里插入图片描述](img/e34f25daf81bfc50d83dd9e9408123dc.png)

# No.6 LSB隐写的信息导出

![在这里插入图片描述](img/6569944d154956518884fa5c16319e20.png)1、下载后是一张 png 图片：
![在这里插入图片描述](img/ba91925bcd658f2c78bba02c1803a6a7.png)
题目的提示很明显了，这是一道 LSB 隐写题。

LSB 全称为 least significant bit，是最低有效位的意思。Lsb 图片隐写是基于 lsb 算法的一种图片隐写术，以下统称为 lsb 隐写，这是一种常见的信息隐藏方法。lsb 隐写很实用，算法简单，能存储的信息量也大，是 CTF 比赛中的常客。

【原理介绍】

png 图片是一种无损压缩的位图片形格式，也只有在无损压缩或者无压缩的图片（BMP）上实现 lsb 隐写。如果图像是 jpg 图片的话，就没法使用lsb隐写了，原因是 jpg 图片对像数进行了有损压缩，我们修改的信息就可能会在压缩的过程中被破坏。而 png 图片虽然也有压缩，但却是无损压缩，这样我们修改的信息也就能得到正确的表达，不至于丢失。BMP 的图片也是一样的，是没有经过压缩的。BMP 图片一般是特别的大的，因为 BMP 把所有的像数都按原样储存，没有进行压缩。

png 图片中的图像像数一般是由 RGB 三原色（红绿蓝）组成，每一种颜色占用8位，取值范围为 `0x00~0xFF`，即有 256 种颜色，一共包含了 256 的3次方的颜色，即 16777216 种颜色。而人类的眼睛可以区分约 1000 万种不同的颜色，这就意味着人类的眼睛无法区分余下的颜色大约有 6777216 种。

![在这里插入图片描述](img/47005a61652cbaaa808b54934884942b.png)
LSB 隐写就是修改 RGB 颜色分量的最低二进制位也就是最低有效位（LSB），而人类的眼睛不会注意到这前后的变化，每个像数可以携带3比特的信息。
![在这里插入图片描述](img/4fd1546c373c14ff32e6c1052f0cdebb.png)
上图我们可以看到，十进制的 235 表示的是绿色，我们修改了在二进制中的最低位，但是颜色看起来依旧没有变化。我们就可以修改最低位中的信息，实现信息的隐写。

2、回到这道题目中来，针对 LSB 隐写，同样可以使用隐写图片查看的神器------**stegsolve**来分析，用 stegsolve 打开这张图片，发现 `red0，green0，blue0` 这三个通道的图片上方有异样：
![在这里插入图片描述](img/63753247051b6c1e4cc0ffd80f78fac2.png)![在这里插入图片描述](img/87e514a878c6cd32abb56f305d136969.png)
3、因此用 Analyse 菜单栏里的 Data Extract 功能（数据抽取，图片中隐藏数据的抽取）查看图片，将三个位置的 0 通道勾选并导出保存为 png 格式的文件：
![在这里插入图片描述](img/c4826a2f3893070f01daf44a87bda8b3.png)

4、获得 flag.png 图片，发现是个二维码：
![在这里插入图片描述](img/6e8ea565b06defbe6c00b2e590206423.png)
5、在线解码，获得 flag：
![在这里插入图片描述](img/eeef7397069abbda7be09b34cb79cd46.png)

# No.7 图片属性中隐藏信息

![在这里插入图片描述](img/9f365b89e8a155620bb4b4215c081630.png)
1、打开是一张路飞的图：
![在这里插入图片描述](img/50ba75c499cb7cbf263d8e8fbe082ab8.png)
2、查看图片属性可直接获得 flag：
![在这里插入图片描述](img/e83aa9a2fb19263c9deef119a7a560cf.png)

# No.8 LSB隐写的数据抽取

![在这里插入图片描述](img/2e6ef8961232932d07058914c9376a84.png)1、下载后打开是一张图片：
![在这里插入图片描述](img/0e64727e999ff73d87b5cc742da67676.png)
2、猜测是隐写，使用 binwalk，查看是否有隐藏文件，然而并没有：
![在这里插入图片描述](img/466279fc448d1f671b4e0beef5c3b941.png)
3、使用 Stegsolve，查看是否存在 LSB 隐写，发现 `red0，green0，blue0` 这三个通道的图片均为异样的黑色：
![在这里插入图片描述](img/6a775e640f8a125f997bb3e960360a41.png)

4、因此用 Analyse 菜单栏里的 Data Extract 功能（数据抽取，图片中隐藏数据的抽取）查看图片，将三个位置的 0 通道勾选并预览，发现 flag：
![在这里插入图片描述](img/8955c1ff1559376ad44135a0dc05ae86.png)

# No.9 RAR文件加密的爆破

![在这里插入图片描述](img/4d3541a1a8bffc07b50df5f8ffe606d6.png)1、下载发现是一张图片：
![在这里插入图片描述](img/a8f70cfa0c3a1e3b5bcdacbc34d46d39.png)
2、使用 **stegsolve** 的格式分析功能，发现 RAR 关键词：
![在这里插入图片描述](img/d7cf9a7d92cf93d7dc426a384e1bef93.png)3、动用 Binwalk 工具分析，分离出 rar 压缩文件：
![在这里插入图片描述](img/3359ccba944c4458c7cfe62cef8fd0c6.png)4、打开压缩文件，发现加密了：
![在这里插入图片描述](img/9122c7825809a4b23a82c4eecf8af39a.png)5、使用 ARCHPR 爆破工具尝试进行 4 位数密码的破解：
![在这里插入图片描述](img/4355e5d90bfef5c4ee71361290b6a5c8.png)
6、成功破解密码，获得 Flag：
![在这里插入图片描述](img/a9518a27cedf7d0892a07b43a250f235.png)

# No.10 二进制编码转AscII

![在这里插入图片描述](img/3c2e46c1673fae12decc58ce749684c7.png)1、下载后如下：
![在这里插入图片描述](img/f7b7be9e3d735d922f62b1ef770323a4.png)
2、使用 Winhex 查看，发现末尾有二进制串：
![在这里插入图片描述](img/6ff9bf4e6d3ffb37c02ddc2da0a0f567.png)
3、猜测可用 8 位二进制一组，转换成 ASCII 编码，脚本如下：

```
import binascii

a = '''01101011
01101111
01100101
01101011
01101010
00110011
01110011'''
b = ''
for ii in a.split('\n'):
	b += chr(int(ii,2))
print(b) 
```

运行脚本，获得 flag：
![在这里插入图片描述](img/8c9f8a45e18043aeb06d958709fbe800.png)

# No.11 Winhex搜索获得flag

![在这里插入图片描述](img/715393a4a5293cfa7f15bf558b8cfed6.png)1、下载后打开如下：
![在这里插入图片描述](img/36357a0976fb6264321f2caae2c31ed0.png)
2、使用 winhex 打开图片并搜索 flag，看到 base64 编码字符串：
![在这里插入图片描述](img/ec045051f643f9404dab6289bae87f86.png)
3、base64 解码获得 flag：
![在这里插入图片描述](img/ddf9c3f5662337befff4ae77ba7f3809.png)

# No.12 ZIP的16进制文件头

![在这里插入图片描述](img/61c0c6954c6db32793bfe577fa12d323.png)下载后是一张图片：
![在这里插入图片描述](img/f51997e085d9232d31220564f96703c1.png)
1、使用 stegsolve 打开图片，发现在 RGB 模式下 0 通道预览是个 ZIP 文件（文件头 `504b0304`），存在 LSB 隐写，Save Bin 保存为 flag.zip：
![在这里插入图片描述](img/bd6928a2033fb973f9dd964e58ce5ffc.png)【**注意**】一定要对常见的文件头高度敏感：
![在这里插入图片描述](img/b61d522be36a8fce1751d9a3f3f17778.png)2、360解压缩导出来的 flag.zip 文件，获得一个没有后缀的文件：
![在这里插入图片描述](img/4355562aa93159a586fabd79f1a3ddea.png)
3、编辑器或者 Winhex 打开发现是 ELF 文件：
![在这里插入图片描述](img/f1315d6ffc5083927673192994e600db.png)
4、使用 IDA 打开可以获得 Flag：
![在这里插入图片描述](img/dde2a3b40cd0c9a85e98d56795f14008.png)

# No.13 盲文与摩斯密码读取

![在这里插入图片描述](img/0ab67447b46c3f3ffb421ee43fa7f702.png)1、下载后是一张图片和压缩文件：
![在这里插入图片描述](img/a8d83acecb76b96eae01ebe9838579e8.png)
2、对照盲文解密图片中隐藏的字符串`kmdonowg`：
![在这里插入图片描述](img/702b1e465b4f5906e92ee927b1e31cb1.png)
3、解压缩 music.zip 文件发现需要密码，输入上面解密的字符串正好可以解开：
![在这里插入图片描述](img/3e65b3a4236b7165ea399d320af94290.png)
4、解压后得到一个 wav 音频文件，用 Audacity 查看其波形，如下图所示：
![在这里插入图片描述](img/4cb8de8863dbd4e2b8cc551086223067.png)较长的蓝色即为“`-`”，较短的即为“`.`”，每一段由`-`和`.`组成的即为一个字符。可以按顺序将其莫斯解密，得到：
![在这里插入图片描述](img/63819f0915e7f6f8122904d86a266d5f.png)
最终 flag： `flag{wpei08732?23dz}`。

# No.14 Brainfuck编码与解码

![在这里插入图片描述](img/5e124c512e18205892e7334092c353c4.png)1、下载后解压需要密码：
![在这里插入图片描述](img/41ea202392ef2b41f01c698f1f02796c.png)
2、尝试使用 ARCHPR 进行四位数字的爆破：
![在这里插入图片描述](img/dfb2da8f19fc73ddc8dc7e6ee339cc4e.png)
3、解压后打开文件：
![在这里插入图片描述](img/6d4ededc1f0c7c8db7716af9a2e12e64.png)4、发现是 brainfuck 代码，使用 [在线执行网站](http://bf.doleczek.pl/) 运行即可得到 flag：
![在这里插入图片描述](img/0d36255c2e5aeedb1b38a2400f13f699.png)科普下 brainfuck 代码：
![在这里插入图片描述](img/3914bbf4d80d3db4fe262fa98e4e1583.png)

# No.15 后门查杀之后门识别

![在这里插入图片描述](img/12daa422def656e55d0f4edcce764f9e.png)
1、下载后解压缩如下：

![在这里插入图片描述](img/c7d3ba0225bf0af5f21f2d2941c6ea3f.png)

2、找 Webshell，直接上火绒扫描该文件夹：

![在这里插入图片描述](img/5b33f501b33846bd65d868d7a3a147d7.png)
3、打开该文件，发现 flag：`flag{6ac45fb83b3bc355c024f5034b947dd3}`：

![在这里插入图片描述](img/0b78853b1afa94fb052756c6f3fd5c0f.png)

# No.16 路由器信息数据查看

![在这里插入图片描述](img/547e7cca94280ae062fd1b350a0454b7.png)
1、下载后解压缩是个 conf.bin 文件，路由器信息数据，一般包含账号密码，题目并没有提示 flag 是什么，猜测是账号或者密码加上格式为最终 flag：
![在这里插入图片描述](img/37a439cce0c17922830f40d77af87eb3.png)
2、无法使用编辑器直接打开阅读该类文件，需要用 `RouterPassView` 工具查看，打开后搜索 username 或者 password：
![在这里插入图片描述](img/46245b2515f2915360ff58d16929da24.png)

最后发现是用户名为 flag，加上格式提交即可`flag{053700357621}`。

# No.17 base64流量导出图片

![在这里插入图片描述](img/fd1683ab8c896a133f18aa0610f4cfee.png)
1、下载后用 wireshark 打开，大部分都是 TCP 的包，直接过滤出 http 的包：
![在这里插入图片描述](img/0f9bac929e2fb7de2cdd15ec1d6b2d16.png)
2、追踪 http 流：
![在这里插入图片描述](img/4a920b0292a73110eec003489a95f4e1.png)

3、传输内容好像是 base64 密文，使用 [在线网站](https://the-x.cn/base64) 解码 base64：
![在这里插入图片描述](img/3c1c4bbfa540b02e9183eb4016e18c79.png)
4、发现解码出来的内容是 jpg 图片格式并以自动转为图片，下载即可得到 flag：
![在这里插入图片描述](img/8719e385ab9536ff74ef3dc45925d8d0.png)

# No.18 脑洞大开歌名猜密钥

![在这里插入图片描述](img/a2b217839279044594ddfa53829b3451.png)
下载后是一张蛇的照片：
![在这里插入图片描述](img/e23c08ab726159ee0accb203868ddffc.png)

1、使用 Binwalk 分离提取出两个隐藏文件：
![在这里插入图片描述](img/a0a325e3e172e772de0a968c32bfb68b.png)
2、打开 Key 文件发现 Base64 编码：
![在这里插入图片描述](img/417c230a368d34522112684205834d8b.png)
3、解码如下：
![在这里插入图片描述](img/682d459f3c72129e8d3bd4ecb8050431.png)

What is Nicki Minaj’s favorite song that refers to snakes?

跟蛇有关，于是我们百度 Nicki Minaj 然后有她的百度百科，观看一下，发现：
![在这里插入图片描述](img/e496160562d7337b75c7f49b198ffcd3.png)![在这里插入图片描述](img/5c13b1fe7a30cae85e8e1988b919523a.png)

Anaconda 是关于蛇的作品，这是不是我们的 key 呢？

于是我们用 Serpent 工具解密，Serpent 是一个加密算法（为何猜测是这种加密算法……不知道……），http://serpent.online-domain-tools.com/，我们用这个然后key是 Anaconda：
![在这里插入图片描述](img/1a1df7249dc99cf9335b7de26456b973.png)

获得 flag：`flag{who_knew_serpent_cipher_existed}`。

# No.19 Steghide隐写工具的使用

![在这里插入图片描述](img/e79ada52b8469a42492e75efe2a4b1df.png)下载后是一张图片：
![在这里插入图片描述](img/5164bf778a7d0ea32243ed8918cdda27.png)
1、Binwalk 查看发先隐藏了一个 ZIP 文件，进行提取：
![在这里插入图片描述](img/67ab78b4f2858ba722218209a7ce42cd.png)2、提取后发现两个文件：
![在这里插入图片描述](img/6609d465cbccb5d0307872a6898bfe24.png)
3、直接打开 qwe.zip 里面的文件发现需要密码，那大概率就是回到另一个图片`good-已合并.jpg`进行信息提取了：
![在这里插入图片描述](img/8c06c021f3b54e6505d6c136edd45072.png)
4、使用 Binwalk 查看 `good-已合并.jpg` 是否由多个文件组合而成，无果：
![在这里插入图片描述](img/9acbbe3c86b12929ba7f0a1419b8ee72.png)5、此时只能用尝试用隐写工具 **Steghide** （[官方下载地址](http://steghide.sourceforge.net/download.php)）进行信息提取。这是一个可以将文件隐藏到图片或音频中的工具，其使用如下：

| 目的 | 命令 |
| --- | --- |
| Liunx安装 | apt-get install steghide |
| 隐藏文件 | steghide embed -cf 1.jpg（图片文件载体） -ef 1.txt（待隐藏文件）-p 123456（-p参数用于添加密码，非必选） |
| 查看图片中嵌入的文件信息 | steghide info 1.jpg |
| 提取图片中隐藏的文件 | steghide extract -sf 1.jpg -p 123456 |

我下载的是 Windows 版本（懒得打开虚拟机…），其使用方法为命令行 cd 到 `steghide.exe` 所在的文件夹后，与 Linux 版本的参数和命令结构都差不多，也是无密码直接回车就好：

![在这里插入图片描述](img/17c7031529066b007d083f1822169af2.png)
6、上面已经发现图片中隐藏了 ko.txt 文件了，进一步进行信息提取：
![在这里插入图片描述](img/a7310648ae41b3616f88784639977222.png)7、拿着上述压缩包密码解密 flag.txt，获得 flag：
![在这里插入图片描述](img/2816b8cbd6346e7970650af4c8cd7766.png)

# No.20 ZIP伪加密与多重编码转换

![在这里插入图片描述](img/f9b48009c0e2dd250bc86a086fc23bcc.png)
下载后是一张图片：
![在这里插入图片描述](img/46a9ffb07b0eccc2462ca76d9a485282.png)
1、使用 Binwalk 发现隐藏着文件，将其分离出来：
![在这里插入图片描述](img/bb95ffcb0fcb5263c0571dbe79e6135a.png)2、分离出来的 ZIP 文件需要密码：
![在这里插入图片描述](img/e0611d9ada49c68db1ddccb74222df74.png)3、使用 Winhex 打开 ZIP 压缩文件，发现是 ZIP 伪加密（[CTF杂项-ZIP伪加密与Base64隐写实战](https://bwshen.blog.csdn.net/article/details/115609257)），如图可见压缩源文件数据区的全局加密为`00 00`：
![在这里插入图片描述](img/103697495a404a8f0e3e1d22b650cc81.png)![在这里插入图片描述](img/33110e68a980d0a657e51aa2a384754d.png)

搜索十六进制数值`504B0102`（压缩源文件目录区头标志），发现全局方式位标记为`09 00`，断定为伪加密：
![在这里插入图片描述](img/c88459fad06f2d074b0a1a6057a6aba8.png)

4、修改 `09 00` 为 `00 00`后保存，即可解压缩该伪加密的 ZIP 文件，获得如下文件：
![在这里插入图片描述](img/4f01ddfff703b74cc6b01dcddcfb71e2.png)
5、对于 vmdk 文件，需要使用 `7-ZIP` 工具解压缩（[官网下载地址](https://sparanoid.com/lab/7z/download.html)），安装后打开后压缩包，如下：
![在这里插入图片描述](img/19fcd910957c2fca0d76984c8634269b.png)
6、打开 Key_part_one 文件夹里面的文件：
![在这里插入图片描述](img/585e5f2a662ef57f9f96a4a7512f986b.png)`Brainfuck` 编码，[Brainfuck在线解密](http://bf.doleczek.pl/)：
![在这里插入图片描述](img/c3ca93627987ebce9378a21ca7ac39bd.png)

7、打开 Key_part_one 文件夹里面的文件：

```
Ook. Ook. Ook. Ook. Ook. Ook. Ook. Ook. Ook. Ook. Ook. Ook. Ook. Ook. Ook.
Ook. Ook. Ook. Ook! Ook? Ook! Ook! Ook. Ook? Ook. Ook. Ook. Ook. Ook. Ook.
Ook. Ook. Ook. Ook. Ook. Ook. Ook. Ook. Ook. Ook. Ook. Ook. Ook? Ook. Ook?
Ook! Ook. Ook? Ook. Ook. Ook. Ook. Ook. Ook. Ook. Ook. Ook. Ook. Ook. Ook.
Ook. Ook. Ook. Ook. Ook. Ook. Ook. Ook. Ook. Ook. Ook. Ook. Ook. Ook. Ook.
Ook. Ook! Ook. Ook? Ook. Ook. Ook. Ook. Ook. Ook. Ook. Ook! Ook? Ook! Ook!
Ook. Ook? Ook. Ook. Ook. Ook. Ook. Ook. Ook? Ook. Ook? Ook! Ook. Ook? Ook.
Ook. Ook! Ook. Ook? Ook. Ook. Ook. Ook. Ook. Ook. Ook. Ook. Ook. Ook. Ook.
Ook. Ook. Ook. Ook. Ook! Ook? Ook! Ook! Ook. Ook? Ook! Ook! Ook! Ook! Ook!
Ook! Ook! Ook! Ook! Ook! Ook! Ook! Ook! Ook! Ook? Ook. Ook? Ook! Ook. Ook?
Ook! Ook! Ook! Ook! Ook! Ook! Ook! Ook. Ook? Ook. Ook. Ook. Ook. Ook. Ook.
Ook. Ook. Ook. Ook. Ook. Ook. Ook. Ook! Ook? Ook! Ook! Ook. Ook? Ook. Ook.
Ook. Ook. Ook. Ook. Ook. Ook. Ook. Ook. Ook. Ook. Ook? Ook. Ook? Ook! Ook.
Ook? Ook. Ook. Ook. Ook. Ook. Ook. Ook. Ook. Ook. Ook. Ook. Ook. Ook! Ook.
Ook. Ook. Ook. Ook. Ook. Ook. Ook. Ook. Ook. Ook. Ook. Ook. Ook. Ook. Ook!
Ook. Ook? Ook. Ook. Ook. Ook. Ook. Ook. Ook. Ook! Ook? Ook! Ook! Ook. Ook?
Ook. Ook. Ook. Ook. Ook. Ook. Ook? Ook. Ook? Ook! Ook. Ook? Ook. Ook. Ook.
Ook. Ook. Ook. Ook. Ook. Ook. Ook. Ook. Ook. Ook! Ook. Ook! Ook! Ook! Ook!
Ook! Ook! Ook! Ook! Ook! Ook! Ook! Ook! Ook! Ook! Ook! Ook. Ook! Ook. Ook?
Ook. Ook. Ook. Ook. Ook. Ook. Ook. Ook! Ook? Ook! Ook! Ook. Ook? Ook. Ook.
Ook. Ook. Ook. Ook. Ook? Ook. Ook? Ook! Ook. Ook? Ook. Ook. Ook. Ook. Ook!
Ook. Ook? Ook. Ook. Ook. Ook. Ook. Ook. Ook. Ook. Ook. Ook. Ook. Ook. Ook.
Ook. Ook. Ook. Ook. Ook. Ook. Ook! Ook? Ook! Ook! Ook. Ook? Ook! Ook! Ook!
Ook! Ook! Ook! Ook! Ook! Ook! Ook! Ook! Ook! Ook! Ook! Ook! Ook! Ook! Ook!
Ook? Ook. Ook? Ook! Ook. Ook? Ook! Ook! Ook! Ook! Ook! Ook! Ook! Ook! Ook!
Ook! Ook! Ook! Ook! Ook! Ook! Ook. Ook? Ook. Ook. Ook. Ook. Ook. Ook. Ook.
Ook. Ook. Ook. Ook. Ook. Ook. Ook. Ook. Ook. Ook. Ook. Ook. Ook! Ook? Ook!
Ook! Ook. Ook? Ook. Ook. Ook. Ook. Ook. Ook. Ook. Ook. Ook. Ook. Ook. Ook.
Ook. Ook. Ook. Ook. Ook. Ook. Ook? Ook. Ook? Ook! Ook. Ook? Ook. Ook. Ook.
Ook. Ook. Ook. Ook. Ook. Ook. Ook. Ook. Ook. Ook. Ook. Ook. Ook. Ook. Ook.
Ook. Ook. Ook. Ook. Ook! Ook. Ook? Ook. 
```

`Ook!`编码，[在线解密](https://www.splitbrain.org/services/ook)：

![在这里插入图片描述](img/8ba53fc5e07fb36d78ee447c6e4c5cce.png)
拼接获得 flag：`flag{N7F5_AD5_i5_funny!}`。

# No.21 F5隐写与ZIP文件头的识别

![在这里插入图片描述](img/c5735895fc580f6475a764307c7a1f2c.png)
下载后是一张图片：
![在这里插入图片描述](img/09a5d089d04d5922ddcb72615786370b.png)
使用各种解密软件（Stegsolve、steghide、binwalk）都没有找到问题所在，回到题目提示：“浏览图片的时候刷新键有没有用呢”，刷新，F5？？？在百度的帮助下了解了 F5 隐写（小白啊，真的蒙了）……还真有！F5 隐写的原理可参见： [F5隐写算法及其隐写分析研究](https://www.docin.com/p-682079942.html)，此处直接上 F5 隐写利用工具（[F5-steganography的Github地址](https://github.com/matthewgao/F5-steganography)）：
![在这里插入图片描述](img/91e4732a8b0dc988d6532bef3574a362.png)
1、在该工具的文件夹路径下执行命令 `java Extract 图片路径` 即可在输出 `output.txt` 文件记录了图片隐写的信息：

![在这里插入图片描述](img/473bf880fcbaf442abc5e467013ae614.png)
2、使用 Winhex 打开 output.txt 发现 ZIP 文件的文件头（实际上根据打开的 txt 文件的 `PK` 首部字符也可猜测是 ZIP 文件：
![在这里插入图片描述](img/47c3353a786d773916f7e81ce5ea600f.png)3、故修改 output.txt 后缀获得 output.zip 压缩文件，解压发现加密：
![在这里插入图片描述](img/153bfcc41b27356705a5d23fe6f682e7.png)
4、伪加密，将 `01 00`改为 `00 00`后保存，即可解压：
![在这里插入图片描述](img/f6d3bab854564e5841d636ff118eab61.png)
5、解压缩文件后获得 flag：
![在这里插入图片描述](img/f94ecc78e54316ef24307cd1ff9cdb0f.png)

# No.22 Py脚本16进制坐标绘二维码

![在这里插入图片描述](img/103ec100a20cbd8b384d2c929b4d22ee.png)下载后是一张图片：
![在这里插入图片描述](img/5c513d93509dca11ce0f61960a1dc1b9.png)
1、编辑器或者 winhex 打开图片，发现末尾存在大量十六进制：
![在这里插入图片描述](img/f7713308c5dc5d5eae8bc473f8376c12.png)2、将 16 进制数据复制到 hex.txt 利用脚本转化为 ascii 获取坐标点：

```
with open('hex.txt', 'r') as h:
    h = h.read()
with open('./ascii.txt', 'a') as a:
    for i in range(0, len(h), 2):
        tmp = '0x'+h[i]+h[i+1]
        tmp = int(tmp, base=16)
        if chr(tmp) != '(' and chr(tmp) != ')':
            a.write(chr(tmp)) 
```

执行脚本：
![在这里插入图片描述](img/3d7b41d219567ed88a7e8e3552db6b1d.png)
3、用 matplotlib 绘图得到二维码：

```
import matplotlib.pyplot as plt
import numpy as np

x, y = np.loadtxt('./ascii.txt', delimiter=',', unpack=True)
plt.plot(x, y, '.')
plt.show() 
```

生成二维码：
![在这里插入图片描述](img/ef50d76b5be68fcd009ebc91f5311c58.png)

扫描二维码获得 flag：
![在这里插入图片描述](img/1178f0ac84047613bc71949d620acab7.png)

最终脚本：

```
import matplotlib.pyplot as plt
import numpy as np

with open('hex.txt', 'r') as h:
    h = h.read()
with open('./ascii.txt', 'a') as a:
    for i in range(0, len(h), 2):
        tmp = '0x'+h[i]+h[i+1]
        tmp = int(tmp, base=16)
        if chr(tmp) != '(' and chr(tmp) != ')':
            a.write(chr(tmp))

x, y = np.loadtxt('./ascii.txt', delimiter=',', unpack=True)
plt.plot(x, y, '.')
plt.show() 
```

# No.22 HTTP流量分析与文件转换

![在这里插入图片描述](img/01e3510699dd9e8398eb9a041c0ce04d.png)下载后是一个流量文件：
![在这里插入图片描述](img/05a18993eda256a79a874d9683a788c4.png)
1、直接过滤出 HTTP 流量，题目因为是菜刀吗？一般都是 post 连接，于是我们过滤 post 数据`http.request.method==POST`，然后分析流量：
![在这里插入图片描述](img/33a55f557d78a5288f448a7a84ebcb4a.png)

2、当我们分析到流7时，发现了 base64 编码，解码一看是上传的图片的地址：
![在这里插入图片描述](img/1d1ec1fbb014e3692d26de9c01a6f3f0.png)
z1 的值拿去解码得到：
![在这里插入图片描述](img/c3b7c1dc2c97236af8bafa14e15154d0.png)
后面跟了个 z2 参数`FF D8`开头`FF D9`结尾，判断为 jpg 图片，将这些十六进制复制出来，以原始文件流写入文件：

```
import struct

a = open("hex.txt","r")
lines = a.read()
res = [lines[i:i+2] for i in range(0,len(lines),2)]

with open("res.jpg","wb") as f:
	for i in res:
		s = struct.pack('B',int(i,16))
		f.write(s) 
```

执行脚本获得图片：
![在这里插入图片描述](img/fbeda94d8e0be54da95debadc52bdf17.png)
2、使用 Binwalk 分析 `666666.pcapng` 文件，发现隐藏着一个 flag.txt，提取：
![在这里插入图片描述](img/7e25ab956dc5377c402a943c4da7e843.png)
分离出来一个压缩包，需要密码，难道这就是压缩包的密码，然后输入密码，打开了文件，然后获得了flag：`flag{3OpWdJ-JP6FzK-koCMAK-VkfWBq-75Un2z}`。

# No.23 HTTP流量分析与文件提取

![在这里插入图片描述](img/fb226cc490bdc86f6e8e1e8c4c380346.png)下载后如下：
![在这里插入图片描述](img/36e48ac84232939474b45a179bcd3d3e.png)
1、传输文件看到了 ftp 协议，过滤一下，发现 flag.rar 文件：
![在这里插入图片描述](img/b94c0b8af8f7f753b7d4f7807e18312e.png)

2、使用 Binwalk 分析流量文件`被偷走的文件.pcapng`，发现隐藏的文件，进行提取：
![在这里插入图片描述](img/4c7a52e21e0dac35f43bc7fafcf789a6.png)

3、获得 170A.rar 文件，需要密码：
![在这里插入图片描述](img/5bd3b938fd55be2463bf3168e1121c88.png)

4、尝试 ARCHPR 四位数字密码爆破：
![在这里插入图片描述](img/cbfa81d19db44023c3f03462f7aa6780.png)
5、输入密码获得 flag：
![在这里插入图片描述](img/1d62156d6365fafff0f784b4b11b6ab4.png)

# 总结

本文是 BUUCTF 较为简单的杂项题目的汇集，总结下本文所涉及的一些杂项题目解题思路，主要还是图片隐写：

1.  拿到文件可先查看属性是否有特殊信息；
2.  可使用 Winhex 或者 Notepad++ 编辑器查看文件是否有特殊信息，同时关注是否隐藏常见的文件头；
3.  必要时可使用 Winhex 修改图片的长、宽数值获得 flag；
4.  隐写图片查看的神器------stegsolve，可以帮助查看文件信息、分离动图、处理 LSB 隐写；
5.  Binwalk 工具可快速分辨文件是否由多个文件合并而成，并可将文件进行分离提取；
6.  对于压缩文件的加密，可看看是不是 ZIP 伪加密或者尝试使用 ARCHPR 工具进行四位数字的爆破；
7.  在 Binwalk 无法分析出存在合并的隐藏文件的情况下，可以考虑使用 Steghide 隐写工具查看是否隐写了字符串。

CTF 杂项题目还很多知识和套路不懂，路漫漫其修远兮……