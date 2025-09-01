---
title: Codeforces Round 1046 (Div. 1) 1A - 1D2
description: ''
publishDate: 2025-09-01
tags:
  - ACM
  - CF
heroImage: {src: './cover.jpeg', color: '#8ed583ff' }
---
## [A - Against the Difference](https://codeforces.com/contest/2135/problem/A)

令 $dp_i$ 表示在 $1 \sim i$ 中能形成的最长 `neat` 序列的长度，在每一位有两种情况，一种是不保留第 $i$ 位，直接取 $dp_{i - 1}$ ，一种是保留第 $i$ 位，就要取最后 $a_i$ 个 $a_i$ 再加上前面部分的 $dp$ 值 ，开 $n$ 个队列存一下每个数字的出现情况，维护每个数字最后出现的 $a_i$ 个位置

### Code

```cpp
#include<bits/stdc++.h>
using namespace std;

#define int long long

typedef pair<int, int> pii;
typedef long long LL;
typedef unsigned long long uLL;
typedef __int128_t i128;

const int inf32 = 1e9;
const LL inf64 = 1e18;

signed main()
{
    cin.tie(0); cout.tie(0);
    ios::sync_with_stdio(0);

    int T = 1;
    cin >> T;
    while (T--) {
        int n;
        cin >> n;
        vector<int> a(n + 5, 0);
        for (int i = 1; i <= n; i++) cin >> a[i];

        vector<int> dp(n + 5, 0);
        vector<queue<int>> q(n + 5);
        for (int i = 1; i <= n; i++) {
            if (a[i] == 1) {
                dp[i] = dp[i - 1] + 1;
                continue;
            }
            
            dp[i] = dp[i - 1];
            q[a[i]].push(i);
            if (q[a[i]].size() > a[i]) q[a[i]].pop();
            if (q[a[i]].size() >= a[i]) {
                dp[i] = max(dp[q[a[i]].front() - 1] + a[i], dp[i]);
            }
        }

        cout << dp[n] << "\n";
    }

    return 0;
}
```

## [B - For the Champion](https://codeforces.com/contest/2135/problem/B)

假设初始位置在最最右上角，公式就变为 $min(\left|x_i - X\right| + \left|y_i - Y\right|) = min(X - x_i + Y - y_i) = X + Y + \min(-x - y)$  

因为 $-1e9 \leq x_i, y_i \leq 1e9$ ，我们可以通过四次移动将机器人移动到右上角，返回的结果就是 $X + Y + 4k + min(-x - y)$，后面两项都是定值，这样就可以获得 $X + Y$ 的值

同理，当我们在右上角时，可以向下移动四次移动到右下角，$\min(X + 2k - x_i + (y_i - (Y - 2k))) = X - Y + 4k + \min(y_i - x_i)$ ，可以获得 $X - Y$ 的值

初始坐标就是 $\left(\frac{(X + Y) + (X - Y)}{2}, \frac{(X + Y) - (X - Y)}{2}\right)$

### Code

```cpp
#include<bits/stdc++.h>
using namespace std;

#define int long long

typedef pair<int, int> pii;
typedef long long LL;
typedef unsigned long long uLL;
typedef __int128_t i128;

const int inf32 = 1e9;
const LL inf64 = 1e18;

int query(char ch, int x) {
    cout << "? " << ch << ' ' << x << endl;
    int res;
    cin >> res;
    return res;
}

void answer(int x, int y) {
    cout << "! " << x << ' ' << y << endl;
}

signed main()
{
    cin.tie(0); cout.tie(0);
    ios::sync_with_stdio(0);

    int T = 1;
    cin >> T;
    while (T--) {
        int n;
        cin >> n;
        int add = inf64, sub = inf64;
        for (int i = 1; i <= n; i++) {
            int x, y;
            cin >> x >> y;
            add = min(add, -x - y);
            sub = min(sub, y - x);
        }

        int A, B, x;
        query('U', inf32);
        query('U', inf32);
        query('R', inf32);
        x = query('R', inf32);
        A = x - 4 * inf32 - add;
        for (int i = 1; i <= 4; i++) x = query('D', inf32);
        B = x - 4 * inf32 - sub;

        answer((A + B) / 2, (A - B) / 2);
    }

    return 0;
}
```

