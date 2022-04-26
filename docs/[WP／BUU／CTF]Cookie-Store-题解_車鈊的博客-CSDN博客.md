<!--yml
category: 未分类
date: 2022-04-26 14:41:50
-->

# [WP/BUU/CTF]Cookie Store 题解_車鈊的博客-CSDN博客

> 来源：[https://blog.csdn.net/DARKNOTES/article/details/119264994](https://blog.csdn.net/DARKNOTES/article/details/119264994)

简单题，涉及Cookie的使用方法

操作方法: 存储-Cookie-session-值

base64解码

```
eyJtb25leSI6IDUwLCAiaGlzdG9yeSI6IFtdfQ==

{"money": 50, "history": []} 
```

服务器端不存储用户信息，依赖前端cookie中的base64数据存储，前端可控，这是极其危险的，会造成数据伪造。

```
{"money": 100, "history": []}

eyJtb25leSI6IDEwMCwgImhpc3RvcnkiOiBbXX0= 
```

替换Value，刷新，购买flag即可。