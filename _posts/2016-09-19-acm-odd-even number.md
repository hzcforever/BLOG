---
layout: post
title: "【2016沈阳网赛】odd-even number"
date: 2016-09-19
excerpt: "题解"
tags: [ACM, 2016网络赛]
comments: true
---

# odd-even number


----------

## 题目链接

- [odd-even number](http://acm.hdu.edu.cn/showproblem.php?pid=5898)

----------

## 题目大意

&#160;&#160;&#160;&#160;有一种数叫做odd-even number，相连的偶数的个数是奇数，相连的奇数的个数是偶数。现在给你一个范围[l,r]，问你在这个范围内的odd-even number有多少个。


----------

## 题解

&#160;&#160;&#160;&#160;不会数位DP，直接按位搜的，从高位向下搜索每一位合法的情况数。

&#160;&#160;&#160;&#160;本来代码可以短点的...但是考场上脑子比较晕。


----------

## 代码

{% highlight c++ %}
#pragma comment(linker, "/STACK:1024000000,1024000000")
#include <iostream>
#include <cstring>
#include <cstdio>
#define LL long long

using namespace std;

int T,numl[25],numr[25],cnt;
LL l,r;

void deal(int num[],LL x)
{
    if (x<=0) return ;
    if (x)
    {
        deal(num,x/10);
        num[cnt++]=x%10;
    }
}

LL dfs(int num[],int n,int last,int l,int d,int eql)
{
    LL a,b,c,D,i,cnt1,cnt2;
    if (n==d)
    {
        if (last==1 && l%2==0) return 1;
        if (last==0 && l%2==1) return 1;
        return 0;
    }

    if (d==0)
    {
        i=1; cnt1=0;
        while (i<=num[d]) i+=2,cnt1++;
        i=2; cnt2=0;
        while (i<=num[d]) i+=2,cnt2++;
        if (num[d]&1)
        {
            cnt1--;
            a=dfs(num,n,1,1,d+1,1);
            b=cnt1*dfs(num,n,1,1,d+1,0);
            c=cnt2*dfs(num,n,0,1,d+1,0);
            D=dfs(num,n,-1,0,d+1,0);
            return a+b+c+D;
        }
        else
        {
            cnt2--;
            a=dfs(num,n,0,1,d+1,1);
            b=cnt2*dfs(num,n,0,1,d+1,0);
            c=cnt1*dfs(num,n,1,1,d+1,0);
            D=dfs(num,n,-1,0,d+1,0);
            return a+b+c+D;
        }
    }

    if (eql)
    {
        i=1; cnt1=0;
        while (i<=num[d]) i+=2,cnt1++;
        i=0; cnt2=0;
        while (i<=num[d]) i+=2,cnt2++;

        if (last==0)
        {
            if (l&1) //done
            {
                if (num[d]&1)
                {
                    cnt1--;
                    a=dfs(num,n,1,1,d+1,1);
                    b=cnt1*dfs(num,n,1,1,d+1,0);
                    c=cnt2*dfs(num,n,0,l+1,d+1,0);
                    return a+b+c;
                }
                else
                {
                    cnt2--;
                    a=dfs(num,n,0,l+1,d+1,1);
                    b=cnt2*dfs(num,n,0,l+1,d+1,0);
                    c=cnt1*dfs(num,n,1,1,d+1,0);
                    return a+b+c;
                }
            }
            else
            {
                if (num[d]&1)
                {
                    a=cnt2*dfs(num,n,0,l+1,d+1,0);
                    return a;
                }
                else
                {
                    cnt2--;
                    a=dfs(num,n,0,l+1,d+1,1);
                    b=cnt2*dfs(num,n,0,l+1,d+1,0);
                    return a+b;
                }
            }
        }
        else // 1
        {
            if (l%2==0) //done
            {
                if (num[d]&1)
                {
                    cnt1--;
                    a=dfs(num,n,1,l+1,d+1,1);
                    b=cnt1*dfs(num,n,1,l+1,d+1,0);
                    c=cnt2*dfs(num,n,0,1,d+1,0);
                    return a+b+c;
                }
                else
                {
                    cnt2--;
                    a=dfs(num,n,0,1,d+1,1);
                    b=cnt2*dfs(num,n,0,1,d+1,0);
                    c=cnt1*dfs(num,n,1,l+1,d+1,0);
                    return a+b+c;
                }
            }
            else
            {
                if (num[d]&1)
                {
                    cnt1--;
                    a=dfs(num,n,1,l+1,d+1,1);
                    b=cnt1*dfs(num,n,1,l+1,d+1,0);
                    return a+b;
                }
                else
                {
                    a=cnt1*dfs(num,n,1,l+1,d+1,0);
                    return a;
                }
            }
        }
    }
    else
    {
        if (last==1)
        {
            if (l%2==0)
            {
                a=5*dfs(num,n,1,l+1,d+1,0);
                b=5*dfs(num,n,0,1,d+1,0);
                return a+b;
            }
            else
            {
                a=5*dfs(num,n,1,l+1,d+1,0);
                return a;
            }
        }
        else if (last==0)
        {
            if (l%2==1)
            {
                a=5*dfs(num,n,0,l+1,d+1,0);
                b=5*dfs(num,n,1,1,d+1,0);
                return a+b;
            }
            else
            {
                a=5*dfs(num,n,0,l+1,d+1,0);
                return a;
            }
        }
        else
        {
            //deal 0
            a=dfs(num,n,-1,0,d+1,0);

            b=4*dfs(num,n,0,1,d+1,0);

            c=5*dfs(num,n,1,1,d+1,0);
            return a+b+c;
        }
    }
}

int main()
{
    int Case=1;
    scanf("%d",&T);
    while (T--)
    {
        scanf("%I64d%I64d",&l,&r);
        if (r<l)
        {
            printf("Case #%d: 0\n",Case++);
            continue ;
        }
        LL a,b;
        cnt=0;
        deal(numl,l-1);
        a=dfs(numl,cnt,-1,0,0,0);

        cnt=0;
        deal(numr,r);
        b=dfs(numr,cnt,-1,0,0,0);
        printf("Case #%d: %I64d\n",Case++,b-a);

    }
    return 0;
}

{% endhighlight %}

