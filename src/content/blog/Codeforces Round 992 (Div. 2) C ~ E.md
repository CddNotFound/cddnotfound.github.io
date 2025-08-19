---
title: 'Codeforces Round 992 (Div. 2) C - E'
description: ''
publishDate: 2024-12-09
tags:
  - ACM
  - CF
---
<!-- more -->
嗯 很无聊的CDE

## [C - Ordered Permutations](https://codeforces.com/contest/2040/problem/C)

不难注意到要使 $S(P)$ 最大可行的生产方法一定是按顺序从边缘往中间放数

这样一共有 $2 ^ {n - 1}$ 个排列

在这种排列方法下，有 $2 ^ {n - 2}$ 个开头为 1 ，$2^{n - 3}$ 个开头为 2 ...

那么根据 k 的大小计算首位的数字，将跳过的全部按顺序填到后面去。然后就转化为一个子问题，用相同方法构造即可

### Code
```cpp
#include<bits/stdc++.h>
using namespace std;

#define int long long

typedef pair<int, int> pii;
typedef long long LL;
typedef unsigned long long uLL;

const int inf32 = 1e9;
const LL inf64 = 1e18;

int qpow(__int128_t x, __int128_t a) {
    int res = 1;
    while (a) {
        if (a & 1) res *= x;
        x *= x;
        a >>= 1;
    }
    return res;
}

signed main()
{
    cin.tie(0); cout.tie(0);
    ios::sync_with_stdio(0);

    int T = 1;
    cin >> T;
    while (T--) {
        int n, k;
        cin >> n >> k;

        auto check = [&] (__int128_t x, __int128_t a, __int128_t std) -> int {
            __int128_t res = 1;
            while (a) {
                if (a & 1) res *= x;
                x *= x;
                a >>= 1;
                if (a && res * x >= std) return 1;
            }
            return res >= std;
        };

        if (!check(2, n - 1, k)) {
            cout << "-1\n";
            continue;
        }

        vector<int> ans(n + 5, 0), vis(n + 5, 0);
        set<int> st;
        int now = 1;

        for (int i = 1; i <= n; i++) {

            while (n - now - 1 >= 0 && k >= 2 && !check(2, n - now - 1, k)) {
                k -= qpow(2, n - now - 1);
                ++now;
            }
            ans[i] = now;
            vis[now] = 1;
            ++now;
            if (now > n) break;
        }
        int rt = n;
        for (int i = 1; i <= n; i++)
            if (!vis[i]) ans[rt--] = i;

        for (int i = 1; i <= n; i++) 
            cout << ans[i] << ' ';
        cout << "\n";
    }

    return 0;
}
```

## [D - Non Prime Tree](https://codeforces.com/contest/2040/problem/D)

题目提示范围是 1 到 2n，并且除了 2 所有偶数均为合数，那么不难想到尝试全用偶数去构造

如果当我们到了某个点，发现找不到与父节点的值 x 的差大于等于 4 的数了，这个时候说明剩下的数一定是 x + 2 或\和 x - 2 ，那么考虑填入 x - 1 和 x + 1即可。因为此时只剩最多两个位置没有填，所以一定有解

### Code

```cpp
#include<bits/stdc++.h>
using namespace std;

#define int long long

typedef pair<int, int> pii;
typedef long long LL;
typedef unsigned long long uLL;

const int inf32 = 1e9;
const LL inf64 = 1e18;

const int maxn = 1e7 + 5;

signed main()
{
    cin.tie(0); cout.tie(0);
    ios::sync_with_stdio(0);

    int T = 1;
    cin >> T;
    while (T--) {
        int n;
        cin >> n;
        vector<vector<int>> G(n + 5);
        for (int i = 1; i < n; i++) {
            int x, y;
            cin >> x >> y;
            G[x].push_back(y), G[y].push_back(x);
        }

        set<int> st[2];
        for (int i = 1; i <= 2 * n; i++)
            if (i & 1) st[1].insert(i);
                else st[0].insert(i);
        vector<int> ans(n + 5, 0);

        function<void(int, int)> dfs = [&] (int cur, int ftr) -> void {
            int ff = 1, typ = ans[ftr] & 1;
            for (auto x : st[typ]) {
                if (abs(x - ans[ftr]) >= 4) {
                    ans[cur] = x;
                    st[typ].erase(x);
                    ff = 0;
                    break;
                }
            }
            if (ff) {
                if (st[typ ^ 1].count(ans[ftr] - 1)) ans[cur] = ans[ftr] - 1, st[typ ^ 1].erase(ans[ftr] - 1);
                    else if (st[typ ^ 1].count(ans[ftr] + 1)) ans[cur] = ans[ftr] + 1, st[typ ^ 1].erase(ans[ftr] + 1);
            }

            for (auto v : G[cur]) {
                if (v == ftr) continue;
                dfs(v, cur);
            }
        };
        ans[0] = -2;
        dfs(1, 0);
        
        for (int i = 1; i <= n; i++)
            cout << ans[i] << ' ';
        cout << "\n";
    }   

    return 0;
}
```

## [E - Control of Randomness](https://codeforces.com/contest/2040/problem/E)

首先因为是随机移动，期望下会向每条边都移动一次，那么多余的移动次数就是 $2 * (G[cur].size() - 1)$ ，对于每个查询，扫到根找出偶数步经过的每个点的额外移动次数，用金币跳过前 p 大的，对后面的求和即可

把树按深度奇偶性分成两棵树可以用树剖做到 $O(nlogn)$ ，但是看范围 n2 能过懒得写了（

### Code
```cpp
#include<bits/stdc++.h>
using namespace std;

const int P = 998244353;
template<const int mod>
struct ModInt {
    static const int P = mod;
    int x;
    ModInt (int x = 0) : x(x % P) {}
    ModInt (long long x) : x(int(x % P)) {}
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

    ModInt pow(long long n) const {
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
        if (x < 0) x += P;
        return x;
    }
};
using mint = ModInt<P>;

#define int long long

typedef pair<int, int> pii;
typedef long long LL;
typedef unsigned long long uLL;

const int inf32 = 1e9;
const LL inf64 = 1e18;

signed main()
{
    cin.tie(0); cout.tie(0);
    ios::sync_with_stdio(0);

    int T = 1;
    cin >> T;
    while (T--) {
        int n, Q;
        cin >> n >> Q;
        vector<vector<int>> G(n + 5);
        for (int i = 1; i < n; i++) {
            int x, y;
            cin >> x >> y;
            G[x].push_back(y), G[y].push_back(x);
        }

        vector<int> dep(n + 5, 0);
        vector<mint> cost(n + 5, 0);
        vector<array<int, 20>> f(n + 5);
        function<void(int, int, int)> dfs = [&] (int cur, int ftr, int depth) -> void {
            dep[cur] = depth; f[cur][0] = ftr;
            for (auto v : G[cur]) {
                if (v == ftr) continue;
                dfs(v, cur, depth + 1);
            }
        };
        dfs(1, 0, 0);
        f[1][0] = 1;

        while (Q--) {
            int x, y;
            cin >> x >> y;

            mint ans = dep[x];
            vector<int> p;
            x = f[x][0];
            while (x != 1) {
                p.push_back((G[x].size() - 1) * 2);
                x = f[x][0];
                x = f[x][0];
            }
            sort(p.begin(), p.end(), greater());
            for (int i = y; i < p.size(); i++)
                ans += p[i];
            cout << ans << "\n";
        }

    }

    return 0;
}
```