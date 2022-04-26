<!--yml
category: 未分类
date: 2022-04-26 14:46:59
-->

# BUUCTF_MISC——[MRCTF2020]ezmisc、荷兰宽带数据泄露、[BJDCTF 2nd]最简单的misc-y1ng_Ho1aAs的博客-CSDN博客

> 来源：[https://blog.csdn.net/Xxy605/article/details/108937651](https://blog.csdn.net/Xxy605/article/details/108937651)

# 一、[MRCTF2020]ezmisc

## Ⅰ、工具

1.  TweakPNG
2.  Winhex

## Ⅱ、题解

使用TweakPNG查看图片，出现chunk报错，即图片尺寸被修改过

![在这里插入图片描述](img/a3717aa08a0b64e1a90c6282b2dd9561.png)
图片为500x319，怀疑该图片高度遭到篡改

![在这里插入图片描述](img/7fa3b2dbae0654daa8bd93622f6e9619.png)
使用Winhex打开，红框部分为PNG图片的尺寸数据，为十六进制

将后一个改成1F4（16），即500（10）

![在这里插入图片描述](img/6375414016fa2ec571c08763f2076d98.png)
保存即可发现隐藏的部分有FLAG

![在这里插入图片描述](img/7320b3d8cf020ec093892c89bca80484.png)

# 二、荷兰宽带数据泄露

## Ⅰ、工具

1.  RouterPassView

## Ⅱ、题解

文件是`conf.bin`，是宽带数据文件，需要用RouterPassView打开

没有给出明确的提示FLAG，那么应该是账户名或者是密码
![在这里插入图片描述](img/8319c6bf05a1235c860af70e4aa11ec9.png)
多次尝试发现是用户名

> **flag{053700357621}**

# 三、[BJDCTF 2nd]最简单的misc-y1ng

## Ⅰ、工具

1.  ZipCenOp
2.  Winhex

## Ⅱ、题解

题目是一个加密的压缩包，没有任何提示密码，考虑是伪加密

使用ZipCenOp破解伪加密

在cmd运行以下代码：

```
java ZipCenOp.jar r secret.zip 
```

![在这里插入图片描述](img/6ea9d5b2dc597e4dd7909048c279d121.png)
成功解压

![在这里插入图片描述](img/ed974950c86fdddb85f1406e0475f323.png)
使用Winhex查阅文件数据，找到`IHDR`串，表明这是一个**缺失文件头的PNG文件**

在第一字节前面插入四个字节0数据，准备补全文件头

![在这里插入图片描述](img/75e997bef741fb32c0950fa7cfd66366.png)
加入PNG的文件头`89504E47`，保存

![在这里插入图片描述](img/e3e929ede09a998d3fe499861fc2f3ee.png)
给文件加上后缀`.png`，发现一个十六进制字符串`424A447B79316E677A756973687561697D`

![在这里插入图片描述](img/e13d87d26be10867a678ed6a517f5b16.png)
![在这里插入图片描述](img/de6f4e50aef3136c66efb28633b2854f.png)
使用在线转换工具，得到FLAG明文
![在这里插入图片描述](img/7049c4187b3c2bed939bafe8d56fde14.png)

> **BJD{y1ngzuishuai}**

# 完

> 欢迎在评论区留言
> 感谢浏览