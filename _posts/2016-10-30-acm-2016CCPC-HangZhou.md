---
layout: post
title: "2016 CCPC 杭州"
excerpt: "A B C F题题解"
date: 2016-10-30
comments: true
tags: [ACM, 2016 CCPC]
---

这一场写的还算是可以，4题过的比较快，但是后面就挂机了，那个数论没搞出来有点可惜...


----------

## Problem A

### 题目大意&题解

&#160;&#160;&#160;&#160;有n个block，每个block都有一定数量的员工，现在要重新分成k个block，要求每个block的员工数量相同，每次可以对相邻的block进行合并，也可以拆分一个block，问最少的操作次数。

&#160;&#160;&#160;&#160;可以注意到如果总数除不尽k的话肯定是分不了的；如果可以分，对于少的block要增加，对于多的block要减少，基于这个思想，我们从第一个block模拟到最后一个block，比标准值多久减少，比标准值少就增加（相同的话直接清零）。

&#160;&#160;&#160;&#160;感觉以前CF有过类似的题。

### 代码
{% highlight c++ %}
#include <iostream>
#include <cstring>
#include <cstdio>
#define maxn 100005
#define LL long long

using namespace std;

int T,n,k;
LL a[maxn];

int main()
{
    int Case=1;
    scanf("%d",&T);
    while (T--)
    {
        memset(a,0,sizeof(a));

        scanf("%d%d",&n,&k); LL sum=0;
        printf("Case #%d: ",Case++);
        for (int i=0;i<n;i++)
        {
            scanf("%I64d",&a[i]);
            sum+=a[i];
        }
        if (sum%k!=0)
        {
            printf("-1\n");
            continue ;
        }
        sum/=k; LL last=0,ans=0;
        for (int i=0;i<n;i++)
        {
            last+=a[i];
            while (last>sum)
            {
                last-=sum;
                ans++;
            }
            if (last==sum)
            {
                last=0;
                continue;
            }
            else ans++;
        }
        //if (last) ans+=(last/sum-1);

        printf("%I64d\n",ans);
    }
    return 0;
}
{% endhighlight %}


----------

## Problem B

### 题目大意&题解

&#160;&#160;&#160;&#160;有n个炸弹，每个炸弹都有一个爆炸半径和爆炸代价，引爆一个炸弹会把半径范围里面未爆炸的所有炸弹引爆。问引爆所有炸弹的最小代价。

&#160;&#160;&#160;&#160;注意到炸弹爆炸的传递性，想到建图后强连通缩点，由于缩点后的图是个DAG，而且我们只用引爆入度为0的点集中代价最少的那一个就行了。所以在重新建图的时候维护度数和最少代价就行了。

###代码

{% highlight c++ %}
#include <iostream>
#include <cstring>
#include <cstdio>
#include <cmath>
#include <vector>
#include <queue>
#define LL long long
#define maxn 1005

using namespace std;

int T,n,pre[maxn],cnt,dfn[maxn],low[maxn],p,c[maxn];
int sta[maxn],h,scc[maxn],num,de[maxn],ans[maxn];
bool vis[maxn];
LL x[maxn],y[maxn],r[maxn];
struct edge
{
    int u,v,next;
};
edge e[maxn*maxn*2];

void addedge(int u,int v)
{
    e[cnt].u=u; e[cnt].v=v;
    e[cnt].next=pre[u]; pre[u]=cnt++;
}

void solve(int i,int j)
{
    LL d=(x[i]-x[j])*(x[i]-x[j])+(y[i]-y[j])*(y[i]-y[j]);

    if (d<=r[i]*r[i] ) addedge(i,j);
    if (d<=r[j]*r[j] ) addedge(j,i);
}

void tarjan(int u)
{
    vis[u]=1; sta[h++]=u;
    dfn[u]=low[u]=p++;

    for (int i=pre[u];~i;i=e[i].next)
    {
        int v=e[i].v;
        if (!dfn[v])
        {
            tarjan(v);
            low[u]=min(low[u],low[v]);
        }
        else if (vis[v]) low[u]=min(low[u],dfn[v]);
    }

    if (dfn[u]==low[u])
    {
        int v;
        do
        {
            v=sta[--h];
            scc[v]=num;
            ans[num]=min(ans[num],c[v]);
            vis[v]=0;
        }
        while (u!=v) ;
        num++;
    }
}

void buildmap()
{
    memset(de,0,sizeof(de));

    int u,v;
    for (int i=0;i<cnt;i++)
    {
        u=e[i].u; v=e[i].v;
        if (scc[u]!=scc[v]) de[scc[v]]++;
    }
}

int answer()
{
    int ret=0;
    for (int i=0;i<num;i++) if (!de[i]) ret+=ans[i];
    return ret;
}

