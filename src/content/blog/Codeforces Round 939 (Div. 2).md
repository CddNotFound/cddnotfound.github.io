---
title: 'Codeforces Round 939 (Div. 2) A - F'
description: ''
publishDate: 2024-04-15
tags:
  - ACM
  - CF
---

<!-- more -->

A - C 全是简单结论题，题又长读半天，感觉挺没意思的（

## [A.Nene's Game](https://codeforces.com/contest/1956/problem/A)

只要人数大于 $a_1$ 就会一直进行，所以答案是 $min(a_1 - 1, n_i)$

当然直接模拟也不是不可以（
 
### Code

```cpp
int main()
{
    T = read();
    while (T--) {
        k = read(); q = read();
        for (int i = 1; i <= k; i++) a[i] = read();
        int ff = 0;
        while (q--) {
            n = read();
            printf("%d ", min(a[1] - 1, n));
        }
        putchar('\n');
    }

    return 0;
}
```

## [B. Nene and the Card Game](https://codeforces.com/contest/1956/problem/B)

注意到两人手中凑不成对牌的数量相等，所以我们每打出一张这种牌，`nene` 就打同样的牌，这部分牌的分会全被 `nene` 拿走

剩下的相等的牌的分必定能拿到，答案就是这部分得分

### Code

```cpp
int main()
{
    T = read();
    while (T--) {
        n = read();
        for (int i = 1; i <= n; i++) a[i] = read();
        sort(a + 1, a + 1 + n);
        ans = 0;
        for (int i = 2; i <= n; i++)
            if (a[i] == a[i - 1]) ans++;
        printf("%d\n", ans);
    }

    return 0;
}
```

## [C. Nene's Magical Matrix](https://codeforces.com/contest/1956/problem/C)

手搓几个小数据就能发现最优解是 $\sum_{i = 1} ^ {n} i \times (2 \times i - 1)$    

构造方法是第一行横着或竖着操作一次，从第二行开始横着一次再竖着一次

每次的序列都是 $n, n - 1, n - 2, ... , 3, 2, 1$

一共操作 $2n - 1$ 次

### Code

```cpp
int main()
{
    T = read();
    while (T--) {
        n = read();
        for (int i = 1; i <= n; i++) p[i] = n - i + 1;
        ans1 = ans2 = 0;
        for (int i = 1; i <= n; i++) {
            ans1 += p[n - i + 1] * (2 * i - 1);
        }
        ans2 = 2 * n - 1;
        printf("%lld %lld\n", ans1, ans2);

        printf("1 1 ");
        for (int i = 1; i <= n; i++) printf("%lld ", p[i]);
        putchar('\n');
        for (int j = 2; j <= n; j++) {
            printf("1 %d ", j);
            for (int i = 1; i <= n; i++) printf("%lld ", p[i]);
            putchar('\n');
            printf("2 %d ", j);
            for (int i = 1; i <= n; i++) printf("%lld ", p[i]);
            putchar('\n');
        }
    }

    return 0;
}
```

## [D. Nene and the Mex Operator](https://codeforces.com/contest/1956/problem/D)

一开始 $ans$ 数组只开到 $25$ 痛吃三次罚时， 不然该直接上紫的（

---

首先我们要对一段区间 $[l, r]$ 进行操作， 那么我们最多能把这个区间全部变为 $r - l + 1$

注意到 $n \leq 18$， 可以爆搜划分区间，然后对每个区间判断要不要进行操作，更新最大值以及划分方案以及要对哪几个区间进行操作

剩下的问题就是如何进行构造

首先为了便于操作， 用最多两次操作把一段区间全变成 $0$， 

对于一段 $1, 2, 3, ..., r - 2, r - 1 ,0$ 的区间，我们可以一次操作把他们全部变成 $r$ ，再对[1, r - 1] 进行一次操作就可以只把第 $r$ 位转换成 $r$ ，那么问题就转换为如何把区间 $[1, r - 1]$ 构造成 $1, 2, 3, ... , r - 2, 0$ ，然后递归构造即可

