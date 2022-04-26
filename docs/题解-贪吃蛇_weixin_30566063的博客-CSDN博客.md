<!--yml
category: 未分类
date: 2022-04-26 14:46:23
-->

# 题解 贪吃蛇_weixin_30566063的博客-CSDN博客

> 来源：[https://blog.csdn.net/weixin_30566063/article/details/97566069](https://blog.csdn.net/weixin_30566063/article/details/97566069)

## 贪吃蛇

### Description

身长为L的贪吃蛇在一个有障碍的N*M的格子中游走，问最少用多少步才能让贪吃蛇的蛇头到达(1，1）。

### Input

第一行三个正整数 N, M，L。L表示贪吃蛇的长度。
接下来 L 行，顺序描述贪吃蛇每节身体的位置。每行两个正整数 X，Y。 表示某节身体的位置，按蛇头到蛇尾的顺序描述。
接下来一个正整数K。 表示有 K个障碍，每个障碍占一个格子。
接下来K行， 每行两个正整数 X，Y， 表示某个障碍的位置。

### Output

一个整数，表示到达格子（1,1）最少的步数。（给定数据保证能够到达，并且蛇头移动的目标格子必须是空的。）

### Sample Input

5 6 4 
4 1 
4 2 
3 2 
3 1 
3 
2 3 
3 3 
3 4

### Hint

对于 100% 的数据，2≤N、M≤20，2≤L≤8

### 解析

这题一看就要用搜索啊啊啊！！！

然而仔细一想，却有很多问题，其中主要有（因为是本人遇到的）：

*   蛇走的时候障碍物是变化的，每走一步都要更新，因此地图走起来就很繁琐（本蒟蒻一开始还用了三维数组）。
*   如果暴搜的话，空间会炸掉（循环队列也一样）。

那么，让我们仔细想想。。。

首先，蛇的位置并不需要O（L）一个个修改。

假设，下面是一条蛇（@为蛇头）

########@

那么，它向前走一步后就变成了

   ########@

也就是说，原来蛇尾的部分没有了，原来蛇头前面多了一部分。

所以，只需要记录下每个部分在蛇头位置时，所对应的蛇尾部分（**也就是说，蛇头是不断在由不同的部分替换着的**），

再修改蛇尾及地图就行了。

具体代码如下：

```
int x1=t[t[p].net].x;    //t[p].net表示当p部分为蛇头时蛇尾是哪部分 
int y1=t[t[p].net].y;
t[t[p].net].x=x;
t[t[p].net].y=y;
s[x][y]=1;s[x1][y1]=0;  //x和y代表将要更新的蛇头的位置，s为地图 
```

然后，我们再枚举需要的步数，并dfs判断能否到达（1,1）就行了。

详细讲解请看代码：

```
#include <cstdio> #include <iostream>
using namespace std; struct snake{ int x,y,net;
}; struct hh{ int x,y;
}e[1001]; int n,m,k,l; int map[21][21]/*地图的初始状态*/,w[21][21]/*（1,1）到个点的最短步数*/; int s[21][21]/*移动中的地图状态*/; int dx[4]={0,0,1,-1}; int dy[4]={1,-1,0,0}; int maxn/*规定步数*/,ok=0/*标记*/;
snake a[10]/*记录蛇的初始状态*/,t[10]/*蛇移动中的状态*/; void bfs()/*简单的深搜*/{ int h=1,t=1,que[100001][3]={0};
    que[1][0]=1;que[1][1]=1;
    w[1][1]=0x3f3f3f3f; while(h<=t){ for(int i=0;i<4;i++){ int x=que[h][0]+dx[i]; int y=que[h][1]+dy[i]; if(x>0&&y>0&&x<=n&&y<=m){ if(!map[x][y]&&!w[x][y]){
                     w[x][y]=que[h][2]+1;
                     que[++t][0]=x;
                     que[t][1]=y;
                     que[t][2]=w[x][y];
                 }

             }
        }
        h++;
    }
    w[1][1]=0;
} void dfs(int p/*当前作为蛇头的部分*/,int d/*已走步数*/){
    snake v=t[p];  //便于书写 
    if(d+w[v.x][v.y]>maxn) return ;//当前位置到（1,1）的最短步数+已走的步数大于规定步数时，直接返回 
    else if(d+w[v.x][v.y]==maxn)/*规定步数走到（1,1）*/{ if(v.x==1&&v.y==1){
            ok=1;//标记 
            return ;
        }
    } for(int i=0;i<4;i++)/*和深搜类似*/{ if(ok) return ; int x=v.x+dx[i]; int y=v.y+dy[i]; if(x>0&&y>0&&x<=n&&y<=m){ if(!s[x][y]){ int x1=t[t[p].net].x;  //t[p].net表示当p部分为蛇头时蛇尾是哪部分 
                int y1=t[t[p].net].y;
                t[t[p].net].x=x;
                t[t[p].net].y=y;
                s[x][y]=1;s[x1][y1]=0;  //x和y代表将要更新的蛇头的位置，s为地图 
                dfs(t[p].net,d+1);
                t[t[p].net].x=x1;
                t[t[p].net].y=y1;
                s[x][y]=0;s[x1][y1]=1;  //回溯 
 }
        }
    }
} int main(){
    scanf("%d%d%d",&n,&m,&l); for(int i=1;i<=l;i++){
        scanf("%d%d",&a[i].x,&a[i].y);
    }
    a[1].net=l; for(int i=2;i<=l;i++) a[i].net=i-1;  //记录i为蛇头时i-1为蛇尾 
    scanf("%d",&k); for(int i=1;i<=k;i++){
        scanf("%d%d",&e[i].x,&e[i].y);
        map[e[i].x][e[i].y]=1;
    } //map记录了障碍物的位置 
    bfs();  //用bfs求出（1,1）在不考虑蛇身的情况下到达每个点的最短步数 
    for(int i=1;i<=l;i++){
        map[a[i].x][a[i].y]=1;
    }//bfs后将蛇身的初始位置加入障碍物中 
    for(maxn=1;ok==0/*还未走到过（1,1）*/;maxn++)/*枚举规定步数*/{ for(int i=1;i<=n;i++){ for(int j=1;j<=m;j++){
                s[i][j]=map[i][j];
            }
        }/*每次将地图还原成初始的状态*/
        for(int i=1;i<=l;i++) t[i]=a[i]/*还原蛇身初始状态*/;
        dfs(1,0)/*判断能否到达（1,1）*/;
    }
    printf("%d\n",maxn-1/*因为最后会多加一下*/);
}
```