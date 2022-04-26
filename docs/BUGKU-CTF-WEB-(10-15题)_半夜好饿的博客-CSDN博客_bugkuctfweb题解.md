<!--yml
category: 未分类
date: 2022-04-26 14:20:06
-->

# BUGKU CTF WEB (10-15题)_半夜好饿的博客-CSDN博客_bugkuctfweb题解

> 来源：[https://blog.csdn.net/baidu_35297930/article/details/83381903](https://blog.csdn.net/baidu_35297930/article/details/83381903)

### 11、web5

![在这里插入图片描述](img/3c6d64338d0dc331bbce7253de45a868.png)
点开网址后：
![在这里插入图片描述](img/49f99606fe400b168859f408d05fb252.png)
随便输入提交看看：

![在这里插入图片描述](img/fab6ad44b6f2035d9cee49f8c004e0ad.png)
并没有出现什么，F12查看代码，会发现一个奇怪的东西：
![在这里插入图片描述](img/cfb66191e9e57bec8886c2180b458a00.png)

百度以后发现是某种编码，将其丢到控制台出直接解码，得到flag。
![在这里插入图片描述](img/d1ad04832159a4308ed7e78f83fde72a.png)

flag：ctf{whatfk}

### 12、头等舱

![在这里插入图片描述](img/3c6b546392c26c021b3701a3a8e1a34f.png)

点开网址后：
![在这里插入图片描述](img/e09e778ea9f117ca4d5181eccc2c0f12.png)

会发现如文字所述，什么也没有，查看代码，emmm，也很干净，什么都莫得orz。
![在这里插入图片描述](img/d3ecb2e1f63f212fa8a969db589f1914.png)

用抓包试试看吧，或许能有什么东西。
![在这里插入图片描述](img/8f2e83f579ca0aae0dd1e0a7036a1d8d.png)

抓包后发现了 flag。查看了其他人的答案，发现，他们的源码中含有一个隐藏的提示，可能是后面给改了？？？源码不给提示了？？
其他人的源码如下：
![在这里插入图片描述](img/3d6e16cd07be1ea2ad534cb18d6bed7d.png)

不管怎样flag是拿到了。

flag：flag{Bugku_k8_23s_istra}:

### 13 、网站被黑

![在这里插入图片描述](img/a9afd92f4f2bf9029ddb4d86643fc5ae.png)

点开网之后：
![在这里插入图片描述](img/b6e4145edce4d4cb4252d8e5814f7345.png)

网之后加入index.php，访问成功，说明网站是php的。使用御剑扫描扫描后台得到webshell地址。
![在这里插入图片描述](img/a759ac1672ee8dbfc016a8b2cd10306c.png)

访问，需要密码：
![在这里插入图片描述](img/f8272150498addf15179fbfd51b78382.png)

随手试了几个密码，不对，使用bp爆破，使用bp自带的password字典就行。
![在这里插入图片描述](img/641736a69a04b5fd0d7dc6baecd0b59c.png)

![在这里插入图片描述](img/687ea79fe17405467bc4c573a2de33af.png)

输入后登陆，即可拿到flag。
![在这里插入图片描述](img/b08a25253a5e556e21d4133730779272.png)

flag:flag{hack_bug_ku035}

### 14、管理员系统

![在这里插入图片描述](img/fd912d08e06aaa8352b52b26e5a53ac5.png)

打开网之后：
![在这里插入图片描述](img/ed590826a66a9ede1833652b3c1aafd1.png)

随手输入，密码不正确：
![在这里插入图片描述](img/0084b8a32a9e7468b6f591f563d2bbdc.png)
查看源码，发现一个base64编码：
![在这里插入图片描述](img/a3752bfeedec05595c1bec19b530743c.png)
解码后得到：test123，试试将此当密码，admin当用户名，还是失败。

根据提示，联系本地管理员登陆，试试利用XFF伪造成本地ip地址试试看。先利用bp抓包：
![在这里插入图片描述](img/f5023d5d19596bbb5524864459ee35b9.png)

> 相关知识：
> 伪造X-Forwarded-For：
> [https://blog.csdn.net/xiewenbo/article/details/72834964?utm_source=blogxgwz0](https://blog.csdn.net/xiewenbo/article/details/72834964?utm_source=blogxgwz0)

flag：flag{ 85ff2ee4171396724bae20c0bd851f6b}

### 15、web4

![在这里插入图片描述](img/71b17dbf9134de19091536a3d1e4ea1e.png)
点开网址后：
![在这里插入图片描述](img/8d9df3a6128c55fce270dfc59e68c0da.png)

根据提示，查看源码发现一串编码。
![在这里插入图片描述](img/5a3b1000b9e3b08911efc5d29f477989.png)

解码后得到：
![在这里插入图片描述](img/2633d1a7d5a382aab5b6722785379707.png)

整理后：

```
function checkSubmit(){
   var a=document.getElementById("password");
   if("undefined"!=typeof a)
   {
        if("67d709b2b54aa2aa648cf6e87a7114f1"==a.value)
               return!0;
        alert("Error");
        a.focus();
        return!1
    }
}
document.getElementById("levelQuest").onsubmit=checkSubmit; 
```

整理后，代码意思为：判断变量a的值是否等于67d709b2b54aa2aa648cf6e87a7114f1
直接输入字符串提交就可以得到flag。
![在这里插入图片描述](img/d443aa41c3e11254fd9174a357c43ab2.png)

flag：KEY{J22JK-HS11}