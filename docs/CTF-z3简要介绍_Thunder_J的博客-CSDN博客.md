<!--yml
category: 未分类
date: 2022-04-26 14:53:58
-->

# CTF-z3简要介绍_Thunder_J的博客-CSDN博客

> 来源：[https://blog.csdn.net/charlesgodx/article/details/89931468](https://blog.csdn.net/charlesgodx/article/details/89931468)

# 介绍

Z3是微软研究院的一个定理验证器。它是由麻省理工学院授权的，常用来解一些方程组，在做一些CTF逆向题目的时候很有用，下面就简单介绍一下这个工具。

# 下载

官网：[https://github.com/Z3Prover/z3](https://github.com/Z3Prover/z3)

这里讲Linux下的安装，执行下面的命令即可，还有很多教程说去官网下载源码安装，没有试过感觉麻烦了些，实在安装不了就用docker

```
sudo pip install z3-solver 
```

# 例子

```
(angr) angr@9fceed56f09e:~$ python
Python 3.6.7 (default, Oct 22 2018, 11:32:17) 
[GCC 8.2.0] on linux
Type "help", "copyright", "credits" or "license" for more information.
>>> from z3 import*
>>> x,y,z=BitVecs('x y z',8) 
>>> s=Solver() 
>>> s.add(x^y&z==12) 
>>> s.add(y&x>>3==3)
>>> s.add(z^y==4)
>>> s.check() 
sat
>>> s.model() 
[z = 19, y = 23, x = 31]
>>> 
```

常用的数据类型如下:

`BitVec` ：至特定大小的数据类型

```
BitVec("x",32)对应C语言中的int => 4个byte
BitVec("x",8)对应C语言中的char => 1个byte 
```

`Int`：整型，`Real`：有理数，`Bool`：布尔类型，`Array`：数组

**CTF demo**

放一个demo做记录以后可能会用到

```
from z3 import*
input = [BitVec('input %d'%i,8)for i in range(32)] 

s = Solver()
s.add(...)
if s.check()!=sat:
	print 'unset'
else:
	m = s.model()
	print m
	print repr("".join([chr(m[input[i]].as_long())for i in range(32)])) 
```

whaleCTF有一道类似的题目，下面有题目和答案的链接
[https://github.com/UnnameBao/My_ctf_path/tree/master/blog/I_Hate_Math](https://github.com/UnnameBao/My_ctf_path/tree/master/blog/I_Hate_Math)

# 总结

更多详细的用法当然是看官网的API定义了，CTF中一般解的方程题不会太难，所以不必太深究其原理