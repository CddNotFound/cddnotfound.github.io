---
title: 'Atcoder Beginner Contest 356 A - F'
description: ''
publishDate: 2024-06-01
tags:
  - ACM
  - Atc
---
<!-- more -->

打得还行所以发一下（

## [A - Subsegment Reverse](https://atcoder.jp/contests/abc356/tasks/abc356_a)

### Code
```cpp
#include<bits/stdc++.h>
#define LL long long
using namespace std;
const int maxn = 1e2 + 5;
int n, l, r;
int a[maxn];
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
    n = read(); l = read(); r = read();
    for (int i = 1; i <= n; i++) a[i] = i;

    for (int i = l; i <= r; i++) a[i] = r + l - i;

    for (int i = 1; i <= n; i++)
        printf("%d ", a[i]);
    putchar('\n');

    return 0;
}
```

## [B - Nutrients](https://atcoder.jp/contests/abc356/tasks/abc356_b)

### Code
```cpp
#include<bits/stdc++.h>
#define LL long long
using namespace std;
const int maxn = 1e2 + 5;
int n, m;
int a[maxn];
int s[maxn];
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
    n = read(); m = read();
    for (int i = 1; i <= m; i++) a[i] = read();

    for (int i = 1; i <= n; i++) {
        for (int j = 1; j <= m; j++) {
            int x;
            x = read();
            a[j] -= x;
        }
    }
    int ff = 1;
    for (int i = 1; i <= m; i++)
        if (a[i] > 0) {ff = 0; break;}
    printf(ff ? "Yes\n" : "No\n");

    return 0;
}
```

## [C - Keys](https://atcoder.jp/contests/abc356/tasks/abc356_c)

枚举每一种情况判断是否合法

### Code
```cpp
#include<bits/stdc++.h>
#define LL long long
using namespace std;
const int maxn = 1e2 + 5;
int n, m, k;
int c[maxn], res[maxn];
vector<int> p[maxn];
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
    n = read(); m = read(); k = read();
    for (int i = 1; i <= m; i++) {
        char ch[5];
        c[i] = read();
        for (int j = 1; j <= c[i]; j++) {
            int x;
            x = read();
            p[i].push_back(x);
        }
        scanf("%s", ch);
        res[i] = ch[0] == 'o' ? 1 : 0;
    }

    int ans = 0;
    vector<int> a(n + 5, 0);
    for (int i = 0; i < (1 << n); i++) {
        for (int j = 1; j <= n; j++)
            a[j] = i >> (j - 1) & 1;
        int ff = 1;
        for (int j = 1; j <= m; j++) {
            int nowres, s = 0;
            for (auto key : p[j])
                s += a[key];
            if (!((s >= k && res[j]) || (s < k && !res[j]))) {ff = 0; break;}
        }
        ans += ff;
    }
    printf("%d\n", ans);


    return 0;
}
```

## [D - Masked Popcount](https://atcoder.jp/contests/abc356/tasks/abc356_d)

