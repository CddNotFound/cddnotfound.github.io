---
title: 'Data Structure'
description: '上帝说要有光，于是，喷气毛毛虫诞生了'
order: 1
---

### 树状数组

```cpp
struct Fenwick {
    int n;
    vector<LL> t;
    Fenwick(int n) : n(n), t(n + 5, 0) {}
    int lowbit(int x) {return x & -x;}
    void add(int pos, int y) {
        for (int i = pos; i <= n; i += lowbit(i)) t[i] += y;
    }
    void add(int l, int r, int y) {
        add(l, y);
        if (r + 1 <= n) add(r + 1, -y);
    }
    LL query(int pos) {
        LL sum = 0;
        for (int i = pos; i; i -= lowbit(i)) sum += t[i];
        return sum;
    }
    LL query(int l, int r) {
        return query(r) - query(l - 1);
    }
};
```

### ST表

```cpp
const int maxn = 1e5 + 5;

array<int, maxn> lg;

void init(int n) {
    lg[1] = 0;
    for (int i = 2; i <= n; i++)
        lg[i] = lg[i >> 1] + 1;
}

int gcd(int a, int b) {
    return !b ? a : gcd(b, a % b);
}

struct Info {
    int mx, mn, g;

    Info(int x = 0) : mx(x), mn(x), g(x) {}
};
Info operator | (Info a, Info b) {
    Info c;
    c.mx = max(a.mx, b.mx);
    c.mn = min(a.mn, b.mn);
    c.g = gcd(a.g, b.g);
    return c;
}
struct ST {
    int n;
    vector<array<Info, 30>> f;

    ST() {};
    ST(int x) {
        init(x);
    }
    void init(int x) {
        n = x;
        f.assign(n + 5, {});
    }

    void build(vector<int> a) {
        for (int i = 1; i <= n; i++) f[i][0] = Info(a[i]);
        for (int j = 1; j <= lg[n]; j++)
            for (int i = 1; i <= n - (1 << j) + 1; i++)
                f[i][j] = f[i][j - 1] | f[i + (1 << (j - 1))][j - 1];
    }
    Info query(int l, int r) {
        int x = lg[r - l + 1];
        return f[l][x] | f[r - (1 << x) + 1][x];
    }
};
```

### 平衡树

#### Splay 平衡树

```cpp
struct Splay {
    const int inf = 2e9;
    int n;
    int rt, tot;
    vector<int> val, cnt, fa, siz;
    vector<array<int, 2>> ch;

    Splay() {}
    Splay(int x) {
        init(x);
    }
    void init(int x) {
        n = x;
        val.assign(n + 5, 0);
        cnt.assign(n + 5, 0);
        fa.assign(n + 5, 0);
        siz.assign(n + 5, 0);
        ch.assign(n + 5, {0, 0});
        rt = tot = 0;
        ins(inf), ins(-inf);
    }

    void pushup(int cur) {return siz[cur] = siz[ch[cur][1]] + siz[ch[cur][0]] + cnt[cur], void();}
    int chk(int cur) {return cur == ch[fa[cur]][1];}
    void rotate(int x) {
        int y = fa[x], z = fa[y], k = chk(x), w = ch[x][k ^ 1];
        ch[y][k] = w, fa[w] = y;
        ch[z][chk(y)] = x, fa[x] = z;
        ch[x][k ^ 1] = y, fa[y] = x;
        pushup(y); pushup(x);
    }
    void splay(int cur, int g = 0) {
        while (fa[cur] != g) {
            int y = fa[cur], z = fa[y];
            if (z != g)
                if (chk(y) == chk(z)) rotate(y);
                    else rotate(cur);
            rotate(cur); 
        }
        if (!g) rt = cur;
    }
    void ins(int x) {
        int cur = rt, ftr = 0;
        while (cur && val[cur] != x) ftr = cur, cur = ch[cur][x > val[cur]]; 
        if (cur) return (void)(++cnt[cur], splay(cur));
        cur = ++tot;
        val[cur] = x;
        cnt[cur] = siz[cur] = 1;
        fa[cur] = ftr;
        if (ftr) ch[ftr][x > val[ftr]] = cur;
        ch[cur][0] = ch[cur][1] = 0;
        splay(cur);
        return ;
    }
    void find(int x) {
        int cur = rt;
        while (ch[cur][x > val[cur]] && x != val[cur]) cur = ch[cur][x > val[cur]];
        splay(cur);
        rt = cur;
    }
    int pre(int x) {
        find(x);
        if (val[rt] < x) return rt;
        int cur = ch[rt][0];
        while (ch[cur][1]) cur = ch[cur][1];
        return splay(cur), cur;
    }
    int succ(int x) {
        find(x);
        if (val[rt] > x) return rt;
        int cur = ch[rt][1];
        while (ch[cur][0]) cur = ch[cur][0];
        return splay(cur), cur;
    }
    void del(int x) {
        int lst = pre(x), nxt = succ(x), cur = 0;
        splay(lst);
        splay(nxt, rt);
        cur = ch[nxt][0];
        if (cnt[cur] > 1) {
            --cnt[cur];
            splay(cur);
        } else ch[nxt][0] = 0, fa[cur] = 0;
        pushup(cur); pushup(nxt);
    }
    int kth(int k) {
        ++k;
        int cur = rt;
        while (1) {
            if (ch[cur][0] && k <= siz[ch[cur][0]]) cur = ch[cur][0];
                else if (k > cnt[cur] + siz[ch[cur][0]]) k -= cnt[cur] + siz[ch[cur][0]], cur = ch[cur][1];
                    else return splay(cur), cur;
        }
    }
    int rk(int x) {
        find(x);
        return val[rt] >= x ? siz[ch[rt][0]] : siz[ch[rt][0]] + cnt[rt];
    }
};
```
#### 文艺平衡树 todo

