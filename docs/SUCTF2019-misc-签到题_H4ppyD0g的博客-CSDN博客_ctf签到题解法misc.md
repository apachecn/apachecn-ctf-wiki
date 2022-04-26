<!--yml
category: 未分类
date: 2022-04-26 14:46:06
-->

# SUCTF2019 misc 签到题_H4ppyD0g的博客-CSDN博客_ctf签到题解法misc

> 来源：[https://blog.csdn.net/weixin_42172261/article/details/99705854](https://blog.csdn.net/weixin_42172261/article/details/99705854)

先看看有没有隐藏文件，并没有发现。
用winhexcn看压缩包信息，ctrl+F搜索jpg，png，txt，flag，只发现有1.txt，也就是在压缩包里能看见的东西。
打开1.txt后一堆编码放在那里，看不懂，用base64解码解不出。卡住。
后来学到这是网页图片的base64编码。
把那些编码嵌入到`<img src="https://img-blog.csdnimg.cn/2022010711050488343.png"/>`中，并且后缀名改成.html打开后发现flag。

* * *

**html img src base64**

`<img src="https://img-blog.csdnimg.cn/2022010711050488343.png"/>`

data表示取得数据的协定名称，image/png 是数据类型名称，base64 是数据的编码方法，逗号后面就是这个image/png文件base64编码后的数据。

网页中一张图片可以这样显示：
`<img src=“http://www.letuknowit.com/images/wg.png”/>`
也可以这样显示：
`<img src="https://img-blog.csdnimg.cn/2022010711050537233.png"/>`
我们把图像文件的内容直接写在了HTML 文件中，这样做的好处是，节省了一个HTTP 请求。
坏处就是浏览器不会缓存这种图像。