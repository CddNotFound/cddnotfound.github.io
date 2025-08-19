---
title: 'Codeforces Round 905 (Div. 2 + Div. 3)'
description: ''
publishDate: 2023-10-26
tags:
- ACM
- CF
---

<!-- more -->

好水的div3早知道不看方舟直播回来写题了（

## A.Morning

把 $0$ 当成 $10$ ，然后加加加（

## B.Chemistry

出现奇数次的字母最多只能出现 $k+1$ 个

## C.Raspberries

$2,3,5$ 都是质数，$a_i$ 只要有一个能被 $k$ 整除即可

$4$ 的时候特判一下就好

## D.In Love

已有线段中最小的右端点小于最大的左端点即存在

### Code

```cpp
#include<bits/stdc++.h>
#define LL long long
using namespace std;
const int maxn = 1e5 + 5;
int T;
char ch[5];
struct ddl {
    int l,r;
};
struct ddr {
    int l,r;
};
struct cmpl {
    bool operator() (const ddl &a,const ddl &b) {
        return a.l>b.l;
    }
};
struct cmpr {
    bool operator() (const ddr &a,const ddr &b) {
        return a.r<b.r;
    }
};
multiset<ddl,cmpl> ll;
multiset<ddr,cmpr> rr;
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
        int l,r;
        scanf("%s",ch);
        l=read(); r=read();
        if (ch[0]=='+') {
            ll.insert((ddl){l,r});
            rr.insert((ddr){l,r});
        }
        if (ch[0]=='-') {
            ll.erase(ll.find((ddl){l,r}));
            rr.erase(rr.find((ddr){l,r}));
        }
        if ((*ll.begin()).l>(*rr.begin()).r) printf("YES\n");
            else printf("NO\n"); 
    }

    return 0;
}
```

## E.Look Back

模拟的话会直接爆掉

所以计算原数组中每一个数乘几次才能不比前一个小（可以为负数）

取前缀和即为该数的操作数（小于 $0$ 要重置为 $0$ ）

### Code

```cpp
#include<bits/stdc++.h>
#define LL long long
using namespace std;
const int maxn = 1e5 + 5;
LL T;
LL n;
LL a[maxn],b[maxn];
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
int main()
{
    T=read();
    while (T--) {
        n=read();
        for (int i=1;i<=n;i++) a[i]=read();
        for (int i=2;i<=n;i++)
            if (a[i]>=a[i-1]) {
                int s=0;
                while ((a[i]>>s)>=a[i-1]) s++;
                b[i]=1-s;
            }
            else {
                int s=0;
                while ((a[i]<<s)<a[i-1]) s++;
                b[i]=s;
            }
        ans=0;
        int ss=0;
        for (int i=2;i<=n;i++) {
            ss+=b[i];
            if (ss<0) ss=0;
            ans+=ss;
        }

        printf("%lld\n",ans);
    }

    return 0;
}
```

## F.You Are So Beautiful

### 题意

就是对于每个子序列 $(l,r)$ ，无法从 $a$ 中按另一种顺序挑出 $r−l+1$ 个数（可以不连续）构成这个子序列。

~~一开始以为必须连续样例4怎么算都是8~~

那么对于一段序列 $(l,r)$ 合法，必须满足 $l$ 左边没有 $a_l$ ， $r$ 右边没有 $a_r$

所以对于每个第一次出现的数字，看他右边有多少个合法的位置 $r$ ，就是右边不再有自己的数的个数或者说不同的数的个数，加起来就行

### Code

```cpp
#include<bits/stdc++.h>
#define LL long long
using namespace std;
const int maxn = 1e5 + 5;
int T;
int n;
struct sdd {
    int c,num;
}a[maxn];
int l[maxn],r[maxn];
int b[maxn],v[maxn];
LL ans,s;
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
bool cmp(sdd x,sdd y) {
    return x.c<y.c;
}
void ini() {
    ans=s=0;
    for (int i=1;i<=n;i++) r[i]=l[i]=v[i]=0;
}
int main()
{
    T=read();
    while (T--) {
        ini();
        n=read();
        for (int i=1;i<=n;i++) a[i]=(sdd){read(),i};
        sort(a+1,a+1+n,cmp);
        int cnt=0;
        for (int i=1;i<=n;i++) {
            if (a[i].c!=a[i-1].c) cnt++;
            b[a[i].num]=cnt;
        }

        for (int i=1;i<=n;i++) {
            if (!l[b[i]]) l[b[i]]=i;
            r[b[i]]=i;
        }
        for (int i=1;i<=cnt;i++) v[r[i]]++;
        s=cnt;
        for (int i=1;i<=n;i++) {
            if (l[b[i]]==i) {
                ans+=s;
            }
            s-=v[i];
        }
        printf("%lld\n",ans);

        ini();
    }

    return 0;
}
```

