<!--yml
category: 未分类
date: 2022-04-26 14:53:01
-->

# 一道逆向CTF题-read asm详解_sherlly666的博客-CSDN博客

> 来源：[https://blog.csdn.net/github_35681219/article/details/52973809](https://blog.csdn.net/github_35681219/article/details/52973809)

题目给出一段c程序：

```
int main(int argc, char const *argv[])
{
char input[] = {0x0, 0x67, 0x6e, 0x62, 0x63, 0x7e, 0x74, 0x62, 0x69, 0x6d,
0x55, 0x6a, 0x7f, 0x60, 0x51, 0x66, 0x63, 0x4e, 0x66, 0x7b,
0x71, 0x4a, 0x74, 0x76, 0x6b, 0x70, 0x79, 0x66 , 0x1c};
func(input, 28);
printf("%s\n",input+1);
return 0;
}
```

接着给出函数func的asm代码：

```
00000000004004e6 : 
4004e6: 55 push rbp 
4004e7: 48 89 e5 mov rbp,rsp 
4004ea: 48 89 7d e8 mov QWORD PTR [rbp-0x18],rdi 
4004ee: 89 75 e4 mov DWORD PTR [rbp-0x1c],esi 
4004f1: c7 45 fc 01 00 00 00 mov DWORD PTR [rbp-0x4],0x1 
4004f8: eb 28 jmp 400522 
4004fa: 8b 45 fc mov eax,DWORD PTR [rbp-0x4] 
4004fd: 48 63 d0 movsxd rdx,eax 
400500: 48 8b 45 e8 mov rax,QWORD PTR [rbp-0x18] 
400504: 48 01 d0 add rax,rdx 
400507: 8b 55 fc mov edx,DWORD PTR [rbp-0x4] 
40050a: 48 63 ca movsxd rcx,edx 
40050d: 48 8b 55 e8 mov rdx,QWORD PTR [rbp-0x18] 
400511: 48 01 ca add rdx,rcx 
400514: 0f b6 0a movzx ecx,BYTE PTR [rdx] 
400517: 8b 55 fc mov edx,DWORD PTR [rbp-0x4] 
40051a: 31 ca xor edx,ecx 
40051c: 88 10 mov BYTE PTR [rax],dl 
40051e: 83 45 fc 01 add DWORD PTR [rbp-0x4],0x1 
400522: 8b 45 fc mov eax,DWORD PTR [rbp-0x4] 
400525: 3b 45 e4 cmp eax,DWORD PTR [rbp-0x1c] 
400528: 7e d0 jle 4004fa 
40052a: 90 nop 
40052b: 5d pop rbp 
40052c: c3 ret
```

题目思路很清晰，主程序主要是调用了func函数对input数组进行操作。所以最主要的是读懂func的这段汇编代码，下面对每段代码详细解读：

这里是调用函数必备的操作，将调用函数的栈帧栈底指针压入，即将rbp寄存器的值压入调用栈中；建立新的栈帧，将被调函数的栈帧栈底地址(rsp)放入rbp寄存器中。

```
4004e6: 55 push rbp 
4004e7: 48 89 e5 mov rbp,rsp 
```

这里先解释下QWORD和DWORD，QWORD是四字，DWORD是双字，一个字是2个字节（16位），所以QWORD存储64位数据，DWORD存储32位数据。还有r开头的寄存器用来存64位数据，e开头的寄存器用来存32位数据。

```
4004ea: 48 89 7d e8 mov QWORD PTR [rbp-0x18],rdi //rdi存参数1（在内存 [rbp-0x18]处）
4004ee: 89 75 e4 mov DWORD PTR [rbp-0x1c],esi //esi存参数2（在内存 [rbp-0x1c]处）
4004f1: c7 45 fc 01 00 00 00 mov DWORD PTR [rbp-0x4],0x1 //向内存 [rbp-0x4] 写入1
4004f8: eb 28 jmp 400522 //跳转到400522处
```

这里说一下cmp与jle指令（一般二者同时出现），例如`cmp A,B`，若A小于B，则执行jle的指令，即进行跳转，否则不进行跳转。

```
400522: 8b 45 fc mov eax,DWORD PTR [rbp-0x4] //eax=1（读取内存[rbp-0x4]处的值）
400525: 3b 45 e4 cmp eax,DWORD PTR [rbp-0x1c] //比较eax与内存[rbp-0x1c]的值(即参数2：28）
400528: 7e d0 jle 4004fa //如果eax小于28，则跳转到4004fa处
40052a: 90 nop //空指令
40052b: 5d pop rbp //函数调用结束，弹出栈底指针
40052c: c3 ret
```

说一下movsxd指令，为带符号扩展并传送。BYTE存储8位数据。

```
4004fa: 8b 45 fc mov eax,DWORD PTR [rbp-0x4] //eax=1
4004fd: 48 63 d0 movsxd rdx,eax //rdx=1
400500: 48 8b 45 e8 mov rax,QWORD PTR [rbp-0x18] //rax=input[0]（参数1）
400504: 48 01 d0 add rax,rdx //rax+=rdx即rax=input[1]
400507: 8b 55 fc mov edx,DWORD PTR [rbp-0x4] //edx=1
40050a: 48 63 ca movsxd rcx,edx //rcx=1
40050d: 48 8b 55 e8 mov rdx,QWORD PTR [rbp-0x18] //rdx=input[0]
400511: 48 01 ca add rdx,rcx //rdx=input[0]+1=input[1]
400514: 0f b6 0a movzx ecx,BYTE PTR [rdx] //ecx=rdx=0x67
400517: 8b 55 fc mov edx,DWORD PTR [rbp-0x4] //edx=1
40051a: 31 ca xor edx,ecx //edx^=ecx即edx=0x1^0x67=0x66="f"
40051c: 88 10 mov BYTE PTR [rax],dl //rax=dl
40051e: 83 45 fc 01 add DWORD PTR [rbp-0x4],0x1 //内存[rbp-0x4]处值为1
400522: 8b 45 fc mov eax,DWORD PTR [rbp-0x4] //这里接上段
400525: 3b 45 e4 cmp eax,DWORD PTR [rbp-0x1c] 
400528: 7e d0 jle 4004fa 
```

以下是最终的func源程序：

```
void func(char input[],int num)
{
    int i = 1;
    while(i <= num)
    {
        *(input[0]+i) ^= i; 
        i++;
    }
}
```

跑一下出结果：
flag{**read_asm_is_the_basic**}