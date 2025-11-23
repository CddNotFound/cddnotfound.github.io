---
title: 'AtCoder Beginner Contest 349 F'
description: ''
publishDate: 2024-04-14
tags:
  - ACM
  - Atc
---

<!-- more -->
## [F. Subsequence LCM](https://atcoder.jp/contests/abc349/tasks/abc349_f)
写的时候脑子里想到可能要特判 $m = 1$

写完就忘了然后与 `WA` 掉的四个点斗智斗勇（

---

根据算数基本定理，一个数可以拆成若干个质因数的乘积

即 $S = p_1^{x_1} \times p_2^{x_2} \times ··· \times p_n^{x_n}$  

$m \leq 10^{16}$ ，所以不同的质因数不会超过 $14$ 个

求公倍数就是对于每个质因数的指数取最大值

所以我们能选择的数字一定是 $m$ 的因子，而且对于一个合法的方案， $m$ 的每个质因数至少要选择一个能达到对应指数的 $a_i$ 

所以对于每个合法的不同 $a_i$ ，预处理出每个质因数的指数能否达到 $m$ 的对应指数 $b[i]$

用 $f_{i, S}$ 表示前 $i$ 种数满足质因数状态为 $S$ 的方案数

转移就是 $f_{i,S} = f_{i - 1,S} + f_{i - 1, S_1} \times (2 ^ {s_i} - 1)$ ，其中 $S = S_1 \oplus b_i$， $s_i$ 是第 $i$ 种数的个数

边界 $f_{0,0} = 1$

---

因为 $1$ 不是质数，所以当 $m = 1$ 时，输出为 $f_{now,0}$ 多加了 $f_{0,0}$ ，要减一

### Code
```cpp
#include<bits/stdc++.h>
#define LL long long
using namespace std;
const LL inf = 1e18;
const int maxn = 2e5 + 5;
const int maxm = 2e1 + 5;
const int P = 998244353;
LL n, m;
LL a[maxn], s[maxn], ta[maxn], b[maxn], cnt;
LL p[maxn], pcnt;
LL f[2][1 << 22];
LL sum1;
LL pro[maxn];
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
int main()
{
    pro[0] = 1;
    for (int i = 1; i <= 2e5; i++) pro[i] = pro[i - 1] * 2 % P;

    n = read(); m = read();
    for (int i = 1; i <= n; i++) {
        a[i] = read();
        if (m % a[i] == 0) ta[++cnt] = a[i];
    }
    sort(ta + 1, ta + 1 + cnt);

    LL t = m, nowp = 1;
    for (LL i = 2; i * i <= t; i++) {
        while (t % i == 0) {
            if (nowp != i) nowp = i, p[++pcnt] = 1;
            t /= i;
            p[pcnt] *= i;
        }
    }
    if (t > 1) p[++pcnt] = t;

    LL tn = cnt;
    cnt = 0;
    ta[0] = inf;
    for (int i = 1; i <= tn; i++)
        if (ta[i] != ta[i - 1]) a[++cnt] = ta[i], s[cnt] = 1;
            else s[cnt]++;
    for (int i = 1; i <= cnt; i++) {
        for (int j = 1; j <= pcnt; j++)
            if (a[i] % p[j] == 0) b[i] |= (1 << (j - 1));
    }

    f[0][0] = 1;
    LL now = 0, lst = 1;
    for (int i = 1; i <= cnt; i++) {
        now ^= 1, lst ^= 1;
        for (int j = 0; j < (1 << pcnt); j++) f[now][j] = f[lst][j];
        for (int j = 0; j < (1 << pcnt); j++) {
            if (!f[lst][j]) continue;
            LL aim = j | b[i];
            (f[now][aim] += f[lst][j] * (pro[s[i]] - 1) % P) %= P;
        }
    }
    if (m == 1) f[now][(1 << pcnt) - 1]--;
    printf("%d\n", (f[now][(1 << pcnt) - 1] + P) % P);
     
    return 0;
}
```