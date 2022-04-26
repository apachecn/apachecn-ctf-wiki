<!--yml
category: 未分类
date: 2022-04-26 14:41:05
-->

# SarCTF Writeup题解 Red King、Confidential_随便吧土豆的博客-CSDN博客

> 来源：[https://blog.csdn.net/SBBTD/article/details/104382553](https://blog.csdn.net/SBBTD/article/details/104382553)

# 2020-SarCTF

ctftime链接：https://ctftime.org/event/975/

## 0x01【Stego】Red King

描述：Just Moriarty? Really?

wp：用Stegsolve，导出数据 Red / Column / LSB：

> In 1989, Moriarty murder ed . Carl Pow ers, whom he cla imed .
> “laugh ed at him”. He k ept Powers’ . trainers almost twenty
> years un til he . used the shoes as pa rt of his plan t o .
> meet Sher lock Holmes and had them planted . in the dis used flat
> at 221 C Baker Street… From that po int onwards he d eveloped his
> . skills at crim inal activities and . went on to create a
> larg e criminal . organisation whi ch would stretch . across
> the whole globe. fl ag is FLAG{who_i s_moriarty}

```
FLAG{who_is_moriarty} 
```

## 0x02【Forensics】Confidential

原文件是抓FTP的一个pcap不放了，很简单可以提取出KeePass文件，文件有密码保护。
工具`John The Ripper`，各种加密的爆破。[参考wp](https://spotless.tech/sarctf-Confidential.html)

1.  用John的实用工具转换kdbx文件为John支持的格式
2.  然后用字典爆破
3.  查看结果

```
$ keepass2john database.kdbx > crack
$ john --wordlist=rockyou.txt crack
$ john --show crack 
```

好吧事实证明，John在Windows上实是难用，功能能用但是GPU加速的opencl对较新的显卡支持太差了，调了整整一下午只能做到用集显计算，也就比CPU快了一倍没啥意义。等以后装了Linux的双系统再议吧。

于是换成`hashcat`，对独显GPU兼容贼好，半分钟就完事。

将上面生成的crack文件裁剪一下，去掉前缀只保留这些：

```
$keepass$*2*60000*0*55b1d133aa811878a7573efbfc94107d035be43e56b22194e33a3982a98548b0*951d07f3d867d9edad2dcf60dab6995cac5246a1fa80841d06c7dccd8d7dd7b3*3e52f184d49556fb5a4052b102f6511a*b27cbff65e1a7ffefe76011a12f0b22b2e325fba30cb1980ce147cfc505023f6*ce7dfdb0f2abb3adb8b0dfd775e1e1e1ddf6bc9146e3582f57b930594b174205 
```

然后运行：

```
hashcat64.exe -m 13400 -a 0 ..\crack ..\rockyou.txt 
```

然后解出的密码显示格式还藏得不容易注意到：

```
$keepass$*2*60000*0*55b1d133aa811878a7573efbfc94107d035be43e56b22194e33a3982a98548b0*951d07f3d867d9edad2dcf60dab6995cac5246a1fa80841d06c7dccd8d7dd7b3*3e52f184d49556fb5a4052b102f6511a*b27cbff65e1a7ffefe76011a12f0b22b2e325fba30cb1980ce147cfc505023f6*ce7dfdb0f2abb3adb8b0dfd775e1e1e1ddf6bc9146e3582f57b930594b174205:blowme! 
```

发现了吗，在最后面！就不能用文件名加编号的方式标注吗，找密码找得好苦。/(ㄒoㄒ)/~~

然后放到KeePass软件里密码一输，依次查看每个账号的密码，flag就有了。

```
FLAG{bru73_p455w0rd_4ll_n16h7_l0n6} 
```

[](https://github.com/SBBTD/ctf-writeups/tree/master/2020-SarCTF)