若对整个区间 $[1, 18]$ 进行操作，次数最多 $196609 < 2e5$ ，满足要求。

实际上翻了下数据，稍微大点的数据这个操作数都比 $std$ 少（

### Code

```cpp
#include<bits/stdc++.h>
#define LL long long
using namespace std;
const int maxn = 5e5 + 5;
int n;
int a[maxn], v[maxn], cnt, optcnt;
struct cdd {
    int l, r, w;
}b[maxn], ans[maxn], opt[maxn];
int ans1, anscnt;
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
void gets() {
    v[n] = 1;
    cnt = 0;
    int l = 1, r = 1;
    for (int i = 1; i <= n; i++)
        if (v[i]) r = i, b[++cnt] = (cdd){l, r, 0}, l = r + 1;
}
void dfs(int t) {
    if (t >= n) {
        gets();
        int sum = 0;
        for (int i = 1; i <= cnt; i++) {
            auto [l, r, w] = b[i];
            int len = r - l + 1;
            if (len * len > a[r] - a[l - 1]) b[i].w = 1;
            sum += max(len * len, a[r] - a[l - 1]);
        }
        if (sum > ans1) {
            ans1 = sum;
            anscnt = cnt;
            for (int i = 1; i <= cnt; i++) ans[i] = b[i];
        }
        return ;
    }
    v[t] = 1;
    dfs(t + 1);
    v[t] = 0;
    dfs(t + 1);
}
void make(int l, int r, int aim, int fir) {
    if (l == r) return (void)(opt[++optcnt] = (cdd){l, r, 0});//边界

    for (int i = r - 1; i >= l; i--) make(l, i, aim - 1, 0);//递归构造

    opt[++optcnt] = (cdd){l, r, 0};
    if (r > l && !fir) opt[++optcnt] = (cdd){l, r - 1, 0};//不是整个区间就把前面位置都弄成0
}
int main()
{
    ans1 = 0;
    n = read();
    a[0] = 0;
    for (int i = 1; i <= n; i++) a[i] = read(), a[i] += a[i - 1];
    dfs(1);

    printf("%d ", ans1);
    for (int i = 1; i <= anscnt; i++) {
        auto [l, r, w] = ans[i];
        if (!w) continue;
        int aim = r - l + 1;
        int ff = 0;
        for (int i = l; i <= r; i++) if (a[i] - a[i - 1] == 0) ff = 1;
        opt[++optcnt] = (cdd){l, r, 0};
        if (ff) opt[++optcnt] = (cdd){l, r, 0};

        make(l, r, aim, 1);
    }
    printf("%d\n", optcnt);

    for (int i = 1; i <= optcnt; i++)
        printf("%d %d\n", opt[i].l, opt[i].r);
    return 0;
}
```

---

## [E1. Nene vs. Monsters (Easy Version)](https://codeforces.com/contest/1956/problem/E1)

`E1 E2` 参考的 Aicu 的思路

话说这两题思维还挺妙的，但是属于那种想不出第一题就肯定想不出第二题，想出第一题可能顺带就能秒二的

---

暴力跑 $1000$ 次后，剩下的段长最长为 $2$

证明：

假设有段长为 $3$ 的为 $x, y, z$

那么对 $z$ 需求最小的是 $1, 1001, z$

$1000$ 次循环后 $z$ 减少 $\sum_{i = 1} ^ {1000} i = \frac {1000 ^ 2 + 1000} {2} > 2e5$

那么对于段长为 $2$ 的， 把后者血量清零即可

### Code

