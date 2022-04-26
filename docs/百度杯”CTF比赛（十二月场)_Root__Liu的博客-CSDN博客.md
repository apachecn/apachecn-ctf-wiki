<!--yml
category: 未分类
date: 2022-04-26 14:44:39
-->

# 百度杯”CTF比赛（十二月场)_Root__Liu的博客-CSDN博客

> 来源：[https://blog.csdn.net/Root__Liu/article/details/70161498](https://blog.csdn.net/Root__Liu/article/details/70161498)

"百度杯"CTF比赛（十二月场）

第一场

1、传说中的签到题

![](img/2f565c031c5e6a63b5de0a5e4539ceb7.png) 

解决：把二进制用在线进制转换器转换，发现转为10进制时，和tips2一样，在qq上一查，是CTF官方群，在公告里有一个字符串ZmxhZ3tiMTU5MDI4Yy05NWZmLTRmNzEtYWQ3Yi1jZWY1MTBhMjJkMDB9 

用base64解码，得到flag，

flag{b159028c-95ff-4f71-ad7b-cef510a22d00}

![](img/5f3002ea2c26b87efe6194cc94a8ece1.png) 

2、福尔摩斯，题目如下： 

![](img/b716defe71a2febd0a86b91f361ebb2f.png) 

解决：网上找福尔摩斯解码器，或者自己写一个福尔摩斯解码程序进行解码，我写的程序解出来是rrrrrre,提交出错，把字母改成大写通过。结果如下： 

![](img/7d50b2fb589ab6e608c167761667cbdd.png) 

3、+--+

![](img/e10bc14d62524cea7501d9114ab288bb.png) 

解决：网上一查，说这是什么brainfuck语言，应该是加解密，找了一下在线解密工具，果然解出flag，工具网址https://www.splitbrain.org/services/ook 

![](img/a6ab3f3a14ecd1cbdd9a6a6fe7cfe8d2.png) 

第二场

1、一个十六岁的少年

![](img/6141f12d98340323a1c32a737008dd42.png) 

解决:抓住敏感字十六，用base16解码得到结果 

flag{ec8b2ee0-3ae9-4c21-a012-08aa5fa7be67}

![](img/7f835e5cdf3f8f20b588ee0e65dab80d.png) 

2、藏在邮件头里的密码

![](img/0980aa4c25267c1a27f89e8d4f9450db.png) 

解决：

![](img/16e1ab6c1ee8f8dc7242db93f5dc825f.png) 

![](img/4924cdcfdf57527dc3ae988bdf9a8741.png) 

3、吃货

![](img/d77afe874bda09cbbb8a2c657331873f.png) 

解决：根据题目，估计flag里是某种加密的密文，百度查找了发现有ab的密文很可能是培根加密，对照培根加密表，得到结果

![](img/4fabf34af13b9e5f2ce1cc5a90c23707.png) 

第三场

1、misc1

![](img/d0d6ac18ced0774ef70cc0a02e1837d4.png) 

解决：图片题应该是隐写，用隐写破解工具winhex打开，直接在最底部查看flag

![](img/efde2acc2086467c91f16a1fe169cb0d.png) 

2、misc2

![](img/1f87c12cb1e533e0883f61383ad2c030.png) 

解决：看图片很明显是猪圈密码，对照猪圈密码表解码得到flag是NSN 

![](img/3eef17aca534f5ae145db8e54291696b.png) 

3、misc3

解决：在键盘上分清行和列，按照坐标对照

![](img/7f9f98d6444b5de2fe1406457971be2b.png) 

第四场

1、misc1-纵横四海

![](img/4fdd6ff37a80e04f4f1e348c4390e30d.png) 

解决：打开附件，解压，好多文件，打开每个文件里只有一个字母，把他们写进一个文件，去掉换行，得到flag{0a47061d-0619-4932-abcd-5426f4ea34aa}![](img/6c58df474471bed2765b38186de7ce65.png) 

2、misc2-对错

![](img/e4cf78e8e58302a11e33b7f5886f0393.png) 

解决：打开调到一个页面，是一串二进制数，把他转换为ASCII码就得到flag{zhEc9034jodsjfosko} 

![](img/c3a1e8788ea7cb2d3fb24057b9de2611.png) 

3、misc3-枯竭

![](img/249fe9d216588fc47a3b4cd23bd0de5b.png) 

解决：附件打开时有密码，用zip password tool工具破解，得到密码是12345，解压后打开文件得到flag{319b7f63-e17d-4ac5-8428-c2476c7ecce3} 

![](img/a5127e60bcaeaff24b2544948cd1e0c9.png)