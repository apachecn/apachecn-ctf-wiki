<!--yml
category: 未分类
date: 2022-04-26 14:34:32
-->

# BUUCTF-Crypto-一眼就解密题解_ASSOINT的博客-CSDN博客_buuctf 一眼就解密

> 来源：[https://blog.csdn.net/weixin_47869330/article/details/110939730](https://blog.csdn.net/weixin_47869330/article/details/110939730)

## 一眼就解密-----base64

### base家族特点：

```
 1. base16：包含数字0-9，大写字母A-F
 2. base32：包含数字2-7，大写字母A-Z,特殊符号===
 3. base64：包含数字0-9，大写字母A-Z，小写字母a-z，特殊符号+、/ 、==、=等 
```

### base32与base64区别：

1.  base32没有小写字母，注意base32与base64都有＝号
2.  base32编码可以用于文件系统的名称（不区分大小写情况）。而base64编码后数据量相比原先不是增加很多，可以用于网络传输。

### 解题：

看到题目 啧 老熟人了

**①看到“=”就想到base家族里的base32编码与base64编码。
②看到小写字母，确定是base64编码。
③base64编码在线解密：**

```
https://www.sojson.com/base64.html 
```

④得到flag：`flag{THE_FLAG_OF_THIS_STRING}`

好耶！