## [C - By the Assignment](https://codeforces.com/contest/2135/problem/C)

对于经过“桥”的路径，“桥”的端点的权值会出现在这两点间的所有简单路径中，应该关注断开桥后每个连通块的情况

考虑一个环 $a_1, a_2, a_3, ..., a_n$ 的情况，起终点相邻时，应该满足 $a_1 \oplus a_2 = \oplus_{i = 1}^{n}a_i$ ，当起终点间隔一个点时，应该满足 $a_1 \oplus a_2 \oplus a_3 = a_1 \oplus \oplus_{i = 3}^{n}a_i$ ，即相当于对左式异或 $a_3$ ，对右式异或 $a_2$ 后等式仍然成立，即 $a_2 = a_3$ ，可以推出整个环的取值应该完全相同

对于一个奇环，两条路径长度的奇偶性不同，所以取值必须是 $0$ ，对于偶环，两条路径奇偶性相同，所以可以是取值范围内的任何值

断桥后对每个连通块进行 `01` 染色判断时候存在奇环，并记录这个连通块中已有的数字集合，如果集合中数字个数大于等于两个，或者奇环中存在不为 $0$ 的数字，最终结果就是 $0$ ，否则只有当偶环全为 $-1$ 或者奇环长度为 $1$ 且取值为 $-1$ 时对答案乘上 $V$ 

### Code

```cpp
#include<bits/stdc++.h>
using namespace std;

#define int long long

typedef pair<int, int> pii;
typedef long long LL;
typedef unsigned long long uLL;
typedef __int128_t i128;

const int inf32 = 1e9;
const LL inf64 = 1e18;

const int mod = 998244353;
int inv(int c) {
    int a = c, b = mod, x = 1, y = 0;
    while (b) {
        int t = a / b;
        a -= t * b; swap(a, b);
        x -= t * y; swap(x, y);
    }
    x = (x % mod + mod) % mod;
    return x;
}
int add(int a, int b) {
    int c = a + b;
    return c >= mod ? c - mod : c;
}
int sub(int a, int b) {
    return add(a, mod - b);
}
int mul(int a, int b) {
    return (a * b) % mod;
}
int frac(int a, int b) {
    return mul(a, inv(b));
}
int qpow(int x, int a) {
    int res = 1;
    while (a) {
        if (a & 1) res = mul(res, x);
        x = mul(x, x);
        a >>= 1;
    }
    return res;
}

struct EBCC
{
    int n;
    vector<vector<int>> G;
    vector<int> dfn, low, ff, fa;
    int id;

    EBCC() {};
    EBCC(int x) {
        init(x);
    }
    void init(int x) {
        n = x;
        G.assign(n + 5, {});
    }
    void add_edge(int x, int y) {
        G[x].push_back(y);
        G[y].push_back(x);
    }

    void dfs(int cur, int ftr) {
        dfn[cur] = low[cur] = ++id;
        fa[cur] = ftr;
        for (auto v : G[cur]) {
            if (!dfn[v]) {
                dfs(v, cur);
                low[cur] = min(low[cur], low[v]);
                if (low[v] > dfn[cur]) {
                    ff[v] = 1;
                }
            } else {
                if (dfn[v] < dfn[cur] && v != ftr) {
                    low[cur] = min(low[cur], dfn[v]);
                }
            }
        }
    }

    void gets() {
        dfn.assign(n + 5, 0);
        low.assign(n + 5, 0);
        ff.assign(n + 5, 0);
        fa.assign(n + 5, 0);
        id = 0;

        for (int i = 1; i <= n; i++)
            if (!dfn[i]) dfs(i, 0);
    }
};

signed main()
{
    cin.tie(0); cout.tie(0);
    ios::sync_with_stdio(0);

    int T = 1;
    cin >> T;
    while (T--) {
        int n, m, V;
        cin >> n >> m >> V;
        vector<int> a(n + 5, 0);
        vector<pii> e;
        EBCC G(n);
        for (int i = 1; i <= n; i++) cin >> a[i];
        for (int i = 1; i <= m; i++) {
            int x, y;
            cin >> x >> y;
            e.push_back({x, y});
            G.add_edge(x, y), G.add_edge(y, x);
        }
        G.gets();

        set<pii> st;
        vector<vector<int>> TG(n + 5);
        for (int i = 1; i <= n; i++)
            if (G.ff[i]) st.insert({i, G.fa[i]}), st.insert({G.fa[i], i});

        for (auto [x, y] : e) {
            if (st.count({x, y})) continue;
            // cerr << x << ' ' << y << endl;
            TG[x].push_back(y), TG[y].push_back(x);
        }

        vector<int> col(n + 5, 0);
        set<int> stk;
        int ans = 1, ff = 0, cnt = 0, siz = 0;
        function<void(int, int, int)> dfs = [&] (int cur, int ftr, int color) -> void {
            if (a[cur] != -1) stk.insert(a[cur]);
                else ++cnt;
            ++siz;
            col[cur] = color;
            for (auto v : TG[cur]) {
                if (v == ftr) continue;
                if (col[v] == color) ff = 0;
                if (col[v]) continue;
                dfs(v, cur, color ^ 1);
            }
        };
        for (int i = 1; i <= n; i++) {
            if (col[i]) continue;
            stk.clear();
            ff = 1;
            cnt = siz = 0;
            dfs(i, 0, 2);

            if (stk.size() >= 2) {ans = 0; break;}
            if (ff) {
                if (!stk.empty()) 
                    ans = mul(ans, 1);
                else 
                    ans = mul(ans, V);
            } else {
                if (!stk.empty() && *stk.begin()) {ans = 0; break;}
                if (siz == 1 && cnt == 1)
                    ans = mul(ans, V);
                else
                    ans = mul(ans, 1);
            }
        }
        
        cout << ans << "\n";
    }

    return 0;
}
```

