<!--yml
category: 未分类
date: 2022-04-26 14:20:49
-->

# ctf学习经历——极客部分题解_RoleMee的博客-CSDN博客

> 来源：[https://blog.csdn.net/qq_51604088/article/details/109891229](https://blog.csdn.net/qq_51604088/article/details/109891229)

> 解题思路
> Web
> 朋友的学妹
> 1，根据提示：打开 ![在这里插入图片描述](img/5c9c203775d260a37eeb195a8d7d0570.png)
> 
> 得到 ：![在这里插入图片描述](img/4620086fe99c6aa46c137b1e781e83d9.png)
> 
> 2，找到flag。
> 
> EZWWW
> 1，打开网站，发现备份
> ![在这里插入图片描述](img/035df8ffe7f1f9b2200cb5d0aebaade9.png)
> 2，用御剑：扫描后台
> ![在这里插入图片描述](img/42d389775450a8b0177b103b1263cbed.png)
> 3，得备份的压缩包，打开
> ![在这里插入图片描述](img/bd2a6744b886cb4fd6523905d536d7ae.png)
> 
> 4，分析代码：base64_decode(‘c3ljbDB2ZXI=’);
> 知是base64加密。百度在线解码。得sycl0ver
> 5，用postman的post请求：得flag
> ![在这里插入图片描述](img/bff44f3715d89a4ee46552b8281ede82.png)
> 
> 刘壮的黑页
> 
> 1，打开网页，得到
> ![在这里插入图片描述](img/4cebb2d8d724c1fb72b5a2d88bc63f9e.png)
> 
> 2，用postman，get方式和post方式：用GET方法输入key=username，value=admin，用POST方法在Body输入key=passwd，value=syclover得flag；
> ![在这里插入图片描述](img/ae705bb2063e10370761f9f1d5682b35.png)
> 
> Welcome
> 1，打开网页：一脸懵逼；
> ![在这里插入图片描述](img/46b298c3de2ce569820bfa415c9a6b8e.png)
> 
> 2，根据提示，用post请求；
> ![在这里插入图片描述](img/4422e8eb94c925a7daba5483e7dd4f98.png)
> 
> 3，分析代码：用数组将sha1绕过；
> 得一堆新代码(再次懵逼；
> ![在这里插入图片描述](img/49c58d7bf848fae35b5d2cb7288e421f.png)
> 
> 4，在学长帮助下；得知类似flag的东西
> ![在这里插入图片描述](img/b61a26c5f912d98a3ce834a48dbc82a7.png)
> 
> 5，用post请求打开网站http://49.234.224.119:8000/f1444aagggg.php
> 得flag
> ![在这里插入图片描述](img/4388845a1f6ef2509b1df09a05db743e.png)
> 
> EzGit
> 1：根据提示用git；
> 在虚拟机中git clone https://github.com/denny0223/scrabble.git
> 2，输入命令：
> ![在这里插入图片描述](img/6a61337e7b7e489f22c4fa843d0097b7.png)
> 
> 发现flag tooooo old
> 3，还原版本：
> ![在这里插入图片描述](img/8b813f98106bc1a676280a91067ddf75.png)
> 
> 得 ![在这里插入图片描述](img/85ba9827ca1d233fe480cf0395b19d40.png)
> 
> ![在这里插入图片描述](img/6abb199a88a8e9743b7031a140f3caf3.png)
> 
> 我是大黑客
> 1，打开网站；发现备份文件 ；![在这里插入图片描述](img/779c39bbdb8c033719e22c91f79de70c.png)
> 
> 2打开 ；![在这里插入图片描述](img/fa0a4cc4c03137aa926e0704c06be89e.png)
> 
> 下载文件；![在这里插入图片描述](img/8a3016838878359aa4bb95fbb8a3a90e.png)
> 
> 得
> 3，白度知一句话密码liuzhuang。
> 4木马，用蚂剑，得 ![在这里插入图片描述](img/642b47bbe8183ff4bacf7938783e0d65.png)
> 
> ezbypass
> 
> 1,用postman打开网站数组绕过得 ![在这里插入图片描述](img/34091271ae611cf5c0774e6f481b8780.png)
> 
> 2，得提示 ![在这里插入图片描述](img/046c2c313799b96306b6aad7f51063db.png)
> 
> 3根据提示，用c，
> ![在这里插入图片描述](img/4423c365ffb6d0dd7b4541a0f594a5d9.png)
> 
> flagshop
> 
> 看到这个，想碰碰运气，第一次输入账号为admin 密码为123456
> ![在这里插入图片描述](img/34487f6aaed47736574f8d4214c7fb1f.png)
> ![在这里插入图片描述](img/99de8e8ffc82a84358c5127a359761a2.png)
> 
> 第二次都输入admin
> ![在这里插入图片描述](img/d67f025af80e9436893859cc3e52a570.png)
> 
> 结束真的进去了
> 在这里购买flag（花了我1w
> ![在这里插入图片描述](img/79aeaee88c75ae91b009e80e7ad0828d.png)
> ![在这里插入图片描述](img/a2473ca054b0bcc199045835a3bd36ab.png)
> 
> 然后成功
> 
> RE
> 
> NO RE no gain:
> 第一次接触ctf中的re，啥也不会，首先看到题目中的提示，想到下载IDA，在尝试几次后，终于打开
> ![在这里插入图片描述](img/b3361ba554a405ccbd229bd7a3ab5ec1.png)
> 
> 打开后即可获得flag
> ![在这里插入图片描述](img/2797e8ecd3da69af38d523905f017380.png)
> 
> 我真不会写驱动！
> 
> 打开后并没有明显的发现flag。于是看到左边的函数列表很少，再加上当时没学过这方面的内容，于是想都看看，于是就找到了flag（啥也不懂）。
> 
> ![在这里插入图片描述](img/220f8ce8ca5c9894ec680d6d73262130.png)
> ![在这里插入图片描述](img/74a13570ee36d0b43020acfaa3fbe51f.png)
> 
> WhatsAPK
> 
> 看到这两个，去CSDN查看APK的资料，看到使用Androidkiller，于是去下载
> ![在这里插入图片描述](img/ab69f6f53359166dc08598445b2b4393.png)
> 
> （就是这个玩意，忽略历史进程）
> 用这个打开题目文件
> ![在这里插入图片描述](img/33795c69f44341defb0bfb8dce5096b2.png)
> 
> （不知所措），尝试搜索syc走捷径
> ![在这里插入图片描述](img/403067ed7022406d97d744e1fdb7709f.png)
> 
> 还真的搜到了
> 
> HelloAndroid
> 根据上一题的经验，我还是尝试用Androidkiller打开
> 还是老方法，搜索
> ![在这里插入图片描述](img/9dbda1e8a3f57376c9ede5481c0314e5.png)
> 
> （秒杀）
> 
> RE00
> 老方法，用ida打开
> 
> 按F5反编译分析代码
> ![在这里插入图片描述](img/04da0db668976378d486699168641ca5.png)
> 
> 看到这个地方，就是去研究byte_4060这个函数
> ![在这里插入图片描述](img/88226da71549d991aa707371f6064975.png)
> 
> 根据wzgg的提示，按shift+e提取出密文
> ![在这里插入图片描述](img/fec9af3fd6d32d36951db16f0fe92395.png)
> 
> 得到这么一串数字，在根据大佬所说异或可以移项，于是开始解密，由于不会python，就使用c语言一个一个解密
> ![在这里插入图片描述](img/7b641aff0952dfd94f80cfbdb2af0b2a.png)
> 
> 最后拼接出来得到syc
> 
> Misc
> 一“页”障目
> ![在这里插入图片描述](img/901f5f80a2cc61af86a60563f864dccd.png)
> 
> 看到右边的，于是放到ps中拼起来
> ![在这里插入图片描述](img/d18e2e1506e9cdafbb079966ce4039a5.png)
> 
> 得到flag（当时觉得杂项还挺容易）
> 
> 壮言壮语
> ![在这里插入图片描述](img/350b3bcdd9bbdba0576b680a47813680.png)
> 
> http://www.keyfc.net/bbs/tools/tudoucode.aspx
> ![在这里插入图片描述](img/322ee3c0829b3d8a9cfa7105127294ad.png)
> 
> 得到flag
> 
> 秘技.反复横跳
> 
> 去CSDN查看相关资料
> https://blog.csdn.net/wxh0000mm/article/details/85683661?biz_id=102&utm_term=binwalk%E5%88%86%E7%A6%BB&utm_medium=distribute.pc_search_result.none-task-blog-2<sub>all</sub>sobaiduweb~default-1-85683661&spm=1018.2118.3001.4449
> 打开之前装的kaili虚拟机去使用binwalk
> ![在这里插入图片描述](img/949f2706663bb23e4e60f120ba8fccf0.png)
> ![在这里插入图片描述](img/3607ec6e7d5fa2f479f3b35acfcc1ff6.png)
> ![在这里插入图片描述](img/cd6a57d349485498718a783797569cda.png)
> ![在这里插入图片描述](img/67e5faf1a58f2b2c4a6bacd5ae9c0fbd.png)
> 
> 扫描二维码得到syc
> 
> 来拼图
> ![在这里插入图片描述](img/c2eed148f0662d0f951b06e111ee9a3f.png)
> 
> 猜测是把原图拆成40*40,然后打开观看
> ![在这里插入图片描述](img/b0e5cc78f6395c384b96f5a2f73239e7.png)
> 
> 看到这个文件是syc我就猜测在这附近，直觉告诉我这是md5编码，于是百度破解
> https://www.atool99.com/md5-crack.php
> ![在这里插入图片描述](img/40811c59016dbf6c9dd61193589f1e56.png)
> 
> 得到144号，然后用md5编码145
> 145加密后为：2B24D495052A8CE66358EB576B8912C8
> ![在这里插入图片描述](img/78fc1f0d573881946282c96a9af95ce9.png)
> 
> 但不是，猜测143，
> ![在这里插入图片描述](img/d75393a0bc565707439e10523e316121.png)
> 
> 也不是，于是猜测是加40，
> ![在这里插入图片描述](img/fc3c0ba3f249a5a8c2f96e19dc0a1261.png)
> 
> 果然是，于是这样找到最后一个，得到一串图片，用ps拼图
> 
> ![在这里插入图片描述](img/ae3649e3a55c591c16e5e79736d58b07.png)
> 
> Crypto
> 二战情报员刘壮
> ![在这里插入图片描述](img/d368b0aef221ee642ce926c3f11d04fb.png)
> 
> 很显然是摩斯密码
> https://www.atool99.com/morse.php
> ![在这里插入图片描述](img/1e8824eaa91230e65215c67c4e06a25b.png)
> 
> 根据要求去掉下划线，加上SYC{}，提交即可
> 
> 铠甲与萨满
> 
> 显然是凯撒密码
> https://www.qqxiuzi.cn/bianma/kaisamima.php
> ![在这里插入图片描述](img/0ac3c9c1317d30edaf80a1e6161f4ba5.png)
> 
> 位移六位即使syc
> 
> ![在这里插入图片描述](img/96269ad113e512f1c4d6ccc2039d8924.png)
> 
> 跳跃的指尖
> 看到跳跃的指尖，在看到奇怪的字符
> 
> 突发奇想，把手放在键盘上试了试，于是发下是夹在他们中间的数，解密成功
> ![在这里插入图片描述](img/b54ff4035fad736f3cf25a208f5c0dc8.png)
> 
> 成都养猪二厂
> 
> http://www.metools.info/code/c90.html
> 
> ![在这里插入图片描述](img/199b32bc485e949f806a285ac490def6.png)
> 
> ![在这里插入图片描述](img/375fa09e849d979038fba29fe3564cf1.png)
> ![在这里插入图片描述](img/0c537d73c060a7bdb6c33ad72fb0f909.png)
> ![在这里插入图片描述](img/f2328d5558620b7f8c7a8d5ab2286ab9.png)
> 
> 发现是int 于是猜测是栅栏密码移位7
> 但是密码不对，于是将图片旋转一下
> ![在这里插入图片描述](img/84ad4050c066a2891264793d238a618f.png)
> 
> 这样就对了
> 
> 规规矩矩的工作
> 下载文件
> http://blog.neargle.com/SecNewsBak/drops/CTF%E4%B8%AD%E9%82%A3%E4%BA%9B%E8%84%91%E6%B4%9E%E5%A4%A7%E5%BC%80%E7%9A%84%E7%BC%96%E7%A0%81%E5%92%8C%E5%8A%A0%E5%AF%86%20.html?tdsourcetag=s_pcqq_aiomsg
> 在这里找到加密方式，线性代数，计算上面方阵的逆矩阵。
> http://www.yunsuan.info/matrixcomputations/solvematrixinverse.html
> ![在这里插入图片描述](img/b89e37bdf809e7b4931224b24a53c3b9.png)
> 
> 然后逆矩阵和上面的矩阵相乘
> https://www.99cankao.com/matrix/matrix-multiplication.php
> ![在这里插入图片描述](img/9e9c86eac0818af21c53dc858e6733c2.png)
> ![在这里插入图片描述](img/978d6268591a7234a1469234bad3c4e7.png)
> 
> 然后讲结果mod26
> https://gonglue.qinggl.com/app/shuxue/qumoyunsuanjisuanqi.jsp
> ![在这里插入图片描述](img/4afffbcf82bea7bae6b794f15cd0f579.png)
> 
> 求出来是key
> 代入之前的程序中
> ![在这里插入图片描述](img/03f7100cd86e3022fc51d34644d78d45.png)
> 
> 韡髻猊岈
> 下载文件发现是维吉尼亚密码，然后百度发现都要密钥，正当自己一筹莫展之际，找到了无密钥破解
> http://atomcated.github.io/Vigenere/
> ![在这里插入图片描述](img/ce7403a244d3374db0d10542b7492701.png)
> 
> 侧重点是三个大写英文字母
> 发现并不是SYC
> 于是更改密钥长度
> ![在这里插入图片描述](img/036e6aec448a95d394ee52cebebf767d.png)
> 
> 得到flag