```cpp
#include<bits/stdc++.h>
#define LL long long
using namespace std;
const int maxn = 2e5 + 5;
int T;
int n;
int a[maxn];
int lis[maxn], top;
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
int main()
{
    T = read();
    while (T--) {
        n = read();
        for (int i = 1; i <= n; i++) a[i] = read();

        for (int t = 1; t <= 1000; t++) {
            for (int i = 1; i <= n; i++) {
                int nxt = i == n ? 1 : i + 1;
                a[nxt] = max(0, a[nxt] - a[i]);
            }
        }
        a[0] = a[n];
        for (int i = 0; i < n; i++)
            if (a[i] && a[i + 1]) a[i + 1] = 0;
        top = 0;
        int ans = 0;
        for (int i = 1; i <= n; i++)
            if (a[i]) ans++, lis[++top] = i;
        printf("%d\n", ans);
        for (int i = 1; i <= top; i++)
            printf("%d ", lis[i]);
        putchar('\n');
    }

    return 0;
}
```

## [E2. Nene vs. Monsters (Hard Version)](https://codeforces.com/contest/1956/problem/E2)

暴力跑 $2000$ 次后， 剩下的段长最长为 $3$

证明：

假设有短长为 $4$ 的为 $w, x, y, z$

那么对 $z$ 需求最小的是 $1, 2001, \sum_{i = 1} ^ {2000} i, z$

$2000$ 次循环后 $z$ 减少 $t \times (\sum_{i = 1} ^ {t} i) - \sum_{i = 1} ^ {t} i^2 = t \times \frac{t(t + 1)} {2} - \frac {t(t + 1)(2t + 1)} {6} = \frac {t(t + 1)(t - 1)} {6}> 1e9$， 其中 $t = 2000$

那么对于段长为 $3$ 的，对第三个数 $y$ 能造成的伤害就是 $x - w$ 到 $x$ $\%$ $w$构成的等差数列，能把第三个数杀掉就把后两个血量清零，不然就只清第二个

特殊的，对于序号 $n, 1, 2$ 这一段，攻击顺序是 $1, 2, n$ 所以伤害是 $x$ 到 $x$ $\%$ $w$ 的等差数列，即多造成 $x$ 的伤害

段长为 $2$ 的依旧是将后者清零

### Code

```cpp
#include<bits/stdc++.h>
#define LL long long
using namespace std;
const int maxn = 2e5 + 5;
int T;
int n;
int a[maxn];
int lis[maxn], top;
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
int main()
{
    T = read();
    while (T--) {
        n = read();
        for (int i = 1; i <= n; i++) a[i] = read();

        for (int t = 1; t <= 2000; t++) {
            for (int i = 1; i <= n; i++) {
                int nxt = i == n ? 1 : i + 1;
                a[nxt] = max(0, a[nxt] - a[i]);
            }
        }

        a[n + 1] = a[1], a[n + 2] = a[2];
        for (int i = 1; i <= n; i++)
            if (a[i] && a[i + 1] && a[i + 2]) {
                LL x = a[i], y = a[i + 1], z = a[i + 2];
                LL t = (y + x - 1) / x - 1;
                LL y2 = y - x * t;
                LL s = (y - x + y2) * t / 2;
                if (i == n) s += y;
                if (s >= z) a[i + 1] = a[i + 2] = 0;
                    else a[i + 1] = 0;
            }

        for (int i = 2; i <= n + 1; i++)
            if (a[i] && a[i + 1]) a[i + 1] = 0;
        top = 0;
        int ans = 0;
        for (int i = 3; i <= n + 2; i++)
            if (a[i]) ans++, lis[++top] = i > n ? i - n : i;
        sort(lis + 1, lis + 1 + top);
        printf("%d\n", ans);
        for (int i = 1; i <= top; i++)
            printf("%d ", lis[i]);
        putchar('\n');
    }

    return 0;
}
```

## [F. Nene and the Passing Game](https://codeforces.com/contest/1956/problem/F)

官方题解 $O(n)$ 的合并方法是真巧妙，建议去看看

---

### 这是我的错解

题目并没有对 $m$ 的大小进行限制，所以球可以多次经过同一个人，也就是说一次可以评估整个联通块内的人

