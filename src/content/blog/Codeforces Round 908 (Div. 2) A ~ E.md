---
title: 'Codeforces Round 908 (Div. 2) A - E'
description: ''
publishDate: 2023-11-08
tags:
  - ACM
  - CF
---
<!-- more -->
## A.Secret Sport
输出最后一位

## B.Two Out of Three
重复的数字至少有两个

一组输出为若干个 $2$ 和一个 $3$ ，其他输出若干个 $1$ 和一个 $2$ ，就能恰好满足两个条件

落单的 $123$ 随便输出就行
### Code
```cpp
#include<bits/stdc++.h>
#define LL long long
using namespace std;
const int maxn = 1e2 + 5;
int T;
int n;
int a[maxn];
int cnt[maxn],tag,tot;
int read() {
    int x=0,w=1;
    char ch=0;
    while (ch<'0'||ch>'9') {
        if (ch=='-') w=-1;
        ch=getchar();
    }
    while (ch>='0'&&ch<='9') {
        x=x*10+ch-48;
       ch=getchar();
    }
    return x*w;
}
int main()
{
    T=read();
    while (T--) {
        memset(cnt,0,sizeof(cnt));
        tag=tot=0;

        n=read();
        for (int i=1;i<=n;i++) a[i]=read();
        for (int i=1;i<=n;i++) {
            cnt[a[i]]++;
            if (cnt[a[i]]==2) tot++;
        }
        if (tot<2) {
            printf("-1\n");
            continue;
        }
        for (int i=1;i<=100;i++)
            if (cnt[i]>=2) {tag=i; break;}
        
        for (int i=1;i<=n;i++) {
            if (tag==a[i]) {
                if (cnt[a[i]]==1) printf("3 ");
                    else printf("2 ");
            }
            else {
                if (cnt[a[i]]==1) printf("2 ");
                    else printf("1 ");
            }
            cnt[a[i]]--;
        }
        putchar('\n');
    }

    return 0;
}
```
## C.Anonymous Informant
按照题意手动推几次就可以看出来每次逆操作就是将尾部的 $b_n$ 个数移到前面，$b_n$ 就变成了$b_{n-b_n}$ 

这样移 $k$ 次就行了，遇到大于 $n$ 的数是No不然Yes

用到用过的数就进循环了，break输出Yes就行

```cpp
#include<bits/stdc++.h>
#define LL long long
using namespace std;
const int maxn = 2e5 + 5;
int T;
int n,k;
int a[maxn];
int v[maxn];
int read() {
    int x=0,w=1;
    char ch=0;
    while (ch<'0'||ch>'9') {
        if (ch=='-') w=-1;
        ch=getchar();
    }
    while (ch>='0'&&ch<='9') {
        x=x*10+ch-48;
       ch=getchar();
    }
    return x*w;
}
int main()
{
    T=read();
    while (T--) {
        n=read(); k=read();
        for (int i=1;i<=n;i++) a[i]=read();

        int now=n,tag=1;
        while (k) {
            if (v[now]) break;
            v[now]=1;
            if (a[now]>n) {tag=0; break;}
            now-=a[now];
            if (now<=0) now+=n;
            k--;
        }
        if (tag) printf("Yes\n");
            else printf("No\n");
        for (int i=1;i<=n;i++) v[i]=0;
    }

    return 0;
}
```
## D.Neutral Tonality
先考虑简单的情况

如果 $a_i$ 是递增的，那么我们在 $a_n$ 左边降序插入任何比 $a_n$ 大的数都不会影响LIS，在右边降序插入比它小的也一样。

那么当 $a$ 无序时，我们可能会得到多个最长上升子序列，而这若干个序列的结尾一定是递减的（如果后面有比它大的数拿它就不是结尾

所以将 $b$ 排序后按照这些结尾分成若干段插进去，并不会使LIS增加，又因为我们是降序插入的，对于其他序列的上升长度最多加一，也不会超过当前LIS
- - -
用 $d_i$ 表示长度为 $i$ 的最小的结尾大小，对于每个 $a_i$ 二分找到它能接上的最长序列，然后更新 $d$ ，就能在 $O(nlogn)$ 内求出LIS
### Code
```cpp
#include<bits/stdc++.h>
#define LL long long
using namespace std;
const int maxn = 2e5 + 5;
int T;
int n,m;
int a[maxn],b[maxn];
int f[maxn];
int d[maxn];
int c[maxn],cnt;
int read() {
    int x=0,w=1;
    char ch=0;
    while (ch<'0'||ch>'9') {
        if (ch=='-') w=-1;
        ch=getchar();
    }
    while (ch>='0'&&ch<='9') {
        x=x*10+ch-48;
       ch=getchar();
    }
    return x*w;
}
bool cmp(int x,int y) {
    return x>y;
}
int main()
{
    T=read();
    while (T--) {
        n=read(); m=read();
        for (int i=1;i<=n;i++) d[i]=INT_MAX,f[i]=0;
        cnt=0;
        for (int i=1;i<=n;i++) a[i]=read();
        for (int i=1;i<=m;i++) b[i]=read();
        sort(b+1,b+1+m,cmp);

        d[1]=a[1]; f[1]=1;
        int len=1;
        for (int i=2;i<=n;i++) {
            int l=1,r=len,tag=0;
            while (l<=r) {
                int mid=(l+r)>>1;
                if (d[mid]<a[i]) l=mid+1,tag=mid;
                    else r=mid-1;
            }
            d[tag+1]=min(d[tag+1],a[i]);
            len=max(len,tag+1);
            f[i]=tag+1;
        }
        int pos=0;
        for (int i=n;i;i--) if (f[i]>f[pos]) pos=i;
        for (int i=1;i<=n;i++)
            if (f[i]==f[pos]) c[++cnt]=a[i];
        c[++cnt]=0;
        
        int pp=1,now=1;
        while (b[pp]>=c[now]&&pp<=m) printf("%d ",b[pp++]);
        for (int i=1;i<=n;i++) {
            printf("%d ",a[i]);
            if (f[i]==f[pos]) now++;
            while (b[pp]>=c[now]&&pp<=m) printf("%d ",b[pp++]);
        }
        putchar('\n');
    }

    return 0;
}
```

