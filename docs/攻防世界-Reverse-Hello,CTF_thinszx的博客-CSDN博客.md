<!--yml
category: 未分类
date: 2022-04-26 14:54:04
-->

# 攻防世界 Reverse Hello,CTF_thinszx的博客-CSDN博客

> 来源：[https://blog.csdn.net/thinszx/article/details/104662371](https://blog.csdn.net/thinszx/article/details/104662371)

# ida反编译

下载下来是一个exe，直接拖到ida然后f5查看源码

```
int __cdecl main(int argc, const char **argv, const char **envp)
{
  signed int v3; 
  char v4; 
  int result; 
  int v6; 
  int v7; 
  char v8; 
  char v9[20]; 
  char v10; 
  __int16 v11; 
  char v12; 
  char v13; 

  strcpy(&v13, "437261636b4d654a757374466f7246756e"); 
  while ( 1 )
  {
    memset(&v10, 0, 0x20u); 
    v11 = 0;
    v12 = 0;
    sub_40134B(aPleaseInputYou, v6); 
    scanf(aS, v9); 
    if ( strlen(v9) > 0x11 ) 
      break;
    v3 = 0;
    do
    {
      v4 = v9[v3];
      if ( !v4 ) 
        break;
      sprintf(&v8, asc_408044, v4); 
      strcat(&v10, &v8); 
      ++v3;
    }
    while ( v3 < 17 );
    if ( !strcmp(&v10, &v13) ) 
      sub_40134B(aSuccess, v7); 
    else
      sub_40134B(aWrong, v7);
  }
  sub_40134B(aWrong, v7); 
  result = stru_408090._cnt-- - 1;
  if ( stru_408090._cnt < 0 )
    return _filbuf(&stru_408090);
  ++stru_408090._ptr;
  return result;
} 
```

里面涉及到了几个c库的基本函数，之前不是很懂，尤其是本题中最重要的`sprintf()`，在[这个博客](https://blog.csdn.net/thinszx/article/details/104662256)做了一下笔记，`memset()`函数见[这里](https://www.runoob.com/cprogramming/c-function-memset.html)

# 解题

先从上到下看一遍，大致流程如下：

1.  将`"437261636b4d654a757374466f7246756e"`存入`v13`中
2.  读入用户输入的字符串到`v9`
3.  `sprintf()`处理输入的字符串，并将处理后的字符串存入`v8`中，而后`v8`中的值又被复制给`v10`
4.  比较`v13`与`v10`中的值是否相同

在整道题中，关键的一个函数就是`sprintf()`，这个函数处理了用户输入的字符串，在这里`sprintf()`的三个参数分别为**一个用来存放字符串的指针**，**x%**，**用户输入字符串中挨个取出的字母**，其中的`x%`是一个格式化参数，它可以将后面的字符转成16进制的对应的数字，因此这道题的flag，也就是输入的值，就是把上面那一堆数转成字符串，即`CrackMeJustForFun`

# 学习到了啥

1.  `sprintf()`函数的用法
2.  `memset()`函数的作用，可以将指针存储的字符串的前n位**全部**替换成想要的字符，**位数不变**
3.  `sub_40134B()`用来输出字符串