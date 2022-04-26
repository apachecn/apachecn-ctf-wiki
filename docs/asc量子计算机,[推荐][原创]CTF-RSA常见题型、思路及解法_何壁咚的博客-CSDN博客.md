<!--yml
category: 未分类
date: 2022-04-26 14:43:05
-->

# asc量子计算机,[推荐][原创]CTF-RSA常见题型、思路及解法_何壁咚的博客-CSDN博客

> 来源：[https://blog.csdn.net/weixin_33411250/article/details/119011961](https://blog.csdn.net/weixin_33411250/article/details/119011961)

RSA入门:

写在前面:

这篇文章是帮助对RSA不熟悉的朋友且需要较为快速、深入的了解RSA而写的

不涉及非常晦涩难懂的数学知识和算法(不过最简单的原理说明还是有的)

攻击方法也是最容易遇到的(RSA的进阶篇除外)

并且所有的例题都是比较新的,甚至还有刚刚结束的比赛的题目

基本原理:

公钥与私钥的产生：

(1)进行加密之前，首先找出2个不同的大质数p和q

(2)计算n=p*q

(3)根据欧拉函数，求得φ(n)=φ(p)φ(q)=(p−1)(q−1)

(4)找出一个公钥e，e要满足: 1

(5)根据e*d除以φ(n)余数为1，找到私钥d。

(6)所以,公钥就是(n,e) 私钥就是(n,d)

消息加密:

m^e除以n求余数即为c(密文)

消息解密:

c^d除以n求余数即为m(明文)

关于基本原理的一些说明:

算法原理:

RSA公开密钥密码体制的原理是:

根据数论，寻求两个大素数比较简单，而将它们的乘积进行因式分解却极其困难，因此可以将乘积公开作为加密密钥

模运算规则:

模运算与基本四则运算有些相似，但是除法例外。其规则如下:

(a + b) % p = (a % p + b % p) % p

(a - b) % p = (a % p - b % p) % p

(a * b) % p = (a % p * b % p) % p

(a ^ b) % p = ((a % p)^b) % p

结合律：

((a+b) % p + c) % p = (a + (b+c) % p) % p (5)

((a*b) % p * c)% p = (a *(b*c)%p) % p (6)

交换律：

(a + b) % p = (b+a) % p

(a * b) % p = (b * a) % p

分配律：

((a +b)% p * c) % p = ((a * c) %p + (b * c) % p) % p

重要定理：

若a≡b (% p)，则对于任意的c，都有(a + c) ≡ (b + c) (%p)

若a≡b (% p)，则对于任意的正整数c，都有(a * c) ≡ (b * c) (%p)

若a≡b (% p)，c≡d (% p)，则 (a + c) ≡ (b + d) (%p)，(a - c) ≡ (b - d) (%p)，

(a * c) ≡ (b * d) (%p)

欧拉函数:

定义:

在数论，对正整数n，欧拉函数是小于或等于n的正整数中与n互质的数的数目(因此φ(1)=1)

例如φ(8)= 4，因为1,3,5,7均和8互质

特殊性质:p^k型欧拉函数:

若N是质数p的k次幂(即N=p^k)，φ(n)=p^k-p^(k-1)=(p-1)p^(k-1)。

mn型欧拉函数(联想rsa算法的p和q)

设n为正整数，以φ(n)表示不超过n且与n互素的正整数的个数，称为n的欧拉函数值。

若m,n互质，φ(mn)=(m-1)(n-1)=φ(m)φ(n)。

基础知识的练习:

buuctf-RSA:

在一次RSA密钥对生成中，假设p=473398607161，q=4511491，e=17

求解出d作为flag提交

buuctf-rsarsa:

题目:

解题:

倒数第二行的gmpy2.powmod也可以用pow函数替代,不过前者比后者的计算速度快很多,之后有道题可以体现出来

RSA的基础攻击方式:

自此,我们已经了解了rsa的原理,现在开始进入学习rsa的一些攻击方式

1.暴力分解n

用于已知e,c,m,不是很大的n(小于等于1024),或者n具有缺陷,q和p相差过大或过小

常用分解方法:

名称

特点

个人使用感受

factordb n(需要安装对应的py模块，pip install factordb-pycli)

利用数据库查询

可以查询到大多数数据,非常推荐

yafu分解

可以对有缺陷的n快速分解

有时候factordb查询不到时可以用yafu分解试试,可能有奇效

linux下命令行: factor n(计算,n较小时推荐)

环境依赖少

应该是爆破计算,对于大数支持很差

在线分解: http://factordb.com/

数据库查询

体验不佳

如果第一项,和第二项都无法分解,就应该考虑换一种思路了,题目考点应该不是暴力分解

例题1：

已知一段RSA加密的信息为：0xdc2eeeb2782c且已知加密所用的公钥：

(N=322831561921859 e = 23)

请解密出明文，提交时请将数字转化为ascii码提交

比如你解出的明文是0x6162，那么请提交字符串ab

提交格式:PCTF{明文字符串}

解题:

首先整理一下已知条件:

我们已知公钥:

n和e,以及密文c

想要通过密文c来解得明文m需要私钥d

而私钥d需要通过e和phi求出,其中e已知,所以我们只需要知道phi

而phi = (q-1) * (p-1),幸运的是我们知道n = q*p,且q和p都是素数,如果我们能通过n分解出p和q则可以解出这道题目了

分解n:

得到p = 13574881 , q = 23781539(q和p不涉及其他关系时,p和q的顺序无所谓)

例题2:[GWCTF 2019]BabyRSA

可以从这两句代码看出:

可以知道,q和p相差很小,所以这个n是具有缺陷的,通常有两种攻击方式

对n开方并把值设为r,那么q < r < p ,且q和p是素数,既然p和q相差很小,可以通过循环快速可以确定p和q

yafu分解

分解出来后,是一个简单是数学等式,用z3约束器可以快速求解

(使用yafu分解的攻击方法比较简单,这里就不写了)

解题:

2.与公钥私钥交互获得数据:工具

优点

缺点

openssl

功能最全

对数据要求严格

RsaCtfTool

操作简单

功能不全

Python

功能灵活

需要自己写脚本

openssl:指令

功能

genrsa

生成并输入一个RSA私钥

rsa

处理RSA密钥的格式转换等问题

rsautl

使用RSA密钥进行加密、解密、签名和验证等运算

genrsa

rsa

rsautl数据补齐方式

输入数据长度

输出数据长度

参数字符串

PKCS#1 v1.5

少于(密钥长度-11)字节

同密钥长度

-pkcs

PKCS#1 OAEP

少于(密钥长度-11)字节

同密钥长度

-oaep

PKCS#1 for SSLv23

少于(密钥长度-11)字节

同密钥长度

-ssl

不使用补齐

同密钥长度

同密钥长度

-raw

生成私钥:

openssl genrsa -out prikey

查看私钥:

openssl rsa -in prikey -text -modulus

从私钥总提取公钥:

openssl rsa -in prikey -pubout -out pubkey

查看公钥:

openssl rsa -in pubkey -text -modulus -pubin

加密:

openssl rsautl -encrypt -in filename -inkey pubkey -pubin -out filename

解密:(如果有对应的补齐方式需要指定)

rsautl -decrypt -inkey prikey -in filename

Rsactftool:

生成公钥:

RsaCtfTool.py --createpub -n number -e number > pubkey

查看公钥:

RsaCtfTool.py --dumpkey --filename pub.pem

已知公钥求私钥:

RsaCtfTool.py --publickey pubkey --private

已知公钥,自动解密:

RsaCtfTool.py --publickey pubkey --uncipherfile filename

例题1.西湖论剑-rsa

给了两个文件,message 和 public.key

以及加密脚本

首先打开公钥看看

可以看到e和n非常大,应该考虑Wiener攻击(后面会介绍到)

不过这里主要是介绍和公钥、私钥的交互

所以我们用RsaCtfTool来爆破出私钥

得到了私钥,尝试用openssl解密

却报错了,为什么呢

我们观察题目给出的加密脚本第二行"from Crypto.Cipher import PKCS1_OAEP"

说明这里是用的使用OAEP的填充方式,而解密是也必须指明对应的填充模式

3.简单的数学变换

例题1:[GUET-CTF2019]BabyRSA

解题:

这类题核心解法大体相同,出题人给出有唯一解的q和p的方程,通过解出q,p来解题

大多数简单的数学方程可以用z3约束器快速解出

例题2.[BJDCTF2020]easyrsa

题目:

解题:

这道题挺有意思的,在给出的脚本第10行

有两个没见过的函数,通过搜索资料,可以查到相关资料

Fraction,返回参数1/参数2的分数形式

Derivative，返回参数1函数在参数2点的导数

联系这两点

可以知道

z = Fraction(1,Derivative(arctan(p),p))-Fraction(1,Derivative(arth(q),q))

= 1/(1/(1+p^2)) - 1/(1/(1-q^2))

= 1+p^2 + q^2 -1

= p^2 + q^2

又把本题转化成简单的数学问题了

常见攻击方式:

1.低加密指数分解攻击

原理:

在 RSA 中 e 也称为加密指数。由于 e 是可以随意选取的，选取小一点的 e 可以缩短加密时间(比如 e=2,e=3)，但是选取不当的话，就会造成安全问题

由于e很小,当n很大时,m^e也比n小很多.尝试把c开根号看能否得到明文.

例题:[NCTF2019]easyRSA

题目描述:

When you need really secure communications, you use RSA with a 4096 bit key.

I want really really really secure communications to transmit the nuclear launch codes (yeah IoT is everywhere man) so I used RSA with a 16777216 bit key. Surely russians will not be able to factor that one !

File md5 : 1049a0c83a2e34760363b4ad9778753f

解题:

题目给出了n和c,e,不过这个文本文件的大小高达7.4m,要是常规分解的话,估计量子计算机也够呛

不过由于n是16777216 bit的数字,e只有0x10001,所以m^e << n,符合低加密指数分解攻击的条件

这里我们也应该理解到,低加密指数并非指e == 2或e == 3这类,e的数值很小,而是m^e和n的相对大小

2.小明文攻击

原理:

公钥e很小，明文m也不大的话，于是m^e=k*n+m 中的的k值很小甚至为0(k==0时,和上一种攻击方式差别不大)，爆破k或直接开e次方即可。

例题:[De1CTF2019]babyrsa(部分)

解题:

我们拿到两个类似rsa加密的式子:

(e1^ee1) mod n == ce1

((e2+tmp)^ee2) mod n == ce2

由于两个式子的加密指数很小(ee1 = 42,ee2 = 3),考虑小指数攻击

3.低解密指数攻击:

本质上,第一种、第二种攻击方式都是因为e选取的较小而造成的缺陷,那么e选取的非常大是不是就很安全呢？

实际上也不是,当e很大时,就会造成d非常的小,也具有相当大的危险

原理:

顾名思义,这是一种针对d很小时的攻击方式

此种攻击方法由Micheal j.Wiener于1989提出,所以也被称为Wiener's attack,该方法基于连分数

该方法在github已有完整可用的代码,之后所有的低解密指数也是利用以上脚本进行的

题目:buuctf-rsa2

解题:

4.模不互素

原理:

把大数分解成两个素数很困难,但是求两个大数的公因子很容易

当:

n1 = a*b

n2 = a*c

我们可以通过求gcd(n1,n2)来得到a,再通过除法分别求得b和c,从而达到了分解大素数的目的

例题:QCTF2018Xman-RSA(部分)

解题:

可以通过gcd(n1,n2)快速求得p1

5.共模攻击:

原理:

对同一明文的多次加密使用相同的模数和不同的公钥指数可能导致共模攻击

证明:

当n不变，知道n,e1,e2,c1,c2 可以在不知道d1,d2情况下，解出m。

题目:RSA_what:

解题:

这道题解出来后是base64加密后的字符串,实际上没有看上去那么简单,这里涉及到base64隐写,不过不是本文的主题,先暂且不管

6.广播攻击:

原理:

对于相同的明文,使用相同的指数e和不同的模数n1,n2...ni加密得到i组密文(i>=e),可以使用中国剩余定理解出明文

题目:buuctf-rsa4:

解题:

7.已知e和d,求p,q

原理:

当已知e,n,可以通过算法快速求得p和q

详细算法见:

https://www.di-mgt.com.au/rsa_factorize_n.html

例题:MRCTF2020Easy_RSA(部分)

解题:

可以看到,这里的p和q都高达1024bit,基本可以排除暴力分解

这时我们知道e*d,和n,可以尝试已知该算法

8.n分解成多个素数

原理:

rsa加密基本原理

需要严格按照欧拉函数的定义来求解phi

例题:15年山东大学生爱好者线上赛

解题:

n = p * q * r

那么φ(n) = (q-1) * (p-1) * (r-1)

9.dp泄漏攻击

原理:

dp本来是为了加速rsa加解密效率的,不过由于dp和p的关系相当密切,所以当dp泄漏也有相当大的危害

例题:buuctf-RSA1

解题:

进阶攻击方式

1.私钥文件修复

题目:Jarvis OJ -Crypto-God Like RSA

题目给出了密文文件,公钥和破损的私钥文件(这里就不放上来了)

解题:

首先读出公钥信息和破损的私钥信息填入以下脚本即可恢复出完整的私钥

解出私钥后,写入到文件中,用openssl解密即可(不过需要注意的是这依然是用oaep的填充方式,需要指明)

2.对e爆破

题目1:[HDCTF2019]bbbbbbrsa

这道题e的大致范围已经给出,可以尝试爆破

不过加密脚本的第一行有点坑

(应该是出题人故意)把人误导成base32解密

题目2:[BJDCTF2020]RSA

也是对e爆破,不过我们可以通过这道题来对比pow()函数和gmpy2.powmod()的速度

pow:

用时信息:

3.03s user 0.01s system 99% cpu 3.050 total

gmpy2.powmod

用时信息:

1.30s user 0.00s system 99% cpu 1.310 total

可以看到,gmpy2中的powmod()函数用时不到pow()函数的二分之一,所以以后优先使用gmpy2.powmod()函数

题目3:[RoarCTF2019]babyRSA

解题:

题目给出了A和p,q的关系,一通花里胡哨的运算之后得到A

以我已知的知识想不到什么好的算法

不过可以直接分解n得到p和q

当我们做完这些后,发现题目并没有告知e,盲猜e = 65537,结果正好是

当然平时做题我们还是严谨一点,尝试爆破e

关于e = 65537的说明

NIST SP800-78 Rev 1 (2007) 曾强调“不允许使用比65537更低的公钥指数e”，但PKCS#1却从未有过类似的建议。e=65537=(2^16+1)，其签名/加密运算只需要17次模乘，性能也算不错。65537可以在安全上和性能上做出一个很好的平衡,因此大多数加密的e都默认为65537

对于e未知的题型的总结:

可以优先尝试e == 65537,不行再爆破

3.已知p和q的最小公倍数

题目:[NPUCTF2020]EzRSA

解题:

已知n = q

gift = lcm(q-1,p-1)

= (q-1)*(p-1) // gcd(q-1,p-1)

又因为(q-1)*(p-1) 约等于 q*p = n

gift 约等于 n // gcd(q-1,p-1)

于是可以得出gcd(q-1,p-1) 约等于 n // gift

于是得出gcd(q-1,p-1)约等于8

设k = gcd(q-1,p-1) ( k 约等于8,于是爆破k的取值范围为0

最后得到q,p,e,又由于gcd(e,phi) != 1

于是:

new_e = e//2

d = gmpy2.invert(e,phi)

m = gmpy2.powmod(c,d,n)

m = gmpy2.iroot(m,2)[0]

m = long_to_bytes(m)

可以求出明文

最后两道综合题:

1.[De1CTF2019]babyrsa

题目:

解题:

本题过程大致可以分为三部分:

解得e1和e3

解得hint和q1

解得flag

1.解得e1和e3

通过这两句断言:

可以建立两个方程,由于两个e都很小,考虑低加密指数攻击:

解的e1,e2

2.解的hint和q1

一个常规的rsa加密,没有明显的缺陷,尝试分解n

得到两个质数

常规的分解

得到完全没用的hint,但是得到了对第三步很有用的q1

3.解得flag

乍一看似乎很简单,连n都已经分解好了(这让我想起了NCTF的easyRSA)

一测试,果然gcd(e1,phi1) == gcd(e2,phi2) == 14

既然到这里了,就总结一下e和phi不互素时的解法(下次)

2\. [QCTF2018]Xman-RSA

题目:

解题:

从密文形式可以看出是用的python编写的加密脚本,不过这个脚本又被再次加密,猜想应该是纺射加密,从中挑选了两端话来对比得出a和b

得出a = 3, b = 17

纺射解密后得到加密脚本,如下

进入了rsa解题过程,我们先看最后一个加密,相同的明文,相同的n,不同的e

用共模攻击解出n1来

对于倒数第二段加密,通过这两句代码

解出两组明文,最后通过栅栏解密得到flag

最后于 2020-10-29 19:39

被happi0编辑

，原因：