## E.Freedom of Choice
### 题意

给 $m$ 个集合，第 $i$ 个集合里有 $c_{i,1}$ 个 $a_{i,1}$ , $c_{i,2}$ 个 $a_{i,2}$ , ... , $c_{i,n}$ 个 $a_{i,n}$

要从每个集合里选至少 $l_i$ 至多 $r_i$ 个数组成集合 $X$

使 $X$ 中等于它长度的元素数量最少

### 思路

又是一道删数题（）

将 $l_i$ 和 $r_i$ 累加，就是 $X$ 长度的范围，如果这个范围里有数（比如$y$）不存在于任何一个 $a_i$ 中， 就能选出来一个长度为 $y$ 的集合，答案就是0。

如果全出现的话，对于每个在长度范围内的 $a_{i,j}$ ，我们记下从这个集合里最少要选出多少个$a_{i,j}$ （即其他数总数达不到 $l_i$ 的时候）加进 $ex_{a_{i,j}-suml+1}$ ，以及选出的数量离 $r_i$ 差多少，加进 $rr_i$。

如果最终能选出的个数（即 $sum_i-rr_i$ ）大于等于这个数，那么该 $a_{i,j}$ 对答案的贡献就是 $ex_{a_{i,j}-suml+1}$，不然剩下的就要用 $a_{i,j}$ 补齐。

实际就是枚举 $X$ 的长度然后尽量不选跟长度一样的数，计算每种情况下的最优解。

全出现的情况下这个长度不会大于 $\Sigma n$ ，时间复杂度 $O(n)$  
### Code
```cpp
#include<bits/stdc++.h>
#define LL long long
using namespace std;
const int maxn = 1e5 + 5;
LL T;
LL m,n;
LL suml,sumr;
LL l[maxn],r[maxn],cnt[maxn];
LL rr[maxn],ex[maxn],sum[maxn];
int v[maxn];
LL cntc;
struct sdd {
    LL a,c;
}a[maxn];
LL ans;
LL read() {
    LL x=0,w=1;
    char ch=0;
    while (ch<'0'||ch>'9') {
        if (ch=='-') w=-1;
        ch=getchar();
    }
    while (ch>='0'&&ch<='9') {
        x=x*10+ch-48;
       ch=getchar();
    }
    return x*w;
}
bool cmp(sdd x,sdd y) {
    return x.a<y.a;
}
int main()
{
    T=read();
    while (T--) {
        m=read();
        cnt[0]=0;
        suml=sumr=0;
        for (int i=1;i<=m;i++) {
            sum[i]=0;
            n=read(); l[i]=read(); r[i]=read();
            suml+=l[i],sumr+=r[i];
            for (int j=1;j<=n;j++) a[j+cnt[i-1]].a=read();
            for (int j=1;j<=n;j++) a[j+cnt[i-1]].c=read(),sum[i]+=a[j+cnt[i-1]].c;
            cnt[i]=cnt[i-1]+n;
        }

        if (sumr-suml+1>cnt[m]) {printf("0\n"); continue;}
        for (int i=1;i<=sumr-suml+1;i++) rr[i]=ex[i]=v[i]=0;
        int tcnt=0;
        for (int i=1;i<=cnt[m];i++)
            if (a[i].a>=suml&&a[i].a<=sumr&&!v[a[i].a-suml+1]) tcnt++,v[a[i].a-suml+1]=1;
        if (tcnt<sumr-suml+1) {printf("0\n"); continue;}

        for (int i=1;i<=m;i++) {
            for (int j=cnt[i-1]+1;j<=cnt[i];j++)
                if (a[j].a>=suml&&a[j].a<=sumr) {
                    LL ss=sum[i]-a[j].c,cur=a[j].a-suml+1;
                    rr[cur]+=r[i]-min(r[i],max(l[i],ss));
                    ex[cur]+=max(1ll*0,l[i]-ss);
                }
        }
        ans=1e18;
        for (int i=1;i<=sumr-suml+1;i++)
            ans=min(ans,ex[i]+max(1ll*0,(i+suml-1)-(sumr-rr[i])));
        printf("%lld\n",ans);
    }

    return 0;
}
```