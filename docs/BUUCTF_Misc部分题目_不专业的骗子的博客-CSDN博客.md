<!--yml
category: 未分类
date: 2022-04-26 14:51:19
-->

# BUUCTF_Misc部分题目_不专业的骗子的博客-CSDN博客

> 来源：[https://blog.csdn.net/m0_47129108/article/details/109010405](https://blog.csdn.net/m0_47129108/article/details/109010405)

#### 签到

签到就完事了

#### 基础破解&rar

![在这里插入图片描述](img/fdc86d90d82f0e7a5d29ed098c1ba430.png)
![在这里插入图片描述](img/76a9c14ebca27008b767a19bf0303a11.png)
题目提示了四位纯数字，暴力破解就行

![在这里插入图片描述](img/1223cf0a3e685a91cad37132d3e24e54.png)

#### qr

![在这里插入图片描述](img/8fe1e491be5e7c45638fd1240ab0f981.png)
啊哈，二维码，扫就完事了

#### 文件中的秘密

拿到题目后得到一个图片，根据题目的意思右键查看属性
在详细信息内得到了flag
![在这里插入图片描述](img/25034ae83c36bc43767cdb0f9154f7a5.png)

#### wireshark

![在这里插入图片描述](img/628713f136e6285f11cc6a34f3edaf66.png)
根据题目及题目提示找到
![在这里插入图片描述](img/cc3c74a511acf6c2b46ebaf76e350a99.png)
如何快速找到有效数据包，我也不太清楚，看了一些题目大部分是在http和tcp类型里边的，不行就一个一个找嘛
看到password即为flag