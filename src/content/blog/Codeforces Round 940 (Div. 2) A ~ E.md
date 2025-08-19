---
title: 'Codeforces Round 940 (Div. 2) A - E'
description: ''
publishDate: 2024-04-22
tags:
  - ACM
  - CF
---

<!-- more -->

赛后五分钟过`D`，还是急了

掉大分

## [A.Stickogon](https://codeforces.com/contest/1957/problem/A)

全部拼等边三角形

### Code

```cpp
int main()
{
    T = read();
    while (T--) {
        n = read();
        memset(s, 0, sizeof(s));
        for (int i = 1; i <= n; i++) s[read()]++;
        ans = 0;

        for (int i = 1; i <= 100; i++) {
            ans += s[i] / 3;
        }
        printf("%d\n", ans);
    }

    return 0;
}
```

## [B. A BIT of a Construction](https://codeforces.com/contest/1957/problem/B)

如果二进制下全是 $1$ 输出自己和一堆 $0$ 就行了

剩下的最多只有位数减一个 $1$ ，拆成两个数输出即可

### Code

```cpp
int main()
{
    T = read();
    while (T--) {
        n = read(); k = read();

        LL now = 0;
        if (n == 1) {
            printf("%d\n", k);
            continue;
        }

        LL tt = k;
        while (tt) now++, tt >>= 1;
        now--;
        if ((1ll << (now + 1)) - 1 == k) {
            printf("%d ", k);
            for (int i = 1; i < n; i++)
                printf("0 ");
            putchar('\n');
            continue;
        }
        LL t = (1ll << now) - 1;
        k -= t;
        printf("%lld %lld ", k, t);
        for (int i = 1; i < n - 1; i++)
            printf("0 ");
        putchar('\n');
    }

    return 0;
}
```

## [C. How Does the Rook Move?](https://codeforces.com/contest/1957/problem/C)

是有递推式的， 我写的太麻烦了不建议看（

---

思路很简单，答案仅于空的行数有关

可以先确定在分界线上下几个， 剩下的行数每次选两个，对于每次选择的两行都有两种情况要乘起来，同时考虑重复情况要除以全排列数

以 $n = 6$ 解释一下:

全下在镜像分界线上有 $C^6_6$ 种情况

分界线上下 $4$ 个是 $C^4_6 \times \frac {C^2_2 \times 2^1} {1!}$

分界线上下 $2$ 个是 $C^2_6 \times \frac{C^2_4 \times C^2_2 \times 2 ^ 2} {2!}$

分界线上下 $6$ 个是 $C^0_6 \times \frac {C^2_6 \times C^2_4 \times C^2_2 \times 2^3} {3!}$

总和就是 $1 + 30 + 180 + 120 = 331$

最终公式是$f_i = \sum_{i = 0}^{\lfloor \frac {n + 1} {2} \rfloor} C^{n - 2i}_n \times \frac {(\prod_{j = 1}^{i}C^2_{2j}) \times 2^i} {i!}$

处理几个前缀积就能算了

### Code

```cpp
#include<bits/stdc++.h>
#define LL long long
using namespace std;
const int maxn = 3e5 + 5;
const int P = 1e9 + 7;
int T;
int n, k;
int v[maxn];
LL nn[maxn], t[maxn];
LL ss[maxn];
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
    nn[0] = t[0] = ss[0] = 1;
    nn[1] = 1, nn[2] = 2;
    t[1] = 2, t[2] = 4;
    for (int i = 3; i <= 3e5; i++)
        nn[i] = (nn[i - 1] * i) % P, t[i] = (t[i - 1] * 2) % P;
    ss[2] = 1, ss[4] = 6;
    for (int i = 6; i <= 3e5; i += 2)
        ss[i] = (ss[i - 2] + i + i - 3) % P;
    for (int i = 4; i <= 3e5; i += 2)
        ss[i] = (ss[i] * ss[i - 2]) % P;

    inv[1] = 1;
    for (LL i = 2; i <= 3e5; i++)
        inv[i] = (P - P / i) * inv[P % i] % P;
    for (int i = 2; i <= 3e5; i++)
        (inv[i] *= inv[i - 1]) %= P;
    d[300001] = 3e5 + 1;
    for (LL i = 3e5; i; i--) d[i] = d[i + 1] * i % P;
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
        n = read(); k = read();
        for (int i = 1; i <= k; i++) {
            int x, y;
            x = read(); y = read();
            v[x] = v[y] = 1;
        }

        int s = 0;
        for (int i = 1; i <= n; i++) {
            if (!v[i]) s++;
            v[i] = 0;
        }

        LL ans = 0;
        for (int i = 0; i <= s; i += 2) {
            LL x, y;
            exgcd(nn[i / 2], P, x, y);
            x = (x + P) % P;
            (ans += gets(s, i) * ss[i] % P * t[i / 2] % P * x % P) %= P;  
        }
        printf("%lld\n", (ans + P) % P);
    }

    return 0;
}
```

