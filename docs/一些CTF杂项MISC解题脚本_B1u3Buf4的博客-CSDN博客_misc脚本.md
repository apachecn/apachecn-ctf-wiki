<!--yml
category: 未分类
date: 2022-04-26 14:54:33
-->

# 一些CTF杂项MISC解题脚本_B1u3Buf4的博客-CSDN博客_misc脚本

> 来源：[https://blog.csdn.net/qq_15174755/article/details/111099396](https://blog.csdn.net/qq_15174755/article/details/111099396)

总结发布一些简单CTF中MISC常用的解题脚本，基于python3。目前有二维码解码、mp3隐写、像素提取。

# 二维码解码

通过pip安装zxing库。

```
def qr_decode():
    """
    二维码解码

    :return:
    """
    import zxing

    reader = zxing.BarCodeReader()
    barcode = reader.decode('/home/QR_code.png')
    print(barcode.parsed) 
```

# 多进程解密mp3隐写

主要针对MP3Stego需要暴力枚举密码的情况，题目限定密码是1-30000的数字。

```
import multiprocessing
import os

os.chdir('D:/tester/MP3Stego_1_1_19/MP3Stego')

def cmdCall(num):
    """
    cmd执行MP3Stego的破解 生成对应的数字临时文件和对应的结果txt

    :param num:
    :return:
    """
    import subprocess
    cmd = f"Decode.exe -X -P {num} svega_stego.mp3 {num} {num}"
    subprocess.call(cmd, shell=True)

if __name__ == '__main__':
    for i in range(30000):
        pool = multiprocessing.Pool(4)
        pool.apply_async(cmdCall, [i, ])
        pool.close() 
```

# 读取图片中的像素

读取图片中的部分像素，原题是左对角线的一些像素值组成的字符。

```
def readImageValue():
    """
    读取图片的像素值脚本。
    """
    from PIL import Image

    img = Image.open("/home/out.png")

    result = []
    for i in range(0, 42): 
        data = (img.getpixel((i, i)))  

        result.append(chr(data[2]))
    print(''.join(result)) 
```