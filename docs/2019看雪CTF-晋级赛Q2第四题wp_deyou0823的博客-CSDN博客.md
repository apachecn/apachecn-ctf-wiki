<!--yml
category: 未分类
date: 2022-04-26 14:50:15
-->

# 2019看雪CTF 晋级赛Q2第四题wp_deyou0823的博客-CSDN博客

> 来源：[https://blog.csdn.net/deyou0823/article/details/101764065](https://blog.csdn.net/deyou0823/article/details/101764065)

上次参加2019看雪CTF 晋级赛Q2卡在了这道题上，虽然逆出算法，但是方程不会解，哈哈哈哈，果然数学知识很重要呀，现在记录一下。

首先根据关键信息，根据错误提示字符串定位到这里：

```
 1 int __thiscall guanjian_401EA0(CWnd *this)
 2 {
 3   CWnd *v1; // esi
 4   int index; // eax
 5   WCHAR String; // [esp+Ch] [ebp-310h]
 6   char v5; // [esp+Eh] [ebp-30Eh]
 7   char ptr; // [esp+20Ch] [ebp-110h]
 8   char v7; // [esp+20Dh] [ebp-10Fh]
 9   DWORD v8; // [esp+30Ch] [ebp-10h]
10   CWnd *v9; // [esp+310h] [ebp-Ch]
11   int v10; // [esp+314h] [ebp-8h]
12   DWORD flOldProtect; // [esp+318h] [ebp-4h]
13 
14   v1 = this; 15   v9 = this; 16   String = 0; 17   memset(&v5, 0, 0x1FEu); 18   ptr = 0; 19   memset(&v7, 0, 0xFFu); 20   CWnd::GetDlgItemTextW(v1, 1000, &String, 20); 21   if ( wcslen(&String) == 16 ) 22 { 23     index = 0; 24     while ( !(*(&String + index) & 0xFF00) ) 25 { 26       *(&ptr + index) = *((_BYTE *)&String + 2 * index); 27       if ( ++index >= 16 ) 28 { 29         v8 = 64; 30         flOldProtect = 0; 31         VirtualProtect(sub_9D10E0, 0xD17u, 0x40u, &flOldProtect);// BOOL VirtualProtect( 32                                                 // LPVOID lpAddress, 33                                                 // DWORD dwSize, 34                                                 // DWORD flNewProtect, 35                                                 // PDWORD lpflOldProtect 36                                                 // lpAddress，要改变属性的内存起始地址。 37                                                 // 
38                                                 // dwSize，要改变属性的内存区域大小。 39                                                 // 
40                                                 // flNewProtect，内存新的属性类型，设置为PAGE_EXECUTE_READWRITE（0x40）时该内存页为可读可写可执行。 41                                                 // 
42                                                 // pflOldProtect，内存原始属性类型保存地址。 43                                                 // 
44                                                 // 
45 GetLastError(); 46         qmemcpy(sub_9D10E0, byte_B347B8, 0x330u); 47         VirtualProtect(sub_9D10E0, 0xD17u, flOldProtect, &v8); 48         if ( !GetLastError() ) 49 { 50           v10 = 0; 51           v10 = sub_9D10E0(); 52           if ( v10 == 1 ) 53             return CWnd::MessageBoxW(v9, L"Congratulations! You are right!", 0, 0); 54 } 55         v1 = v9; 56         return CWnd::MessageBoxW(v1, L"Wrong!", 0, 0); 57 } 58 } 59 } 60   return CWnd::MessageBoxW(v1, L"Wrong!", 0, 0); 61 }
```

```
注意到运行时先修改sub_9D10E0()代码段，然后再运行sub_9D10E0()，我们动态调试，dump出修改完成的sub_9D10E0()代码段
```

```
 1 int __cdecl sub_9D10E0(char *input)
 2 {
 3   signed int i; // eax
 4   char v2; // cl
 5   signed int v3; // ecx
 6   signed int v4; // eax
 7   signed int low_index; // eax
 8   signed int j; // esi
 9   signed int k; // ecx
 10   __int16 v8; // dx
 11   char *buffer_ptr_; // edi
 12   __int16 v10; // ax
 13   signed int temp_1; // eax
 14   signed int m_1; // ecx
 15   unsigned __int16 t_3; // bx
 16   signed int j_1; // esi
 17   signed int k_1; // ecx
 18   __int16 v16; // dx
 19   char *buffer_ptr; // edi
 20   __int16 t_4; // ax
 21   signed int t; // eax
 22   signed int m; // ecx
 23   unsigned __int16 temp; // bx
 24   unsigned int t_1; // eax
 25   signed int i_2; // ecx
 26   unsigned __int16 temp_2; // dx
 27   char f; // dl
 28   signed int i_1; // eax
 29   __int16 t_2; // si
 30   int index; // eax
 31   char s_xor[17]; // [esp+8h] [ebp-90h]
 32   int v31; // [esp+1Ch] [ebp-7Ch]
 33   int v32; // [esp+20h] [ebp-78h]
 34   int v33; // [esp+24h] [ebp-74h]
 35   char x[8]; // [esp+28h] [ebp-70h]
 36   char buffer[16]; // [esp+30h] [ebp-68h]
 37   char y[8]; // [esp+40h] [ebp-58h]
 38   char yy7_result[17]; // [esp+48h] [ebp-50h]
 39   char result[17]; // [esp+5Ch] [ebp-3Ch]
 40   char xx_result[17]; // [esp+70h] [ebp-28h]
 41   char yy_result[17]; // [esp+84h] [ebp-14h]
 42 
 43   s_xor[4] = 0x81u;
 44   s_xor[11] = 0x81u;
 45   i = 0;
 46   s_xor[0] = 0x16;
 47   s_xor[1] = -106;
 48   s_xor[2] = -116;
 49   s_xor[3] = -29;
 50   s_xor[5] = -104;
 51   s_xor[6] = 110;
 52   s_xor[7] = 100;
 53   s_xor[8] = 0x84u;
 54   s_xor[9] = 8;
 55   s_xor[10] = -36;
 56   s_xor[12] = 0xBEu;
 57   s_xor[13] = 77;
 58   s_xor[14] = 72;
 59   s_xor[15] = 79;
 60   *(_DWORD *)&s_xor[16] = 0;
 61   v31 = 0;
 62   v32 = 0;
 63   v33 = 0;
 64   *(_DWORD *)x = 0;
 65   *(_DWORD *)&x[4] = 0;
 66   *(_DWORD *)y = 0;
 67   *(_DWORD *)&y[4] = 0;
 68   do
 69   {                                             // 先将输入分成两部分，记为x,y，然后进行异或操作，
 70     v2 = s_xor[i + 8] ^ input[i + 8];           // 从输入的第9位开始处理，索引8-15 71                                                 // 异或
 72     x[i] = s_xor[i] ^ s_xor[i + input - s_xor]; // 输入的前8位 73                                                 // 异或
 74     y[i++] = v2; 75   }
 76   while ( i < 8 ); 77   *(_DWORD *)s_xor = 0;
 78   *(_DWORD *)xx_result = 0;
 79   *(_DWORD *)&xx_result[4] = 0;
 80   *(_DWORD *)&xx_result[8] = 0;
 81   *(_DWORD *)&xx_result[12] = 0;
 82   xx_result[16] = 0;
 83   *(_DWORD *)yy_result = 0;
 84   *(_DWORD *)&yy_result[4] = 0;
 85   *(_DWORD *)&yy_result[8] = 0;
 86   *(_DWORD *)&yy_result[12] = 0;
 87   yy_result[16] = 0;
 88   *(_DWORD *)yy7_result = 0;
 89   *(_DWORD *)&yy7_result[4] = 0;
 90   *(_DWORD *)&yy7_result[8] = 0;
 91   *(_DWORD *)&yy7_result[12] = 0;
 92   yy7_result[16] = 0;
 93   *(_DWORD *)result = 0;
 94   *(_DWORD *)&result[4] = 0;
 95   *(_DWORD *)&result[8] = 0;
 96   *(_DWORD *)&result[12] = 0;
 97   result[16] = 0;
 98   *(_DWORD *)&s_xor[4] = 0;
 99   *(_DWORD *)&s_xor[8] = 0; 100   *(_DWORD *)&s_xor[12] = 0; 101   s_xor[16] = 0; 102   v3 = 8; 103   s_xor[0] = 8; 104   v4 = 7; 105   do
106 { 107     if ( x[v4] )                                // x[7]!=0
108       break; 109     --v3; 110     --v4; 111 } 112   while ( v4 >= 0 ); 113   if ( v3 == 8 ) 114 { 115     low_index = 7; 116     do
117 { 118       if ( y[low_index] )                       // y[7]!=0
119         break; 120       --v3; 121       --low_index; 122 } 123     while ( low_index >= 0 );                   // 输入为16位
124     if ( v3 == 8 && !(x[7] & 0xF0) )            // 第8位<0x10
125 { 126       j = 0; 127       do
128 { 129         *(_DWORD *)buffer = 0; 130         *(_DWORD *)&buffer[4] = 0; 131         *(_DWORD *)&buffer[8] = 0; 132         *(_DWORD *)&buffer[12] = 0; 133         k = 0; 134         v8 = (unsigned __int8)x[j]; 135         buffer_ptr_ = &buffer[j]; 136         do
137 { 138           v10 = (unsigned __int8)buffer[j + 8] + v8 * (unsigned __int8)x[k]; 139           buffer_ptr_[k] = buffer[j + 8] + v8 * x[k]; 140           ++k; 141           buffer[j + 8] = HIBYTE(v10); 142 } 143         while ( k < 8 ); 144         LOBYTE(temp_1) = 0; 145         m_1 = 0; 146         do
147 { 148           t_3 = (char)temp_1 + (unsigned __int8)xx_result[m_1 + j] + (unsigned __int8)buffer_ptr_[m_1]; 149           xx_result[m_1++ + j] = t_3; 150           temp_1 = (signed int)t_3 >> 8; 151 } 152         while ( m_1 < 9 ); 153         ++j;                                    // 先按字节乘，再加进位，和手算乘法原理一致
154 } 155       while ( j < 8 );                          // 通过两层循环其实是为了计算大数相乘，这里算出x^2
156       j_1 = 0; 157       do
158 { 159         *(_DWORD *)buffer = 0; 160         *(_DWORD *)&buffer[4] = 0; 161         *(_DWORD *)&buffer[8] = 0; 162         *(_DWORD *)&buffer[12] = 0; 163         k_1 = 0; 164         v16 = (unsigned __int8)y[j_1]; 165         buffer_ptr = &buffer[j_1]; 166         do
167 { 168           t_4 = (unsigned __int8)buffer[j_1 + 8] + v16 * (unsigned __int8)y[k_1]; 169           buffer_ptr[k_1] = buffer[j_1 + 8] + v16 * y[k_1]; 170           ++k_1; 171           buffer[j_1 + 8] = HIBYTE(t_4); 172 } 173         while ( k_1 < 8 ); 174         LOBYTE(t) = 0; 175         m = 0; 176         do
177 { 178           temp = (char)t + (unsigned __int8)yy_result[m + j_1] + (unsigned __int8)buffer_ptr[m]; 179           yy_result[m++ + j_1] = temp; 180           t = (signed int)temp >> 8; 181 } 182         while ( m < 9 ); 183         ++j_1;                                  // 这里计算出y^2
184 } 185       while ( j_1 < 8 ); 186       LOBYTE(t_1) = yy7_result[16]; 187       i_2 = 0; 188       do
189       {                                         // 这里计算7*y^2
190         temp_2 = (unsigned __int8)t_1 + 7 * (unsigned __int8)yy_result[i_2]; 191         yy7_result[i_2++] = temp_2; 192         t_1 = (unsigned int)temp_2 >> 8; 193 } 194       while ( i_2 < 17 ); 195       yy7_result[16] = HIBYTE(temp_2); 196       f = 0; 197       i_1 = 0; 198       do
199 { 200         t_2 = (unsigned __int8)xx_result[i_1] - (unsigned __int8)yy7_result[i_1] - f; 201         result[i_1] = t_2; 202         if ( t_2 < 0 ) 203           f = 1; 204         ++i_1; 205 } 206       while ( i_1 < 17 ); 207       if ( !f ) 208 { 209         index = 0; 210         while ( result[index] == s_xor[index] ) // 这里相当于验证x^2-7*y^2==8
211 { 212           if ( ++index >= 17 ) 213             return 1; 214 } 215 } 216 } 217 } 218   return 0; 219 }
```

到这里思路已经很明确了，16位输入，分成两部分进行异或操作，验证x^2-7*y^2=8

限定条件为

0x100000000000000<x < 0x1000000000000000,（x为8字节，且第8字节<0x10）

0x100000000000000<y<x （y为8字节，）

在进行一步异或即可得到原输入

x^2-7*y^2=8为非标准佩尔方程，求解使用了wolframalpha

https://www.wolframalpha.com/input/?i=x%5E2-7y%5E2%3D8,72057594037927936%3Cx+%3C+1152921504606846976,72057594037927936%3Cy%3Cx

```
 1 x=385044246406735194
 2 y=145533045678356702
 3 
 4 s_xor1=0x646e9881e38c9616
 5 s_xor2=0x4f484dbe81dc0884
 6 
 7 t1=hex(x^s_xor1)[2:]
 8 t2=hex(y^s_xor2)[2:]
 9 
10 m1=[] 11 m2=[] 12 for i in range(0,16,2): 13     m1.append(int(t1[i:i+2],16)) 14     m2.append(int(t2[i:i+2],16)) 15 a=''.join(map(chr,m1)) 16 b=''.join(map(chr,m2)) 17 print(a[-1::-1]+b[-1::-1]) 18 # L3mZ2k9aZ0a36DMM
```

链接：https://pan.baidu.com/s/1ZpzCus2BdlSujkVRBQZZxQ
提取码：qrlz
复制这段内容后打开百度网盘手机App，操作更方便哦