## [D. A BIT of an Inequality](https://codeforces.com/contest/1957/problem/D)

挺有意思的一个 `dp` ，不过好像这题也写麻烦了，跟大部分题解完全不一样（

---

首先 $f(x, y) \oplus f(y, z) > f(x, z)$ 满足时当且仅当 $f(x, z)$ 在 $a_y$ 的最高位位置是 $0$

所以我们用 $f_{i, k}$ 表示选位置 $i$ 为 $z$， $a_y$ 的最高位位置是 $k$ 时符合的三元组个数，$g_{i, k}$ 就是不符合的个数

当 $i$ 到下一个数时，如果第 $k$ 位是 $1$ ，意味着以 $k$ 为最高位 $a_y$ 的合法方案的 $f(x, z)$ 在这一位的 $01$ 将改变，也就是合法变不合法，不合法变合法

接着考虑如何加入 $y = z$ 的情况，也是只要看 $a_y$ 的最高位

那么想象一下从这 $a_z$ 开始往前异或，这一位有奇数个 $1$ 的区间为不合法方案，偶数个 $1$ 才合法

比如前 $z$ 个数的第 $k$ 位情况如下：

$00001｜001｜0001｜001$

那么当 $x$ 取在，与 $z$ 所在的区间序号奇偶性不同的区间时均为合法

这里 $z$ 在第 $4$ 个区间，所以 $x$ 要取在 $1$ 或 $3$ 号区间

那么用两个数组 $s1$ , $s2$ 分别储存与 $i$ 奇偶性不同/相同的区间，然后遇到 $1$ 说明将进入新的区间，将$s1$ , $s2$ 交换再把这个区间长度加到 $s2$ 就行了

详情见代码（

### Code

```cpp
#include<bits/stdc++.h>
#define LL long long
using namespace std;
const int maxn = 1e5 + 5;
int T;
int n;
int a[maxn], up[maxn];
LL f[50], g[50], s1[50] ,lst[50], s2[50];
LL ans;
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
        a[0] = 0;
        for (int i = 1; i <= n; i++) {
            a[i] = read();
            int t = a[i];
            up[i] = 0;
            while (t) up[i]++, t >>= 1;
        }
        for (int i = 1; i <= 30; i++) s1[i] = s2[i] = f[i] = g[i] = lst[i] = 0;

        g[up[1]] = 1, f[up[1]] = 0;
        ans = 0;
        for (int k = 1; k <= 30; k++) {
            if (a[1] & (1 << (k - 1))) {
                    swap(s1[k], s2[k]);
                    s2[k] += 1 - lst[k], lst[k] = 1;
                }
        }
        for (int i = 2; i <= n; i++) {
            for (int k = 1; k <= 30; k++)
                if (a[i] & (1 << (k - 1))) swap(g[k], f[k]);

            for (int k = 1; k <= 30; k++) {
                if (a[i] & (1 << (k - 1))) {
                    swap(s1[k], s2[k]);
                    s2[k] += i - lst[k], lst[k] = i;
                }
            }
            f[up[i]] += s1[up[i]];
            g[up[i]] += i - s1[up[i]];

            for (int k = 1; k <= 30; k++) ans += f[k];
        }
        printf("%lld\n", ans);
    }

    return 0;
}
```

