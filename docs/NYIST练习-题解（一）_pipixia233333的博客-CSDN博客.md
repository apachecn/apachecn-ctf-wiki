<!--yml
category: 未分类
date: 2022-04-26 14:54:49
-->

# NYIST练习 题解（一）_pipixia233333的博客-CSDN博客

> 来源：[https://blog.csdn.net/qq_41071646/article/details/82740463](https://blog.csdn.net/qq_41071646/article/details/82740463)

CTF 皮皮虾  所写题解

想了解CTF的同学可以进群了解 （只限本校18级的新生） 811409334

![](img/26ac41e86e7b15fe035835e6ca29ea18.png)

此题解为弱校的新生题解 大佬请绕路

题目链接为   [链接](http://nyoj.top/web/contest/show/cid/17)

A、 A+B

这道题就是简单的四则运算  

```
#include<stdio.h>
int main()
{
    int a,b;
    scanf("%d%d",&a,&b);
    printf("%d\n",a+b);
    return 0;
}
```

B 奇偶数分离

这个重点是 注意格式 主要是

```
每组数据后面有一个换行。
```

这一点要理解 就是在每个数据后 还要再打印一个换行 其实在自己输出的时候如果感觉和样例不一样的话 就应该明白。。

```
#include<stdio.h>
int main()
{
    int n;
    scanf("%d",&n);
    while(n--)
    {
        int a;
        scanf("%d",&a);
        for(int i=1;i<=a;i=i+2)
        {
            printf("%d ",i);
        }
        printf("\n");
        for(int i=2;i<=a;i=i+2)
        {
            printf("%d ",i);
        }
        printf("\n");
        printf("\n");
    }
    return 0;
} 
```

# C 素数求和问题

这个题还是有难度的 有两种方法 第一种就是暴力一些的 直接利用素数的性质 直接for循环看看比他小的数有没有 能% 等于0的 如有 就不是素数   第二种就是打表

打表就是逆向思维 把所有的非素数都标记 1  然后0 就是素数  先让所有的数都变成0  具体的话  看这个图片

![](img/33887f383c82591d78873b09dd6a67cc.png)

就可以看出来了 保留的素数 因为前面素数都被筛选了出来 代码如下

```
#include<stdio.h>
#include<string.h>
bool ppx[1500];
void init()
{
    memset(ppx,0,sizeof(ppx));//这个函数是将数组所有的元素清0
    ppx[0]=ppx[1]=1;
    for(int i=2;i<675;i++)//i<1500的平方就行 具体看图
    {
         if(ppx[i])
            continue;
         for(int j=i*i;j<1500;j+=i)
         {
             ppx[j]=1;
         }
    }
}
int main()
{
    init();
    int t;
    int n;
    int a[1005];
    scanf("%d",&t);
    int sum=0;
    while(t--)
    {
        sum=0;
        scanf("%d",&n);
        for(int i=0;i<n;i++)
        {
            scanf("%d",&a[i]);
            if(!ppx[a[i]])
            {
                sum+=a[i];
            }
        }
        printf("%d\n",sum);
    }
} 
```

也可以用简单的方法直接求素数

```
#include<stdio.h>
#include<string.h>
int main()
{
    int h[1000],t[1000];
    int i,j,n,a=1,r,sum=0,z=0;
    scanf("%d",&n);
    for(i=1; i<=n; i++)
    {
        scanf("%d",&h[i]);
        sum=0;

        for(j=1; j<=h[i]; j++)
        {

            a=1;
            scanf("%d",&t[j]);
            for(r=2; r<=t[j]; r++)
            {
                if(t[j]%r==0&&r!=t[j])
                    a=0;

            }
            if(t[j]==1||t[j]==0)
                a=0;
            if(a==1)  sum=sum+t[j];
        }
        printf("%d\n",sum);

    }

    return 0;

} 
```

# D 5个数求最值

这个题 看起来比较无脑  其实按理说暴力就行 或者存数组里排序就行（冒泡  快排）

```
#include<stdio.h>
#include<string.h>
int main()
{
    int a[5]={0};
    int maxx=0;
    int minn=10000;
    for(int i=0;i<5;i++)
    {
        scanf("%d",&a[i]);
        if(a[i]>maxx)
            maxx=a[i];
        if(a[i]<minn)
            minn=a[i];
    }
    printf("%d %d",minn,maxx); 
    return 0;
} 
```

 E 韩信点兵

这个直接暴力找就行  for循环+判断

```
#include<stdio.h>
#include<string.h>
int main()
{
    int a,b,c;
    int o=0;
    scanf("%d%d%d",&a,&b,&c);
    for(int i=11;i<=100;i++)
    {
        if(i%3==a&&i%5==b&&i%7==c)
        {
            o=1;
            printf("%d",i);
            break;
        }
    }
    if(!o)
        printf("No answer\n");
    return 0;
}
#include<stdio.h>
#include<string.h>
int main()
{
    int a,b,c;
    int o=0;
    scanf("%d%d%d",&a,&b,&c);
    for(int i=11;i<=100;i++)
    {
        if(i%3==a&&i%5==b&&i%7==c)
        {
            o=1;
            printf("%d",i);
            break;
        }
    }
    if(!o)
        printf("No answer\n");
    return 0;
} 
```

# F 水仙花数

这个就更简单了   将三位数的 个位 十位 百位 取出来 然后直接判断就行

```
#include<stdio.h>
#include<string.h>
int main()
{
    int a,b,c;
    int n;
    int o=0;
    while(scanf("%d",&n)&&n)
    {
        a=n/100;//百位
        b=n%100/10;//十位
        c=n%10;//个位
        if(n==a*a*a+b*b*b+c*c*c)
            printf("Yes\n");
        else
            printf("No\n");
    }
    return 0;
}
```

# G 公约数和公倍数

这个题是有难度的 其实也不是很难  只要求出公约数 那么公倍数就是 a*b/公约数  就行了  公约数可以看辗转相除法

```
#include<stdio.h>
#include<string.h>
int gys(int m,int n)
{
    int t;
    if(m>n)//题目没有指定m n 谁大谁小 所以只能变化一下
    {
        int i=m;
        m=n;
        n=i;
    }
    while(n)
    {
        t=n;
        n=m%n;
        m=t;
    }
    return m;
}
int main()
{
    int n;
    scanf("%d",&n);
    while(n--)
    {
        int a,b;
        scanf("%d%d",&a,&b);
        printf("%d %d\n",gys(a,b),a*b/gys(a,b));
    }
    return 0;
} 
```

# H 三个数从小到大排序

这个也很简单了  就是输入三个数 比较大小就行 这里百度 搜索一下sort就行 sort 是一个类似快排的函数 一般数组排序都会用sort

```
#include<stdio.h>
#include<string.h>
#include<math.h>
#include<algorithm>
using namespace std;
int main()
{
    int a[5];
    for(int i=0;i<3;i++)
    {
         scanf("%d",&a[i]);
    }
    sort(a,a+3);
    printf("%d %d %d",a[0],a[1],a[2]);
    return 0;
} 
```

# I 鸡兔同笼

这个推一下就出来  设鸡为 x  兔 为 y   x+y=n  2x+4y=m （这个式子可以看出来m必须为偶数） 化简一下就行了 但是 x y不能为0

```
#include<stdio.h>
#include<string.h>
#include<math.h>
#include<algorithm>
using namespace std;
int main()
{
    int t,n,m,j,tt;
    scanf("%d",&t);
    while(t--)
    {
         scanf("%d%d",&n,&m);
         if(m%2)
             printf("No answer\n");
           else
          {
            tt=m/2-n;
            j=n-tt;
            if(tt<0||j<0)
               printf("No answer\n");
            else
             printf("%d %d\n",j,tt);
          }

    }
    return 0;
} 
```

下一篇链接  [链接](https://mp.csdn.net/postedit/82744056)