<!--yml
category: 未分类
date: 2022-04-26 14:37:46
-->

# 【CTF reverse】逆向入门题解集合+逆向相关软件安装_hans774882968的博客-CSDN博客

> 来源：[https://blog.csdn.net/hans774882968/article/details/122944644](https://blog.csdn.net/hans774882968/article/details/122944644)

下载安徽理工大学的ctf软件包：[传送门](http://jsj.aust.edu.cn/tools/CTFToolkit-v1.1.0.rar)。里面包含了不少软件，IDA、AndroidKiller、jd-gui等。

除此以外，还需要：

*   PETools：查看exe基本信息，在GitHub上开源。
*   UPX.exe：exe加壳工具，也可以用来去UPX壳（但连变种的UPX壳都没法去~），在GitHub上开源。
*   JEB：参考h鶸的文章安装即可[😙](https://blog.csdn.net/hans774882968/article/details/122935187)
*   uncompyle6：把pyc转为python。`pip install uncompyle6`
*   Linux的file命令可以帮助我们分析一个未知文件的基本信息。

**作者：[hans774882968](https://blog.csdn.net/hans774882968)**

buuoj题目链接：https://buuoj.cn/challenges

jarvisoj题目链接：https://www.jarvisoj.com/challenges

南邮CTF：https://cgctf.x1ct34m.com/challenges

### buu-easyre

把`easyre.exe`拖到`idaq64.exe`。然后用软件提供的search功能，搜索`flag{`即可（这个软件不允许你使用ctrl+F）。

```
aFlagThis_is_a_ db 'flag{this_Is_a_EaSyRe}',0 ; DATA XREF: main+31 
```

**关闭**：关闭时有选项，选择`Pack database(Store)`即可，生成idb文件并删除4个数据库文件（id0等）。`Don't pack db`选项不生成idb文件（i64）。而`Pack database(Deflate)`保留4个数据库文件和idb文件。再次打开时拖拽idb文件进去即可。

main函数

```
__int64 __fastcall main(__int64 a1, __int64 a2)
{
  int b; 
  int a; 

  _main();
  scanf(a1, a2, &a, "%d%d", &b);
  if ( a == b )
    printf(a1, a2, (unsigned int)a, "flag{this_Is_a_EaSyRe}");
  else
    printf(a1, a2, (unsigned int)a, "sorry,you can't get flag");
  return 0LL;
} 
```

### buu-reverse1

在虚拟机里用Linux的file命令查看文件类型得：`reverse_1.exe: PE32+ executable (console) x86-64, for MS Windows`。所以是64位程序。用PETools查看，得AMD64（K8）也可以验证。

用ida的搜索功能，搜索字符串flag，直接找到关键函数。

另一个做法：shift+F12看到字符串带flag的，点一下，然后点两下data xref，就可以看到那个函数的汇编代码。按F5转为C伪代码。

```
int sub_1400118C0()
{
  char *v0; 
  signed __int64 i; 
  size_t v2; 
  size_t v3; 
  char v5; 
  signed int v6; 
  char Str1; 
  unsigned __int64 v8; 
  unsigned __int64 v9; 

  v0 = &v5;
  for ( i = 82i64; i; --i )
  {
    *(_DWORD *)v0 = -858993460;
    v0 += 4;
  }
  v9 = (unsigned __int64)&v6 ^ _security_cookie;
  for ( *(&v6 + 1) = 0; ; ++*(&v6 + 1) )
  {
    v8 = *(&v6 + 1);
    v2 = j_strlen(Str2);
    if ( v8 > v2 )
      break;
    if ( Str2[(signed __int64)*(&v6 + 1)] == 111 )
      Str2[(signed __int64)*(&v6 + 1)] = 48;
  }
  sub_1400111D1("input the flag:");
  sub_14001128F("%20s", &Str1);
  v3 = j_strlen(Str2);
  if ( !strncmp(&Str1, Str2, v3) )
    sub_1400111D1("this is the right flag!\n");
  else
    sub_1400111D1("wrong flag\n");
  sub_14001113B(&v5, &unk_140019D00);
  return sub_1400112E9((unsigned __int64)&v6 ^ v9);
} 
```

右键111和48分别换成Char，得`'o'`和`'0'`。`Str2`是`"{hello_world}"`。所以for循环就是把所有的`o`变成`0`。

最后oj的题干有”注意：得到的 flag 请包上 flag{} 提交“，所以提交的字符串就是`flag{hell0_w0rld}`

### buu-reverse2

这题是Linux可执行程序的hello world。

虽然是在64位Linux下运行的，但也能直接拖进idaq64.exe分析。

在strings window看到和flag有关的字符串，定位到main函数。

```
int __cdecl main(int argc, const char **argv, const char **envp)
{
  int result; 
  __int64 v4; 
  int stat_loc; 
  int i; 
  __pid_t pid; 
  char s2; 
  __int64 v9; 

  v9 = *MK_FP(__FS__, 40LL);
  pid = fork();
  if ( pid )
  {
    argv = (const char **)&stat_loc;
    waitpid(pid, &stat_loc, 0);
  }
  else
  {
    for ( i = 0; i <= strlen(&flag); ++i )
    {
      if ( *(&flag + i) == 'i' || *(&flag + i) == 'r' )
        *(&flag + i) = '1';
    }
  }
  printf("input the flag:", argv);
  __isoc99_scanf(4196628LL, &s2);
  if ( !strcmp(&flag, &s2) )
    result = puts("this is the right flag!");
  else
    result = puts("wrong flag!");
  v4 = *MK_FP(__FS__, 40LL) ^ v9;
  return result;
} 
```

`flag`的ascii是`{`，注意到aHacking_for_fu这个变量虽然没用到（所以strings window查不到），但它和flag的地址是相邻的，所以**&flag就是字符串`{hacking_for_fun}`**。然后那个循环逻辑很简单，不赘述。答案`flag{hack1ng_fo1_fun}`

### buu-内涵的软件

这题是32位程序的hello world。

用Linux的file命令得：`xx.exe: PE32 executable (console) Intel 80386, for MS Windows`

在String window看到了一些和问题有关的信息，比如中文。于是找到了main函数。

```
int main_0()
{
  int result; 
  char v1; 
  char v2; 
  int v3; 
  int v4; 

  memset(&v1, 0xCCu, 0x4Cu);
  v4 = 5;
  v3 = (int)"DBAPP{49d3c93df25caad81232130f3d2ebfad}";
  while ( v4 >= 0 )
  {
    printf("距离出现答案还有%d秒，请耐心等待！\n", v4);
    sub_40100A();
    --v4;
  }
  printf("\n\n\n这里本来应该是答案的,但是粗心的程序员忘记把变量写进来了,你要不逆向试试看:(Y/N)\n");
  v2 = 1;
  scanf("%c", &v2);
  if ( v2 == 'Y' )
  {
    printf("OD吾爱破解或者IDA这些逆向软件都挺好的！");
    result = sub_40100A();
  }
  else if ( v2 == 'N' )
  {
    printf("那没办法了，猜是猜不出的．");
    result = sub_40100A();
  }
  else
  {
    printf("输入错误,没有提示.");
    result = sub_40100A();
  }
  return result;
} 
```

这tm啥逻辑都没有啊。后来才意识到v3变量就是所求。

### buu-新年快乐

这题是加壳程序的hello world。

用PETools查看exe的基本信息。看入口：`Section: [UPX1], EP: 0x000014F0`，所以是加了UPX壳的。用UPX解压即可（`(powershell) .\upx.exe -d 新年快乐.exe`）。

顺便记录一下PETools的一些用法：

*   File Header --> Machine，发现是Intel 386；查看characteristics也可知这是**32位程序**。
*   File Header --> Time/Date的16进制解码得：2016年1月19日16：09：36。但UTC+8才是真实时间。

```
int __cdecl main(int argc, const char **argv, const char **envp)
{
  int result; 
  char v4; 
  __int16 v5; 
  __int16 v6; 

  __main();
  qmemcpy(&v4, "HappyNewYear!", 0xEu);
  v5 = 0;
  memset(&v6, 0, 0x1Eu);
  printf("please input the true flag:");
  scanf("%s", &v5);
  if ( !strncmp((const char *)&v5, &v4, strlen(&v4)) )
    result = puts("this is true flag!");
  else
    result = puts("wrong!");
  return result;
} 
```

代码逻辑就是，期望输入串v5 == “HappyNewYear”。所以这就是flag。

### buu-xor

这题是mac可执行程序的hello world。

用Linux的file命令查看

```
$ file xor
xor: Mach-O 64-bit x86_64 executable, flags:<NOUNDEFS|DYLDLINK|TWOLEVEL|PIE> 
```

64位程序。

一眼就能看到main函数。

```
int __cdecl main(int argc, const char **argv, const char **envp)
{
  signed __int64 v3; 
  __int64 v4; 
  signed int i; 
  char v7[264]; 
  __int64 v8; 

  v8 = *(_QWORD *)__stack_chk_guard_ptr[0];
  memset(v7, 0, 0x100uLL);
  v3 = 256LL;
  printf("Input your flag:\n", 0LL);
  get_line(v7, 256LL);
  if ( strlen(v7) != 33 )
    goto LABEL_13;
  for ( i = 1; i < 33; ++i )
    v7[i] ^= v7[i - 1];
  v3 = (signed __int64)global;
  if ( !strncmp(v7, global, 0x21uLL) )
    printf("Success", v3);
  else
LABEL_13:
    printf("Failed", v3);
  v4 = *(_QWORD *)__stack_chk_guard_ptr[0];
  if ( *(_QWORD *)__stack_chk_guard_ptr[0] == v8 )
    LODWORD(v4) = 0;
  return v4;
} 
```

global的值为06FEH，在那个地址找到字符串，并用shift+E来导出数组：

```
unsigned char ida_chars[] =
{
  102,  10, 107,  12, 119,  38,  79,  46,  64,  17, 
  120,  13,  90,  59,  85,  17, 112,  25,  70,  31, 
  118,  34,  77,  35,  68,  14, 103,   6, 104,  15, 
   71,  50,  79,   0
}; 
```

下面的代码利用的是异或的性质：x<sup>y</sup>y = x，前缀异或和的相邻元素异或即可还原元素。

```
let a = [102, 10, 107, 12, 119, 38, 79, 46, 64, 17, 120, 13, 90, 59, 85, 17, 112, 25, 70, 31, 118, 34, 77, 35, 68, 14, 103, 6, 104, 15, 71, 50, 79]
let ans = String.fromCharCode(a[0])
for (let i = 1; i < a.length; ++i) {
  ans += String.fromCharCode(a[i] ^ a[i - 1])
}
console.log(ans) 
```

### buu-helloword-安卓逆向helloworld

这题是安卓逆向的hello world。只要JEB能正常用就能AC了。

```
package com.example.helloword;

import android.os.Bundle;
import android.support.v7.app.ActionBarActivity;
import android.view.Menu;
import android.view.MenuItem;

public class MainActivity extends ActionBarActivity {
    @Override  
    protected void onCreate(Bundle arg5) {
        super.onCreate(arg5);
        this.setContentView(0x7F030018);  
        "flag{7631a988259a00816deda84afb29430a}".compareTo("xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx");
    }

    @Override  
    public boolean onCreateOptionsMenu(Menu arg3) {
        this.getMenuInflater().inflate(0x7F0C0000, arg3);  
        return 1;
    }

    @Override  
    public boolean onOptionsItemSelected(MenuItem arg3) {
        return arg3.getItemId() == 0x7F05003C ? true : super.onOptionsItemSelected(arg3);  
    }
} 
```

### buu-Java逆向解密

如题名，Java逆向的hello world。Java逆向有开源的jd-gui，[下载](https://github.com/java-decompiler/jd-gui/releases)

反编译得

```
import java.util.ArrayList;
import java.util.Scanner;

public class Reverse {
  public static void main(String[] args) {
    Scanner s = new Scanner(System.in);
    System.out.println("Please input the flag");
    String str = s.next();
    System.out.println("Your input is");
    System.out.println(str);
    char[] stringArr = str.toCharArray();
    Encrypt(stringArr);
  }

  public static void Encrypt(char[] arr) {
    ArrayList<Integer> Resultlist = new ArrayList<>();
    for (int i = 0; i < arr.length; i++) {
      int result = arr[i] + 64 ^ 0x20;
      Resultlist.add(Integer.valueOf(result));
    } 
    int[] KEY = { 
        180, 136, 137, 147, 191, 137, 147, 191, 148, 136, 
        133, 191, 134, 140, 129, 135, 191, 65 };
    ArrayList<Integer> KEYList = new ArrayList<>();
    for (int j = 0; j < KEY.length; j++)
      KEYList.add(Integer.valueOf(KEY[j])); 
    System.out.println("Result:");
    if (Resultlist.equals(KEYList)) {
      System.out.println("Congratulations!");
    } else {
      System.err.println("Error!");
    } 
  }
} 
```

输入串进行enc操作，等于KEY，所以为了得到congratulations要输入的串就通过enc的**逆过程**得到。随便写个js即可。

```
let k = [180, 136, 137, 147, 191, 137, 147, 191, 148, 136, 133, 191, 134, 140, 129, 135, 191, 65], ans = ''
for (let x of k) {
  ans += String.fromCharCode((x ^ 32) - 64)
}
console.log(ans) 
```

得：`This_is_the_flag_!`，记得包上`flag{}`。

### buu-findit-安卓逆向

用JEB打开，看Java代码，看到一个char数组被反复使用，于是就拿到本地跑一下看看有啥。意外发现输出的变量y就是答案……

```
public class FindItShow {

    public static void main(String[] args) {
        final char[] flagHome = {'T', 'h', 'i', 's', 'I', 's', 'T', 'h', 'e', 'F', 'l', 'a', 'g', 'H', 'o', 'm', 'e'},
            ansStr = {
                'p', 'v', 'k', 'q', '{', 'm', '1', '6', '4', '6', '7', '5', '2', '6',
                '2', '0', '3', '3', 'l', '4', 'm', '4', '9', 'l', 'n', 'p', '7', 'p', '9',
                'm', 'n', 'k', '2', '8', 'k', '7', '5', '}'
            };
        char[] x = new char[17];
        char[] y = new char[38];
        int i;
        for (i = 0; i < 17; ++i) {
            if (flagHome[i] < 73 && flagHome[i] >= 65 || flagHome[i] < 105 && flagHome[i] >= 97) {
                x[i] = (char) (flagHome[i] + 18);
            } else if (flagHome[i] >= 65 && flagHome[i] <= 90 || flagHome[i] >= 97 && flagHome[i] <= 0x7A) {
                x[i] = (char) (flagHome[i] - 8);
            } else {
                x[i] = flagHome[i];
            }
        }

        int v0_1;
        for (v0_1 = 0; v0_1 < 38; ++v0_1) {
            if (ansStr[v0_1] >= 65 && ansStr[v0_1] <= 90 || ansStr[v0_1] >= 97 && ansStr[v0_1] <= 0x7A) {
                y[v0_1] = (char) (ansStr[v0_1] + 16);
                if (y[v0_1] > 90 && y[v0_1] < 97 || y[v0_1] >= 0x7A) {
                    y[v0_1] = (char) (y[v0_1] - 26);
                }
            } else {
                y[v0_1] = ansStr[v0_1];
            }
        }
        System.out.println(x);
        System.out.println(y);

        StringBuilder what = new StringBuilder();
        for (char v : y) {
            if (v >= 97 && v <= 122) what.append(v);
        }
        System.out.println(what.toString());
    }
} 
```

另外，根据https://blog.csdn.net/Waffle666/article/details/109901358，这题跟凯撒密码有关。

### 南邮CTF-py交易-python逆向helloworld

[传送门](https://cgctf.x1ct34m.com/challenges#Re)

命令

```
pip install uncompyle6
uncompyle6 Py.pyc > Py.py 
```

uncompyle6的安装有些注意点：

1.  最高支持python3.8，版本太高的要先装一个低版本的，然后在Path变量处，把低版本的路径提到高版本的路径的上面，使得低版本python为默认版本。然后再pip install。
2.  安装过程中会生成exe，exe的位置不同寻常，可以考虑把它加到环境变量的**系统变量**。

得到

```
import base64

def encode(message):
    s = ''
    for i in message:
        x = ord(i) ^ 32
        x = x + 16
        s += chr(x)

    return base64.b64encode(s)

correct = 'XlNkVmtUI1MgXWBZXCFeKY+AaXNt'
flag = ''
print 'Input flag:'
flag = raw_input()
if encode(flag) == correct:
    print 'correct'
else:
    print 'wrong' 
```

写出其逆过程即可，很简单。

```
import base64

def main():
    s = 'XlNkVmtUI1MgXWBZXCFeKY+AaXNt'
    a = base64.b64decode(s)
    ans = ''
    for v in a:
        ans += chr((v - 16) ^ 32)
    print(ans)

if __name__ == '__main__':
    main() 
```

### jarvisoj-FindKey-python逆向

文件后缀名看不懂，用file命令分析一下。

```
file findkey.31a509f4006ba41368dcf963762388bb
// python 2.7 byte-compiled 
```

是pyc文件。`uncompyle6 -o findkey.py findkey.pyc`得

```
 flag = raw_input('Input your Key:').strip()
if len(flag) != 17:
    print 'Wrong Key!!'
    sys.exit(1)
flag = flag[::-1]
for i in range(0, len(flag)):
    if ord(flag[i]) + pwda[i] & 255 != lookup[(i + pwdb[i])]:
        print 'Wrong Key!!'
        sys.exit(1)

print 'Congratulations!!' 
```

每个`i`是相互独立的，并且3个数组出现的所有值都比256小，所以很容易可以写出逆过程

```
 ans = ''
for i in range(len(pwda)):
    v = lookup[(i + pwdb[i])] - pwda[i]
    v = (v % 256 + 256) % 256
    ans += chr(v)
ans = ans[::-1]
print(ans) 
```