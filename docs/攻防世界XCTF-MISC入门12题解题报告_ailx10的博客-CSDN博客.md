<!--yml
category: 未分类
date: 2022-04-26 14:54:45
-->

# 攻防世界XCTF-MISC入门12题解题报告_ailx10的博客-CSDN博客

> 来源：[https://blog.csdn.net/admin_gt/article/details/122106811](https://blog.csdn.net/admin_gt/article/details/122106811)

MISC属于CTF中的脑洞题，简直就是信息安全界的脑筋急转弯。你说它渣，它也有亮点，不好评说。这块最亮眼的入门题就属隐写术，出题人骚的狠。但是我感觉未来其中一个重点，就是大数据安全，从海量流量中捕获恶意流量，比如流量中有一个常见的OWASP10攻击，flag就藏在恶意流量里面，找到攻击就找到flag。准备大年30晚上把这块吃掉。写文章不容易，求三连。感谢小伙伴们了，祝点赞的小伙伴2020年好事连连～

**攻防世界XCFT刷题信息汇总如下：[攻防世界XCTF黑客笔记刷题记录](https://zhuanlan.zhihu.com/p/103650970)** ，收藏一下哈～

## 第一题：送分题

解题报告：

复制粘贴flag，就通过了，啧啧

```
flag{th1s_!s_a_d4m0_4la9} 
```

## 第二题：考察pdf隐写

解题报告：

将pdf转为txt就看到flag咯

```
pdftotext ad00be3652ac4301a71dedd2708f78b8.pdf q.txt 
```

## 第三题：考察组合加密

第一步：与佛论禅

第二步：rot13<sup>[1]</sup>

第三步：base64解码

## 第四题：考察GIF隐写

解题报告：

第一步：观察gif动画，发现第50帧有一个残缺的二维码

第二步：导出这一帧，并手动画上小正方形，补全二维码

感叹我是个灵魂画手～

第三步：手机微信扫一扫，哎，flag就到手啦

```
flag{e7d478cf6b915f50ab1277f78502a2c5} 
```

## 第五题：考察java反编译

解题报告：

第一步：jar包解压缩

```
unzip -qo 9dc125bf1b84478cb14813d9bed6470c.jar -d classes 
```

第二步：java反编译

```
jad -o -r -sjava -dsrc 'cn/**/*.class' > /dev/null 2>&1 
```

第三步：字符串搜索flag

第四步：base64解码获得最终flag

```
flag{DajiDali_JinwanChiji} 
```

## 第六题：考察二进制隐写

解题报告：

第一步：查看发现有104张jpg黑白图片，有点像2进制里的0和1

`104/8=13`，也就是说有13个数字，那不就是flag吗？

第二步：听说你是一张图片一张图片看，人肉转换黑白为01或10？

编写一口气脚本，不求人：

```
from PIL import Image
seq1=""
seq2=""
for i in range(104):
    filename = str(i)+".jpg"
    img = Image.open(filename)
    clrs = img.getcolors()
    if sum(clrs[0][1])>400:
        seq1+="0"
        seq2+="1"
    else:
        seq1+="1"
        seq2+="0"

def getflag(seq1):
    a1 = ""
    for i in range(104/8):
        s1 = seq1[:8]
        seq1 = seq1[8:]
        i1 = int(s1,2)
        a1 += chr(i1)
    return a1

print(getflag(seq1))
print(getflag(seq2)) 
```

## 第七题：考察进制转换

解题报告：

第一步：一眼看上去就是16进制鸭

转成10进制，发现还不是ascii码，凯撒移位了

```
200,233,172,160,198,242,229,243,232,196,239,231,161,160,212,232,229,160,230,236,225,231,160,233,243,186,160,232,234,250,227,249,228,234,250,226,234,228,227,234,235,250,235,227,245,231,233,243,228,227,232,234,249,234,243,226,228,230,242, 
```

第二步：一口气脚本，不求人：

ascii码的范围是32到128，这题的flag格式是：`flag{xxxxx}`，坑。

```
s0="c8e9aca0c6f2e5f3e8c4efe7a1a0d4e8e5a0e6ece1e7a0e9f3baa0e8eafae3f9e4eafae2eae4e3eaebfaebe3f5e7e9f3e4e3e8eaf9eaf3e2e4e6f2"
for i in range(100,150):
    flag=""
    s = s0
    while s:
        tmp = s[:2]
        ch = chr(int(str(tmp),16)-i)
        s=s[2:]
        flag += ch
    if all(ord(c) < 127 and ord(c)>31 for c in flag):
        print(flag) 
```

## 第八题：考察图片隐写

解题报告：

第一步：解压rar文件，发现一个损坏，使用bless打开rar文件

第二步：发现隐藏的图片，修改`A8 3C 7A`为`A8 3C 74`

第三步：解压成功，开始正题

先打开图片看看是什么鬼吧，结果发现打不开，这是什么神仙图片？

查看文件类型，发现是gif：

修改后缀名为gif，发现有2帧：

第四步：使用StegSolve打开，为啥我看2张结果都长这样？喵，有大佬解释解释吗？

经过评论区大佬 [@丙乙](http://www.zhihu.com/people/1fb7c49c1223c19ca17983ee3f2e7562) 的提醒，从第二张图中分离出来上半部分。

反正就是得到2张二维码，扫一扫就拿到flag了。

## 第九题：考察base64隐写

第一步：发现要输入解压密码

说实话吧，在Kali Linux做MISC真的太浪费时间了，各种不友好

绕过ZIP伪加密的方法可以参考github<sup>[2]</sup>

```
java -jar ZipCenOp.jar r xxxxx.zip 
```

顺便说一下，用360压缩可以直接解开哦～

第二步：base64解码

解码后都是英文的：

```
Steganography is the art and science of writing hidden messages in such a way that no one, apart from the sender and intended recipient, suspects the existence of the message, a form of security through obscurity. The word steganography is of Greek origin and means "concealed writing" from the Greek words steganos meaning "covered or protected", and graphein meaning "to write". The first recorded use of the term was in 1499 by Johannes Trithemius in his Steganographia, a treatise on cryptography and steganography disguised as a book on magic. Generally, messages will appear to be something else: images, articles, shopping lists, or some other covertext and, classically, the hidden message may be in invisible ink between the visible lines of a private letter.

The advantage of steganography, over cryptography alone, is that messages do not attract attention to themselves. Plainly visible encrypted messagesóno matter how unbreakableówill arouse suspicion, and may in themselves be incriminating in countries where encryption is illegal. Therefore, whereas cryptography protects the contents of a message, steganography can be said to protect both messages and communicating parties.

Steganography includes the concealment of information within computer files. In digital steganography, electronic communications may include steganographic coding inside of a transport layer, such as a document file, image file, program or protocol. Media files are ideal for steganographic transmission because of their large size. As a simple example, a sender might start with an innocuous image file and adjust the color of every 100th pixel to correspond to a letter in the alphabet, a change so subtle that someone not specifically looking for it is unlikely to notice it.

The first recorded uses of steganography can be traced back to 440 BC when Herodotus mentions two examples of steganography in The Histories of Herodotus. Demaratus sent a warning about a forthcoming attack to Greece by writing it directly on the wooden backing of a wax tablet before applying its beeswax surface. Wax tablets were in common use then as reusable writing surfaces, sometimes used for shorthand. Another ancient example is that of Histiaeus, who shaved the head of his most trusted slave and tattooed a message on it. After his hair had grown the message was hidden. The purpose was to instigate a revolt against the Persians.

Steganography has been widely used, including in recent historical times and the present day. Possible permutations are endless and known examples include:
* Hidden messages within wax tablets: in ancient Greece, people wrote messages on the wood, then covered it with wax upon which an innocent covering message was written.
* Hidden messages on messenger's body: also used in ancient Greece. Herodotus tells the story of a message tattooed on a slave's shaved head, hidden by the growth of his hair, and exposed by shaving his head again. The message allegedly carried a warning to Greece about Persian invasion plans. This method has obvious drawbacks, such as delayed transmission while waiting for the slave's hair to grow, and the restrictions on the number and size of messages that can be encoded on one person's scalp.
* In WWII, the French Resistance sent some messages written on the backs of couriers using invisible ink.
* Hidden messages on paper written in secret inks, under other messages or on the blank parts of other messages.
* Messages written in Morse code on knitting yarn and then knitted into a piece of clothing worn by a courier.
* Messages written on the back of postage stamps.
* During and after World War II, espionage agents used photographically produced microdots to send information back and forth. Microdots were typically minute, approximately less than the size of the period produced by a typewriter. WWII microdots needed to be embedded in the paper and covered with an adhesive (such as collodion). This was reflective and thus detectable by viewing against glancing light. Alternative techniques included inserting microdots into slits cut into the edge of post cards.
* During World War II, a spy for Japan in New York City, Velvalee Dickinson, sent information to accommodation addresses in neutral South America. She was a dealer in dolls, and her letters discussed how many of this or that doll to ship. The stegotext was the doll orders, while the concealed "plaintext" was itself encoded and gave information about ship movements, etc. Her case became somewhat famous and she became known as the Doll Woman.
* Cold War counter-propaganda. In 1968, crew members of the USS Pueblo (AGER-2) intelligence ship held as prisoners by North Korea, communicated in sign language during staged photo opportunities, informing the United States they were not defectors but rather were being held captive by the North Koreans. In other photos presented to the US, crew members gave "the finger" to the unsuspecting North Koreans, in an attempt to discredit photos that showed them smiling and comfortable.

--
http://en.wikipedia.org/wiki/Steganography 
```

谷歌翻译如下：

```
隐写术是一种以隐藏方式编写隐藏消息的艺术和科学，除了发送者和预期的接收者之外，没有人怀疑消息的存在，这是一种通过隐蔽的安全方式。隐写术一词起源于希腊语，意为“隐匿书写”，源于希腊语steganos的意思是“被遮盖或保护”，而graphein则是“写”。该术语的首次记录使用是在1499年，由约翰内斯·特里米缪斯（Johannes Trithemius）在他的《密写术》中作为密码术和密写术的专着伪装成一本魔术书。通常，消息看起来像是其他东西：图像，文章，购物清单或其他封面文字，通常，隐藏的消息可能在私人字母的可见行之间以不可见的墨水显示。

相对于仅使用密码学而言，隐秘术的优势在于消息不会引起人们的注意。清晰可见的加密消息至关重要，牢不可破-会引起怀疑，并且在加密非法的国家/地区可能会成为犯罪。因此，虽然密码术保护消息的内容，但是可以说隐秘术既保护消息又保护通信方。

隐写术包括计算机文件中信息的隐藏。在数字隐写术中，电子通信可以包括诸如文件文件，图像文件，程序或协议之类的传输层内部的隐写术编码。媒体文件由于尺寸大而非常适合进行隐秘传输。举一个简单的例子，发件人可能从一个无害的图像文件开始，并调整每第100个像素的颜色以对应于字母中的一个字母，这种变化是如此微妙，以至于没有专门寻找它的人不太可能注意到它。

隐写术的首次记录使用可追溯到公元前440年，当时希罗多德斯在《希罗多德的历史》中提到了隐写术的两个例子。Demaratus通过将其直接涂在蜡片的木制背衬上，然后涂上蜂蜡表面，向希腊发出了即将袭击的警告。蜡片通常用作可重复使用的书写表面，有时用作速记。另一个古老的例子是希斯蒂厄斯（Histiaeus），他剃了他最受信任的奴隶的头，并在上面刻了一条信息。他的头发长了以后，信息被隐藏了。目的是煽动对波斯人的反抗。

隐写术已被广泛使用，包括最近的历史时期和今天。可能的排列是无止境的，已知的示例包括：
*蜡片中的隐藏信息：在古希腊，人们在木头上写下信息，然后用蜡覆盖它，在上面写上了无辜的覆盖信息。
*在信使身上隐藏的信息：在古希腊也使用。希罗多德斯讲述了一个信息的故事，该信息在一个奴隶的剃光头上刺青，被头发的生长所掩盖，并再次被剃光头而暴露出来。据称该消息向希腊发出了有关波斯入侵计划的警告。这种方法有明显的缺点，例如在等待奴隶的头发长大时传输延迟，以及对可以在一个人的头皮上编码的消息的数量和大小的限制。
*在第二次世界大战中，法国抵抗军使用隐形墨水发送了一些信使在信使背面写的信息。
*隐藏在用秘密墨水写在纸上，其他消息下方或其他消息空白部分上的消息。
*用摩尔斯电码写的信息，用在针织纱上，然后编织成快递员穿的一件衣服。
*邮件写在邮票背面。
*在第二次世界大战期间和之后，间谍活动人员使用照相产生的微点来回发送信息。微点通常很小，大约小于打字机产生的周期的大小。第二次世界大战的微点需要嵌入纸中，并用粘合剂（例如胶棉）覆盖。这是反射性的，因此通过观察掠射光可以检测到。替代技术包括将微点插入到明信片边缘的狭缝中。
*第二次世界大战期间，在纽约市的日本间谍Velvalee Dickinson向南美洲中立的住所地址发送了信息。她是玩偶的经销商，她的来信中讨论了要运送多少个这种玩偶。隐身文字是洋娃娃的命令，而隐藏的“纯文字”本身是经过编码的，并提供了有关船只运动的信息，等等。她的案子有些出名，被称为“娃娃女人”。
*冷战反宣传。1968年，被朝鲜俘虏的普韦布洛（AGER-2）情报船的船员在上演拍照的机会中以手语进行了交流，告知美国他们不是叛逃者，而是被朝鲜人俘虏。在向美国展示的其他照片中，机组人员用手指“指”了毫无戒心的朝鲜人，以抹黑显示他们微笑和舒适的照片。

-
http://en.wikipedia.org/wiki/隐身术 
```

第三步：搞不到了，有大佬提示一下吗？

待续...

```
flag{Base_sixty_four_point_five} 
```

## 第十题：考察文件挂载

解题报告：

发现文件系统里面有一个疑似flag的信息：

挂载文件系统，查找flag文件，base64解码得到flag：

```
mount f1fc23f5c743425d9e0073887c846d23 /mnt/ 
```

## 第十一题：考察莫斯密码

解题报告：

第一步：在chrome浏览器的Console一顿操作：

得到如下信息：

```
NoFlagHere! NoFlagHere! NoFlagHere!
XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
Close - but still not here !
BABA BBB BA BBA ABA AB B AAB ABAA AB B AA BBB BA AAA BBAABB AABA ABAA AB BBA BBBAAA ABBBB BA AAAB ABBBB AAAAA ABBBB BAAA ABAA AAABB BB AAABB AAAAA AAAAA AAAAB BBA AAABB
Lorem ipsum dolor sit amet,  consectetur adipiscing elit.  Cras faucibus odio ut metus vulputate,  id laoreet magnavolutpat.  Integer nec enim vel arcu porttitor egestas.  
Vestibulum suscipit lorem sed sem faucibus rutrum.  Nunc diamorci,  convallis  vitae  auctor  vehicula,  interdum  ut  mi.   Maecenas  nec  urna  at  dolor  mattis  dictum  sit  amet  at  orci.
Mauris condimentum adipiscing erat nec feugiat.  Curabitur scelerisque varius ligula,  iaculis adipiscing dui.  Duis egetullamcorper arcu.  In facilisis et tortor commodo aliquam.  
Nulla feugiat, sem eu molestie bibendum, leo nisi porttitormassa, id accumsan sapien libero id tellus.  In enim lacus, sollicitudin a felis quis, blandit porta ipsum.  Donec sed nibhegestas, tristique mauris eu, rutrum justo.  
Nulla facilisi.  Duis gravida semper dui laoreet vulputate.  Aenean quis tempororci.   Cras  placerat  lectus  nulla,  eu  bibendum  metus  interdum  in.Lorem  ipsum  dolor  sit  amet,  consectetur  adipiscingelit.   
Cras  faucibus  odio  ut  metus  vulputate 
```

第二步：这不就是嘀嗒嘀嗒，莫斯密码：

`BABA BBB BA BBA ABA AB B AAB ABAA AB B AA BBB BA AAA BBAABB AABA ABAA AB BBA BBBAAA ABBBB BA AAAB ABBBB AAAAA ABBBB BAAA ABAA AAABB BB AAABB AAAAA AAAAA AAAAB BBA AAABB`

一口气脚本，不求人：

```
dict1 = {'A': '.',
        'B': '-',
        ' ': '/'
        };

dict2 = {'.-': 'a',
        '-...': 'b',
        '-.-.': 'c',
        '-..':'d',
        '.':'e',
        '..-.':'f',
        '--.': 'g',
        '....': 'h',
        '..': 'i',
        '.---':'j',
        '-.-': 'k',
        '.-..': 'l',
        '--': 'm',
        '-.': 'n',
        '---': 'o',
        '.--.': 'p',
        '--.-': 'q',
        '.-.': 'r',
        '...': 's',
        '-': 't',
        '..-': 'u',
        '...-': 'v',
        '.--': 'w',
        '-..-': 'x',
        '-.--': 'y',
        '--..': 'z',
        '.----': '1',
        '..---': '2',
        '...--': '3',
        '....-': '4',
        '.....': '5',
        '-....': '6',
        '--...': '7',
        '---..': '8',
        '----.': '9',
        '-----': '0',
        '--..--': ',',
        '---...': ':',
        '/': ' '
        };

enc_str0="BABA BBB BA BBA ABA AB B AAB ABAA AB B AA BBB BA AAA BBAABB AABA ABAA AB BBA BBBAAA ABBBB BA AAAB ABBBB AAAAA ABBBB BAAA ABAA AAABB BB AAABB AAAAA AAAAA AAAAB BBA AAABB"
enc_str1=""
dec_str=""
for i in enc_str0:
  enc_str1+=dict1[i]
enc_str1=enc_str1.split("/")
print(enc_str1)
for i in enc_str1:
  dec_str += dict2[i]
print(dec_str) 
```

## 第十二题：考察文件隐写

解题报告：

第一步：查看pacp包中的隐藏文件

```
binwalk acfff53ce3fa4e2bbe8654284dfc18e1.pcapng 
```

第二步：分离出隐藏文件

```
foremost acfff53ce3fa4e2bbe8654284dfc18e1.pcapng 
```

第三步：获得一个加密的ZIP文件

第四步：Wireshark流量分析

解压密码就藏在：6666.jpg

第五步：追踪HTTP流

从这开始：

到这结束：

第六步：新建hex文件<sup>[3]</sup>

拿到解码密码：`Th1s_1s_p4sswd_!!!`

第七步：拿着密码解压得到flag