<!--yml
category: 未分类
date: 2022-04-26 14:19:45
-->

# 南邮CTF平台-Vigenere题解（穷举法）_无意中流逝的博客-CSDN博客

> 来源：[https://blog.csdn.net/jakekong/article/details/79884365](https://blog.csdn.net/jakekong/article/details/79884365)

题目链接：[http://ctf.nuptzj.cn/challenges#Vigenere](http://ctf.nuptzj.cn/challenges#Vigenere)

题目提示使用重合因子，但是我尝试失败了

我从文章[https://blog.csdn.net/qq_31344951/article/details/77934717](https://blog.csdn.net/qq_31344951/article/details/77934717)受到启发，明文必定是可见字符，然后对密钥进行穷举

以下为解题思路：

假设加密者使用的密钥为vigenerekey，长度为keylen，密文为字符串arr

那么字符串subarr0=arr[0]+arr[keylen]+arr[keylen*2]……是被vigenerekey[0]解密的内容

依次类推，subarr1=arr[1]+arr[1+keylen]+arr[1+keylen*2]……是被vigenerekey[1]解密的内容

subarr2=arr[2]+arr[2+keylen]+arr[2+keylen*2]……是被vigenerekey[2]解密的内容

……

按照这种分割方法，可以把密文arr分割成keylen份，每份都被密钥vigenerekey的同一位解密

比如：

密钥为WXYZ，密文为abcdefghijklmnop

那么解密过程为

| a | b | c | d | e | f | g | h | i | j | k | l | m | n | o | p |
| W | X | Y | Z | W | X | Y | Z | W | X | Y | Z | W | X | Y | Z |

subarr0=aeim

subarr1=bfjn

以此类推

对每个vigenerekey[index]，穷举vigenerekey[index]的值（范围是0x00~0xFF），并与对应的subarr里的内容解码，找到能将该subarr所有内容解为可见字符的所有可能值

python代码如下：

```
def findindexkey(subarr):#该函数可以找出将密文subarr解密成可见字符的所有可能值
    visiable_chars=[]#可见字符
    for x in range(32,126):
        visiable_chars.append(chr(x))
    #print(vi)
    test_keys=[]#用于测试密钥
    ans_keys=[]#用于结果的返回
    for x in range(0x00,0xFF):# 枚举密钥里所有的值
        test_keys.append(x)
        ans_keys.append(x)
    for i in test_keys:#对于0x00~0xFF里的每一个数i和subarr里的每个值s异或
        for s in subarr:
            if chr(s^i) not in visiable_chars:#用i解密s，如果解密后明文不是可见字符，说明i不是密钥
                ans_keys.remove(i)#去掉ans_keys里测试失败的密钥
                break
    #print(ans_keys)
    return ans_keys

strmi='F96DE8C227A259C87EE1DA2AED57C93FE5DA36ED4EC87EF2C63AAE5B9A7EFFD673BE4ACF7BE8923C\
AB1ECE7AF2DA3DA44FCF7AE29235A24C963FF0DF3CA3599A70E5DA36BF1ECE77F8DC34BE129A6CF4D126BF\
5B9A7CFEDF3EB850D37CF0C63AA2509A76FF9227A55B9A6FE3D720A850D97AB1DD35ED5FCE6BF0D138A84C\
C931B1F121B44ECE70F6C032BD56C33FF9D320ED5CDF7AFF9226BE5BDE3FF7DD21ED56CF71F5C036A94D96\
3FF8D473A351CE3FE5DA3CB84DDB71F5C17FED51DC3FE8D732BF4D963FF3C727ED4AC87EF5DB27A451D47E\
FD9230BF47CA6BFEC12ABE4ADF72E29224A84CDF3FF5D720A459D47AF59232A35A9A7AE7D33FB85FCE7AF5\
923AA31EDB3FF7D33ABF52C33FF0D673A551D93FFCD33DA35BC831B1F43CBF1EDF67F0DF23A15B963FE5DA\
36ED68D378F4DC36BF5B9A7AFFD121B44ECE76FEDC73BE5DD27AFCD773BA5FC93FE5DA3CB859D26BB1C63C\
ED5CDF3FE2D730B84CDF3FF7DD21ED5ADF7CF0D636BE1EDB79E5D721ED57CE3FE6D320ED57D469F4DC27A8\
5A963FF3C727ED49DF3FFFDD24ED55D470E69E73AC50DE3FE5DA3ABE1EDF67F4C030A44DDF3FF5D73EA250\
C96BE3D327A84D963FE5DA32B91ED36BB1D132A31ED87AB1D021A255DF71B1C436BF479A7AF0C13AA14794'
arr=[]#密文，每个元素为字符的ascii码
for x in range(0,len(strmi),2):
    arr.append(int(strmi[x:2+x],16))

for keylen in range(1,14):#枚举密钥的长度1~14
    for index in range(0,keylen):#对密钥里的第index个进行测试
        subarr=arr[index::keylen]#每隔keylen长度提取密文的内容，提取出来的内容都被密文的第index个加密
        ans_keys=findindexkey(subarr)#找出密钥中第index个的可能的值
        print('keylen=',keylen,'index=',index,'keys=',ans_keys)
        if ans_keys:#如果密钥第index个有可能存在，尝试用密钥的index个去解密文
            ch=[]
            for x in ans_keys:
                ch.append(chr(x^subarr[0]))
            print(ch)
```

此处输出的结果为

```
keylen= 1 index= 0 keys= []
keylen= 2 index= 0 keys= []
keylen= 2 index= 1 keys= []
keylen= 3 index= 0 keys= []
keylen= 3 index= 1 keys= []
keylen= 3 index= 2 keys= []
...省略中间的输出...
keylen= 7 index= 0 keys= [168, 169, 174, 175, 178, 179, 184, 185, 186, 187, 190, 191]
['Q', 'P', 'W', 'V', 'K', 'J', 'A', '@', 'C', 'B', 'G', 'F']
keylen= 7 index= 1 keys= [10, 11, 26, 27, 28, 29, 30, 31, 66, 67, 68, 69, 70, 71, 72, 73, 74, 75, 76, 77, 80, 81, 82, 83, 84, 85, 86, 87, 88, 89, 90, 91, 92, 93, 94, 95]
['g', 'f', 'w', 'v', 'q', 'p', 's', 'r', '/', '.', ')', '(', '+', '*', '%', '$', "'", '&', '!', ' ', '=', '<', '?', '>', '9', '8', ';', ':', '5', '4', '7', '6', '1', '0', '3', '2']
keylen= 7 index= 2 keys= [132, 133, 144, 145, 146, 147, 148, 149, 192, 193, 194, 195, 196, 197, 198, 199, 200, 201, 202, 203, 204, 205, 208, 209, 210, 211, 212, 213, 214, 215, 216, 217, 218, 219, 220, 221, 222, 223]
['l', 'm', 'x', 'y', 'z', '{', '|', '}', '(', ')', '*', '+', ',', '-', '.', '/', ' ', '!', '"', '#', '$', '%', '8', '9', ':', ';', '<', '=', '>', '?', '0', '1', '2', '3', '4', '5', '6', '7']
keylen= 7 index= 3 keys= [166, 167, 176, 177, 178, 179, 180, 181, 182, 183]
['d', 'e', 'r', 's', 'p', 'q', 'v', 'w', 't', 'u']
keylen= 7 index= 4 keys= [2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23, 24, 25, 26, 27, 28, 29, 30, 31, 80, 81, 82, 83, 86, 87]
['%', '$', '#', '"', '!', ' ', '/', '.', '-', ',', ')', '(', '7', '6', '5', '4', '3', '2', '1', '0', '?', '>', '=', '<', ';', ':', '9', '8', 'w', 'v', 'u', 't', 'q', 'p']
keylen= 7 index= 5 keys= [128, 129, 130, 131, 132, 133, 134, 135, 136, 137, 138, 139, 140, 141, 142, 143, 144, 145, 148, 149, 150, 151, 152, 153, 154, 155, 156, 157, 158, 159, 200, 201, 204, 205, 206, 207, 216, 217]
['"', '#', ' ', '!', '&', "'", '$', '%', '*', '+', '(', ')', '.', '/', ',', '-', '2', '3', '6', '7', '4', '5', ':', ';', '8', '9', '>', '?', '<', '=', 'j', 'k', 'n', 'o', 'l', 'm', 'z', '{']
keylen= 7 index= 6 keys= [58, 59, 60, 61, 62, 63]
['c', 'b', 'e', 'd', 'g', 'f']
keylen= 8 index= 0 keys= []
...省略中间的输出...
keylen= 13 index= 12 keys= []
```

可以看出，keylen=7时有结果

不妨缩小一下范围，假设明文只有字母、数字、空格、逗号和句号，再进行穷举

该部分的python代码如下：

```
import string
def findindexkey2(subarr):#再造一个函数筛选密钥
    test_chars=string.ascii_letters+string.digits+','+'.'+' '#将检查的字符改为英文+数字+逗号+句号+空格
    #print(test_chars)
    test_keys=[]#用于测试密钥
    ans_keys=[]#用于结果的返回
    for x in range(0x00,0xFF):# 枚举密钥里所有的值
        test_keys.append(x)
        ans_keys.append(x)
    for i in test_keys:#对于0x00~0xFF里的每一个数i和substr里的每个值s异或
        for s in subarr:
            if chr(s^i) not in test_chars:#用i解密s，如果解密后不是英文、数字、逗号、句号、空格，说明i不是密钥
                ans_keys.remove(i)#去掉ans_keys里测试失败的密钥
                break
    #print(ans_keys)
    return ans_keys

vigenerekeys=[]#维基尼尔密码的密钥
for index in range(0,7):#已经知道密钥长度是7
    subarr=arr[index::7]
    vigenerekeys.append(findindexkey2(subarr))
print(vigenerekeys) 
```

该部分的输出结果：

```
[[186], [31], [145], [178], [83], [205], [62]]
```

很幸运，结果已经是唯一的了，这就是密钥

我们再使用这个密钥去解密密文，Python的代码如下：

```
ming=''
for i in range(0,len(arr)):
    ming=ming+chr(arr[i]^vigenerekeys[i%7][0])
print(ming) 
```

解密的结果为：

Cryptography is the practice and study of techniques for, among other things, secure communication in the presence of attackers. Cryptography has been used for hundreds, if not thousands, of years, but traditional cryptosystems were designed and evaluated in a fairly ad hoc manner. For example, the Vigenere encryption scheme was thought to be secure for decades after it was invented, but we now know, and this exercise demonstrates, that it can be broken very easily.

文章已经解密出来了

按照题目的要求，flag为最后6个单词，不含标点，因此flag：nctf{it can be broken very easily}

比较遗憾的是，我解决这题的是偶并没有用到重合因子，而是使用穷举。我想题目设计的本意是让解题者使用字母出现频率之类的统计学原理进行解题吧

以下是整段程序源码：

```
def findindexkey(subarr):#该函数可以找出将密文subarr解密成可见字符的所有可能值
    visiable_chars=[]#可见字符
    for x in range(32,126):
        visiable_chars.append(chr(x))
    #print(vi)
    test_keys=[]#用于测试密钥
    ans_keys=[]#用于结果的返回
    for x in range(0x00,0xFF):# 枚举密钥里所有的值
        test_keys.append(x)
        ans_keys.append(x)
    for i in test_keys:#对于0x00~0xFF里的每一个数i和subarr里的每个值s异或
        for s in subarr:
            if chr(s^i) not in visiable_chars:#用i解密s，如果解密后明文不是可见字符，说明i不是密钥
                ans_keys.remove(i)#去掉ans_keys里测试失败的密钥
                break
    #print(ans_keys)
    return ans_keys

strmi='F96DE8C227A259C87EE1DA2AED57C93FE5DA36ED4EC87EF2C63AAE5B9A7EFFD673BE4ACF7BE8923C\
AB1ECE7AF2DA3DA44FCF7AE29235A24C963FF0DF3CA3599A70E5DA36BF1ECE77F8DC34BE129A6CF4D126BF\
5B9A7CFEDF3EB850D37CF0C63AA2509A76FF9227A55B9A6FE3D720A850D97AB1DD35ED5FCE6BF0D138A84C\
C931B1F121B44ECE70F6C032BD56C33FF9D320ED5CDF7AFF9226BE5BDE3FF7DD21ED56CF71F5C036A94D96\
3FF8D473A351CE3FE5DA3CB84DDB71F5C17FED51DC3FE8D732BF4D963FF3C727ED4AC87EF5DB27A451D47E\
FD9230BF47CA6BFEC12ABE4ADF72E29224A84CDF3FF5D720A459D47AF59232A35A9A7AE7D33FB85FCE7AF5\
923AA31EDB3FF7D33ABF52C33FF0D673A551D93FFCD33DA35BC831B1F43CBF1EDF67F0DF23A15B963FE5DA\
36ED68D378F4DC36BF5B9A7AFFD121B44ECE76FEDC73BE5DD27AFCD773BA5FC93FE5DA3CB859D26BB1C63C\
ED5CDF3FE2D730B84CDF3FF7DD21ED5ADF7CF0D636BE1EDB79E5D721ED57CE3FE6D320ED57D469F4DC27A8\
5A963FF3C727ED49DF3FFFDD24ED55D470E69E73AC50DE3FE5DA3ABE1EDF67F4C030A44DDF3FF5D73EA250\
C96BE3D327A84D963FE5DA32B91ED36BB1D132A31ED87AB1D021A255DF71B1C436BF479A7AF0C13AA14794'
arr=[]#密文，每个元素为字符的ascii码
for x in range(0,len(strmi),2):
    arr.append(int(strmi[x:2+x],16))

for keylen in range(1,14):#枚举密钥的长度1~14
    for index in range(0,keylen):#对密钥里的第index个进行测试
        subarr=arr[index::keylen]#每隔keylen长度提取密文的内容，提取出来的内容都被密文的第index个加密
        ans_keys=findindexkey(subarr)#找出密钥中第index个的可能的值
        print('keylen=',keylen,'index=',index,'keys=',ans_keys)
        if ans_keys:#如果密钥第index个有可能存在，尝试用密钥的index个去解密文
            ch=[]
            for x in ans_keys:
                ch.append(chr(x^subarr[0]))
            print(ch)
#运行到这里，观察输出可以发现，密钥长度为7时有解
print('###############')
import string
def findindexkey2(subarr):#再造一个函数筛选密钥
    test_chars=string.ascii_letters+string.digits+','+'.'+' '#将检查的字符改为英文+数字+逗号+句号+空格
    #print(test_chars)
    test_keys=[]#用于测试密钥
    ans_keys=[]#用于结果的返回
    for x in range(0x00,0xFF):# 枚举密钥里所有的值
        test_keys.append(x)
        ans_keys.append(x)
    for i in test_keys:#对于0x00~0xFF里的每一个数i和substr里的每个值s异或
        for s in subarr:
            if chr(s^i) not in test_chars:#用i解密s，如果解密后不是英文、数字、逗号、句号、空格，说明i不是密钥
                ans_keys.remove(i)#去掉ans_keys里测试失败的密钥
                break
    #print(ans_keys)
    return ans_keys

vigenerekeys=[]#维基尼尔密码的密钥
for index in range(0,7):#已经知道密钥长度是7
    subarr=arr[index::7]
    vigenerekeys.append(findindexkey2(subarr))
print(vigenerekeys)#输出的是[[186], [31], [145], [178], [83], [205], [62]].

print("#########")
ming=''
for i in range(0,len(arr)):
    ming=ming+chr(arr[i]^vigenerekeys[i%7][0])
print(ming)
```