---
title: 'Codeforces Round 982 (Div. 2) A - E1'
description: ''
publishDate: 2024-10-27
tags:
  - ACM
  - CF
---
<!-- more -->

## [A - Rectangle Arrangement](https://codeforces.com/contest/2027/problem/A)

长宽取最大加起来乘二

### Code

```cpp
#include<bits/stdc++.h>
using namespace std;

typedef pair<int, int> pii;
typedef long long LL;
typedef unsigned long long uLL;
#define mp make_pair

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
    cin.tie(0); cout.tie(0);
    ios::sync_with_stdio(0);

    int T = 1;
    T = read();
    while (T--) {
        int n;
        n = read();
        int w = 0, h = 0;
        for (int i = 1; i <= n; i++) {
            int x, y;
            x = read(); y = read();
            w = max(w, x);
            h = max(h, y);
        }
        cout << (w + h) * 2 << "\n";

    }

    return 0;
}
```

## [B - Stalin Sort](https://codeforces.com/contest/2027/problem/B)

假设最终序列中最大的数为 x，在 x 前面的所有数和在 x 后面的比 x 大的数都要手动删掉

每个位置挨个算即可

### Code

```cpp
#include<bits/stdc++.h>
using namespace std;

typedef pair<int, int> pii;
typedef long long LL;
typedef unsigned long long uLL;
#define mp make_pair

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
    cin.tie(0); cout.tie(0);
    ios::sync_with_stdio(0);

    int T = 1;
    T = read();
    while (T--) {
        int n;
        n = read();
        vector<int> a(n + 5, 0);
        for (int i = 1; i <= n; i++) a[i] = read();

        int ans = 1e9;
        for (int i = 1; i <= n; i++) {
            int tmp = i - 1;
            for (int j = i + 1; j <= n; j++)
                if (a[j] > a[i]) ++tmp;
            ans = min(ans, tmp);
        }
        cout << ans << "\n";
    }

    return 0;
}
```

## [C - Add Zeros](https://codeforces.com/contest/2027/problem/C)

$a_i$ 可以将数组长度从 $a_i + i - 1$ 增加到 $a_i + i - 1 + i - 1$

可以视为增加了一条有向边，起点为 n ，能到达的最大的点就是答案

可以用 map 记录到达状态，按顺序把这 n 条边扫一遍
### Code

```cpp
#include<bits/stdc++.h>
using namespace std;

typedef pair<int, int> pii;
typedef long long LL;
typedef unsigned long long uLL;
// #define mp make_pair

#define int long long

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

struct node {
    int u, v;
    bool operator < (const node a) const {
        if (u != a.u) return u < a.u;
        return v < a.v;
    }
};

signed main()
{
    cin.tie(0); cout.tie(0);
    ios::sync_with_stdio(0);

    int T = 1;
    T = read();
    while (T--) {
        int n;
        n = read();
        vector<int> a(n + 5, 0);
        for (int i = 1; i <= n; i++) a[i] = read();

        vector<node> l;
        for (int i = 1; i <= n; i++) {
            int nowlen = a[i] + i - 1;
            if (nowlen < n) continue;
            int newlen = nowlen + i - 1;
            l.push_back({nowlen, newlen});
        }

        map<int, int> mp;
        mp[n] = 1;
        
        int ans = n;
        sort(l.begin(), l.end());
        for (auto [u, v] : l) {
            if (!mp.count(u)) continue;
            mp[v] = 1;
            ans = max(ans, v);
        }

        cout << ans << "\n";
    }

    return 0;
}
```

## [D2 - The Endspeaker (Hard Version)](https://codeforces.com/contest/2027/problem/D2)

D1 有点背包的感觉

令 $dp_{i, j}$ 表示用了 b 数组的前 j 个，删除了 a 数组的前 i 个的花费。二分找到区间右端点为 i ，能删到的最远的左端点，从那个位置转移过来，就是 $dp_{i, j} = min\{dp_{i, j - 1}, dp_{left, j - 1} + m - j\}$ 

再看 D2 的方案数，转移的时候 $[left, i]$ 这个区间里可能有多个转移后为最小值的点，从其中任意一个点转移过来都是当前的合法方案，可以用线段树维护区间最小值对应的方案数的和。最小值比当前大就不更新，相等就加上区间方案数的和，比当前值小就直接覆盖

### Code

