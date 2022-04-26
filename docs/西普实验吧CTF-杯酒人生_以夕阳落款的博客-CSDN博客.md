<!--yml
category: 未分类
date: 2022-04-26 14:52:38
-->

# 西普实验吧CTF-杯酒人生_以夕阳落款的博客-CSDN博客

> 来源：[https://blog.csdn.net/u010379510/article/details/50913016](https://blog.csdn.net/u010379510/article/details/50913016)

题目描述：

使用古典密码
一喵星人要想喵星发送一段不知道干什么用的密码“BLOCKCIPHERDESIGNPRINCIPLE”，
但是它忘记了密钥是什么， 手头（爪头）只有它自己加密过的密钥“HTRUZYJW”， 而且它
还知道原密钥是一个单词， 你可以帮助它传递信息， 早日攻克蓝星， 征服人类吗？

显示解凯撒密码：

```
using System;
using System.Collections.Generic;

namespace a1
{
	class Program
	{
		public static void Main(string[] args)
		{
			string s="HTRUZYJW";
			char[] ch=s.ToCharArray();
			for(int i=0;i<ch.Length;i++)
				Console.Write((char)(ch[i]-5));
			Console.ReadLine();
		}
	}
}
```

解得key：computer，然后把密钥和明文加密：

```
using System;
using System.Collections.Generic;

namespace a1
{
	class Program
	{
		public static void Main(string[] args)
		{
			string s="BLOCKCIPHERDESIGNPRINCIPLE";
			string s1="COMPUTER";
			char[] ch=s.ToCharArray();
			char[] ch1=s1.ToCharArray();
			for(int i=0;i<ch.Length;i++){
				int k=ch[i]+ch1[i%ch1.Length]-'A';
				while(k>'Z') k=k-26;
				Console.Write((char)k);
			}
			Console.ReadLine();
		}
	}
}
```

得到DZAREVMGJSDSYLMXPDDXHVMGNS，这题坑爹，不用加外面那个框，通过！