```cpp
#include<bits/stdc++.h>
#define LL long long
using namespace std;
const int maxn = 1e6+5;
int n,m;
int siz[maxn],cnt[maxn],val[maxn],fa[maxn],ch[maxn][2],rev[maxn],tot=0,rt=0;
void pushup(int x) {siz[x]=siz[ch[x][1]]+siz[ch[x][0]]+cnt[x];}
void pushdown(int x)
{
    if (rev[x]) {
        swap(ch[x][1],ch[x][0]);
        rev[ch[x][0]]^=1; rev[ch[x][1]]^=1;
        rev[x]=0;
    }
}
int chk(int x) {return ch[fa[x]][1]==x;}
void rotate(int x)
{
    int y=fa[x],z=fa[y],k=chk(x),w=ch[x][k^1];
    ch[y][k]=w,fa[w]=y;
    ch[z][chk(y)]=x,fa[x]=z;
    ch[x][k^1]=y,fa[y]=x;
    pushup(y); pushup(x);
}
void splay(int x,int g=0)
{
    while (fa[x]!=g) {
        int y=fa[x],z=fa[y];
        if (z!=g) if (chk(x)==chk(y)) rotate(y);
                    else rotate(x);
        rotate(x);
    }
    if (!g) rt=x;
}
int kth(int k)
{
    k++;
    int cur=rt;
    while (1) {
        pushdown(cur);
        if (ch[cur][0]&&k<=siz[ch[cur][0]]) cur=ch[cur][0];
            else if (k>siz[ch[cur][0]]+cnt[cur]) k-=siz[ch[cur][0]]+cnt[cur],cur=ch[cur][1];
                    else {splay(cur); return cur;}
    }
}
void find(int x)
{
    int cur=rt;
    while (ch[cur][x>val[cur]]&&val[cur]!=x) cur=ch[cur][x>val[cur]];
    splay(x);
}
void in(int x)
{
    int cur=rt,ftr=0;
    while (cur&&val[cur]!=x) ftr=cur,cur=ch[cur][x>val[cur]];
    val[++tot]=x;
    if (ftr) ch[ftr][x>val[ftr]]=tot;
    fa[tot]=ftr;
    ch[tot][0]=ch[tot][1]=0;
    siz[tot]=cnt[tot]=1;
    splay(tot);
}
void swa(int l,int r)
{
    int lst=kth(l-1),nxt=kth(r+1);
    splay(lst); splay(nxt,rt);
    rev[ch[nxt][0]]^=1;
}
void print(int x)
{
    pushdown(x);
    if (ch[x][0]) print(ch[x][0]);
    if (val[x]>=1&&val[x]<=n) printf("%d ",val[x]);
    if (ch[x][1]) print(ch[x][1]);
}
int main()
{
    scanf("%d%d",&n,&m);

    in(-INT_MAX);
    for (int i=1;i<=n;i++) in(i);
    in(INT_MAX);

    while (m--) {
        int x,y;
        scanf("%d%d",&x,&y);
        swa(x,y);
    }
    print(rt);
    
    return 0;
}
```

### 线段树

#### 普通线段树 todo

