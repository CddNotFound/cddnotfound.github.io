---
title: 'CodeTON Round 8 (Div. 1 + Div. 2, Rated, Prizes!) A - F'
description: ''
publishDate: 2024-04-08
tags:
  - ACM
  - CF
---

<!-- more -->

## A. Farmer John's Challenge

$n = k$ 时全部相等

$k \geq 2$ 不成立

$k = 1$ 按顺序输出就行

### Code

```cpp
int main()
{
    T = read();
    while (T--) {
        n = read(); k = read();
        if (n == k) {
            for (int i = 1; i <= n; i++) printf("1 ");
            putchar('\n');
            continue;
        }
        if (k >= 2) {
            printf("-1\n");
            continue;
        }
        for (int i = 1; i <= n; i++)
            printf("%d ", i);
        putchar('\n');
    }

    return 0;
}
```

## B. Bessie and MEX

每一位的解是唯一的

用set维护一下当前MEX

### Code

```cpp
int main()
{
    T = read();
    while (T--) {
        set <int> s;
        s.clear();
        n = read();
        for (int i = 1; i <= n; i++) a[i] = read(), s.insert(i);
        s.insert(0);
        for (int i = 1; i <= n; i++) {
            auto now = *s.lower_bound(0);
            auto next = *s.upper_bound(now);
            if (next - now == a[i]) ans[i] = now;
                else ans[i] = now - a[i];
            s.erase(ans[i]);
        }
        for (int i = 1; i <= n; i++)
            printf("%d ", ans[i]);
        putchar('\n');
    }

    return 0;
}
```

## C. Bessie's Birthday Cake

把已经选择的点看作关键点，如果一个点左右相邻的点都是关键点，那么它也是关键点

最后的答案就是关键点的个数减 $2$

所以每选择一个点，可以增加两个关键点

对于长度为 $4$ （中间有$3$ 个点）的间隔，一次选择可以增加三个关键点

然后我们按奇偶为第一关键字，长度为第二关键字

对 $m$ 个间隔排序后选点即可

### Code

```cpp
#include<bits/stdc++.h>
#define LL long long
using namespace std;
const int maxn = 2e5 + 5;
int T;
int n, m, k;
int a[maxn], v[maxn], d[2][maxn];
int ans;
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
        n = read(); m = read(); k = read();
        ans = d[0][0] = d[1][0] = 0;
        for (int i = 1; i <= m; i++) a[i] = read(), ans++;
        sort(a + 1, a + 1 + m);
        a[0] = a[m] - n;
        for (int i = 1; i <= m; i++) {
            int dis = a[i] - a[i - 1];
            d[dis & 1][++d[dis & 1][0]] = dis;
        }

        sort(d[0] + 1, d[0] + 1 + d[0][0]);
        sort(d[1] + 1, d[1] + 1 + d[1][0]);

        for (int i = 1; i <= d[0][0]; i++) {
            if (d[0][i] > 2 && k) {
                int s = min(k, (d[0][i] - 2) >> 1);
                d[0][i] -= 2 * s, ans += 2 * s;
                k -= s;
            }
            if (d[0][i] == 2) ans++;
        }
        for (int i = 1; i <= d[1][0]; i++) {
            if (d[1][i] > 2 && k) {
                int s = min(k, d[1][i] >> 1);
                d[1][i] -= 2 * s, ans += 2 * s;
                k -= s;
            }
            if (d[1][i] == 2) ans++;
        }

        printf("%d\n", ans - 2);
    }

    return 0;
}
```

## D. Learning to Paint

设 $f_{i, 0/1, j}$ 表示选到第 $i$ 个数，选或不选该数时第 $j$ 大的答案

$f_{i,0}$ 就是从 $f_{i - 1, 0/1}$ 中选取前 $k$ 大个答案。

$f_{i,1}$ 将 $f_{j, 0, 1} + a_{j + 1, i} （0 \leq j < i）$ 依次入堆，每次选取最大的，并将该位置的下一个数入堆

最后输出就是从$f_{n,0}$ 和 $f_{n,1}$ 中选出前 $k$ 大

### Code

```cpp
#include<bits/stdc++.h>
#define LL long long
using namespace std;
const int maxn = 1e3 + 5;
const int maxm = 5e3 + 5;
const int inf = 2e9;
int T;
int n, k;
int a[maxn][maxn];
struct cdd {
    int val, id, lst;
};
priority_queue<cdd> q;
int f[maxn][2][maxm];
int cnt[maxn][2];
int ans[maxm];
bool operator < (cdd x, cdd y) {
    return x.val < y.val;
}
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
        n = read(); k = read();
        for (int i = 1; i <= n; i++)
            for (int j = i; j <= n; j++)
                a[i][j] = read();
        for (int i = 0; i <= n; i++)
            for (int j = 1; j <= k; j++)
                f[i][0][j] = f[i][1][j] = -inf;
        f[0][0][1] = 0;
        for (int i = 1; i <= n; i++) {
            while (!q.empty()) q.pop();
            for (int j = 1; j <= i; j++)
                q.push((cdd){f[j - 1][0][1] + a[j][i], 1, j});
            for (int j = 1; j <= k; j++) {
                auto now = q.top();
                q.pop();
                f[i][1][j] = now.val;
                if (now.id < k) q.push((cdd){f[now.lst - 1][0][now.id + 1] + a[now.lst][i], now.id + 1, now.lst});
            }
            int s1 = 1, s2 = 1;
            for (int j = 1; j <= k; j++)
                f[i][0][j] = f[i - 1][0][s1] > f[i - 1][1][s2] ? f[i - 1][0][s1++] : f[i - 1][1][s2++];
        }
        int s1 = 1, s2 = 1;
        for (int i = 1; i <= k; i++)
            printf("%d ", f[n][0][s1] > f[n][1][s2] ? f[n][0][s1++] : f[n][1][s2++]);
        putchar('\n');
    }

    return 0;
}
```

