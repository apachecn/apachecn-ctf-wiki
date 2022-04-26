<!--yml
category: 未分类
date: 2022-04-26 14:48:16
-->

# BUUCTF-Crypto-Quoted-printable题解_ASSOINT的博客-CSDN博客

> 来源：[https://blog.csdn.net/weixin_47869330/article/details/110977056](https://blog.csdn.net/weixin_47869330/article/details/110977056)

## Quoted-printable编码：

> Quoted-printable可译为“可打印字符引用编码”，编码常用在电子邮件中，如：Content-Transfer-Encoding: quoted-printable ，它是MIME编码常见一种表示方法。
> 在邮件里面我们常需要用可打印的ASCII字符 (如字母、数字与"=")表示各种编码格式下的字符。

> **Quoted-printable将任何8-bit字节值可编码为3个字符：一个等号"=“后跟随两个十六进制数字(0–9或A–F)表示该字节的数值。例如，ASCII码换页符（十进制值为12）可以表示为”=0C"，**

> 等号"="（十进制值为61）必须表示为"=3D"，gb2312下“中”表示为=D6=D0。除了可打印ASCII字符与换行符以外，所有字符必须表示为这种格式。

### 特征：

**‘=’后跟随俩十六进制数字（0-9或A-F）**

## 题解：

1.  看到题目知道是Quoted-printable编码
2.  在线解密：

> http://www.mxcz.net/tools/QuotedPrintable.aspx

3.  得到flag：`flag{那你也很棒哦}`

## 小结：

其实这个题目没啥好小结的，就是一个在线解密而已，但是有一个点提醒自己的就是：

```
flag可以是中文 
```

*当解密结果出来的一瞬间震惊我一脸
生草 我还以为自己解密错误了*

好耶！