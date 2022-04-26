<!--yml
category: 未分类
date: 2022-04-26 14:36:40
-->

# BUUCTF-Crypto-看我回旋踢题解_ASSOINT的博客-CSDN博客_看我回旋踢

> 来源：[https://blog.csdn.net/weixin_47869330/article/details/110940053](https://blog.csdn.net/weixin_47869330/article/details/110940053)

## ROT13编码

> ROT13（回转13位，rotate by 13 places，有时中间加了个连字符称作ROT-13）是一种简易的替换式密码。
> 
> ROT13被描述成“杂志字谜上下颠倒解答的Usenet点对点体”。ROT13 也是过去在古罗马开发的凯撒加密的一种变体。

## 特点：

> 套用ROT13到一段文字上仅仅只需要检查字元字母顺序并取代它在13位之后的对应字母， 有需要超过时则重新绕回26英文字母开头即可。

```
A换成N、B换成O、依此类推到M换成Z，然后序列反转：N换成A、O换成B、最后Z换成M。 
```

只有这些出现在英文字母里头的字元受影响；
数字、符号、空白字元以及所有其他字元都不变。

`ROT13函数是它自己的逆反`

```
ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz
NOPQRSTUVWXYZABCDEFGHIJKLMnopqrstuvwxyzabcdefghijklm 
```

## 解题：

看到题目我傻眼了一会儿，这嘛玩意儿？？
自己实在是太菜了

题目：`synt{5pq1004q-86n5-46q8-o720-oro5on0417r1}`

1.  直接百度可疑字符：synt编码
2.  看到搜索栏里跳出rot13
3.  在线解密（贼好用）：`http://www.rot13.de/index.php`
4.  得到flag：`flag{5cd1004d-86a5-46d8-b720-beb5ba0417e1}`

## （不知道靠不靠谱的）小结：

1.  遇到synt{xx-xx-xx} 判定他为rot13；
2.  题目一般都是提示信息，“回旋踢”就暗示了rot13的特点，会重新绕回；

好耶！