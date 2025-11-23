---
title: 'Codeforces Round 1042 (Div. 3) A - G'
description: ''
publishDate: 2025-08-17
tags:
  - ACM
  - CF
---
<!-- more -->

## [A - Lever](https://codeforces.com/contest/2131/problem/A)

答案与第二步无关，为第一步的次数 + 1

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
        vector<int> a(n + 5, 0), b(n + 5, 0);
        for (int i = 1; i <= n; i++) cin >> a[i];
        for (int i = 1; i <= n; i++) cin >> b[i];

        int x = 0;
        for (int i = 1; i <= n; i++)
            if (a[i] > b[i]) x += a[i] - b[i];

        cout << x + 1 << "\n";
    }

    return 0;
}
```

## [B - Alternating Series](https://codeforces.com/contest/2131/problem/B)

要使字典序最小的话，一定是 `-1 3 -1 3` 这样的循环，才能保证任何一个正数与左右形成一个长度为 3 的区间时和为正数

当 $n$ 为偶数时，以正数结尾，所以只需要是 2

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

        vector<int> ans(n + 5, 0);
        int cnt = (n + 1) / 2;
        for (int i = 1; i <= n; i += 2) ans[i] = -1;
        for (int i = 2; i <= n; i += 2) ans[i] = 3;
        if (n % 2 == 0) ans[n] = 2;

        for (int i = 1; i <= n; i++)
            cout << ans[i] << ' ';
        cout << "\n";
    }

    return 0;
}
```

## [C - Make it Equal](https://codeforces.com/contest/2131/problem/C)

操作的效果只有三种：

- 让 $x$ 增加 $k$
- 让 $x$ 减少 $k$
- 令 $x + y = k (x, y > 0)$ 让 $x$ 与 $y$ 互相转换

所以我们可以将 $a$ 先全部变成小于 $k$ 的数，然后将 $x, y$ 两两一组，比较每组数量是否不同

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
        int n, k;
        cin >> n >> k;
        vector<int> a(n + 5, 0), b(n + 5, 0);
        for (int i = 1; i <= n; i++) cin >> a[i];
        for (int i = 1; i <= n; i++) cin >> b[i];

        map<int, int> s, t;
        for (int i = 1; i <= n; i++) {
            int x = a[i] % k;
            if (x > k - x) x = k - x;
            s[x] += 1;
            x = b[i] % k;
            if (x > k - x) x = k - x;
            t[x] += 1;
        }

        int ff = 1;
        for (auto [x, y] : s)
            if (y != t[x]) {ff = 0; break;}
        cout << (ff ? "YES\n" : "NO\n");
    }

    return 0;
}
```

## [D - Arboris Contractio](https://codeforces.com/contest/2131/problem/D)

假设所有的叶子节点离我们选定的根的距离都大于等于 2 ，对每一条叶子到根的路径我们都需要进行一次操作，此时答案为叶节点的个数

所以我们需要选择一个与叶子节点直接相连的最多的点作为根节点，假设这个点直接连接了 $x$ 个叶节点，答案为叶节点的个数减去 $x$

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
        vector<vector<int>> G(n + 5);
        for (int i = 1; i < n; i++) {
            int x, y;
            cin >> x >> y;
            G[x].push_back(y), G[y].push_back(x);
        }
        if (n <= 3) {
            cout << "0\n";
            continue;
        }
        int root = 0;
        for (int i = 1; i <= n; i++)
            if (G[i].size() > 1) {root = i; break;}

        vector<int> leaf(n + 5, 0);
        int mx = 0, tot = 0;
        function<void(int, int)> dfs = [&] (int cur, int ftr) -> void {
            if (G[cur].size() == 1) leaf[cur] = 1, ++tot;
            int tmp = 0;
            for (auto v : G[cur]) {
                if (v == ftr) continue;
                dfs(v, cur);
                if (leaf[v]) ++tmp;
            }
            mx = max(mx, tmp);
        };
        dfs(root, 0);

        cout << tot - mx << "\n";
    }

    return 0;
}
```

## [E - Adjacent XOR](https://codeforces.com/contest/2131/problem/E)

假设要让 $a_i = b_i$ 需要连续异或到的最近的位置为 $f_i$ ，可以发现对于 $[i + 1, f_i - 1]$ 这个范围的所有 $f_j$ 都应该满足 $f_i = f_j$ 