```cpp
#include<bits/stdc++.h>
using namespace std;

typedef pair<int, int> pii;
typedef long long LL;
typedef unsigned long long uLL;
#define mp make_pair

const LL inf = 1e18;

// #define int long long

const int P = 1e9 + 7;
template<const int mod>
struct ModInt {
    static const int P = mod;
    int x;
    ModInt (int x = 0) : x(x % P) {}
    ModInt (LL x) : x(int(x % P)) {}
    int val() {return x;}

    ModInt operator + (const ModInt &a) const {int x0 = x + a.x; return ModInt(x0 < P ? x0 : x0 - P);}
    ModInt operator - (const ModInt &a) const {int x0 = x - a.x; return ModInt(x0 < 0 ? x0 + P : x0);}
    ModInt operator * (const ModInt &a) const {return ModInt(1ll * x * a.x % P);}
    ModInt operator / (const ModInt &a) const {return *this * a.inv();}
    bool operator == (const ModInt &a) const {return x == a.x;};
    bool operator != (const ModInt &a) const {return x != a.x;};
    void operator += (const ModInt &a) {x += a.x; if (x >= P) x -= P;}
    void operator -= (const ModInt &a) {x -= a.x; if (x < 0) x += P;}
    void operator *= (const ModInt &a) {x = 1ll * x * a.x % P;}
    void operator /= (const ModInt &a) {*this = *this / a;}
    friend ModInt operator + (int y, const ModInt &a){int x0 = y + a.x; return ModInt(x0 < P ? x0 : x0 - P);}
    friend ModInt operator - (int y, const ModInt &a){int x0 = y - a.x; return ModInt(x0 < 0 ? x0 + P : x0);}
    friend ModInt operator * (int y, const ModInt &a){return ModInt(1ll * a.x * y % P);}
    friend ModInt operator / (int y, const ModInt &a){return ModInt(y) / a;}
    friend ostream &operator<<(ostream &os, const ModInt &a) {return os << (a.x + P) % P;}
    friend istream &operator>>(istream &is, ModInt &t) {return is >> t.x;}

    ModInt pow(LL n) const {
       ModInt sum(1), base(x);
       n %= (P - 1);
       while (n) {
           if (n & 1) sum *= base;
           base *= base;
           n >>= 1;
       }
       return sum;
    }

    ModInt inv() const {
        int a = x, b = P, x = 1, y = 0;
        while (b) {
        int t = a / b;
           a -= t * b; swap(a, b);
           x -= t * y; swap(x, y);
        }
        if (x < 0) y += P;
        return x;
    }
};
using mint = ModInt<P>;

const int maxn = 3e5 + 5;

struct node {
    LL mn;
    mint s;
};
node tt[maxn << 2];
LL lazy[maxn << 2];

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
    if (l == r) return tt[p] = {inf, 1}, void();
    int mid = (l + r) >> 1;
    tbuild(l, mid, p << 1);
    tbuild(mid + 1, r, p << 1 | 1);
    tpushup(p);
}
void tupdate(int l, int r, int x, node k, int p) {
    if (x <= l && r <= x) {
        tt[p] = k;
        // tt[p].mn += k;
        // lazy[p] += k;
        return ;
    }
    tpushdown(l, r, p);
    int mid = (l + r) >> 1;
    if (x <= mid) tupdate(l, mid, x, k, p << 1);
        else tupdate(mid + 1, r, x, k, p << 1 | 1);
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

signed main()
{
    cin.tie(0); cout.tie(0);
    ios::sync_with_stdio(0);

    int T = 1;
    cin >> T;
    while (T--) {
        int n, m;
        cin >> n >> m;
        vector<LL> a(n + 5, 0), b(m + 5, 0);
        vector<LL> s(n + 5, 0);
        for (int i = 1; i <= n; i++) cin >> a[i], s[i] = s[i - 1] + a[i];
        for (int i = 1; i <= m; i++) cin >> b[i];

        vector<LL> dp(n + 5, inf);
        vector<mint> f(n + 5, 0);
        dp[0] = 0;

        tbuild(0, n, 1);
        tupdate(0, n, 0, {0, 1}, 1);

        for (int k = 1; k <= m; k++) {
            for (int i = 1; i <= n; i++) {
                int l = 1, r = i, tag = -1;
                while (l <= r) {
                    int mid = (l + r) >> 1;
                    if (s[i] - s[mid - 1] <= b[k]) r = mid - 1, tag = mid;
                        else l = mid + 1;
                }
                if (tag == -1) continue;
                --tag;
                
                auto tmp = tquery(0, n, tag, i - 1, 1);
                if (tmp.mn + m - k > dp[i]) {
                    continue;
                } else if (tmp.mn + m - k == dp[i]) {
                    f[i] += tmp.s;
                } else {
                    dp[i] = tmp.mn + m - k;
                    f[i] = tmp.s;
                }

                tupdate(0, n, i, {dp[i], f[i]}, 1);
            }
        }

        if (dp[n] == inf) {
            cout << "-1\n";
        } else {
            cout << dp[n] << ' ' << f[n] << "\n";
        }
    }

    return 0;
}
```

## [E1 - Bit Game (Easy Version)](https://codeforces.com/contest/2027/problem/E1)

前置芝士：Nim 博弈与 SG 函数

条件相当于是每次可以将 $x_i$ 在二进制下的若干个 1 变为 0，并且在十进制下的和不超过 $a_i$

那么对于二进制下超过 a 的最高位部分的石头是肯定取不走了，可以直接删了当不存在 

