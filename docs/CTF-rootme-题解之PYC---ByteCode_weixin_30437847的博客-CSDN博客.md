<!--yml
category: 未分类
date: 2022-04-26 14:39:25
-->

# CTF-rootme 题解之PYC - ByteCode_weixin_30437847的博客-CSDN博客

> 来源：[https://blog.csdn.net/weixin_30437847/article/details/99600326](https://blog.csdn.net/weixin_30437847/article/details/99600326)

LINK：[https://www.root-me.org/en/Challenges/Cracking/PYC-ByteCode](https://www.root-me.org/en/Challenges/Cracking/PYC-ByteCode)

使用Pyc的逆向脚本uncompyle6进行反编译，该逆向脚本内容如下：

```
#!/usr/bin/python
# EASY-INSTALL-ENTRY-SCRIPT: 'uncompyle6==3.2.4','console_scripts','uncompyle6'
__requires__ = 'uncompyle6==3.2.4'
import re
import sys
from pkg_resources import load_entry_point

if __name__ == '__main__':
    sys.argv[0] = re.sub(r'(-script\.pyw?|\.exe)?$', '', sys.argv[0])
    sys.exit(
        load_entry_point('uncompyle6==3.2.4', 'console_scripts', 'uncompyle6')()
    ) 
```

 得到反编译结果如下：

```
# uncompyle6 version 3.2.4
# Python bytecode 3.1 (3151)
# Decompiled from: Python 3.7.2 (default, Jan 10 2019, 23:51:51) 
# [GCC 8.2.1 20181127]
# Embedded file name: crackme.py
# Compiled at: 2013-07-02 15:00:05
if __name__ == '__main__':
    print('Welcome to the RootMe python crackme')
    PASS = input('Enter the Flag: ')
    KEY = 'I know, you love decrypting Byte Code !'
    I = 5
    SOLUCE = [57, 73, 79, 16, 18, 26, 74, 50, 13, 38, 13, 79, 86, 86, 87]
    KEYOUT = []
    for X in PASS:
        KEYOUT.append((ord(X) + I ^ ord(KEY[I])) % 255)
        I = (I + 1) % len(KEY)

    if SOLUCE == KEYOUT:
        print('You Win')
    else:
        print('Try Again !')
# okay decompiling ch19.pyc 
```

 介绍一个源代码涉及到的函数：ord()

## 描述

ord() 函数是 chr() 函数（对于8位的ASCII字符串）或 unichr() 函数（对于Unicode对象）的配对函数，它以一个字符（长度为1的字符串）作为参数，返回对应的 ASCII 数值，或者 Unicode 数值，如果所给的 Unicode 字符超出了你的 Python 定义范围，则会引发一个 TypeError 的异常。

### 语法

以下是 ord() 方法的语法:

```
ord(c)
```

### 参数

### 返回值

返回值是对应的十进制整数。

由源代码可以编写出破解脚本如下：

```
#!/usr/bin/env python 
if __name__ == '__main__':
    print('Welcome to the RootMe python crackme')
   # PASS = input('Enter the Flag: ')
    KEY = 'I know, you love decrypting Byte Code !'
    DICT = "abcdefghijklmnopqrstuvwxyz1234567890ABCDEFGHIJKLMNOPQRSTUVWXYZ!@#$%`+^&*(){}[]\";?'\/<>~=:-_.   |,"
    I = 5
    SOLUCE = [57, 73, 79, 16, 18, 26, 74, 50, 13, 38, 13, 79, 86, 86, 87]
    KEYOUT = []
    RESULT = []
    for i in SOLUCE:
        for X in DICT:
            DATA=(ord(X) + I ^ ord(KEY[I])) % 255
            if DATA==i:
                KEYOUT.append(DATA)
                I = (I + 1) % len(KEY)
                RESULT.append(X)
                break
    print(RESULT)

['I', '_', 'h', 'a', 't', 'e', '_', 'R', 'U', 'B', 'Y', '_', '!', '!', '!'] 
```

逆向运算得到password为： I_hate_RUBY_!!!