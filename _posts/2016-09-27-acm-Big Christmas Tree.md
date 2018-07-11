---
layout: post
title: "【POJ 3013】Big Christmas Tree"
excerpt: "有意思的最短路"
date: 2016-09-27
comments: true
tags: [ACM, 图论, 最短路]
---

# Big Christmas Tree


----------

## 题目链接

- [Big Christmas Tree](http://poj.org/problem?id=3013)


----------

## 题目大意

&#160;&#160;&#160;&#160;挺有意思的一题。

&#160;&#160;&#160;&#160;题目大意是现在有一个图，n个点，m条边，现在需要建一棵树，规定1为根，每条边的权值是子树上的点的权值乘上边的权值，现在要让这棵树的代价最小，问最小代价。


----------

## 题解

&#160;&#160;&#160;&#160;我们考虑每个点对总代价的贡献，发现：
\\[
每个点的贡献=点权值*该点到根的路径和
\\]

&#160;&#160;&#160;&#160;这样就很清楚了，要使总贡献最小，使路径最短就可以了。于是变成了裸的单源最短路。


----------

## 代码

{% highlight c++ %}

#include <iostream>
#include <cstdio>
#include <cstring>
#include <queue>
#define LL long long
#define maxn 100005

using namespace std;

int T,v,E,cnt,pre[maxn];
LL c[maxn],d[maxn];
struct edge
{
    int u,v,next;
    LL d;
};
edge e[maxn<<1];

void addedge(int u,int v,LL dis)
{
    e[cnt].u=u; e[cnt].v=v; e[cnt].d=dis;
    e[cnt].next=pre[u]; pre[u]=cnt++;
}

void spfa(int st)
{
    queue<int> q;
    while (!q.empty()) q.pop();
    bool vis[maxn];
    memset(vis,0,sizeof(vis));

    q.push(st); vis[st]=1; d[st]=0;
    while (!q.empty())
    {
        int u=q.front(); q.pop();
        vis[u]=0;
        for (int i=pre[u];i!=-1;i=e[i].next)
        {
            int v=e[i].v;
            if (d[v]==-1 || d[v]>d[u]+e[i].d)
            {
                d[v]=d[u]+e[i].d;
                if (!vis[v])
                {
                    vis[v]=1; q.push(v);
                }
            }
        }
    }
}

int main()
{
    scanf("%d",&T);
    while (T--)
    {
        memset(c,0,sizeof(c));
        memset(e,0,sizeof(e)); cnt=0;
        memset(d,-1,sizeof(d));
        memset(pre,-1,sizeof(pre));
        int a,b; LL dis;

        scanf("%d%d",&v,&E);

        for (int i=0;i<v;i++) scanf("%I64d",&c[i]);
        for (int i=0;i<E;i++)
        {
            scanf("%d%d%I64d",&a,&b,&dis);
            addedge(a,b,dis); addedge(b,a,dis);
        }
        spfa(1);

        LL ans=0; bool f=0;
        for (int i=1;i<=v;i++)
        {
            if (d[i]==-1)
            {
                f=1;
                break;
            }
            ans+=d[i]*c[i-1];
        }
        if (f) printf("No Answer\n");
        else printf("%I64d\n",ans);
    }
    return 0;
}
{% endhighlight %}


----------