int main()
{
    int Case=1;
    scanf("%d",&T);
    while (T--)
    {
        memset(pre,-1,sizeof(pre)); cnt=0;
        memset(dfn,0,sizeof(dfn)); h=0; p=1;
        memset(low,0,sizeof(low));
        memset(scc,0,sizeof(scc)); num=0;
        memset(ans,0x3f,sizeof(ans));
        memset(vis,0,sizeof(vis));

        scanf("%d",&n);

        for (int i=1;i<=n;i++) scanf("%I64d%I64d%I64d%d",&x[i],&y[i],&r[i],&c[i]);
        for (int i=1;i<=n-1;i++)
            for (int j=i+1;j<=n;j++) solve(i,j);

        for (int i=1;i<=n;i++) if (!dfn[i]) tarjan(i);

        buildmap();
        printf("Case #%d: %d\n",Case++,answer());
    }
    return 0;
}
{% endhighlight %}


----------

## Problem C

### 题目大意&题解

&#160;&#160;&#160;&#160;有个老司机正在飙车，他的速度是非减的，现在警察发现了他，警察有n个记录，每个记录是他在某个整点的位置，记录的位置是依次递增的，问这个老司机经过最后面一个记录点时的最短时间。

&#160;&#160;&#160;&#160;要求最短，首先最后面一段的时间肯定是1，也就是速度值等于那一段的长，然后我们从最后面一段向前模拟，每次二分找到最大的满足条件的时间，累加进答案即可。

### 代码
{% highlight c++ %}
#include <iostream>
#include <cstring>
#include <cstdio>
#define maxn 100005

using namespace std;

int T,a[maxn],n;

int main()
{
    int Case=1;
    scanf("%d",&T);
    while (T--)
    {
        memset(a,0,sizeof(a));

        scanf("%d",&n);
        for (int i=1;i<=n;i++) scanf("%d",&a[i]);
        double spe=a[n]-a[n-1],c; int l,r,mid,ans=1;
        for (int i=n-1;i>=1;i--)
        {
            c=(double) (a[i]-a[i-1]);
            l=1; r=(int)c +1;
            while (l<=r)
            {
                mid=(l+r)>>1;
                if (c/mid>spe) l=mid+1;
                else r=mid-1;
            }
            spe=c/l;
            ans+=l;
        }
        printf("Case #%d: %d\n",Case++,ans);
    }
    return 0;
}
{% endhighlight %}


----------

## Problem F

### 题目大意&题解

&#160;&#160;&#160;&#160;给你一个字符串s，要你把\\(+-*/\\)这四个运算符依次放入，求结果的最大值。

&#160;&#160;&#160;&#160;这一题大胆猜想只可能是两个数相乘除一个两位数或者是一位数，前面的加法也一定是一个一位数加上另外一个剩下的数，这样贪心先算出减去的值，再算出前面加法的最大值，总和一下就好了。

&#160;&#160;&#160;&#160;这个贪心感觉挺容易想到的...只是不知道怎么证明（笑着哭）

### 代码
{% highlight c++ %}
#include <iostream>
#include <cstring>
#include <cstdio>
#define LL long long

using namespace std;

int T;
char s[25];

int main()
{
    int Case=1;
    scanf("%d",&T);
    while (T--)
    {
        memset(s,0,sizeof(s));

        scanf("%s",s);
        int l=strlen(s);
        LL cost=(s[l-3]-'0')*(s[l-2]-'0')/(s[l-1]-'0');
        LL ans1=0,ans2=0,ans3=0,k;
        int i=l-4; k=1;

        while (i>=1)
        {
            ans1+=(s[i]-'0')*k;
            k*=10; i--;
        }
        ans1+=(s[0]-'0');

        i=l-5; k=1;
        while (i>=0)
        {
            ans2+=(s[i]-'0')*k;
            k*=10; i--;
        }
        ans2+=(s[l-4]-'0');
        ans1=max(ans1,ans2);
        ans3=ans1-cost;

        //way 2
        if (l>=6)
        {
            LL cost=(s[l-4]-'0')*(s[l-3]-'0')/((s[l-2]-'0')*10+s[l-1]-'0');
            int i=l-5; k=1; ans1=0;
            while (i>=1)
            {
                ans1+=(s[i]-'0')*k;
                k*=10; i--;
            }
            ans1+=(s[0]-'0');

            i=l-6; k=1; ans2=0;
            while (i>=0)
            {
                ans2+=(s[i]-'0')*k;
                k*=10; i--;
            }
            ans2+=(s[l-5]-'0');
            ans1=max(ans1,ans2);
            ans1-=cost;
            ans3=max(ans3,ans1);
        }
        printf("Case #%d: %I64d\n",Case++,ans3);
    }
    return 0;
}
{% endhighlight %}