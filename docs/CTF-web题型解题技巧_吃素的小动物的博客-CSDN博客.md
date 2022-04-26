<!--yml
category: 未分类
date: 2022-04-26 14:34:17
-->

# CTF web题型解题技巧_吃素的小动物的博客-CSDN博客

> 来源：[https://blog.csdn.net/weixin_30654419/article/details/96490387](https://blog.csdn.net/weixin_30654419/article/details/96490387)

工具集

基础工具：Burpsuite，python，firefox(hackbar，foxyproxy，user-agent，swither等)

扫描工具：nmap，nessus，openvas

sql注入工具：sqlmap等

xss平台：xssplatfrom，beef

文件上传工具：cknife

文件包含工具：LFlsuite

暴力破解工具：burp暴力破解模块，md5Crack，hydra

常用套路总结

直接查看网页源码，即可找到flag

查看http请求/响应，使用burp查看http头部信息，修改或添加http请求头（referer--来源伪造，x-forwarded-for--ip伪造，user-agent--用户浏览器，cookie--维持登陆状态，用户身份识别）

web源码泄漏：vim源码泄漏(线上CTF常见)，如果发现页面上有提示vi或vim，说明存在swp文件泄漏，地址：/.index.php.swp或index.php~

恢复文件 vim -r index.php。备份文件泄漏，地址：index.php.bak，www.zip，htdocs.zip，可以是zip，rar，tar.gz，7z等

.git源码泄漏，地址：http://www.xxx.com/.git/config，工具：GitHack，dvcs-ripper

svn导致文件泄漏，地址：http://www/xxx/com/.svn/entries，工具：dvcs-ripper，seay-svn

编码和加解密，各类编码和加密，可以使用在线工具解密，解码

windows特性，短文件名，利用～字符猜解暴露短文件/文件夹名，如backup-81231sadasdasasfa.sql的长文件，其短文件是backup~1.sql，iis解析漏洞，绕过文件上传检测

php弱类型

绕waf，大小写混合，使用编码，使用注释，使用空字节

友情链接  http://www.cnblogs.com/klionsec

                http://www.cnblogs.com/l0cm

                http://www.cnblogs.com/Anonyaptxxx

                http://www.feiyusafe.cn