```cpp
#include<bits/stdc++.h>
#define LL long long
using namespace std;
const int maxn = 1e6 + 5;
int n,m,P;
int a[maxn];
LL t[maxn<<2],lzya[maxn<<2],lzyt[maxn<<2];
void build(int l,int r,int p) {
    lzyt[p]=1;
    if (l==r) return (void)(t[p]=a[l]%P);
    int mid=(l+r)>>1;
    build(l,mid,p<<1);
    build(mid+1,r,p<<1|1);
    t[p]=t[p<<1]+t[p<<1|1];
}
void ddown(int l,int r,LL k1,LL k2,int p) {
    lzya[p]=(lzya[p]*k2%P+k1)%P;
    lzyt[p]=lzyt[p]*k2%P;
    t[p]=(t[p]*k2%P+k1*(r-l+1))%P;
}
void down(int l,int r,int p) {
    int mid=(l+r)>>1;
    ddown(l,mid,lzya[p],lzyt[p],p<<1);
    ddown(mid+1,r,lzya[p],lzyt[p],p<<1|1);
    lzya[p]=0,lzyt[p]=1;
}
void add(int l,int r,int x,int y,int k,int p) {
    if (x<=l&&y>=r) {
        t[p]=(t[p]+k*(r-l+1))%P;
        lzya[p]=(lzya[p]+k)%P;
        return; 
    }
    down(l,r,p);
    int mid=(l+r)>>1;
    if (x<=mid) add(l,mid,x,y,k,p<<1);
    if (y>=mid+1) add(mid+1,r,x,y,k,p<<1|1);
    t[p]=(t[p<<1]+t[p<<1|1])%P;
}
void times(int l,int r,int x,int y,int k,int p) {
    if (x<=l&&y>=r) {
        t[p]=t[p]*k%P;
        lzyt[p]=lzyt[p]*k%P;
        lzya[p]=lzya[p]*k%P;
        return ;
    }
    down(l,r,p);
    int mid=(l+r)>>1;
    if (x<=mid) times(l,mid,x,y,k,p<<1);
    if (y>=mid+1) times(mid+1,r,x,y,k,p<<1|1);
    t[p]=(t[p<<1]+t[p<<1|1])%P;
}
LL query(int l,int r,int x,int y,int p) {
    down(l,r,p);
    if (x<=l&&y>=r) return t[p];
    int mid=(l+r)>>1;
    LL ans=0;
    if (x<=mid) ans=(ans+query(l,mid,x,y,p<<1))%P;
    if (y>=mid+1) ans=(ans+query(mid+1,r,x,y,p<<1|1))%P;
    return ans;
}
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
    n=read(); m=read(); P=read();
    for (int i=1;i<=n;i++) a[i]=read();
    build(1,n,1);
    while (m--) {
        int tag,x,y,z;
        tag=read(); x=read(); y=read();
        if (tag==1) {
            z=read();
            times(1,n,x,y,z,1);
        }
        if (tag==2) {
            z=read();
            add(1,n,x,y,z,1);
        }
        if (tag==3) printf("%lld\n",query(1,n,x,y,1));
    }
    return 0;
}
```

#### 区间最小值的权值和 todo

```cpp
struct node {
    int mn, s;
};
node tt[maxn << 2];
int lazy[maxn << 2];

void tpushup(int p) {
    int ls = p << 1, rs = p << 1 | 1;
    tt[p].mn = min(tt[ls].mn, tt[rs].mn);
    if (tt[ls].mn == tt[rs].mn) tt[p].s = tt[ls].s + tt[rs].s;
        else tt[p].s = tt[ls].mn < tt[rs].mn ? tt[ls].s : tt[rs].s;
}
void tpushdown(int l, int r, int p) {
    if (lazy[p]) {
        int ls = p << 1, rs = p << 1 | 1, k = lazy[p];
        lazy[ls] += k, lazy[rs] += k;
        tt[ls].mn += k, tt[rs].mn += k;
        lazy[p] = 0;
    }
}
void tbuild(int l, int r, int p) {
    lazy[p] = 0;
    if (l == r) return tt[p] = {0, 1}, void();
    int mid = (l + r) >> 1;
    tbuild(l, mid, p << 1);
    tbuild(mid + 1, r, p << 1 | 1);
    tpushup(p);
}
void tupdate(int l, int r, int x, int y, int k, int p) {
    if (x <= l && r <= y) {
        tt[p].mn += k;
        lazy[p] += k;
        return ;
    }
    tpushdown(l, r, p);
    int mid = (l + r) >> 1;
    if (x <= mid) tupdate(l, mid, x, y, k, p << 1);
    if (y > mid) tupdate(mid + 1, r, x, y, k, p << 1 | 1);
    tpushup(p);
}
node tquery(int l, int r, int x, int y, int p) {
    if (x <= l && r <= y) {
        return tt[p];
    }
    tpushdown(l, r, p);
    int mid = (l + r) >> 1;
    node sum = {inf, 0};
    if (x <= mid) {
        auto tmp = tquery(l, mid, x, y, p << 1);
        if (tmp.mn < sum.mn) sum = tmp;
            else if (tmp.mn == sum.mn) sum.s += tmp.s;
    }
    if (y > mid) {
        auto tmp = tquery(mid + 1, r, x, y, p << 1 | 1);
        if (tmp.mn < sum.mn) sum = tmp;
            else if (tmp.mn == sum.mn) sum.s += tmp.s;
    }
    return sum;
}
```