## G.Dances

1跟2没啥区别写一块算了

先将 $a$ 跟 $b$ 排序，不难发现每次删除一定是删一个最大的 $a_i$ 和最小的$b_i$ ，所以从 $a_1$ 开始，一个一个二分找到 $b$ 中最小的大于 $a_i$ 的数，因为 $a$ 是递增的，所以每次只需要在上一个配对的 $b_i$ 右边找。

算出最大配对数 $cnt$ 之后，无论 $c_1$ 为多少，每次至少要删除 $(n-1)-cnt$ 次
然后找出 $b$ 中最大的未使用的数 $R$ ，如果 $c_1 < R$ 可以直接配对，不然就得再删一次

最终 $ans = m * (n-1-cnt) + max(m-R+1,0)$ 

### Code

```cpp
#include<bits/stdc++.h>
#define LL long long
using namespace std;
const int maxn = 1e5 + 5;
int T;
int n,m;
int a[maxn],b[maxn];
int v[maxn],R;
LL ans;
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
void ini() {
    ans=0;
    for (int i=1;i<=n;i++) v[i]=0;
}
int main()
{
    T=read();
    while (T--) {
        n=read(); m=read();
        for (int i=1;i<n;i++) a[i]=read();
        for (int i=1;i<=n;i++) b[i]=read();
        sort(a+1,a+n);
        sort(b+1,b+1+n);

        int lst=0,cnt=0;
        for (int i=1;i<n;i++) {
            int l=lst+1,r=n,tag=0;
            while (l<=r) {
                int mid=(l+r)>>1;
                if (b[mid]>a[i]) r=mid-1,tag=mid;
                    else l=mid+1;
            }
            if (tag) cnt++,lst=tag,v[tag]=1;
                else break;
        }
        ans+=(LL)m*(LL)(n-cnt-1);
        for (int i=n;i;i--)
            if (!v[i]) {R=i; break;}

        ans+=max(m-b[R]+1,0);
        printf("%lld\n",ans);

        ini();
    }

    return 0;
}
```

------

## E.Time Travel

其实思路跟dij一模一样

就是转移的时候对于第 $i$ 条边，要找到当前时间节点后面第一个第 $i$ 条边对应的关键时刻，找不到话这条边就不能用。

那么按照关键时刻为第一关键字，时间节点为第二关键字，就能用二分找了。

### Code

```cpp
#include<bits/stdc++.h>
#define LL long long
using namespace std;
const int maxn = 2e5 + 5;
int n,t;
int m;
int head[maxn],to[maxn<<1],nxt[maxn<<1],tim[maxn<<1],cnt;
int d[maxn],v[maxn];
struct sdd {
    int c,num;
}a[maxn];
struct sddd {
    int num,dis;
    bool operator <(const sddd &x) const
    {
        return x.dis < dis;
    }
};
priority_queue<sddd> q;
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
void in(int x,int y,int z) {
    nxt[++cnt]=head[x],head[x]=cnt,to[cnt]=y,tim[cnt]=z;
}
bool cmp(sdd x,sdd y) {
    if (x.c==y.c) return x.num<y.num;
    return x.c<y.c;
}
int main()
{
    n=read(); t=read();
    for (int i=1;i<=t;i++) {
        m=read();
        for (int j=1;j<=m;j++) {
            int x,y;
            x=read(); y=read();
            in(x,y,i); in(y,x,i);
        }
    }
    m=read();
    for (int i=1;i<=m;i++) a[i]=(sdd){read(),i};
    sort(a+1,a+1+m,cmp);
    d[1]=0;
    for (int i=2;i<=n;i++) d[i]=1e9;
    q.push((sddd){1,0});
    while (!q.empty()) {
        int cur=(q.top()).num,dis=(q.top()).dis;
        q.pop();
        if (v[cur]) continue;
        v[cur]=1;
        for (int j=head[cur];j;j=nxt[j]) {
            int l=1,r=m,tag=0;
            while (l<=r) {
                int mid=(l+r)>>1;
                if (a[mid].c<tim[j]) l=mid+1;
                if (a[mid].c>tim[j]) r=mid-1;
                if (a[mid].c==tim[j]) {
                    if (a[mid].num>dis) tag=a[mid].num,r=mid-1;
                        else l=mid+1;
                }
            }
            if (tag) if (tag<d[to[j]]) {
                d[to[j]]=tag;
                if (!v[to[j]]) q.push((sddd){to[j],d[to[j]]});
            }
        }
    }
    if (d[n]==1e9) printf("-1\n");
        else printf("%d\n",d[n]);

    return 0;
}
```

