<!--yml
category: 未分类
date: 2022-04-26 14:53:17
-->

# BUUCTF逆向题练习记录（wp） --（3）WUSTCTF2020&&level1-4已完成_Air_cat的博客-CSDN博客

> 来源：[https://blog.csdn.net/Air_cat/article/details/108276687](https://blog.csdn.net/Air_cat/article/details/108276687)

注：funnyre待我搞懂angr后来解

### WUSTCTF2020-level1

解密嗷

```
hexData = [0,
           198
,           232
,           816
,           200
 ,          1536
  ,         300
   ,        6144
    ,       984
     ,      51200
      ,     570
       ,    92160
        ,   1200
         ,  565248
          , 756
          , 1474560
           ,800
           ,6291456
           ,1782
           ,65536000

           ]
for i in range(1,20):
    if( i & 1):
        print((chr)(hexData[i]>>i),end="")
    else:
        print((chr)((int)(hexData[i]/i)),end="") 
```

ctf2020{d9-dE6-20c}
别忘了前缀改成flag

## level2

这题的简单思路被我标了个震惊题

第二次做，string拉到底的那个瞬间再次被震惊到了

wctf2020{Just_upx_-d}

脱upx壳应该不用人教了吧？在被加upx壳的文件的对应目录下，放upx执行文件，然后upx -d 文件名。

## level3

疯狂明示是b64
然后疯狂暗示变表了
还好只是变表，要是魔改b64会很恶心。

这里魔改一个[大哥](https://blog.csdn.net/qq_42967398/article/details/101778364)的b64变表脚本：

```
 import base64
import string
import array

s = "ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789+/"

def My_base64_encode(inputs):

    bin_str = []
    for i in inputs:
        x = str(bin(ord(i))).replace('0b', '')
        bin_str.append('{:0>8}'.format(x))

    outputs = ""

    nums = 0
    while bin_str:

        temp_list = bin_str[:3]
        if (len(temp_list) != 3):
            nums = 3 - len(temp_list)
            while len(temp_list) < 3:
                temp_list += ['0' * 8]
        temp_str = "".join(temp_list)

        temp_str_list = []
        for i in range(0, 4):
            temp_str_list.append(int(temp_str[i * 6:(i + 1) * 6], 2))

        if nums:
            temp_str_list = temp_str_list[0:4 - nums]

        for i in temp_str_list:
            outputs += s[i]
        bin_str = bin_str[3:]
    outputs += nums * '='
    print("Encrypted String:\n%s " % outputs)

def My_base64_decode(inputs):

    bin_str = []
    for i in inputs:
        if i != '=':
            x = str(bin(s.index(i))).replace('0b', '')
            bin_str.append('{:0>6}'.format(x))

    outputs = ""
    nums = inputs.count('=')
    while bin_str:
        temp_list = bin_str[:4]
        temp_str = "".join(temp_list)

        if (len(temp_str) % 8 != 0):
            temp_str = temp_str[0:-1 * nums * 2]

        for i in range(0, int(len(temp_str) / 8)):
            outputs += chr(int(temp_str[i * 8:(i + 1) * 8], 2))
        bin_str = bin_str[4:]
    print("Decrypted String:\n%s " % outputs)

print()
print("     *************************************")
print("     *    (1)encode         (2)decode    *")
print("     *************************************")
print()

offset = 19
s = list(s)
for i in range(0,9):
    t = s[i]
    s[i] = s[offset-i]
    s[offset-i] = t

num = input("Please select the operation you want to perform:\n")
if (num == "1"):
    input_str = input("Please enter a string that needs to be encrypted: \n")
    My_base64_encode(input_str)
else:
    input_str = input("Please enter a string that needs to be decrypted: \n")
    My_base64_decode(input_str) 
```

（其实就是在主程序里加了个表变换
wctf2020{Base64_is_the_start_of_reverse}

## level4

kali跑一下嗷
然后可以根据已有信息，推测是考遍历算法的基础知识。
已有 的两种可以看出来，分别是中序和后序
然后就是画二叉树咯~画出来前序一推就是flag了

wctf2020{This_IS_A_7reE}