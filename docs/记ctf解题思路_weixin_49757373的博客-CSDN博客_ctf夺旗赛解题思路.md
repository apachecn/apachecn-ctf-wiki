<!--yml
category: 未分类
date: 2022-04-26 14:52:11
-->

# 记ctf解题思路_weixin_49757373的博客-CSDN博客_ctf夺旗赛解题思路

> 来源：[https://blog.csdn.net/weixin_49757373/article/details/112438712](https://blog.csdn.net/weixin_49757373/article/details/112438712)

老虎：爆破得到密码进去后看到图片上有flag，看起来是md5，md5解后得到this_is_flag

re：丢进ida查看主函数，发现一串16进制666c61677b54686973697361656173797265217d，转换成字符串得到flag

签到：转换成10进制后，每个数字加1.，然后再转成ascii，得到flag