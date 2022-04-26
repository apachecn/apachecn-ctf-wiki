<!--yml
category: 未分类
date: 2022-04-26 14:34:35
-->

# BUUCTF-basic——Linux labs题解wp_东方黑手的博客-CSDN博客

> 来源：[https://blog.csdn.net/weixin_52653109/article/details/116381659](https://blog.csdn.net/weixin_52653109/article/details/116381659)

basic算是一类入门网安操作测试。
这题主要考察Linux的——ssh（远程登入）指令
![在这里插入图片描述](img/de55a1e11242293147759b0f5d19e2f1.png)
ssh命令指导参考链接：https://www.jb51.net/article/115461.htm

根据格式，结合题目给的端口
![在这里插入图片描述](img/aeb05b2c469ef7b7cce2f2cfa71e0f58.png)
登入后，这个填**yes**
![在这里插入图片描述](img/71bcecfc02b23ef16d5490b5ab727d03.png)
然后就最基本的操作指令了。

```
ls / 
```

![在这里插入图片描述](img/0a9e3bbbed87ab6e39d534f71c5f3c8f.png)

```
cat /flag.txt 
```

![在这里插入图片描述](img/86a3ca5fdb2b94849fce816721647438.png)
Linux还是要好好学