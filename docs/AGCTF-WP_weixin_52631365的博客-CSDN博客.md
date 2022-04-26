<!--yml
category: 未分类
date: 2022-04-26 14:44:12
-->

# AGCTF WP_weixin_52631365的博客-CSDN博客

> 来源：[https://blog.csdn.net/weixin_52631365/article/details/111036102](https://blog.csdn.net/weixin_52631365/article/details/111036102)

## 密码题

做密码题首先先用百度搜索有关ctf各种密码总结的博客，然后再大体浏览一下都有什么密码以及这些密码都是什么样的形式
推荐博客网址：https://blog.csdn.net/qq_40837276/article/details/83080460?utm_medium=distribute.pc_relevant.none-task-blog-title-11&spm=1001.2101.3001.4242
**1.贝斯**
QUdDVEZ7ZG9feTB1X2tuMHdfYmFzZTY0fQ==
题解：贝斯贝斯谐音base，看博客发现有base64密码，接着百度base64在线解码工具，使用在线工具解码得到flag
AGCTF{do_y0u_kn0w_base64}
**2.你喜欢吃培根吗**
AAABBABBBABBAAAABBBABABAAABABBABAAAABABAAABAAAABAAAAAAABAABBAAAABAAAAAAAABAABBBAABBAB
题解：看博客，然后直接百度培根密码在线解码工具，经解码得到flag doyoulikeeatbacon
提交AGCTF{doyoulikeeatbacon}
**3.进制转换**
41474354467b6865785f31735f766572795f6330306c7d
题解：进制转换有很多种，关于这道题可以联想到进制转换成字符串，至于是几进制转换成字符串，这一串数最大的是15，猜测是16进制转成字符串，然后百度搜索16进制转换字符串在线工具，转换得到flag
AGCTF{hex_1s_very_c00l}
**4.麻辣兔头**
U2FsdGVkX1+JcJhoCLNntvv0cBevTgz2Q3Qb2ngik7kBQyMohLem3B1UhU6KfdXX
KbTdoRzYMFFstyvkfJE=
题解：兔子的英文是rabbit，然后百度发现有rabbit密码，接着搜索rabbit密码在线解码工具，利用工具得到flag
AGCTF{兔兔那么可爱为什么要吃兔兔}
**5.麻辣兔头2.0**
听说栅栏里有4只兔兔？
AFb_tfeG{bihe}CRinenTat__c
题解：由第四题很容易能想到rabbit密码，但是继续用rabbit解密发现解不出来，继续看题，发现有栅栏二字，联想到栅栏密码，然后百度利用工具解码，解码时发现有一个组数，题中提到有四只兔兔，将组数改为四解码得到flag
AGCTF{Rabbit_in_the_fence}
**6.Veni Vidi Vici**
怎么这么多V啊！
DJFWI{f4ph_vdz_f0qtxhuhg}
题解：这一题乍一看没什么思路，就直接百度Veni Vidi Vici ,
发现百度出来个拉丁文，凯撒著名的三“v”，直接百度搜索凯撒密码解码工具，解码得到flag
AGCTF{c4me_saw_c0nquered}
**7.Vigenere**
这次只有一个V
VBXOA{oczt_vmz_vgg_ncdao_xdkczm}
题解：直接百度Vigenere ，发现是维吉尼亚密码，搜索在线解码工具，利用工具得到flag
**8.Do you know js well？**
题解：这一题根据题目就知道和js有关，直接百度js发现有js密码，然后按F12键，点控制台把题中的密码直接复制过去按回车键就直接得到flag
注：按F12可以解js密码
**9.Are you Ook?**
题解：看之前的密码总结博客我们发现有一个Ook密码，然后直接百度Ook在线解码工具，利用工具解码得到flag
**10.佛曰**
题解：看题知道这一题应该是与佛有点关系，然后继续看密码总结的博客，发现有一个与佛论坛，那大概就是这个密码了，我们可以试一下，百度相关的解码工具，利用工具解码得到flag
**11.rotate by 13 places！**
题解：看到这道题发现没有什么显眼的线索，但是题目的这句话是什么意思呢，我们百度翻译一下，它的意思是旋转13个位置，仔细看博客发现这符合rot13这个密码，然后百度相关解码工具，利用工具得到flag
**12.Morse**
题解：这一题最简单，很明显是著名的摩斯密码，直接百度相关解码工具，利用工具得到flag
**13.猪圈里的奇怪字符**
题解：打开这一题发现都是一些奇怪字符，看密码总结的博客发现符合猪圈密码，直接百度相关解码工具，利用工具得到flag

## Web题

**web1**

<?php error_reporting(0); include 'flag.php';//这句话是说有一个php文件，名叫flag highlight_file(__FILE__); $c=$_POST['c'];//这句话是用post传参 eval("$c"); ?>

题解：首先我们需要在火狐浏览器上面下载一个hackbar插件，利用这个小插件传参得到flag,打开hackbar,点击post data,然后输入c=echo($flag);点击运行即可得到flag
注：在做着道题时可以百度一下post传参的相关知识,掌握php文件的基础知识
**web 2**

<?php include 'flag.php';//有一个php文件名叫flag highlight_file(__FILE__); if(isset($_GET['num'])){ $num = $_GET['num']; if(preg_match("/[a-z]/i", $num)){ echo("no no no!"); } else{ echo($flag); }//如果num=a到z中的任意一个就输出no no no!,如果不是就输出flag文件的内容 } ?>

题解：直接在网址那一行加入“/?num=1",然后访问这个网址即可得到flag
注：这一题做之前同样需要了解php文件的相关知识，掌握get请求的相关用法
**web 3**
You are not one of us，please use F0tsec Browser.
题解：打开这一题，就只看到了之一句话，使用百度翻译得知这句话的意思是“您不是我们中的一员，请使用F0tsec浏览器。”这时我们需要利用抓包工具进行抓包，抓到包之后把有关浏览器的那一行改成 F0tsec Browser，然后放包，又得到一句话“You are not from 192.168.0.1，这句话在说我们要想得到flag,ip地址得是192.168.0.1,这时我们就需要再次进行抓包然后进行改包，把ip地址改为192.168.0.1，只需在抓到的包中加入一行“Referer：192.168.0.1”即可，然后放包得到flag

## 逆向题

这几道逆向题一般都是直接拖到ida里面查看源代码，ida分为32位和64位，在做题时我们也不知道它是多少位的，但是我们可以一个一个试试，32位的打不开就用64位的，Shift+f12是查看函数，X是交叉引用，f5是查看其伪c语言代码
**1.no.1**
题解：这一题是将文件拖到32位的ida里面，这一题比较简单，一眼就能看出flag是AGCTF{513NB}
**2.字符串**
题解：这一题也是将文件拖到32位的ida里面，然后按Shift+f12，直接找到flag是AGCTF{Shift_F12}
**3.亦或者呢个这个**
题解：这一题是将文件直接拖到64位的ida里面，然后Shift+f12查看函数，X交叉引用，f5查看其伪c语言代码,

v9 = 0;
strcpy(v8, “KaTeX parse error: Undefined control sequence: \n at position 118: …ssword guesser.\̲n̲", a2, a3); p…%.54>!6=*.=%7&6+”};
char s[30];
for(i=0; i < 30; ++i)
{
if(i< 18)
s[i]= (v7[i%v6])^v8[i];
}
printf("%s",s);
return 0;
}
运行就能得到flag了
注：在看伪代码时，遇到不知道的函数就直接百度
**4.一样的flag**
题解：首先将文件拖到32位的ida中，重复上述操作查看其伪c代码，发现这是一道迷宫题，其迷宫如下
*1111
01000
01010
00010
1111#
这个迷宫要走到#，
puts(“1 up”);
puts(“2 down”);
puts(“3 left”);
printf(“4 right\n:”);//看着几行代码知道上是1，下是2，左是3，右是4，一步一步得记录下来就是flag