先在 `map<int, set<int>>` 中储存对应前缀异或和 $v$ 的位置，对于第 $i$ 个位置，我们需要的异或和为 $b_i \oplus v_{i - 1}$ ，如果在对应 `set` 中查出来的下一个位置与前面记录的 $f$ 值不相等则输出 NO，当我们到 $f$ 这个位置时清空之前记录的 $f$ 值

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
        vector<int> a(n + 5, 0), b(n + 5, 0);
        for (int i = 1; i <= n; i++) cin >> a[i];
        for (int i = 1; i <= n; i++) cin >> b[i];

        if (a[n] != b[n]) {
            cout << "NO\n";
            continue;
        }

        map<int, set<int>> mp;
        vector<int> v(n + 5, 0);
        for (int i = 1; i <= n; i++) {
            v[i] = v[i - 1] ^ a[i];
            mp[v[i]].insert(i);
        }

        int ff = 1, lst = 0;
        for (int i = 1; i < n; i++) {
            if (i >= lst) lst = 0;
            int ned = b[i] ^ v[i - 1];
            auto it = mp[ned].lower_bound(i);
            if (it == mp[ned].end() || (lst && lst != *it)) {
                ff = 0;
                break;
            } 
            lst = *it;
        }

        cout << (ff ? "YES\n" : "NO\n");
    }

    return 0;
}
```

## [F - Unjust Binary Life](https://codeforces.com/contest/2131/problem/F)

画一下生成的表格，可以发现如果我们要到达 $(x, y)$ ，那么 $a[1...x]$ 和 $b[1...y]$ 应该完全相等，所以 $(x, y)$ 的贡献应该是 $a[1...x]$ 和 $b[1...y]$ 中 1 和 0 的个数较小的那一个

我们先对每个 $a_i$ 计算 0 和 1 的个数，在 $cnt0 - cnt1$ 处记录三个信息的前缀和。一个是个数，一个是 $cnt0$ ，一个是 $cnt1$

然后再对每个 $b_i$ 计算 0 和 1 的个数，在我们记录的信息中，小于  $cnt1 - cnt0$ 的部分，说明这些行中 1 的数量更多，所以应该计算 0 的和，即为

$num[-n...cnt1 - cnt0] \times cnt0 + sum0[-n...cnt1 - cnt0]$

大于等于的部分同理，应该计算 1 的和。

算式的前半部分计算的是对列需要进行的操作数，即这一列中 0 个数较少的点的个数乘上 $b[1...i]$ 中 0 的个数（让 $b[1...i]$ 全为 1 的操作数）

后半部分则是对 $a$ 需要进行的操作数，对每个点 $j$ 需要的操作数就是 $a[1...j]$ 中 0 的个数

查询过程中不需要修改，用查分数组即可，下意识 `Fenwick` 结果T了（

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
        string a, b;
        cin >> a;
        cin >> b;

        vector<int> cnt(n * 2 + 5, 0), sum0(n * 2 + 5, 0), sum1(n * 2 + 5, 0);
        int ans = 0;
        int cnt0 = 0, cnt1 = 0;
        for (int i = 0; i < n; i++) {
            if (a[i] & 1) ++cnt1;
                else ++cnt0;

            cnt[cnt1 - cnt0 + n] += 1;
            sum0[cnt1 - cnt0 + n] += cnt0;
            sum1[cnt1 - cnt0 + n] += cnt1;
        }
        for (int i = 1; i <= 2 * n; i++)
            cnt[i] += cnt[i - 1], sum0[i] += sum0[i - 1], sum1[i] += sum1[i - 1];

        cnt0 = cnt1 = 0;
        for (int i = 0; i < n; i++) {
            if (b[i] & 1) ++cnt1;
                else ++cnt0;
            int x = cnt0 - cnt1 + n;
            ans += cnt[x] * cnt1 + sum1[x];
            ans += (n - cnt[x]) * cnt0 + (sum0[2 * n] - sum0[x]);
        }

        cout << ans << "\n";
    }

    return 0;
}
```

## [G - Sea, You & copriMe](https://codeforces.com/contest/2131/problem/H)

令 $power_i$ 表示完全消掉 $i$ 需要的次数，$t_i$ 表示完全消掉 $i$ 产生的贡献，则

$$power_i = i + \sum_{j = 1}^{i - 1}power_j\ \ \ \ \ t_i = i \times \prod_{j = 1}^{i - 1}t_j$$

因为 $k \leq 10^9$ ，所以算到 30 即可

然后我们从小到大判断能不能完全消掉 $a_i$ ，并记录其贡献，遇到第一个不能完全消掉的就递归计算

令 $dfs(x, num)$ 表示当前在消的数为 $x$ ，还能消除 $num$ 次，边界条件就是 $num = 0$ 和 $num = 1$

然后先用一次将 $x$ 拆成 $1, ..., x - 1$ ，然后再循环找第一个不能完全消掉的数

每次循环至少会让 $k$ 减少两位 （二进制下），然后单个循环次数不会超过 30 次，复杂度大概是 $O(30logk)$ （其实不太会证

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

const int mod = 1e9 + 7;
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

const int maxn = 45;

int t[maxn], power[maxn], sumt[maxn];

void init(int n) {
    t[1] = power[1] = sumt[1] = 1;
    for (int i = 2; i <= n; i++) {
        t[i] = mul(sumt[i - 1], i);
        sumt[i] = mul(sumt[i - 1], t[i]);
        power[i] = power[i - 1] * 2;
    }
}

map<pii, int> mp;

signed main()
{
    cin.tie(0); cout.tie(0);
    ios::sync_with_stdio(0);

    init(40);

    int T = 1;
    cin >> T;
    while (T--) {
        int n, k;
        cin >> n >> k;
        vector<int> a(n + 5, 0);
        for (int i = 1; i <= n; i++) cin >> a[i];
        sort(a.begin() + 1, a.begin() + 1 + n);

        int st = 0, ans = 1;
        for (int i = 1; i <= n; i++) {
            if (a[i] <= 40 && k >= power[a[i]]) {
                ans = mul(ans, t[a[i]]);
                k -= power[a[i]];
                continue;
            }
            st = a[i];
            break;
        }

        if (!st) {
            cout << ans << "\n";
            continue;
        }

        function<int(int, int)> dfs = [&] (int x, int num) -> int {
            int tmp = num;
            if (num == 0) return 1;
            if (num == 1) {
                return x;
            }
            if (mp.count({x, num})) return mp[{x, num}];
            int res = x, st = 0;
            num -= 1;
            for (int i = 1; i < x; i++) {
                if (num >= power[i]) {
                    num -= power[i];
                    res = mul(res, t[i]);
                    continue;
                }
                st = i;
                break;
            }
            return mp[{x, tmp}] = mul(res, dfs(st, num));
        };

        cout << mul(ans, dfs(st, k)) << "\n";
    }

    return 0;
}
```