### 树哈希

#### 有根树哈希

```cpp
const int maxn = 3e5 + 5;

vector<int> G[maxn];
uLL Hash[maxn]; // 别用hash
uLL mask;

uLL shift(uLL x) {
    x ^= mask;
    x ^= x << 13;
    x ^= x >> 7;
    x ^= x << 17;
    x ^= mask;
    return x;
}

void getHash(int cur, int ftr) {
    Hash[cur] = 1;
    for (auto v : G[cur]) {
        if (v == ftr) continue;
        getHash(v, cur);
        Hash[cur] += shift(Hash[v]);
    }
}

int main()
{
    srand(time(NULL));
    mask = rand();

    return 0;
}
```

#### 无根树哈希tmp （ [P5043 -【模板】树同构](https://www.luogu.com.cn/problem/P5043)）

```cpp
#include<bits/stdc++.h>
#define LL long long
using namespace std;
const int maxn = 5e1 + 5;
int m;
int n;
int head[maxn], to[maxn], nxt[maxn], cnt;
unsigned LL rt[maxn], sub[maxn];
int f[maxn];
int read() {
    int x = 0, w = 1;
    char ch = 0;
    while (ch < '0' || ch > '9') {
        if (ch == '-') w = -1;
        ch = getchar();
    }
    while (ch >= '0' && ch <= '9') {
        x = x * 10 + (ch ^ 48);
       ch = getchar();
    }
    return x * w;
}
unsigned LL shift(unsigned x) {
    x ^= x << 13;
    x ^= x >> 7;
    x ^= x << 17;
    return x;
}
void in(int x, int y) {
    nxt[++cnt] = head[x], head[x] = cnt, to[cnt] = y;
}
void treehash(int cur) {
    sub[cur] = 1;
    for (int i = head[cur]; i; i = nxt[i]) {
        treehash(to[i]);
        sub[cur] += shift(sub[to[i]]);
    }
}
void dp(int cur) {
    for (int i = head[cur]; i; i = nxt[i]) {
        rt[to[i]] = sub[to[i]] + shift(rt[cur] - shift(sub[to[i]]));
        dp(to[i]);
    }
}
int main()
{
    set <pair<unsigned LL, int> > s;
    m = read();
    for (int k = 1; k <= m; k++) {        
        n = read();
        for (int i = 1; i <= n; i++) head[i] = 0;
        cnt = 0;

        int root;
        for (int i = 1; i <= n; i++) {
            int x;
            x = read();
            if (x) in(x, i);
                else root = i;
        }
        treehash(root);
        rt[root] = sub[root];
        dp(root);
        int sum = 0;
        for (int i = 1; i <= n; i++) sum += rt[i];
        s.insert(make_pair(sum, k));
        printf("%d\n", s.lower_bound(make_pair(sum, 0)) -> second);
    }
    return 0;
}
```

### 并查集

#### 路径压缩

```cpp
struct DSU {
    vector<int> f, siz;
    int n;

    DSU() {};
    DSU(int x) {
        init(x);
    }
    void init(int x) {
        n = x;
        f.assign(n + 5, 0);
        iota(f.begin() + 1, f.end(), 1);
        siz.assign(n + 5, 1);
    }
    int find(int x) {
        return f[x] == x ? x : f[x] = find(f[x]);
    }
    bool same(int x, int y) {
        return find(x) == find(y);
    }
    void merge(int x, int y) {
        x = find(x); y = find(y);
        if (x == y) return ;
        siz[x] += siz[y];
        f[y] = x;
    }
    int size(int x) {
        return siz[find(x)];
    }
    int count() {
        vector<int> v(n + 5, 0);
        int cnt = 0;
        for (int i = 1; i <= n; i++) {
            int x = find(i);
            if (v[x]) continue;
            v[x] = 1; ++cnt;
        }
        return cnt;
    }
};
```