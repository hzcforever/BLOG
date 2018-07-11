---
layout: post
title: "赛前集训（重现赛-2）题解"
excerpt: "A B D E题解"
date: 2016-10-24
comments: true
tags: [ACM]
---




这里是题目链接和原题解的地址

- [题目链接](https://acm.bnu.edu.cn/v3/statments/jag2016.pdf)

下载下来看应该会好看一点。

- [原题解（非官方）](https://async.icpc-camp.org/d/581-jag-practice-contest-for-acm-icpc-asia-regional-2016-hints)

还有一个官方题解，不过是日文的...有兴趣的可以看看...

- [官方题解](http://acm-icpc.aitea.net/index.php?2016%2FPractice%2F%E6%A8%A1%E6%93%AC%E5%9C%B0%E5%8C%BA%E4%BA%88%E9%81%B8%2F%E8%AC%9B%E8%A9%95)


----------

## Problem A


----------

### 题目大意&题解

&#160;&#160;&#160;&#160;题目意思是有n个数，每两个数都可以乘，还要求这个乘积满足每个数位从左到右连续递增1，最后求满足条件的最大乘积。

&#160;&#160;&#160;&#160;这个题主要是读懂题意，之后直接按照题目意思模拟个\\(O(n^2)\\)的算法就可以了。（每两个点都试一次，然后在满足要求的情况下取最大的）


----------

### 代码

{% highlight c++ %}
#include <iostream>
#include <cstring>
#include <cstdio>
#define maxn 2005
#define LL long long

using namespace std;

int n; LL a[maxn];

bool Check(LL a)
{
    int last=a%10,c;
    a/=10;
    while (a)
    {
        c=a%10;
        if (last-c!=1) break;
        a/=10;
        last=c;
    }
    if (a) return 0;
    else return 1;
}

int main()
{
    while (scanf("%d",&n)!=EOF)
    {
        memset(a,0,sizeof(a));
        LL ans=0,p;

        for (int i=0;i<n;i++) scanf("%lld",&a[i]);
        for (int i=0;i<n;i++)
            for (int j=0;j<i;j++)
        {
            p=a[i]*a[j];
            if (p>ans && Check(p)) ans=p;
        }
        if (ans) printf("%lld\n",ans);
        else printf("-1\n");
    }
    return 0;
}
{% endhighlight %}


----------

## Problem B


----------

### 题目大意&题解

&#160;&#160;&#160;&#160;说有一个公主被困在城堡里面，城堡里面还有士兵和，城堡只有一个出口，公主现在想逃出去，每秒钟公主和士兵都可以向四周不是石头的地方前进一格，而且他们都不知道彼此的动向，问是否存在一种方法，公主一定能逃出去？

&#160;&#160;&#160;&#160;直接用两个队列模拟公主和士兵的move就可以了，当士兵比公主优先走到终点时直接判断为不可行（士兵一直在这里站着就行了，当然公主还有可能根本就走不到终点）

&#160;&#160;&#160;&#160;当然这样写确实复杂了，实际上这样做会快很多：

> 求出出口到每个位置的距离, 如果距离公主的距离比距离士兵的距离都小, 那么一定能逃出去, 否则士兵肯定可以先到出口位置等着公主.


----------

### 代码

{% highlight c++ %}
#include <iostream>
#include <cstring>
#include <cstdio>
#include <queue>
#define maxn 205

using namespace std;

int n,m,way[4][2]={ {0,1},{0,-1},{-1,0},{1,0} };
bool vis[maxn][maxn],qvis[maxn][maxn];
char s[maxn][maxn];
struct node
{
    int x,y,t;
    node(int a,int b,int c) {x=a; y=b; t=c;}
};
queue<node> Q,S;

bool Check(int x,int y)
{
    if (x<0 || x>=n || y<0 || y>=m) return 0;
    return 1;
}

bool solve()
{
    node c(0,0,0); int t=1,x,y;
    while (1)
    {
        while (!S.empty())
        {
            c=S.front();
            if (c.t!=t) break;
            S.pop();
            for (int i=0;i<4;i++)
            {
                x=c.x+way[i][0]; y=c.y+way[i][1];
                if (!Check(x,y)) continue ;
                if (!vis[x][y] && s[x][y]!='#')
                {
                    vis[x][y]=1;
                    S.push(node(x,y,t+1));
                    if (s[x][y]=='%') return 0;
                }
            }
        }
        while (!Q.empty())
        {
            c=Q.front();
            if (c.t!=t) break;
            Q.pop(); qvis[c.x][c.y]=0;
            for (int i=0;i<4;i++)
            {
                x=c.x+way[i][0]; y=c.y+way[i][1];
                if (!Check(x,y)) continue ;
                if (!vis[x][y] && s[x][y]=='%') return 1;
                if (!vis[x][y] && !qvis[x][y] && s[x][y]!='#')
                {
                    qvis[x][y]=1;
                    Q.push(node(x,y,t+1));
                }
            }
            if (!vis[c.x][c.y] && !qvis[c.x][c.y])
            {
                qvis[c.x][c.y]=1;
                Q.push(node(c.x,c.y,t+1));
            }
        }
        if (Q.empty()) return 0;
        t++;
    }
}

int main()
{
    while (~scanf("%d%d",&n,&m))
    {
        memset(s,0,sizeof(s));
        memset(vis,0,sizeof(vis));
        memset(qvis,0,sizeof(qvis));
        while (!Q.empty()) Q.pop();
        while (!S.empty()) S.pop();

        for (int i=0;i<n;i++) scanf("%s",&s[i]);
        for (int i=0;i<n;i++)
            for (int j=0;j<m;j++)
            if (s[i][j]=='@')
            {
                qvis[i][j]=1;
                Q.push(node(i,j,1));
            }
            else if (s[i][j]=='$')
            {
                vis[i][j]=1;
                S.push(node(i,j,1));
            }
        if (solve()) printf("Yes\n");
        else printf("No\n");
    }
    return 0;
}
{% endhighlight %}


----------


## Problem D


----------

### 题目大意&题解

&#160;&#160;&#160;&#160;给出一个数字n，让你构造一个串，由'('  ')'组成，满足**必须**交换n次后，该串合法（一个前括号对应一个后括号）

&#160;&#160;&#160;&#160;构造题，可以发现类似这样的串

\\[ )))(((\\]

&#160;&#160;&#160;&#160;是同等长度的串中交换次数最多的，而且交换次数为\\[ \frac{(1+n)n}{2}\\]

&#160;&#160;&#160;&#160;这样我们有了一个思路：先预处理出所有这样的串需要的次数，然后二分找到 大于等于 它的第一个串，如果等于直接输出就好，否则把第一个左括号右移（减少交换次数），直到满足n次即可。


----------

### 代码

{% highlight c++ %}
#include <iostream>
#include <cstring>
#include <cstdio>
#define LL long long

using namespace std;

int n,cnt,sta[100005],h;
LL p[100005];

void setup()
{
    LL sum;
    for (LL i=1; ;i++)
    {
        sum=(1+i)*i/2;
        p[i]=sum;
        if (sum>1e9)
        {
            cnt=i;
            return ;
        }
    }
}

int main()
{
    setup();
    while (~scanf("%d",&n))
    {
        int l,r,mid;
        l=1,r=cnt;
        while (l<=r)
        {
            mid=(l+r)>>1;
            if (n>=p[mid]) l=mid+1;
            else r=mid-1;
        }

        if (n==p[r])
        {
            for (int i=0;i<r;i++) printf(")");
            for (int i=0;i<r;i++) printf("(");
            printf("\n");
        }
        else
        {
            int c=p[l]-n;
            for (int i=1;i<=l-c;i++) printf(")");
            printf("(");
            for (int i=l-c+1;i<=l;i++) printf(")");
            for (int i=0;i<l-1;i++) printf("(");
            printf("\n");
        }
    }
    return 0;
}
{% endhighlight %}


----------

## Problem E


----------

### 题目大意&题解

&#160;&#160;&#160;&#160;定义\\(S（T，d）\\)为树T中深度为d的点的个数，如果有两个树T，T'，对于所有的d，满足\\(S（T，d）=S（T'，d）\\)，就说这两个树是相似的。

&#160;&#160;&#160;&#160;现在要求一颗树中相同子树的对数。

&#160;&#160;&#160;&#160;一开始以为是个DP，没什么思路，后来发现HASH就可以了...

&#160;&#160;&#160;&#160;规定一个大质数为p，深度为\\(d_i\\)的子树的个数为\\(a_i\\)，整棵树的HASH函数即为\\[ H(d)=\sum_{i=1}^d a_ip^{d_i} \\]

&#160;&#160;&#160;&#160;最后用这个函数再模上一个大质数怕溢出，放到map里，最终对于每个值都计算一下就行了。

&#160;&#160;&#160;&#160;另外一提，这个题好像会因为很奇怪的原因RE（必须用LL存节点编号？）


----------

### 代码

{% highlight c++ %}
#include <iostream>
#include <cstring>
#include <cstdio>
#include <map>
#define maxn 1000005
#define mod 1000000007
#define LL long long
#define C 10007

using namespace std;

int n,pre[maxn],cnt;
LL has[maxn];
struct edge
{
    int u,v;
    int next;
};
edge e[maxn];
map<LL,LL> mp;
map<LL,LL>::iterator it;

void addedge(int u,int v)
{
    e[cnt].u=u; e[cnt].v=v;
    e[cnt].next=pre[u]; pre[u]=cnt++;
}

void dfs(LL u)
{
    has[u]=1;

    for (int i=pre[u];~i;i=e[i].next)
    {
        int v=e[i].v;
        dfs(v);
        has[u]=(has[u]+has[v]*C)%mod;
    }
    mp[has[u]]++;
}

int main()
{
    int u,v;
    while (~scanf("%d",&n))
    {
        memset(pre,-1,sizeof(pre)); cnt=0;
        memset(has,0,sizeof(has));
        mp.clear();

        for (int i=0;i<n-1;i++)
        {
            scanf("%d%d",&u,&v);
            addedge(u,v);
        }
        dfs(1); LL ans=0;
        for (it=mp.begin();it!=mp.end();it++) ans+=(it->second*(it->second-1)/2);
        printf("%lld\n",ans);
    }
    return 0;
}
{% endhighlight %}