<!--yml
category: 未分类
date: 2022-04-26 14:20:34
-->

# CTF中常见密码题解密网站总结_greedy-hat的博客-CSDN博客_当铺密码在线解密

> 来源：[https://blog.csdn.net/qq_41638851/article/details/100526839](https://blog.csdn.net/qq_41638851/article/details/100526839)

### 0x00.综合

网站中包含大多编码的解码。
http://web2hack.org/xssee/
https://www.sojson.com/
http://web.chacuo.net/

### 0x01.文字倒序排列

http://www.qqxiuzi.cn/zh/daoxu/

### 0x02.cmd5解密

http://www.cmd5.com/
http://pmd5.com/
http://tool.chinaz.com/Tools/MD5.aspx
http://md5jiami.51240.com/

### 0x03.

#### ①base64解密

http://tool.chinaz.com/Tools/Base64.aspx
http://www1.tc711.com/tool/BASE64.htm
http://base64.supfree.net/
http://web.chacuo.net/charsetbase64

#### ②base32解密

http://tomeko.net/online_tools/base32.php?lang=en

### 0x04.ROT5、ROT13、ROT18、ROT47位移编码

http://www.qqxiuzi.cn/bianma/ROT5-13-18-47.php

### 0x05.颜文字（aadecode）

打开谷歌浏览器–>F12–>点击Console–>粘贴复制回车就会出现答案
https://cat-in-136.github.io/2010/12/aadecode-decode-encoded-as-aaencode.html
http://utf-8.jp/public/aaencode.html

### 0x06.邮件编码（UUencode：M=75E;F-O9&7HOYGDN*KED）

http://web.chacuo.net/charsetuuencode
http://www.mxcz.net/tools/UUEncode.aspx

### 0x07.查尔斯加密（playfair解密）

http://rumkin.com/tools/cipher/playfair.php

### 0x08.在线编码解码

http://bianma.911cha.com/

### 0x09.进制间转换

http://tool.oschina.net/hexconvert/

### 0x010.16进与文本转换

http://www.5ixuexiwang.com/str/from-hex.php
http://www.5ixuexiwang.com/str/hex.php

### 0x011.Unicode编码转换（4种）

&#x [Hex]： The
&# [Decimal]： The
\U [Hex]： \U0054\U0068\U0065
\U+ [Hex]： \U+0054\U+0068\U+0065
http://tool.oschina.net/encode?type=3 （\u5927）
http://tool.chinaz.com/tools/unicode.aspx （\u5927）
http://tool.oschina.net/encode （&#23433）

### 0xc12.Brainfuck（++++++++++++[>++++>）

https://www.splitbrain.org/services/ook （Ook!与Brainfuck）
https://www.nayuki.io/page/brainfuck-interpreter-javascript
http://esoteric.sange.fi/brainfuck/impl/interp/i.html

### 0x013.URL（%23）

http://tool.chinaz.com/tools/urlencode.aspx
http://web.chacuo.net/charseturlencode
http://tool.oschina.net/encode?type=4

### 0x014.二维码生成/解码器

http://tool.chinaz.com/qrcode/
http://jiema.wwei.cn/

### 0x015.摩斯密码

http://www.zou114.com/mesm/
http://www.jb51.net/tools/morse.htm
http://rumkin.com/tools/cipher/morse.php

### 0x016.quipqiup

https://www.xarg.org/tools/caesar-cipher/

### 0x017.与佛论禅

http://www.keyfc.net/bbs/tools/tudoucode.aspx

### 0x018.xxencode(Ri64NjS0-eRKpkQm-jRaJm6)

http://web.chacuo.net/charsetxxencode

### 0x019.jsfuck（[][(![]+[])[+[]）

注：用  ! +加密
打开谷歌浏览器–>F12–>点击Console–>粘贴复制回车就会出现答案
http://discogscounter.getfreehosting.co.uk/js-noalnum.php?ckattempt=1&i=1（放在下面的大框里，点击eval(–v)）

### 0x020.UTF-8编码(&#x5B89)

http://tool.chinaz.com/Tools/UTF-8.aspx
http://tool.oschina.net/encode?type=2

http://www.3464.com/Tools/HtmlToOther/

### 0x021.DES(要密匙)

http://encode.chahuo.com/
http://tool.oschina.net/encrypt/

### 0x022.凯撒

http://www.zjslove.com/3.decode/kaisa/index.html
http://planetcalc.com/1434/

### 0x023.Rabbit（兔子）解密

注：此编码与和base64很相似
http://tool.chinaz.com/Tools/TextEncrypt.aspx
http://tool.oschina.net/encrypt/

### 0x024.AES

部分例子：U2FsdGVkX1+qtU8KEGmMJwGgKcPUK3XBTdM+KhNRLHSCQL2nSXaW8++yBUkSylRp
http://tool.oschina.net/encrypt/
http://aes.online-domain-tools.com/

### 0x025.JS解密

http://www.haokuwang.com/jsendecode.htm
http://www.dheart.net/decode/index.php

### 0x026.ASP解密

部分例子：#@<sub>^EQAAAA==VXlj4UmkaYAUmKN3bAYAAA==^#</sub>@
http://adophper.com/encode.html

### 0x027.Snake解密（要密匙）

http://serpent.online-domain-tools.com/

### 0x028.GRB与十六进制的转换

http://www.sioe.cn/yingyong/yanse-rgb-16/
http://www.atool.org/colorpicker.php

### 0x029.html隐写在线解密

注：密匙和网址
http://fog.misty.com/perry/ccs/snow/snow/snow.html

### 0x030.shellcode编码（\x54\x68\x65\x7f）

http://www.jb51.net/article/10399.htm

### 0x031.Quoted-printable编码

我们收邮件，查看信件原始信息，经常会看到这种类型的编码！（=E6=95=8F=E6=8D=B7=E7=9A）
function quoted_printable_encode($string) {
return preg_replace(‘/[<sup>\r\n]{73}[</sup>=\r\n]{2}/‘, "KaTeX parse error: Can't use function '\r' in math mode at position 3: 0=\̲r̲\n", str_replac…string)));
}

