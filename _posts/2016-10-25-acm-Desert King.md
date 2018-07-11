---
layout: post
title: "POJ 2728 Desert King"
excerpt: "最优比例生成树"
date: 2016-10-25
comments: true
tags: [ACM, 图论, 生成树]
---

## 题目大意&题解

&#160;&#160;&#160;&#160;给你n个点，每个点都有一个高度h和一个位置\\((x,y)\\)，每两个点的距离是欧几里得距离，每两个点之间的代价是高度差，问你把这n个点连起来，要求总代价除以总长度最小。

&#160;&#160;&#160;&#160;可以构造这样一个式子
\\[
\frac{\sum cost_i}{\sum len_i}=ans
\\]

&#160;&#160;&#160;&#160;其中ans是最终的解（最小比率）。

&#160;&#160;&#160;&#160;通过移项：
\\[
\sum (cost_i-ans·len_i )=0
\\]

&#160;&#160;&#160;&#160;我们发现，如果我们取得ans偏小，该式就大于0否则就会小于零，于是我们考虑到二分答案，然后从取小边（这里思考为什么要取小的边？）

## 代码

{% highlight c++ %}
#include <iostream>
#include <cstring>
#include <cstdio>
#include <algorithm>
#include <cstdlib>
#include <queue>
#include <cmath>
#define maxn 1005

using namespace std;

int x[maxn],y[maxn],z[maxn],pre[maxn],cnt,n;
double dis[maxn],D[maxn][maxn],C[maxn][maxn];
bool vis[maxn];

double prim(int u,double k)
{
    memset(vis,0,sizeof(vis));
    memset(dis,0,sizeof(dis));

    for (int i=0;i<n;i++) dis[i]=C[u][i]-D[u][i]*k;
    vis[u]=1;
    int p;
    double Min,ans=0;
    for (int i=1;i<n;i++)
    {
        Min=100000000000;
        for (int j=0;j<n;j++) if (!vis[j] && dis[j]<Min)
        {
            Min=dis[j];
            p=j;
        }
        vis[p]=1;
        ans+=dis[p];
        for (int j=0;j<n;j++)
        {
            double V=C[p][j]-D[p][j]*k;
            if (!vis[j] && V<dis[j])
                dis[j]=V;
        }
    }
    return ans;
}

int main()
{
    double d,c;
    while (scanf("%d",&n),n!=0)
    {
        memset(D,0,sizeof(D));
        memset(C,0,sizeof(C));

        for (int i=0;i<n;i++) scanf("%d%d%d",&x[i],&y[i],&z[i]);
        for (int i=0;i<n-1;i++)
            for (int j=i+1;j<n;j++)
            {
                d=sqrt( 1.0*(x[i]-x[j])*(x[i]-x[j]) +1.0*(y[i]-y[j])*(y[i]-y[j]) ); c=abs(z[i]-z[j]);
                D[i][j]=D[j][i]=d; C[i][j]=C[j][i]=c;
            }
        double l=0,r=100,mid;
        while (r-l>=1e-7)
        {
            mid=(l+r)/2;
            if (prim(0,mid)>=0) l=mid;
            else r=mid;
        }
        printf("%.3f\n",r);
    }
    return 0;
}
{% endhighlight %}