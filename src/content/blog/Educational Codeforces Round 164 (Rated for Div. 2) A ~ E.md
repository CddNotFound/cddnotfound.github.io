---
title: 'Educational Codeforces Round 164 (Rated for Div. 2) A - E'
description: ''
publishDate: 2024-04-13
tags:
  - ACM
  - CF
---
<!-- more -->

## [A. Painting the Ribbon](https://codeforces.com/contest/1954/problem/A)
尽可能平均的把 $n$ 块染成 $m$ 种颜色

$k$ 比 $m - 1$ 种颜色的总和小就可以
### Code
```cpp
int main()
{
    T = read();
    while (T--) {
        n = read(); m = read(); k = read();
        if (m == 1) {
            printf("NO\n");
            continue;
        }
        int ff = 0;
        int s = n / m + (n % m != 0);

        int res = n - s;

        printf(k < res ? "YES\n" : "NO\n");
    }

    return 0;
}
```

## [B. Make It Ugly](https://codeforces.com/contest/1954/problem/B)
最后等于的那个数一定是 $a_1$ ，所以只要删掉最短的一段连续的 $a_1$ 就行了

如果要全删了则无解
### Code
```cpp
int main()
{
    T = read();
    while (T--) {
        n = read();
        for (int i = 1; i <= n; i++) a[i] = read();
        int aim = a[1], ans = inf;
        int s = 0;
        a[n + 1] = inf;
        for (int i = 1; i <= n + 1; i++)
            if (a[i] == aim) s++;
                else ans = min(ans, s), s = 0;

        if (ans == n) printf("-1\n");
            else printf("%d\n", ans);            
    }

    return 0;
}
```

## [C. Long Multiplication](https://codeforces.com/contest/1954/problem/C)
$a$ 跟 $b$ 的和一定，要使乘积最大则要让 $a$ 和 $b$ 尽可能接近

找到第一位不一样的， 后面每一位的大小和这位反着来就行
### Code
```cpp
int main()
{
    T = read();
    while (T--) {
        scanf("%s", &a);
        scanf("%s", &b);

        int len = strlen(a), pos = len, mx = 0;
        for (int i = 0; i < len; i++) //应该固定让a更大，写起来会简洁很多
            if (a[i] != b[i]) {
                pos = i;
                if (a[i] > b[i]) mx = 1;
                    else mx = 2;
                break;
            }
        for (int i = pos + 1; i < len; i++) {
            char t;
            if (mx == 1 && a[i] > b[i]) t = a[i], a[i] = b[i], b[i] = t;
            if (mx == 2 && a[i] < b[i]) t = a[i], a[i] = b[i], b[i] = t;
        }
        printf("%s\n%s\n", a, b);
    }

    return 0;
}
```

## [D. Colored Balls](https://codeforces.com/contest/1954/problem/D)
对于一个集合，如果数量最多颜色比其他颜色的数量和还多，$value$ 就是这个最多的数量

不然就是 $\lceil \frac {球的总数} {2} \rceil$

先对球的数量进行排序

令 $s_{i,j}$ 表示前 $i$ 个颜色的球数量总和为 $j$ 的集合数，集合包含第 $i$ 种球的 $value$ 的贡献就是 $i - 1$ 的所有方案按上述计算的和

$s$ 则分为带 $i$ 的集合和不带 $i$ 的集合两种，$s_{i,j} = s_{i - 1, j} + s_{i - 1, j - a[i]}$

边界是 $f[1] = a[1], s[1][0] = s[1][a[1]] = 1$ 
### Code
```cpp
#include<bits/stdc++.h>
#define LL long long
using namespace std;
const int maxn = 5e3 + 5;
const int P = 998244353;
LL n;
LL a[maxn];
LL f[maxn], s[maxn][maxn];
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
    n = read();
    for (int i = 1; i <= n; i++) a[i] = read();
    sort(a + 1, a + 1 + n);

    f[1] = a[1];
    s[1][a[1]] = 1;
    s[1][0] = 1;

    for (int i = 2; i <= n; i++) {
        f[i] = f[i - 1];
        for (int j = 0; j <= 5000; j++) {
            if (s[i - 1][j]) {
                if (j <= a[i]) (f[i] += a[i] * s[i - 1][j]) %= P;
                    else (f[i] += (j + a[i] + 1) / 2 * s[i - 1][j]) %= P;

            }
        }
        for (int j = 0; j <= 5000; j++) {
            if (!s[i - 1][j]) continue;
            (s[i][j] += s[i - 1][j]) %= P;
            int ts = j + a[i];
            if (ts > 5000) continue;
            (s[i][ts] += s[i - 1][j]) %= P;
        }
    }
    printf("%d\n", f[n] % P);

    return 0;
}
```

## [E. Chain Reaction](https://codeforces.com/contest/1954/problem/E)
一开始被样例输出迷惑当成二分了

写完搓了好几组数据才发现不仅会超时，答案还不满足递减（

---

伤害为 $k$ 击杀血量为 $a_i$ 的敌人需要 $\lceil \frac {a_i} {k} \rceil = \lfloor \frac {a_i + k - 1} {k} \rfloor$ 次

当伤害为 $k$ 时，每一位对答案的贡献就是 $max(0, a_i / k - a_{i - 1} / k)$

所以只有连续上升的子序列对答案才有贡献

注意到 $\lceil \frac {a_i} {k} \rceil$ 最多会变化 $d(a_i)$ 次（为 $a_i$ 的因子个数）

所以考虑维护每一个数不同消灭次数对答案差分数组的贡献

对于每个消灭次数对应的 $k$ 的区间 $[now, nxt]$ ，若满足 $a_{i - 1} < a_i$ ，则对该区间贡献加上所需次数 $num$ ，若满足 $a_i < a_{i + 1}$ ，则对该区间减去次数 $num$ ，对应上面的公式 $a_i / k - a_{i - 1} / k$

当 $now = a_i$ 时，避免除 $0$ 要将 $nxt$ 手动置为 $mx$ 

记得开 `long long`

### Code

```cpp
#include<bits/stdc++.h>
#define LL long long
using namespace std;
const int maxn = 2e5 + 5;
const LL inf = 1e18;
int T;
int n;
LL a[maxn];
LL v[maxn]; 
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
    T = 1;
    while (T--) {
        n = read();
        LL mx = 0;
        for (int i = 1; i <= n; i++) a[i] = read(), mx = max(mx, a[i]);
        for (int i = 1; i <= mx; i++) v[i] = 0;
        for (int i = 1; i <= n; i++) {
            int nxt = 1;
            for (int now = 1; now <= a[i]; now = nxt + 1) {
                int num = (a[i] + now - 1) / now;
                if (now == a[i]) nxt = mx;
                    else nxt = (a[i] + num - 2) / (num - 1) - 1;
                if (a[i] > a[i - 1]) v[now] += num, v[nxt + 1] -= num;
                if (a[i] < a[i + 1]) v[now] -= num, v[nxt + 1] += num;
            }
        }
        for (LL i = 1, ans = v[1]; i <= mx; ans += v[++i])
            printf("%lld ", ans);
        putchar('\n');
    }

    return 0;
}
```