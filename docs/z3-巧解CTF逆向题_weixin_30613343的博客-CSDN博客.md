<!--yml
category: 未分类
date: 2022-04-26 14:39:08
-->

# z3 巧解CTF逆向题_weixin_30613343的博客-CSDN博客

> 来源：[https://blog.csdn.net/weixin_30613343/article/details/97154205](https://blog.csdn.net/weixin_30613343/article/details/97154205)

# z3 巧解逆向题

题目下载链接:[http://reversing.kr/download.php?n=7](http://reversing.kr/download.php?n=7)

这次实验的题目为Reversing.kr网站中的一道题目。

题目要求:

```
ReversingKr KeygenMe

Find the Name when the Serial is 76876-77776
This problem has several answers.

Password is ***p
```

这是一道典型的*用户名-序列号*形式的题目，序列号已经给出，且用户名的最后一位为p。

# z3 求解器是什么？

```
z3是由微软公司开发的一个优秀的SMT求解器（也就定理证明器），它能够检查逻辑表达式的可满足性。
```

通俗讲，就是解方程。比如使用z3解二元一次方程：

```
x-y == 3
3x-8y == 4
```

z3代码如下:
ipython 交互

```
In [130]: from z3 import *

In [131]: x = Int('x')

In [132]: y = Int('y')

In [133]: solver = Solver()

In [134]: solver.add(x-y == 3)

In [135]: solver.add(3*x-8*y == 4)

In [136]: solver.check()
Out[136]: sat

In [137]: solver.model()
Out[137]: [y = 1, x = 4] 
```

z3 难道就只能用来解小学方程吗？当然不是！来看题。

# 0x0 定位

界面有两个输入框，无按钮，直接对GetWindowTextW下断后两次向上回溯即可到达核心逻辑函数，下面为该函数的代码。

```
signed int __stdcall sub_FA1740(int a1)
{
  int v1; // edi
  int v3; // esi
  int v4; // esi
  __int16 v5; // bx
  unsigned __int8 v6; // al
  unsigned __int8 v7; // ST2C_1
  unsigned __int8 v8; // al
  unsigned __int8 v9; // bl
  wchar_t *v10; // eax
  __int16 v11; // di
  wchar_t *v12; // eax
  __int16 v13; // di
  wchar_t *v14; // eax
  __int16 v15; // di
  wchar_t *v16; // eax
  __int16 v17; // di
  wchar_t *v18; // eax
  __int16 v19; // di
  unsigned __int8 v20; // al
  unsigned __int8 v21; // ST2C_1
  unsigned __int8 v22; // al
  unsigned __int8 v23; // bl
  wchar_t *v24; // eax
  __int16 v25; // di
  wchar_t *v26; // eax
  __int16 v27; // di
  wchar_t *v28; // eax
  __int16 v29; // di
  wchar_t *v30; // eax
  __int16 v31; // di
  wchar_t *v32; // eax
  __int16 v33; // si
  unsigned __int8 v34; // [esp+10h] [ebp-28h]
  unsigned __int8 v35; // [esp+10h] [ebp-28h]
  unsigned __int8 v36; // [esp+11h] [ebp-27h]
  unsigned __int8 v37; // [esp+11h] [ebp-27h]
  unsigned __int8 v38; // [esp+13h] [ebp-25h]
  unsigned __int8 v39; // [esp+13h] [ebp-25h]
  unsigned __int8 v40; // [esp+14h] [ebp-24h]
  unsigned __int8 v41; // [esp+14h] [ebp-24h]
  unsigned __int8 v42; // [esp+19h] [ebp-1Fh]
  unsigned __int8 v43; // [esp+19h] [ebp-1Fh]
  unsigned __int8 v44; // [esp+1Ah] [ebp-1Eh]
  unsigned __int8 v45; // [esp+1Ah] [ebp-1Eh]
  unsigned __int8 v46; // [esp+1Bh] [ebp-1Dh]
  unsigned __int8 v47; // [esp+1Bh] [ebp-1Dh]
  unsigned __int8 v48; // [esp+1Ch] [ebp-1Ch]
  unsigned __int8 v49; // [esp+1Ch] [ebp-1Ch]
  int username; // [esp+20h] [ebp-18h]
  int serial; // [esp+24h] [ebp-14h]
  char v52; // [esp+28h] [ebp-10h]
  int v53; // [esp+34h] [ebp-4h]

  ATL::CStringT<wchar_t,StrTraitMFC_DLL<wchar_t,ATL::ChTraitsCRT<wchar_t>>>::CStringT<wchar_t,StrTraitMFC_DLL<wchar_t,ATL::ChTraitsCRT<wchar_t>>>(&username);
  v1 = 0;
  v53 = 0;
  ATL::CStringT<wchar_t,StrTraitMFC_DLL<wchar_t,ATL::ChTraitsCRT<wchar_t>>>::CStringT<wchar_t,StrTraitMFC_DLL<wchar_t,ATL::ChTraitsCRT<wchar_t>>>(&serial);
  ATL::CStringT<wchar_t,StrTraitMFC_DLL<wchar_t,ATL::ChTraitsCRT<wchar_t>>>::CStringT<wchar_t,StrTraitMFC_DLL<wchar_t,ATL::ChTraitsCRT<wchar_t>>>(&v52);
  LOBYTE(v53) = 2;
  CWnd::GetWindowTextW(a1 + 304, &username);
  if ( *(username - 12) == 4 )
  {
    v3 = 0;
    while ( ATL::CSimpleStringT<wchar_t,1>::GetAt(&username, v3) >= 'a'
         && ATL::CSimpleStringT<wchar_t,1>::GetAt(&username, v3) <= 'z' )
    {
      if ( ++v3 >= 4 )
      {
LABEL_7:
        v4 = 0;
        while ( 1 )
        {
          if ( v1 != v4 )
          {
            v5 = ATL::CSimpleStringT<wchar_t,1>::GetAt(&username, v4);
            if ( ATL::CSimpleStringT<wchar_t,1>::GetAt(&username, v1) == v5 )
              goto LABEL_2;
          }
          if ( ++v4 >= 4 )
          {
            if ( ++v1 < 4 )
              goto LABEL_7;
            CWnd::GetWindowTextW(a1 + 420, &serial);
            if ( *(serial - 12) == 11 && ATL::CSimpleStringT<wchar_t,1>::GetAt(&serial, 5) == '-' )
            {
              v6 = ATL::CSimpleStringT<wchar_t,1>::GetAt(&username, 0);
              v7 = (v6 & 1) + 5;
              v48 = ((v6 >> 4) & 1) + 5;
              v42 = ((v6 >> 1) & 1) + 5;
              v44 = ((v6 >> 2) & 1) + 5;
              v46 = ((v6 >> 3) & 1) + 5;
              v8 = ATL::CSimpleStringT<wchar_t,1>::GetAt(&username, 1);
              v34 = (v8 & 1) + 1;
              v40 = ((v8 >> 4) & 1) + 1;
              v36 = ((v8 >> 1) & 1) + 1;
              v9 = ((v8 >> 2) & 1) + 1;
              v38 = ((v8 >> 3) & 1) + 1;
              v10 = ATL::CSimpleStringT<wchar_t,1>::GetBuffer(&v52);
              itow_s(v7 + v9, v10, 10u, 10);
              v11 = ATL::CSimpleStringT<wchar_t,1>::GetAt(&v52, 0);
              if ( ATL::CSimpleStringT<wchar_t,1>::GetAt(&serial, 0) == v11 )
              {
                ATL::CSimpleStringT<wchar_t,1>::ReleaseBuffer(&v52, -1);
                v12 = ATL::CSimpleStringT<wchar_t,1>::GetBuffer(&v52);
                itow_s(v46 + v38, v12, 10u, 10);
                v13 = ATL::CSimpleStringT<wchar_t,1>::GetAt(&serial, 1);
                if ( v13 == ATL::CSimpleStringT<wchar_t,1>::GetAt(&v52, 0) )
                {
                  ATL::CSimpleStringT<wchar_t,1>::ReleaseBuffer(&v52, -1);
                  v14 = ATL::CSimpleStringT<wchar_t,1>::GetBuffer(&v52);
                  itow_s(v42 + v40, v14, 0xAu, 10);
                  v15 = ATL::CSimpleStringT<wchar_t,1>::GetAt(&serial, 2);
                  if ( v15 == ATL::CSimpleStringT<wchar_t,1>::GetAt(&v52, 0) )
                  {
                    ATL::CSimpleStringT<wchar_t,1>::ReleaseBuffer(&v52, -1);
                    v16 = ATL::CSimpleStringT<wchar_t,1>::GetBuffer(&v52);
                    itow_s(v44 + v34, v16, 0xAu, 10);
                    v17 = ATL::CSimpleStringT<wchar_t,1>::GetAt(&serial, 3);
                    if ( v17 == ATL::CSimpleStringT<wchar_t,1>::GetAt(&v52, 0) )
                    {
                      ATL::CSimpleStringT<wchar_t,1>::ReleaseBuffer(&v52, -1);
                      v18 = ATL::CSimpleStringT<wchar_t,1>::GetBuffer(&v52);
                      itow_s(v48 + v36, v18, 0xAu, 10);
                      v19 = ATL::CSimpleStringT<wchar_t,1>::GetAt(&serial, 4);
                      if ( v19 == ATL::CSimpleStringT<wchar_t,1>::GetAt(&v52, 0) )
                      {
                        ATL::CSimpleStringT<wchar_t,1>::ReleaseBuffer(&v52, -1);
                        v20 = ATL::CSimpleStringT<wchar_t,1>::GetAt(&username, 2);
                        v21 = (v20 & 1) + 5;
                        v49 = ((v20 >> 4) & 1) + 5;
                        v43 = ((v20 >> 1) & 1) + 5;
                        v45 = ((v20 >> 2) & 1) + 5;
                        v47 = ((v20 >> 3) & 1) + 5;
                        v22 = ATL::CSimpleStringT<wchar_t,1>::GetAt(&username, 3);
                        v35 = (v22 & 1) + 1;
                        v41 = ((v22 >> 4) & 1) + 1;
                        v37 = ((v22 >> 1) & 1) + 1;
                        v23 = ((v22 >> 2) & 1) + 1;
                        v39 = ((v22 >> 3) & 1) + 1;
                        v24 = ATL::CSimpleStringT<wchar_t,1>::GetBuffer(&v52);
                        itow_s(v21 + v23, v24, 0xAu, 10);
                        v25 = ATL::CSimpleStringT<wchar_t,1>::GetAt(&serial, 6);
                        if ( v25 == ATL::CSimpleStringT<wchar_t,1>::GetAt(&v52, 0) )
                        {
                          ATL::CSimpleStringT<wchar_t,1>::ReleaseBuffer(&v52, -1);
                          v26 = ATL::CSimpleStringT<wchar_t,1>::GetBuffer(&v52);
                          itow_s(v47 + v39, v26, 0xAu, 10);
                          v27 = ATL::CSimpleStringT<wchar_t,1>::GetAt(&serial, 7);
                          if ( v27 == ATL::CSimpleStringT<wchar_t,1>::GetAt(&v52, 0) )
                          {
                            ATL::CSimpleStringT<wchar_t,1>::ReleaseBuffer(&v52, -1);
                            v28 = ATL::CSimpleStringT<wchar_t,1>::GetBuffer(&v52);
                            itow_s(v43 + v41, v28, 0xAu, 10);
                            v29 = ATL::CSimpleStringT<wchar_t,1>::GetAt(&serial, 8);
                            if ( v29 == ATL::CSimpleStringT<wchar_t,1>::GetAt(&v52, 0) )
                            {
                              ATL::CSimpleStringT<wchar_t,1>::ReleaseBuffer(&v52, -1);
                              v30 = ATL::CSimpleStringT<wchar_t,1>::GetBuffer(&v52);
                              itow_s(v45 + v35, v30, 0xAu, 10);
                              v31 = ATL::CSimpleStringT<wchar_t,1>::GetAt(&serial, 9);
                              if ( v31 == ATL::CSimpleStringT<wchar_t,1>::GetAt(&v52, 0) )
                              {
                                ATL::CSimpleStringT<wchar_t,1>::ReleaseBuffer(&v52, -1);
                                v32 = ATL::CSimpleStringT<wchar_t,1>::GetBuffer(&v52);
                                itow_s(v49 + v37, v32, 0xAu, 10);
                                v33 = ATL::CSimpleStringT<wchar_t,1>::GetAt(&serial, 10);
                                if ( v33 == ATL::CSimpleStringT<wchar_t,1>::GetAt(&v52, 0) )
                                {
                                  ATL::CSimpleStringT<wchar_t,1>::ReleaseBuffer(&v52, -1);
                                  ATL::CStringT<wchar_t,StrTraitMFC_DLL<wchar_t,ATL::ChTraitsCRT<wchar_t>>>::~CStringT<wchar_t,StrTraitMFC_DLL<wchar_t,ATL::ChTraitsCRT<wchar_t>>>(&v52);
                                  ATL::CStringT<wchar_t,StrTraitMFC_DLL<wchar_t,ATL::ChTraitsCRT<wchar_t>>>::~CStringT<wchar_t,StrTraitMFC_DLL<wchar_t,ATL::ChTraitsCRT<wchar_t>>>(&serial);
                                  ATL::CStringT<wchar_t,StrTraitMFC_DLL<wchar_t,ATL::ChTraitsCRT<wchar_t>>>::~CStringT<wchar_t,StrTraitMFC_DLL<wchar_t,ATL::ChTraitsCRT<wchar_t>>>(&username);
                                  return 1;
                                }
                              }
                            }
                          }
                        }
                      }
                    }
                  }
                }
              }
            }
            goto LABEL_2;
          }
        }
      }
    }
  }
LABEL_2:
  ATL::CStringT<wchar_t,StrTraitMFC_DLL<wchar_t,ATL::ChTraitsCRT<wchar_t>>>::~CStringT<wchar_t,StrTraitMFC_DLL<wchar_t,ATL::ChTraitsCRT<wchar_t>>>(&v52);
  ATL::CStringT<wchar_t,StrTraitMFC_DLL<wchar_t,ATL::ChTraitsCRT<wchar_t>>>::~CStringT<wchar_t,StrTraitMFC_DLL<wchar_t,ATL::ChTraitsCRT<wchar_t>>>(&serial);
  ATL::CStringT<wchar_t,StrTraitMFC_DLL<wchar_t,ATL::ChTraitsCRT<wchar_t>>>::~CStringT<wchar_t,StrTraitMFC_DLL<wchar_t,ATL::ChTraitsCRT<wchar_t>>>(&username);
  return 0;
}
```

# 0x1 初步分析，输入有效性分析

username 只有4个字节。
serial 只有11字节，且第serial[5]=='-'。

# 0x2 进一步分析，约束条件

这段代码量不是很多，只要细心，很快就能找出所有的限制条件。
1、username[0~3]值域为['a','z']
2、username[3] == 'p' //这个是题目给出的条件，非逆向所得。
3、0x0代码片段中有10个方程:

```
((username[0]&1)+5+(((username[1]>>2) & 1 )+1))==ord('7')-0x30
((((username[0]>>3) & 1)+5)+(((username[1]>>3)&1)+1))==ord('6')-0x30
(((username[0]>>1) & 1)+5+(((username[1]>>4) & 1 )+1))==ord('8')-0x30
(((username[0]>>2) & 1)+5+(((username[1]) & 1 )+1))==ord('7')-0x30
(((username[0]>>4) & 1)+5+(((username[1]>>1) & 1 )+1))==ord('6')-0x30
(((username[2]) & 1)+5+(((username[3]>>2) & 1 )+1))==ord('7')-0x30
(((username[2]>>3) & 1)+5+(((username[3]>>3) & 1 )+1))==ord('7')-0x30
(((username[2]>>1) & 1)+5+(((username[3]>>4) & 1 )+1))==ord('7')-0x30
(((username[2]>>2) & 1)+5+(((username[3]) & 1 )+1))==ord('7')-0x30
(((username[2]>>4) & 1)+5+(((username[3]>>1) & 1 )+1))==ord('6')-0x30
```

ord是python中的函数，功能是将字符转成对应int。为什么我要这么做呢？从逆向出的代码片段可知，原程序用itow_s将运算值转为文本，然后取文本的最高位和输入的ASCII进行比较，但是运算结果只有一位数，我就直接用加减0x30，其次z3条件里面好像不能有str()这样的函数出现。

# 0x3 编写程序，取得flag！

```
from z3 import *
username = [BitVec('u%d'%i,8) for i in range(0,4)]
solver = Solver() #76876-77776
solver.add(((username[0]&1)+5+(((username[1]>>2) & 1 )+1))==ord('7')-0x30)
solver.add(((((username[0]>>3) & 1)+5)+(((username[1]>>3)&1)+1))==ord('6')-0x30)
solver.add((((username[0]>>1) & 1)+5+(((username[1]>>4) & 1 )+1))==ord('8')-0x30)
solver.add((((username[0]>>2) & 1)+5+(((username[1]) & 1 )+1))==ord('7')-0x30)
solver.add((((username[0]>>4) & 1)+5+(((username[1]>>1) & 1 )+1))==ord('6')-0x30)
solver.add((((username[2]) & 1)+5+(((username[3]>>2) & 1 )+1))==ord('7')-0x30)
solver.add((((username[2]>>3) & 1)+5+(((username[3]>>3) & 1 )+1))==ord('7')-0x30)
solver.add((((username[2]>>1) & 1)+5+(((username[3]>>4) & 1 )+1))==ord('7')-0x30)
solver.add((((username[2]>>2) & 1)+5+(((username[3]) & 1 )+1))==ord('7')-0x30)
solver.add((((username[2]>>4) & 1)+5+(((username[3]>>1) & 1 )+1))==ord('6')-0x30)
solver.add(username[3] == ord('p'))
for i in range(0,4):
    solver.add(username[i] >= ord('a'))
    solver.add(username[i] <= ord('z'))

solver.check()
result = solver.model()

flag = ''
for i in range(0,4):
    flag += chr(result[username[i]].as_long().real)
print flag 
```

参考:
[https://github.com/Z3Prover/z3](https://github.com/Z3Prover/z3)