跟昨天 div2 的 [B - Turtle and an Infinite Sequence](https://codeforces.com/contest/1981/problem/B) 的原理差不多

二进制下第 $i$ 位从 $0$ 开始的规律是：$2^i$ 个 $0$ ， $2^i$ 个 $1$ ， $2^i$ 个 $0$ ，...

算一下每一位的贡献即可

### Code
```cpp
int main()
{
    n = read(); m = read();

    mint ans = 0;
    for (int i = 0; i <= 60; i++) {
        if (!(m >> i & 1)) continue;
        LL now = 1ll << i;
        LL num = n % (2 * now);
        ans += max(0ll, num - now + 1);
        num = n / (2 * now);
        ans += num * now;
    }
    printf("%d\n", ans);

    return 0;
}
```

## [E - Max/Min](https://atcoder.jp/contests/abc356/tasks/abc356_e)

两个数的结果与大小顺序无关，因为 $i < j$ 所以只计算一次，那么考虑从小到大处理每个数与比他大的数产生的贡献

对于 $a_i$ ，处于 $[a_i, 2 \times a_i - 1]$ 的数与其计算的结果均为 $1$ ，处于$[2 \times a_i,3 \times a_i - 1]$ 的数与其计算的结果均为 $2$ ，以此类推

那么可以枚举每个数的每个结果 $x$ 的区间，计算该区间内包含的数字个数 $s$ ，a那么这段区间的贡献就是 $x \times s$

相同的数字可以合并到一起计算，区间内数字个数用树状数组维护，最终的时间复杂度就是 $O(x * m / x·logm) = O(mlogm)$ （m 为 $max(a_i)$）

然后注意到某个数字如果有 $k$ 个，他们之间的贡献应该是 $C^2_k$ 个 $1$ ，但是我们计算了 $k ^ 2$ 个，减去多余的这部分即可

### Code
```cpp
#include<bits/stdc++.h>
#define LL long long
using namespace std;
const int maxn = 1e6 + 5;
int n;
int a[maxn];
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
int main()
{
    n = read();
    Fenwick t(1e6);

    for (int i = 1; i <= n; i++) {
        a[i] = read();
        t.add(a[i], 1);
    }

    LL ans = 0;
    vector<int> v(1000005, 0);
    for (int i = 1; i <= n; i++) {
        v[a[i]]++;
        if (v[a[i]] > 1) continue;
        LL l = a[i], r = l + a[i] - 1, cnt = 0, s = 0;
        while (l <= 1000000) {
            r = min(1000000ll, r);
            cnt++;
            s += cnt * t.query(l, r);
            l += a[i], r += a[i];
        }
        ans += (s - 1) * t.query(a[i], a[i]);
    }
    for (int i = 1; i <= 1e6; i++) {
        if (v[i] <= 1) continue;
        ans -= 1ll * v[i] * (v[i] - 1) / 2;
    }
    printf("%lld\n", ans);

    return 0;
}
```

## [F - Distance Component Size Query](https://atcoder.jp/contests/abc356/tasks/abc356_f)

思路挺简单的，但是写成超级大分讨了（

---

首先互相连通的点一定是连续的，那么形成的联通块可以看成若干条不相交的线段，我们用两个 `set`分别记录每条线段的左端点和右断点

先来考虑插入一个点：

1. 如果这个点是唯一一个点，那么将区间 $[x, x]$ 插入

2. 如果这个点在最右边，看他能不能和左边最近的点合并。如果能合并就是将左边那条线段的右端点变为 $x$ ，否则插入区间 $[x, x]$

3. 如果这个点在最左边，与 (2) 同理

4. 如果这个点在中间，且位于某条线段上，那么不做修改。否则分别考虑能否和左边右边的线段合并

再来看删除：

1. 唯一一个点，删除区间 $[x, x]$

2. 在最右边，看左边最近的点，如果在一条线段内，将这条线段右端点从 $x$ 变为 $L$

3. 在最左边，与 (2) 同理

4. 在中间，如果左边右边最近的两个点能够相连就不做修改，不然分别判断是否需要和左边、右边最近的点断开

查询就是这个点最左边的线段右端点下标 + 1 到 最右边的线段左端点下标 - 1 之间点的个数

懒得画图了，应该不难理解（

### Code

```cpp
int main()
{
    Q = read(); k = read();
    for (int i = 1; i <= Q; i++) q[i] = {read(), read()};

    map<LL, int> mp;
    for (int i = 1; i <= Q; i++) f[i] = q[i].x;
    sort(f + 1, f + 1 + Q);
    int now = 0;
    for (int i = 1; i <= Q; i++) {
        if (f[i] != f[i - 1]) now++;
        mp[f[i]] = now;
        pos[now] = f[i];
    }
    for (int i = 1; i <= Q; i++) q[i].x = mp[q[i].x];

    Fenwick t(2e5);
    LL ans = 0;
    set<int> s, lgap, rgap;
    for (int i = 1; i <= Q; i++) {
        auto [opt, x] = q[i];
        if (opt == 1) {
            if (s.find(x) == s.end()) {
                s.insert(x);
                t.add(x, 1);
                auto pre = s.lower_bound(x), succ = s.upper_bound(x);
                LL L, R;
                if (pre != s.begin()) pre--, L = *pre;
                    else L = -1;
                if (succ != s.end()) R = *succ;
                    else R = -1;
                if (R == -1 && L == -1) {
                    lgap.insert(x), rgap.insert(x);
                } else if (R != -1 && L == -1) {
                    if (abs(pos[R] - pos[x]) <= k) {
                        rgap.insert(x);
                        rgap.erase(R);
                    } else {
                        lgap.insert(x);
                        rgap.insert(x);
                    }
                } else if (R == -1 && L != -1) {
                    if (abs(pos[L] - pos[x]) <= k) {
                        lgap.insert(x);
                        lgap.erase(L);
                    } else {
                        lgap.insert(x);
                        rgap.insert(x);
                    }
                } else {
                    if (abs(pos[L] - pos[R]) <= k) continue;
                    lgap.insert(x), rgap.insert(x);
                    if (abs(pos[L] - pos[x]) <= k) {
                        rgap.erase(x);
                        lgap.erase(L);
                    }
                    if (abs(pos[R] - pos[x]) <= k) {
                        lgap.erase(x);
                        rgap.erase(R);
                    }
                }
            } else {
                auto pre = s.lower_bound(x), succ = s.upper_bound(x);
                LL L, R;
                if (pre != s.begin()) {
                    pre--;
                    L = *pre;
                } else L = -1;
                if (succ != s.end()) R = *succ;
                    else R = -1;
                if (R == -1 && L == -1) {
                    lgap.erase(x);
                    rgap.erase(x);
                } else if (R != -1 && L == -1) {
                    if (abs(pos[R] - pos[x]) <= k) {
                        rgap.erase(x);
                        rgap.insert(R);
                    }
                    else {
                        lgap.erase(x);
                        rgap.erase(x);                        
                    }
                } else if (R == -1 && L != -1) {
                    if (abs(pos[L] - pos[x]) <= k) {
                        lgap.erase(x);
                        lgap.insert(L);
                    } else {
                        lgap.erase(x);
                        rgap.erase(x);
                    }
                } else {
                    if (abs(pos[L] - pos[R]) <= k) {
                    } else {
                        if (lgap.find(L) == lgap.end()) lgap.insert(L);
                        if (rgap.find(R) == rgap.end()) rgap.insert(R);
                        lgap.erase(x);
                        rgap.erase(x);  
                    }
                }
                s.erase(x);
                t.add(x, -1);
            }
        } else {
            auto pre = lgap.lower_bound(x), succ = rgap.upper_bound(x);
            LL L, R;
            if (pre != lgap.begin()) pre--, L = *pre + 1;
                else L = 1;
            if (succ != rgap.end()) R = *succ - 1;
                else R = Q;
            printf("%lld\n", t.query(L, R));
        }
    }

    return 0;
}
```