## [E. Carousel of Combinations](https://codeforces.com/contest/1957/problem/E)

打个表， 可以发现对于一个 $j$ ，如果 $j$ 是质数，从 $C(j, j)$ 开始会处于一个$j - 1, ..., j - 1, j - 2, ..., j - 2, ..., 0, ..., 0$ 的循环

也就是 $j$ 个 $j - 1$ ，$j$ 个 $j - 2$， 一直到 $j$ 个 $0$ 。循环长度为$j \times j$

证明如下：

首先题目中 $C(i, j) = \frac{\prod_{i = i - j + 1}^j i} {j}$

当$j$ 是质数

拆开就是 $j$ 一直乘到 $i - j + 1$ 一共 $j$ 个数

如果先对不是 $j$ 的倍数的数先取模， 就是$1 \times 2 \times ... \times j - 1$ 再乘上一个 $j$ 的倍数

起始项是 $C(j, j)$ 那么这个倍数就是 $j$ ，除 $j$ 后为 $1$ ，最后的结果就是$j - 1$， 那么接下来的$j - 1$ 项， 这个倍数不会变，剩下取的 $j - 1$ 个数取模后的集合也不会变 

直到第 $j + 1$ 项， 倍数变成 $2j$ , 除 $j$ 后为 $2$ ，最后的结果变成 $j - 2$

然后一直到 $j \times j + 1$ 项，倍数变成 $j \times (j + 1)$ 除 $j$ 再取模后变成 $1$ ，回到循环初始位置

那么对于合数 $j$ ，如果 $j$ 不是平方数， 那么它的每个因子都能在 $2$ 到 $j - 1$ 里找到，最后的积一定是 $j$ 的倍数，所以结果为 $0$

对于平方数，会缺少一个根号因子，可以从 $2 \times \sqrt j$ 里提出来，结果也都是 $0$ 

特殊的， 当 $j = 4$ 时凑不出来，循环是 $4$ 个 $2$ ， $4$ 个 $0$ ......

---

所以对于 $j$ 的每段区间用直接差分就可以了， 一次处理 $j$ 的长度，时间复杂度$O(n\sqrt n)$

最后求两次前缀和， 一次是求出对于某个 $i$ 的结果的和，两次才是 $1$ 到 $i$ 的和

### Code

```cpp
#include<bits/stdc++.h>
#define LL long long
using namespace std;
const int maxn = 1e6 + 5;
const int P = 1e9 + 7;
int T;
int n;
int v[maxn];

int vis[maxn], a[maxn], cnt;
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
    for (int i = 2; i <= 1e6; i++) {
        if (!vis[i]) a[++cnt] = i;
        for (int j = 1; j <= cnt; j++) {
            if (i * a[j] > 1e6) break;
            vis[i * a[j]] = 1;
            if (i % a[j] == 0) break; 
        }
    }

    for (int i = 1; i <= cnt; i++) {
        int t = a[i], now = t - 1;
        for (int j = t; j <= 1e6; j += t) {
            int l = j, r = min(1000000, l + t - 1);
            (v[l] += now) %= P, (v[r + 1] -= now) %= P;
            now--;
            if (now < 0) now = t - 1;
        }
    }
    int now = 2;
    for (int i = 4; i <= 1e6; i += 4) {
        int l = i, r = min(i + 3, 1000000);
        (v[l] += now) %= P; (v[r + 1] -= now) %= P;
        now = now ? 0 : 2;
    }
    for (int t = 1; t <= 2; t++)
        for (int i = 1; i <= 1e6; i++)
            (v[i] += v[i - 1]) %= P;
}
int main()
{
    ini();
    T = read();
    while (T--) {
        n = read();
        printf("%d\n", (v[n] + P) % P);
    }

    return 0;
}
```