### 0x032.Escape/Unescape编码（%u0054%u0068%u0065）

https://escape.supfree.net/

### 0x032.敲击码(Tap code)

1 2 3 4 5
1 A B C/K D E
2 F G H I J
3 L M N O P
4 Q R S T U
5 V W X Y Z

### 0x033.加密为汉字

http://www.qqxiuzi.cn/bianma/wenbenjiami.php

### 0x034.栅栏

http://www.practicalcryptography.com/ciphers/classical-era/rail-fence/
http://www.atoolbox.net/Tool.php?Id=777

### 0x035.曲路密码

需要事先双方约定密钥(也就是曲路路径)
![技术分享图片](img/bcd917a7c3ca896411188d0968c9bc64.png)

### 0x036.列移位密码

![技术分享图片](img/382c50dc0ea33c406bda2fe1c127bcfa.png)
密钥： how are u
密文： qoury inpho Tkool hbxva uwmtd cfseg erjez

### 0x037.埃特巴什码

ABCDEFGHIJKLMNOPQRSTUVWXYZ ZYXWVUTSRQPONMLKJIHGFEDCBA
http://www.practicalcryptography.com/ciphers/classical-era/atbash-cipher/

### 0x038.希尔密码（利用矩阵）

http://www.practicalcryptography.com/ciphers/hill-cipher/

### 0x039.夏多密码(曲折加密)

技术分享图片

### 0x040.编制密码表

注：如果出现"Z"，则需要去除，因为在英文里"Z"的使用频率最低，相应的如果是德文，则需将"I"与"J"当作一个字母来看待，而法语则去掉"W"或"K"。
整理密钥字母 C U L T U R E ，去掉后面重复的字母得到： C U L T R E
![技术分享图片](img/882c2cf0559e380140e3257d9a9ca7fa.png)
http://www.practicalcryptography.com/ciphers/classical-era/playfair/

### 0x041.维吉尼亚密码

http://planetcalc.com/2468/（已知密匙）
http://rumkin.com/tools/cipher/gronsfeld.php（未知密匙）
http://www.zjslove.com/3.decode/weijiniya/index.html

### 0x042.自动密钥密码

http://www.practicalcryptography.com/ciphers/classical-era/autokey/

### 0x043.博福特密码

http://www.practicalcryptography.com/ciphers/classical-era/beaufort/

### 0x044.滚动密钥密码

http://www.practicalcryptography.com/ciphers/classical-era/running-key/

### 0x045.Porta密码

http://www.practicalcryptography.com/ciphers/classical-era/porta/

### 0x046.仿射密码

以E(x) = (5x + 8) mod 26加密，通过计算可得D(x)=21(x - 8) mod 26，这样便可以得到明文。
http://www.practicalcryptography.com/ciphers/classical-era/affine/

### 0x047.培根密码

http://rumkin.com/tools/cipher/baconian.php
https://netair.xyz/tools/培根密码加密解密.html

### 0x048.ADFGX密码

http://www.practicalcryptography.com/ciphers/adfgx-cipher/

### 0x049.双密码

明文: THE QUICK BROWN FOX=>5121542133 5435452521 3523311521 34 =>密文: WETED TKZNE KYOME X
http://www.practicalcryptography.com/ciphers/classical-era/bifid/

### 0x050.ppencode

把Perl代码转换成只有英文字母的字符串
http://namazu.org/~takesako/ppencode/demo.html

### 0x051.rrencode(把ruby代码全部转换成符号)

### 0x052.jother

可以在浏览器(IE可以)的控制台里输入密文即可执行解密（用! + ( ) [ ] { }加密）
http://tmxk.org/jother/（编码）

### 0x053.CR4解密

http://tool.oschina.net/encrypt
http://tool.chinaz.com/Tools/TextEncrypt.aspx

### 0x054.VBScript

部分例子：#@<sub>^TgAAAA==‘[6*liLa6++p‘aXvfiLaa6i[[avWi[[a*p[[6*!I‘[6cp‘aXvXILa6fp[:6+Wp[:XvWi[[6+XivRIAAA==^#</sub>@
http://www.dheart.net/decode/index.php

### 0x055.执行php文件

https://www.shucunwang.com/RunCode/php/

### 0x056.图片/BASE64转换

http://tool.oschina.net/encrypt?type=4

### 0x057.QWE解密

![在这里插入图片描述](img/838bd58f7726e8e2bae9f802d91a929a.png)

### 0x058.当铺密码

![在这里插入图片描述](img/c2c46ea315a1f91a437603b6e106d318.png)

### 0x059.RSA算法

http://www.factordb.com/index.php

### 0x060.词频分析

https://www.quipqiup.com

### 0x061.加盐密码算法

https://blog.csdn.net/JBlock/article/details/78446604

### 0x062.play fair密码

http://rumkin.com/tools/cipher/playfair.php
https://www.cnblogs.com/xdjun/p/7469841.html
![在这里插入图片描述](img/5f37ad52b5e0b4204ddd013acf54425f.png)
![在这里插入图片描述](img/a4c251133664c83b36543b1ad36b6c27.png)