这么处理过后，如果 $a_i >= x_i$ ，说明无论怎么取都满足条件一。让 $bit$ 表示 $x_i$ 在二进制下 1 的个数，取一次之后可以将 $bit$ 变为 $[0, bit - 1]$ 中的任何一个值。当 bit 为 1，取一次变为 0 ，所以sg值为 1，bit 为 2 可以取到 sg(0) 和 sg(1) ，所以 sg 值为 2，可以发现 $x_i$ 的 sg 值就是 bit

当 $a_i < x_i$ 时，统计一下跟在首位后的连续的 $x_i$ 中为 1 但是 $a_i$ 中为 0 的位置数量，以及 $x_i$ 中第一个比 $a_i$ 大的位置后的 1 的数量

比如

```
100010
110111
```

big = 2, cnt = 3

```
11010
11110
```

big = 0, cnt = 1

首先是 sg 为 0 的情况：

当 cnt = 0，说明 $x_i$ 中比 $a_i$ 大的位置仅有一个，且是 $x_i$ 中最小的那个 1 ，此时无论怎么取，取之后的石头数量都小于 $a_i$ 且不为 0，对应的 sg 值不为 0， 所以 $sg(x_i) = 0$

对于剩下的情况均可以通过取一次转移到上面的情况，补上了 0 的空缺，考虑如何计算 sg

首先当 $big \neq 0$ 时，转移可以分为两种：

1. 首位为 1，其余的所有 1 为任意状态
2. 首位为 0，big 对应的位置均为 1，剩下 1 为任意状态

先看第二种情况，若除了 big 均为 0，对应的 sg 值为 big，我们可以通过增加剩下位置的 1 来凑出 $[big, bit(x_i) - 1]$ 的所有 sg 值。

然后是第一种情况，先考虑把除了首位和 big 的 1 全取走，对于剩下的big，全取为 0 获得一个 sg = 1，单留一位 big 获得一个 sg = 0，单留两位可以转移成前面的状态，结合第二种情况获得 sg = 3，单留三位由于前面没有 2，所以 sg = 2，以此类推。

目前只有 big 为奇数（除了1）的时候，$sg \neq bit$ ，再考虑剩下位置的贡献：

此时 $sg = big - 1$ ，所以只需要看能不能再凑出一个 big - 1 。如果除了首位和 big 对应位置还有 1，我们把这些 1 全取走，根据上面的结论就可以获得一个 big - 1，最终 $sg = bit(x_i)$ ，否则无论怎么取 big 都凑不出来

当 $big = 0$ ，且不属于上面的 sg 为 0 的情况时，说明能补上 0 ，并且只要取了次高位，不取首位，剩下无论取不取最终数量都小于 $a_i$ ，就可以获得 $[1, bit(x_i) - 1]$ ，最终 $sg = bit(x_i)$

---

表达能力较差见谅

### Code

```cpp
#include<bits/stdc++.h>
using namespace std;

typedef pair<int, int> pii;
typedef long long LL;
typedef unsigned long long uLL;
#define mp make_pair

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

int lowbit(int x) {
    return x & -x;
}

int main()
{
    cin.tie(0); cout.tie(0);
    ios::sync_with_stdio(0);

    int T = 1;
    T = read();
    while (T--) {
        int n;
        n = read();
        vector<int> a(n + 5, 0), x(n + 5, 0);
        for (int i = 1; i <= n; i++) a[i] = read();
        for (int i = 1; i <= n; i++) x[i] = read();

        for (int i = 1; i <= n; i++) {
            for (int j = 0; j <= 30; j++)
                if ((x[i] >> j & 1) && (1 << j) > a[i]) x[i] ^= 1 << j;
        }

        auto bit = [&] (int x) {
            int cnt = 0;
            while (x) {
                x -= lowbit(x);
                ++cnt;
            }
            return cnt;
        };
        auto check = [&] (int limit, int num) -> pii {
            int cnta = 0;
            int big = 0;
            int ff = 0, cnt = 0;
            for (int i = 30; i >= 0; i--) {
                if (((num >> i & 1) && !(limit >> i & 1)) && (cnta == 1)) ++big;
                if (limit >> i & 1) ++cnta;
                if ((num >> i & 1) && ff) ++cnt;
                if (!(limit >> i & 1) && (num >> i & 1)) ff = 1;
            }
            return {big, cnt};
        };

        vector<int> sg(n + 5, 0);
        for (int i = 1; i <= n; i++) {
            if (a[i] >= x[i]) {
                sg[i] = bit(x[i]);
            } else {
                auto [big, cnt] = check(a[i], x[i]);
                if (!cnt) {
                    sg[i] = 0;
                    continue;
                }

                if (big & 1) {
                    sg[i] = max(sg[i], big ^ 1);
                    if (bit(x[i]) - 1 - big || big == 1) sg[i] = bit(x[i]);
                } else {
                    sg[i] = max(sg[i], bit(x[i]));
                }
            }
        }

        int ff = 0;
        for (int i = 1; i <= n; i++)
            ff ^= sg[i];
        
        cout << (ff ? "Alice\n" : "Bob\n");
    } 

    return 0;
}
```