## [D1 - From the Unknown (Easy Version)](https://codeforces.com/contest/2135/problem/D1)

先询问 $10^5$ 个 $1$ ，根据返回的行数可以初步确定一个宽度的范围 $[l, r]$ ，然后再询问一次 $[l, l, 1, l, 2, l, 3, ..., l, r - l]$ ，对于范围内的每个宽度返回的行数都不同，可以计算出宽度

### Code

```cpp
#include<bits/stdc++.h>
using namespace std;

#define int long long

typedef pair<int, int> pii;
typedef long long LL;
typedef unsigned long long uLL;
typedef __int128_t i128;

const int inf32 = 1e9;
const LL inf64 = 1e18;

int stdd;

int query(vector<int>& list, int n) {
    cout << "? " << n;
    for (int i = 1; i <= n; i++)
        cout << ' ' << list[i];
    cout << endl;
    int res;
    cin >> res;
    return res;
}

void answer(int x) {
    cout << "! " << x << endl;
}

signed main()
{
    cin.tie(0); cout.tie(0);
    ios::sync_with_stdio(0);

    int T = 1;
    cin >> T;
    while (T--) {
        vector<int> a(100005, 1);
        int x = query(a, 1e5);
        if (x == 1) {
            answer(1e5);
            continue;
        }
        if (x == 100000) {
            answer(1);
            continue;
        }

        int l, r;
        l = (100000 + x - 1) / x;
        r = (100000 + x - 2) / (x - 1) - 1;

        int cnt = 1;
        a[cnt] = l;
        for (int i = l + 1; i <= r; i++) {
            ++cnt;
            a[cnt] = l;
            ++cnt;
            a[cnt] = i - l;
        }

        x = query(a, cnt);
        answer(l + (cnt - x));
    }

    return 0;
}
```

## [D2 - From the Unknown (Hard Version)](https://codeforces.com/contest/2135/problem/D2)

根据 `D1` 的思路，要确定一个范围 $[l, r]$ 需要一次长度为 $2n - 1$ 的询问，如果能将区间长度控制在 $1e4$ 就有可能用相同的思路计算