## E. Farm Game

首先谁的牛在第一位对结果没有影响，只算一种情况然后结果乘 $2$ 即可

考虑 $a$ 在首位的情况

对于一次移动，$ab$ 间隔的空地数如果全为偶数则必败

如果存在奇数，FJ 可以通过一次转移让 FN 处于必败态

所以只要考虑 $l - 2 \times n$ 块空地如何插入牛之间：

必败态 $ab$ 间隔只能是偶数，所以枚举总共插入的空地数 $i$

剩下的空地则不分奇偶插入开头、结尾和 $ba$ 间隔

就转化为 $i/2$ 个小球放入 $n$ 个盒子的情况数量乘上 $l - 2 \times n - i$ 个小球放入 $n + 1$ 个盒子中的情况

根据隔板法公式 $n$ 球 $m$ 盒 数量为$C(n + m - 1, m - 1)$

则必败情况数量为 $\Sigma_{i = 0}^{\lfloor \frac {l - 2 \times n} {2} \rfloor}C(i + n - 1, n - 1) \times C(l - n - i \times 2, n)$

最后用全部情况 $C(l,2 \times n)$ 减去必败情况

### Code

```cpp
#include<bits/stdc++.h>
#define LL long long
using namespace std;
const int maxn = 1e6 + 5;
const LL P = 998244353;
int T;
LL l, n;
LL inv[maxn], d[maxn];
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
void ini() {
    inv[1] = 1;
    for (LL i = 2; i <= 1e6; i++)
        inv[i] = (P - P / i) * inv[P % i] % P;
    for (int i = 2; i <= 1e6; i++)
        (inv[i] *= inv[i - 1]) %= P;
    d[1000001] = 1e6 + 1;
    for (LL i = 1e6; i; i--) d[i] = d[i + 1] * i % P;
}
void exgcd(LL a, LL b, LL &x, LL &y) {
    if (!b) return (void)(x = 1, y = 0);
    exgcd(b, a % b, y, x);
    y -= a / b * x;
}
LL gets(LL a, LL b) {
    if (a == b || !b) return 1;
    if (a < b) return 0;
    LL now = (d[a - b + 1] * inv[b]) % P;
    LL x = 0, y = 0;
    exgcd(d[a + 1], P, x, y);
    x = (x + P) % P;
    return now * x % P;
}
int main()
{
    ini();
    T = read();
    while (T--) {
        l = read(); n = read();
        if (n * 2 == l) {
            printf("0\n");
            continue;
        }
        LL sum = 0;
        for (LL i = 0; i <= l - 2 * n; i += 2) {
            (sum += gets(i / 2 + n - 1, n - 1) * gets(l - n - i, n) % P) %= P;
        }
        printf("%lld\n", ((gets(l, 2 * n) - sum) * 2 % P + P) % P);
    }

    return 0;
}
```

## F. Farmer John's Favorite Function

参考cup-pyy佬的思路

---

因为式子中有根号，一个数在六次计算后对下一个数的贡献最多为 $1$，同时向下取整，所以小数部分对答案没有贡献，可以当成整数储存。

我们把 $n$ 个数先分块，对于每个块计算：

块的边界 $c[i].l$ 和 $c[i].r$

假设该块上一个数是0，最后输出的结果 $c[i].val$ 

以及倒推出如果要让$c[i].val + 1$ ，上一块需要输出的最小数字 $c[i].need$
$val$ 暴力模拟一遍即可

倒推 $need$ 的时候注意可能会爆long long

对于每一次更改，暴力更新该块的信息

然后依次计算每一块的结果

最后不足一块的部分暴力计算

分块用 $sqrt(n)$ 最优，但 $n$ 太小不行所以取个 $max$ 

### Code

```cpp
#include<bits/stdc++.h>
#define LL long long
using namespace std;
const int maxn = 2e5 + 5;
const LL inf = 1e18;
int n, Q;
LL a[maxn], cnt;
struct block {
    LL l, r, val, need;
}c[maxn];
LL read() {
    LL x = 0, w = 1;
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
LL _sqrt(LL x) {
    LL s = sqrt(x);
    while ((s + 1) * (s + 1) <= x) s++;
    while (s * s > x) s--;
    return s;
}
void update(int k) {
    int l = c[k].l, r = c[k].r;
    LL s = 0;
    for (int i = l; i <= r; i++)
        s = _sqrt(s + a[i]);
    c[k].val = s;
    __int128 t = s + 1;
    for (int i = r; i >= l && t <= inf; i--)
        t = t * t - a[i];
    c[k].need = min((__int128)inf, t);
}
int main()
{
    n = read(); Q = read();
    int len = max(_sqrt(n), 100ll);
    for (int i = 1; i <= n; i++) a[i] = read();

    cnt = 0;
    for (int st = 1, ed = len; ed <= n / len * len; st = ed + 1, ed += len) {
        c[++cnt] = (block){st, ed, 0, 0};
        update(cnt);
    }

    while (Q--) {
        LL x, y;
        x = read(); y = read();
        a[x] = y;
        LL block = x / len + (x % len != 0);
        if (block <= cnt) update(block);

        LL s = 0;
        for (int i = 1; i <= cnt; i++)
            s = c[i].val + (s >= c[i].need);

        int st = c[cnt].r + 1;
        for (int i = st; i <= n; i++)
            s = _sqrt(s + a[i]);
        printf("%lld\n", s);
    }

    return 0;
}
```