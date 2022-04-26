<!--yml
category: 未分类
date: 2022-04-26 14:46:18
-->

# 初遇z3并与starCTF碰面_黑羽re的博客-CSDN博客

> 来源：[http://blog.csdn.net/m0_38094687/article/details/80113019](http://blog.csdn.net/m0_38094687/article/details/80113019)

# z3的初次使用与*CTF的web题解

## z3的介绍与使用

### 介绍

Z3 是一个微软出品的开源约束求解器，能够解决很多种情况下的给定部分约束条件寻求一组满足条件的解的问题.在CTF中的应用主要在CRYPTO上.

### 安装

pip安装:`pip install z3`
如果发现安装了用不了,可以使用`pip install z3-solver`

### 使用

```
from z3 import *

x = Int('x')
y = Int('y')
solve(x > 2, y < 10, x + 2*y == 7)
```

结果为:

```
[y = 0, x = 7]
```

若无解则`check()=unsat`

```
from z3 import *

x = Int('x')
y = Int('y')
s = Solver()
s.add(x > 2, y < 10, x<3,y>9,x + 2 * y == 8.5)
print s.check()
```

结果为:

```
unsat
```

所以平时在使用脚本时,可加判断:

```
if s.check()==sat:
    print s.model()
```

同时z3包含Or,Not,And,if等逻辑运算符,详见[官方API](http://www.cs.tau.ac.il/~msagiv/courses/asv/z3py/guide-examples.htm)

### 举例

[八皇后问题](https://en.wikipedia.org/wiki/Eight_queens_puzzle)

```
 Q = [ Int('Q_%i' % (i + 1)) for i in range(8) ]

val_c = [ And(1 <= Q[i], Q[i] <= 8) for i in range(8) ]

col_c = [ Distinct(Q) ]

diag_c = [ And(Q[i] - Q[j] != i - j, Q[i] - Q[j] != j - i))
           for i in range(8) for j in range(i) ]

solve(val_c + col_c + diag_c)
```

## *CTF SimpleWeb

说实话,本人解这题的时候正在铁三场外观摩队里大佬操作.地点老远.而且没电源,BCTF同时开赛,那个该死的盲注也把我弄懵了(虽然知道要把’sorry’盲注出来,但不知道怎么让它报错).
因此我一开始想到了js的数组`sort()`问题,纯数字会按照字典序排还是数字大小排,但是没有测试,然后就gg了,浪费了一次宝贵的晋升大佬的机会 :P

### 题目

```
var net = require('net');

flag='fake_flag';

var server = net.createServer(function(socket) {
    socket.on('data', (data) => { 

        ok = true;
        arr = data.toString().split(' ');
        arr = arr.map(Number);
        if (arr.length != 5)
            ok = false;
        arr1 = arr.slice(0);
        arr1.sort();
        for (var i=0; i<4; i++)
            if (arr1[i+1] == arr1[i] || arr[i] < 0 || arr1[i+1] > 127)
                ok = false;
        arr2 = []
        for (var i=0; i<4; i++)
            arr2.push(arr1[i] + arr1[i+1]);
        val = 0;
        for (var i=0; i<4; i++)
            val = val * 0x100 + arr2[i];
        if (val != 0x23332333)
            ok = false;
        if (ok)
            socket.write(flag+'\n');
        else
            socket.write('nope\n');
    });

});
```

### 分析

> 正常思路由题目`val != 0x23332333`可知最后计算要得到`0x23332333`,向上分析看来是迭代累加`x*0x100+y`,所以用`0x23332333`逐次`%0x100`,得到数组`arr2=[35,51,35,51]`.
> 
> 再向上解,发现arr1数组sort后,每两项相加,得到arr2,即`[35,51,35,51]`

1.看题目没要求整数,所以非预期(好歹是个web题)用小数解决
2.javascript的`sort()`方法采用的是字典序,举例`[9,18,32,33]`的结果为`[18,32,33,9]`

### 非预期

找到一组小数解
`[34.75, 114.5, 162.5, 179]`
回推得到
`answer:1 33.75 80.75 81.75 97.25`

### 预期

```
from z3 import *

value = [BitVec("val_%d" % i,32) for i in range(5)]

s = Solver()

for i in range(5):
    s.add(value[i] >= 0)
    s.add(value[i] <= 127)

for i in range(4):
    s.add(Or(And(value[i]<value[i+1],value[i]>=10,value[i+1]>=10),And(value[i+1]<10,value[i]/10<value[i+1]),And(value[i]<10,value[i]<=value[i+1]/10)))

s.add(((((value[0] + value[1]) * 0x100 + (value[1] + value[2])) * 0x100 +
        (value[2] + value[3])) * 0x100 + (value[3] + value[4])) == 0x23332333)

if s.check() != sat:
    print "unsat"
else:
    print  s.model()
```

得到结果`[val_3 = 4, val_2 = 31, val_0 = 15, val_1 = 20, val_4 = 47]`

## 相关链接

z3语法举例: [https://nen9ma0.github.io/2018/03/14/z3py/](https://nen9ma0.github.io/2018/03/14/z3py/)
z3安装报错问题: [https://github.com/Z3Prover/z3/issues/904](https://github.com/Z3Prover/z3/issues/904)
simpleweb预期解: [https://github.com/balsn/ctf_writeup/tree/master/20180421-*ctf#simpleweb-how2hack](https://github.com/balsn/ctf_writeup/tree/master/20180421-%2actf#simpleweb-how2hack)
simpleweb非预期解: [https://ctftime.org/writeup/9835](https://ctftime.org/writeup/9835)