## F.Minimum Array

涉及到区间修改的话可以考虑用差分来做。

那么对于一串操作，如果差分数组的第一个非 $0$ 位为负，那么经过这一串操作后字典序肯定是变小的

这样大致思路就出来了

假设我们有两个空集合 $S1$ 和 $S2$ ，分别表示我们已经选择的操作和待定的操作，当待定集合 $S2$ 为空时，如果该操作是减法，那么一定会使字典序变小，将它加入 $S1$ 。如果遇到加法了，就将它加入 $S2$ 。此时 $S2$ 不为空，后续的每一次操作都加进去，直到 $S2$ 构成的差分数组第一个非 $0$ 位为负，将 $S2$ 全部加进 $S1$ 然后清空。

实际上 $S1$ 只要用一个变量来更新就行了。

那么怎么判断第一位的正负呢，用一个小根堆维护差分数组中不为 $0$ 的位置，每次修改差分时，如果该位置是 $0$ ，就把它push进去，就只用判断 $v[q.top()]$ 的正负，清空 $S2$ 也就是将堆清空，同时把对应位置置为 $0$ 。

对于每次操作的两个点最多被push一次，pop一次，所以时间复杂度是 $O(qlogn)$ （应该是的不太会算；；

一开始置 $0$ 用的时间戳，结果情况考虑少写挂了，调了好久才发现还是要判断 $v_i$ 是不是 $0$（

### Code

```cpp
#include<bits/stdc++.h>
#define LL long long
using namespace std;
const int maxn = 5e5 + 5;
LL T;
LL n,m;
LL a[maxn];
LL l[maxn],r[maxn],c[maxn];
LL v[maxn];
LL ans;
priority_queue<LL ,vector<LL> ,greater<LL> > q;
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
void ini() {
    ans=0;
    for (int i=1;i<=n+1;i++) v[i]=0;
    while (!q.empty()) q.pop();
}
int main()
{
    T=read();
    while (T--) {
        n=read();
        for (int i=1;i<=n;i++) a[i]=read();
        m=read();
        for (int i=1;i<=m;i++) l[i]=read(),r[i]=read(),c[i]=read();
        for (int i=1;i<=m;i++) {
            if (c[i]<0) {
                if (q.empty()) ans=i;
                    else {
                        if (!v[l[i]]) q.push(l[i]);
                        if (!v[r[i]+1]) q.push(r[i]+1);
                        v[l[i]]+=c[i],v[r[i]+1]-=c[i];
                        while (!q.empty()&&!v[q.top()]) q.pop();
                        if (v[q.top()]<0) {
                            ans=i;
                            while (!q.empty()) v[q.top()]=0,q.pop();
                        }
                    }
            }
            if (c[i]>0) {
                if (!v[l[i]]) q.push(l[i]);
                if (!v[r[i]+1]) q.push(r[i]+1);
                v[l[i]]+=c[i],v[r[i]+1]-=c[i];
            }
        }
        for (int i=1;i<=n;i++) v[i]=0;
        for (int i=1;i<=ans;i++) v[l[i]]+=c[i],v[r[i]+1]-=c[i];
        LL s=0;
        for (int i=1;i<=n;i++) {
            s+=v[i];
            printf("%lld ",a[i]+s);
        }
        putchar('\n');
        ini();
    }

    return 0;
}
```