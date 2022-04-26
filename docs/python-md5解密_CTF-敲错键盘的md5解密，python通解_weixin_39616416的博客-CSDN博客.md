<!--yml
category: 未分类
date: 2022-04-26 14:54:39
-->

# python md5解密_CTF-敲错键盘的md5解密，python通解_weixin_39616416的博客-CSDN博客

> 来源：[https://blog.csdn.net/weixin_39616416/article/details/110320867](https://blog.csdn.net/weixin_39616416/article/details/110320867)

常常会有无聊的出题人，给出这样的crypto题。md5值可能错了几位，多了几位……

小明想入侵小红的网站，发现小红是用自己的生日作为自己网站的密码，可是小明只知道小红是99年的，小明通过某些手段得到了小红存密码的文件，但是好像有个字符串被篡改了你能解出小红的生日吗？

093bd2299e0d90de4ch96a53ec3d13c1

年轻的时候可以用眼睛找，年纪大了以后就想写通解一劳永逸了。思路是用生成的字典去生成md5字典，再和题目密文比较相似度，正好python里有个difflib库，调用一下就能得到相似度值。

有了这个算法后随便改几位md5值都不怕啦！大不了多输出几位！

![04a90d3bb7f6?from=timeline](img/432acc0901391a1ecb60d445ce6f517d.png)

执行效果为（按相关度排序）

与君共享

def birthGen():#生日字典生成

list = []

for day in range(1,32):

day = str(day).rjust(2,'0')

for month in range(1,13):

month = str(month).rjust(2,'0')

list.append('1999'+month+day)

return list

def md5Brute(list):#列表生成md5字典

import hashlib

b = {}

for i in list:

b[hashlib.md5(i.encode('utf-8')).hexdigest()] = i

return b

def md5Correct(a,dict):#md5标准化校验

import difflib

result = {}

for i in dict:

result[i] = difflib.SequenceMatcher(None, a,i).quick_ratio()

return sorted(result.items(),key = lambda x:x[1],reverse = True)

b = md5Brute(birthGen())

list = md5Correct(a,b)

print(list[0],b[list[0][0]])

print(list[1],b[list[1][0]])

考虑difflib是用类似于in的算法，汉明距离是用操作距离来算，更接近出题思路。但汉明距离只适用于输错的情况，严格要求len(str1)==len(str2)，所以留给以后补充吧，现在够用就行了，在苍茫的md5海洋里不会那么巧的https://segmentfault.com/a/1190000002915566