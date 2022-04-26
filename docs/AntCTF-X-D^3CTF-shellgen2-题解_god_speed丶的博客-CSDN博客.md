<!--yml
category: 未分类
date: 2022-04-26 14:40:48
-->

# AntCTF X D^3CTF shellgen2 题解_god_speed丶的博客-CSDN博客

> 来源：[https://blog.csdn.net/qq_38677814/article/details/115037481](https://blog.csdn.net/qq_38677814/article/details/115037481)

### 1\. 前言

本题是一个misc题目，但其中涉及了一些PHP的知识，巧妙的用到了一些算法，笔者将分析本题的设计思想，并给出一个“较”完美的算法。

先来看题目，我们需要给一个php写的webshell，题目会输入一串随机小写字母串,如字符串S：`skadkehqasbjkaskad`，运行该webshell要输出一模一样的字符串S。

### 2\. 题目描述

题目的waf如下：

```
def waf(phpshell):
    if not phpshell.startswith(b'<?php'):
        return True
    phpshell = phpshell[6:]
    for c in phpshell:
        if c not in b'0-9$_;+[].=<?>':
            return True
        else:
            print(chr(c), end='')
    return False 
```

简单看下来，我们能用的字符很有限，数字就两个`0`,`9`,字母一个都没有，几番搜寻，我们找到了p牛曾经写的一篇文章《[一些不包含数字和字母的webshell](https://www.leavesongs.com/PENETRATION/webshell-without-alphanum.html)》，通读下来，收获良多。

回归到题目本身，这题需要有一个比较短的webshell，需要一个优秀的算法来做这件事情。

既然要输出字母，那我们就得构造字母了，p牛的文章里给了如下方法：

```
php > var_dump([[].[]]);
array(1) {
  [0]=>
  string(10) "ArrayArray"
}
php > var_dump([[].[]][0]);
string(10) "ArrayArray"
php > var_dump([[].[]][0][1]);
string(1) "r"
php > var_dump([[].[]][0][3]);
string(1) "a" 
```

简直nice！

那怎么得到其余字符呢？用递增的办法

```
php > $test = [[].[]][0][3];
php > echo $test;
a
php > $test++;
php > echo $test;
b
php > $test++;
php > echo $test;
c 
```

递减可不可以呢？ 不可以的。

```
php > $test = 'h';
php > $test--;
php > echo $test;
h
php > $test--;
php > echo $test;
h 
```

如何在限制条件下得到a? 要尽可能的短。

这里就不卖关子了,Array拼接大法。

```
php > var_dump([[].[].[].[]][0][9+9]);
string(1) "a" 
```

### 3\. 暴力的算法

看完上面这些，写代码就很容易了，给每个字母一个变量名，利用`a`递增得到b~z，最后拼接输出即可。

```
target=input()
def gen(ch):
    return (ch - ord('a') + 1) * '_'

payload = "<?php ${}=[[].[].[].[]][0][9+9];".format(gen(ord('a')))

for ch in range(ord('b'), ord('z') + 1):
    payload += "${}=${};".format(gen(ch), gen(ch - 1))
    payload += "${}++;".format(gen(ch))

ans = []
for c in target:
    ans.append("${}".format(gen(ord(c))))

payload += "$_={};".format(".".join(ans))
payload += "?><?=$_;"
print(payload)

with open('1.php','w') as f:
    f.write(payload) 
```

### 4\. 优化思想

##### 4.1 节省变量个数

我们有好多字符没用到，我给你一堆aaaaaaaaaa，你还需要拼接其他字符b~z吗？

##### 4.2 Array中剩下的字母r、y怎么用起来

```
a = "${}=[[].[].[].[]][0][9+9];"

r = "${}=[[].[]][0][0==0];"

y = "${}=[[].[]][0][9];" 
```

利用y直接构造z，理论上会更加方便

##### 4.3 变量名只能一堆下划线吗

显然不是的

```
$_0
$_09
$_9 
```

这些都是非常简短的变量名，类似三进制

##### 4.4 变量名怎么分配

这个主要体现在最后拼接的时候，aaaaaaaaaz,我们自然希望给a分配`$_`,而不是`$_0`

一个简单的思想就是根据频率分配，高频的分配短一些的变量名。考虑到有两个步骤，构造和拼接，因此比较好的办法是将两个步骤统一起来考虑。python可以采用如下写法，非常优雅。

```
>>> s = "${a};${b};${c}"
>>> s.format(**{'a':'2333','b':'6666','c':'anquanke'})
'$2333;$6666;$anquanke' 
```

### 5\. 编写代码

思想：暴力枚举是否需要加入a、r、y, 频度分析节省payload长度，最后取最小长度

```
import itertools, string
target = input()
name_list = []
for i in range(4):
    name_list += ["_" + "".join(x) for x in itertools.product(['_', '0', '9'], repeat=i)]

def gen_from_last(ch, last_ch):
    payload = "${%s}=${%s};" % (ch, last_ch)
    payload += ("${%s}++;" % ch )* (ord(ch) - ord(last_ch))
    return payload

def gen_from_construct(ch):
    if ch == 'a':
        return "${%s}=[[].[].[].[]][0][9+9];" % ch
    if ch == 'r':
        return "${%s}=[[].[]][0][0==0];" % ch
    if ch == 'y':
        return "${%s}=[[].[]][0][9];" % ch

def generate_str(need_a, need_r, need_y):
    payload = "<?php "
    we_have = set()
    if need_a: we_have.add('a')
    if need_r: we_have.add('r')
    if need_y: we_have.add('y')
    payload += gen_from_construct('a') if need_a else ""
    payload += gen_from_construct('r') if need_r else ""
    payload += gen_from_construct('y') if need_y else ""
    alpha_order = list(set(target) | we_have)
    alpha_order.sort()

    last_ch = None

    for alpha in alpha_order:

        if alpha in we_have: 
            last_ch = alpha
            continue

        if last_ch is None:
            return None
        we_have.add(alpha)
        payload += gen_from_last(alpha, last_ch)
        last_ch = alpha

    ans = []
    for c in target:
        ans.append("${%s}" % c)
    payload +="?><?={};".format(".".join(ans))

    freq_list = []
    for c in string.ascii_lowercase:
        freq_list.append((c, payload.count("${%s}" % c)))
    freq_list.sort(key=lambda x:x[1], reverse=True)

    record = {}
    for idx, pair in enumerate(freq_list):
        record[pair[0]] = name_list[idx]
    return payload.format(**record)

MIN = 1 << 233
good_payload = None
for need_a in range(2):
    for need_r in range(2):
        for need_y in range(2):
            payload = generate_str(need_a,need_r,need_y)
            if payload and len(payload) < MIN:
                MIN = len(payload)
                good_payload = payload

print(good_payload)
with open('1.php','w') as f:
    f.write(good_payload) 
```

### 6\. 测试样例

```
abcdefghijklmnopqrstuvwxyzzyxwvutsrqponmlkjihgfedcbazyxwvutsrqponmlkjihgfedcbazzzzzzzzzzzaaaaaaaaaaaaaaaa
aaaaaaaazzzzzzz
aaaaaabbbbbbbddddddddd
ddddddddaaaaaaaaaccccccccc
zzzzzzzzzzyyyyyzzzzzzzzz
yyyyyyyyyyyyy
rrrrrrr
aaaaayyyyyyyyy
aaaarrrryyyyyyrrrryyyyyy
zzzzzzzzzzyyyyyyy 
```

对拍脚本

```
#!/bin/bash
while read line
do
    echo -n $line > input
    python3 exp.py < input
    php 1.php > output
    if (diff input output) then
      echo "Accepted"
    else
      echo "Wrong Answer"
      break
    fi
    sleep 0.1;
done 
```

### 7\. 后记

php warning会影响本题的输出，这也是为什么我们最后花许多功夫写成这副样子，起初的几个优化一直过不了。

```
php > echo _.[];

Warning: Use of undefined constant _ - assumed '_' (this will throw an Error in a future version of PHP) in php shell code on line 1
_Array 
```

本题的最终实现离不开和队友的激烈讨论，是一道非常好的web与算法的结合题。