这个时候我们很自然就能想到线段树建图，用节点 $1 \sim n$ 表示人，$n + 1 \sim 2n$ 和 $2n + 1 \sim 3$ 表示位置，然后每 $4n$ 共建四棵线段树，分别表示左手传出和右手传入；右手传出和左手传入。然后看要遍历多少次就可以了。

但是很不幸的是空间和时间都会爆（虽然期望时间复杂度是 $O(nlogn)$

### 能过的解

依旧是人对地块连边的思想，当一个位置同时存在左手区间和右手区间时，这些人可以并入同一个联通块

所以只要维护在某个位置左手区间和右手区间的数量和对应序号即可

考虑用两个 `set` 分别维护当前位置左手区间和右手区间的编号和右边界，当左右手均不为空时，就将这些人全部合并。为了防止重复合并，合并后只保留右边界最右的（也就是出集合最晚的）的区间，其他的可以直接删掉

时间复杂度 $O(nlogn)$ ，跑得最慢的数据 2.4s

感觉瓶颈在排序（

### Code

```cpp
#include<bits/stdc++.h>
#define LL long long
using namespace std;
const int maxn = 2e6 + 5;
const int maxm = 2e6 + 5;
int T;
int n;
int ans;
int a[maxn], b[maxn], cntl, cntr;
int f[maxn];
struct cdd {
    int l, r, id;
}Lin[maxn], Rin[maxn], Lout[maxn], Rout[maxn];
int stl, str, edl, edr;

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
bool cmp(cdd x, cdd y) {
    return x.l < y.l;
}
bool cmp2(cdd x, cdd y) {
    return x.r < y.r;
}
int find(int x) {
    return f[x] == x ? x : f[x] = find(f[x]);
}
int main()
{
    T = read();
    while (T--) {
        cntl = cntr = 0;
        n = read();

        for (int i = 1; i <= n; i++) {
            a[i] = read(), b[i] = read(), f[i] = i;
            int l = max(1, i - b[i]), r = i - a[i];
            if (r >= 1) cntl++, Lin[cntl] = Lout[cntl] = (cdd){l, r, i};
            l = i + a[i], r = min(n, i + b[i]);
            if (l <= n) cntr++, Rin[cntr] = Rout[cntr] = (cdd){l, r, i};
        }
        sort(Lin + 1, Lin + cntl + 1, cmp);
        sort(Rin + 1, Rin + cntr + 1, cmp);
        sort(Lout + 1, Lout + cntl + 1, cmp2);
        sort(Rout + 1, Rout + cntr + 1, cmp2);

        set<pair<int, int> > L, R;

        stl = str = edl = edr = 1;
        for (int pos = 1; pos <= n; pos++) {
            while (Lin[edl].l == pos && edl <= cntl) L.insert(make_pair(Lin[edl].id, Lin[edl].r)), edl++;
            while (Rin[edr].l == pos && edr <= cntr) R.insert(make_pair(Rin[edr].id, Rin[edr].r)), edr++;

            if (L.size() && R.size()) {
                pair<int, int> mxL = make_pair(0, 0), mxR = make_pair(0, 0);
                int v = L.begin() -> first;
                while (L.size()) {
                    auto [id, r] = *L.begin();
                    if (r > mxL.second) mxL = make_pair(id, r);
                    f[find(id)] = find(v);
                    L.erase(L.begin());
                }
                while (R.size()) {
                    auto [id, r] = *R.begin();
                    if (r > mxR.second) mxR = make_pair(id, r);
                    f[find(id)] = find(v);
                    R.erase(R.begin());
                }
                L.insert(mxL), R.insert(mxR);
            }

            while (stl <= edl && Lout[stl].r == pos) L.erase(make_pair(Lout[stl].id, Lout[stl].r)), stl++;
            while (str <= edr && Rout[str].r == pos) R.erase(make_pair(Rout[str].id, Rout[str].r)), str++;
        }

        ans = 0;
        for (int i = 1; i <= n; i++)
            if (find(i) == i) ans++;
        printf("%d\n", ans);
    }

    return 0;
}
```