考虑先进行一次 $0.5e4$ 个 200 的询问，总长度 $1e6$ ，得到的最长区间为 $[91000, 99999]$ ，区间长度约 $0.9e4$ , 当答案大于等于 200 时，直接按 `D1` 的思路计算即可

当答案小于 200，第一次询问返回 0，考虑构造一个仅包含 1 和 2 的询问，宽度从 $1$ 到 $199$ 会返回 199 个不同的结果。如果包含 3 对于 1、2都会返回 0，无法区分。

通过暴力打表得到的一个可行方案如下（

```cpp
t[1] = 1;
cntt = 1;
for (int i = 2; i <= 199; i++) {
    int tmp = i;
    if (tmp & 1) t[++cntt] = 1, tmp ^= 1;
    while (tmp) t[++cntt] = 2, tmp -= 2;
}
for (int i = cntt + 1; i <= 2e4; i++) // x = 15
    t[i] = 2 - ((i / x) & 1);
```

这样剩下的宽度也可以通过一次询问确定

### Code

```cpp
#include<bits/stdc++.h>
using namespace std;

#define int long long

typedef pair<int, int> pii;
typedef long long LL;
typedef unsigned long long uLL;
typedef __int128_t i128;

const int inf32 = 1e9;
const LL inf64 = 1e18;

int stdd, sumQ;

int query(vector<int>& list, int n) {
    // sumQ += n;
    cout << "? " << n;
    for (int i = 1; i <= n; i++)
        cout << ' ' << list[i];
    cout << endl;

    // int l = 1, s = 0;
    // for (int i = 1; i <= n; i++) {
    //     if (list[i] > stdd) return 0;
    //     if (s + list[i] <= stdd) {
    //         s += list[i];
    //     } else {
    //         ++l;
    //         s = list[i];
    //     }
    // }
    // return l;

    int res;
    cin >> res;
    return res;
}

void answer(int x) {
    cout << "! " << x << endl;
    // if (x != stdd) cerr <<"err. "<< x << ' ' << stdd << endl;
    // if (sumQ > 2.5e4) cerr << "too much queries." << endl;
    // assert(x == stdd);
}

vector<int> t(100005, 0);
map<int, int> mp;
int cntt = 0;

void init(int x) {
    // set<int> st;
    // st.insert(0);
    t[1] = 1;
    cntt = 1;
    for (int i = 2; i <= 199; i++) {
        int tmp = i;
        if (tmp & 1) t[++cntt] = 1, tmp ^= 1;
        while (tmp) t[++cntt] = 2, tmp -= 2;
    }
    for (int i = cntt + 1; i <= 2e4; i++)
        t[i] = 2 - ((i / x) & 1);
    cntt = 2e4;
    mp[0] = 1;
    for (int i = 2; i <= 199; i++) {
        int l = 1, s = 0;
        for (int j = 1; j <= cntt; j++) {
            if (s + t[j] <= i) {
                s += t[j];
            } else {
                ++l;
                s = t[j];
            }
        }
        mp[l] = i;
        // st.insert(l);
    }
    // cerr << x << ' ' << st.size() << endl;
}

signed main()
{
    cin.tie(0); cout.tie(0);
    ios::sync_with_stdio(0);

    // for (int i = 1; i <= 200; i++) init(i);
    init(15);
    
    // return 0;

    int T = 1;
    cin >> T;
    while (T--) {
    // for (int tt = 1; tt <= 1e5; tt++) {
        // sumQ = 0;
        // stdd = tt;
        // cin >> stdd;

        vector<int> a(100005, 200);
        int x = query(a, 5e3);
        if (x == 10) {
            answer(1e5);
            continue;
        }

        if (x == 0) {
            int tmp = query(t, cntt);
            answer(mp[tmp]);            
            continue;
        }

        int l, r;
        l = 200 * ((5000 + x - 1) / x);
        r = 200 * ((5000 + x - 2) / (x - 1)) - 1;

        int cnt = 1;
        a[cnt] = l;
        for (int i = l + 1; i <= r; i++) {
            ++cnt;
            a[cnt] = l;
            ++cnt;
            a[cnt] = i - l;
        }

        x = query(a, cnt);
        answer(l + (cnt - x));
    }

    return 0;
}
```

