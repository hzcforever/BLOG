---
layout: post
title: "初识主席树"
date: 2017-08-22
excerpt: "Gym 1012376 & HDU 2665 "
tags: [ACM, 数据结构]
comments: true
---

&#160;&#160;&#160;&#160;第一次接触主席树，前队友说是个挺简单的东西，一直到今天才学...

&#160;&#160;&#160;&#160;主席树准确来讲是一棵可重用的线段树，仍然是解决区间问题，但是这里的询问会比普通的询问更多一维，比如询问\\([L,R]\\)的第k小（或者第k大），会发现一般的线段树很难处理——这时我们引入主席树。

&#160;&#160;&#160;&#160;主席树由很多个线段树组成的，但是这些线段树上有很多节点都是可以公用的。对于一棵线段树而言，他看上去就像是由很多个节点“拼起来”的。而对于每个节点，它可能会被多个线段树所利用，这样一来使得我们可以存储更多的信息，另一方面使得我们程序的空间不至于太大。

&#160;&#160;&#160;&#160;一般来讲我们做主席树习惯以前缀来建，前缀可以很好的维护一些具有可加性的性质，这个马上会看到。

## [HDU 2665](http://acm.hdu.edu.cn/showproblem.php?pid=2665)

&#160;&#160;&#160;&#160;上面提到过的，这个题让我们求一个区间的第k小，我们考虑一个区间，如果我们要求这个区间的第k小，我们可以用先在线段树上插入每一个值，然后像排序树一样左右查找。但是现在有n个区间，于是我们想到了主席树。

&#160;&#160;&#160;&#160;我们对每一个前缀建线段树，这样对于每一个区间，我们可以通过前缀的可加性来减出当前区间的值，因为我们是对于每个前缀建树，所以查询的时候有点像在两颗线段树上查，然后减出当前的结果。

&#160;&#160;&#160;&#160;代码上写的很清晰。

{% highlight C++ %}
#include <iostream>
#include <cstring>
#include <cstdio>
#include <map>
#define maxn 100005

using namespace std;

int n,a[maxn],ans[maxn],root[maxn],cnt,q;
map<int,int> mp;
map<int,int>::iterator it;
struct tree
{
    int v,lc,rc;
}d[maxn*18];

void Insert(int pre,int cur,int p,int l,int r)
{
    if (l==r)
    {
        d[cur].v=d[pre].v+1;
        return ;
    }
    int mid=(l+r)>>1;
    if (p<=mid)
    {
        d[cur].lc=cnt++;
        d[cur].rc=d[pre].rc;
        Insert(d[pre].lc,d[cur].lc,p,l,mid);
    }
    else
    {
        d[cur].lc=d[pre].lc;
        d[cur].rc=cnt++;
        Insert(d[pre].rc,d[cur].rc,p,mid+1,r);
    }
    d[cur].v=d[d[cur].lc].v+d[d[cur].rc].v;
}

int query(int curL,int curR,int k,int l,int r)
{
    if (l==r) return l;
    int mid=(l+r)>>1,c=d[d[curR].lc].v-d[d[curL].lc].v;
    if (c>=k) return query(d[curL].lc,d[curR].lc,k,l,mid);
    else return query(d[curL].rc,d[curR].rc,k-c,mid+1,r);
}

int main()
{
    int T;
    scanf("%d",&T);
    while (T--)
    {
        memset(d,0,sizeof(d));
        memset(ans,0,sizeof(ans));
        mp.clear();

        scanf("%d%d",&n,&q);
        for (int i=1;i<=n;i++)
        {
            scanf("%d",&a[i]);
            mp[a[i]]=1;
        }
        cnt=0;
        for (it=mp.begin();it!=mp.end();it++)
        {
            it->second=++cnt;
            ans[cnt]=it->first;
        }
        int si=cnt;
        cnt=1;
        for (int i=1;i<=n;i++)
        {
            root[i]=cnt++;
            Insert(root[i-1],root[i],mp[a[i]],1,si);
        }
        while (q--)
        {
            int L,R,k;
            scanf("%d%d%d",&L,&R,&k);
            //k=d[root[R]].v-d[root[L-1]].v-k+1;
            printf("%d\n",ans[query(root[L-1],root[R],k,1,si)]);
        }
    }
    return 0;
}

{% endhighlight %}

## [Gym 101237A](https://cn.vjudge.net/contest/169822#problem/A)

&#160;&#160;&#160;&#160;这一题的意思是让我们求出一个区间里，没有出现过的最小正整数。

&#160;&#160;&#160;&#160;依旧是对前缀建树，只不过不用减了，我们记录每个值出现过的最右位置，在线段树上我们保存这个值的最小值，听起来有点绕口——意思是对于一个以\\(1..i\\)为根的线段树中，节点\\([L,R]\\)保存的是\\([L,R]\\)这些值中，某个值出现位置的最小位置（如果有值未出现，那么这个值就是0）。

&#160;&#160;&#160;&#160;这样我们就可以查询左区间的值来和L作比较，如果左区间的值小于l，说明左区间有值未在\\([L,R]\\)中出现，这样我们就可以像排序树一样左右查询了。

{% highlight c++ %}
#include <iostream>
#include <cstring>
#include <cstdio>
#include <cmath>
#define maxn 1000005

using namespace std;

struct tree
{
    int v,lc,rc;
}d[maxn*21];
int root[maxn],cnt,n,q,L,R;

void Insert(int pre,int cur,int p,int v,int l,int r)
{
    if (l==r)
    {
        d[cur].v=v;
        return ;
    }
    int mid=(l+r)>>1;
    if (p<=mid)
    {
        d[cur].lc=++cnt;
        d[cur].rc=d[pre].rc;
        Insert(d[pre].lc,d[cur].lc,p,v,l,mid);
    }
    else
    {
        d[cur].lc=d[pre].lc;
        d[cur].rc=++cnt;
        Insert(d[pre].rc,d[cur].rc,p,v,mid+1,r);
    }
    d[cur].v=min(d[d[cur].lc].v,d[d[cur].rc].v);
}

int query(int cur,int l,int r)
{
    if (l==r) return l;
    int mid=(l+r)>>1;
    if (d[d[cur].lc].v<L) return query(d[cur].lc,l,mid);
    else return query(d[cur].rc,mid+1,r);
}

int main()
{
   // printf("%lf\n",log2(1e6));
    scanf("%d",&n);
    for (int i=1;i<=n;i++)
    {
        int a;
        scanf("%d",&a);
        root[i]=++cnt;
        Insert(root[i-1],root[i],a+1,i,1,1e6+1);
    }
    scanf("%d",&q);
    while (q--)
    {
        scanf("%d%d",&L,&R);
        printf("%d\n",query(root[R],1,1e6+1)-1);
    }
    return 0;
}
{% endhighlight %}