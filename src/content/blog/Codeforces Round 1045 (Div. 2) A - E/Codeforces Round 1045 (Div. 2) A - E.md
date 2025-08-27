---
title: 'Codeforces Round 1045 (Div. 2) A - E'
publishDate: '2025-08-27'
tags:
  - ACM
  - CF
description: ''
heroImage: {src: './cover.jpeg', color: '#709ee3ff' }
---

## [A - Painting With Two Colors](https://codeforces.com/contest/2134/problem/A)

只能涂一次，所以蓝色必须涂在正中间，红色如果长度比蓝色小可以被蓝色覆盖，否则也必须涂在正中间，检验 $n, a, b$ 的奇偶性即可

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
        int n, a, b;
        cin >> n >> a >> b;

        if ((n & 1) != (b & 1)) {
            cout << "NO\n";
            continue;
        }

        cout << ((n & 1) == (a & 1) || a <= b ? "YES\n" : "NO\n");
    }

    return 0;
}
```

## [B - Add 0 or K](https://codeforces.com/contest/2134/problem/B)

取 $k + 1$ 作为最终的 `gcd` ，即最终每个数模 $k + 1$ 的结果都应该是 0

因为 $k \equiv -1\ (mod\ (k + 1))$  ，对每个数增加 $a_i\ \%\ (k + 1)$ 个 $k$ 就可以让结果变成 $0$ ，最多增加 $k$ 次，符合题目要求

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
        vector<int> a(n + 5, 0);
        for (int i = 1; i <= n; i++) cin >> a[i];

        int std = k + 1;
        for (int i = 1; i <= n; i++) {
            int tmp = a[i] % std;
            a[i] += k * tmp;
        }

        for (int i = 1; i <= n; i++)
            cout << a[i] << ' ';
        cout << "\n";
    }

    return 0;
}
```

## [C - Even Larger](https://codeforces.com/contest/2134/problem/C)

考虑长度为 3 的子数组，如果取 `奇偶奇` 的位置，那么每个偶数位置的数必须大于等于相邻的两个奇数的和，在这种情况下所有子数组都是符合条件的

假设某一个奇数位置已经能满足前面的偶数的需求，如果后面的偶数还不满足要求，这个时候减少下一个奇数位置肯定是更优的（除非不够减了），贪心扫一遍即可

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

        if (!(n & 1)) ++n;

        int ans = 0;
        if (a[1] > a[2]) ans += a[1] - a[2], a[1] = a[2];
        for (int i = 3; i <= n; i += 2) {
            int tmp = max(0ll, a[i] + a[i - 2] - a[i - 1]);
            ans += tmp;
            a[i] = max(0ll, a[i] - tmp);
        }

        cout << ans << "\n";
    }

    return 0;
}
```

## [D - Sliding Tree](https://codeforces.com/contest/2134/problem/D)

一次操作是将 b 和 c 上的所有子树转移到 c 上，当只有一棵子树时，可以一直往后移动而不需要进行操作，直到遇到下一个岔路口，所以我们肯定希望最后的子树尽可能长，每次操作将这个最长子树接到岔路口的其他节点上。假设起始操作 a, b 及前面的部分是一条链，我们的操作数就是 $n$ 减去这条链的长度和那个最长子树的深度，这部分最长就是树的直径

所以我们从直径的一段开始遍历直径，直到碰到某个岔路口 $x$ ，$x$ 前一个点、 $x$ 和任意一个岔路口的点就是第一次操作

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

        int cnt1 = 0, cnt2 = 0;
        for (int i = 1; i <= n; i++)
            if (G[i].size() == 1) ++cnt1;
                else if (G[i].size() == 2) ++cnt2;
            
        if (n == 1 || (cnt1 == 2 && cnt1 + cnt2 == n)) {
            cout << "-1\n";
            continue;
        }

        int rt1 = 0, maxDep = 0, rt2 = 0;
        vector<int> fa(n + 5, 0);
        function<void(int, int, int)> dfs = [&] (int cur, int ftr, int depth) -> void {
            fa[cur] = ftr;
            if (depth > maxDep) maxDep = depth, rt1 = cur;
            for (auto v : G[cur]) {
                if (v == ftr) continue;
                dfs(v, cur, depth + 1);
            }
        };
        dfs(1, 0, 1);
        rt2 = rt1, maxDep = 0;
        dfs(rt2, 0, 1);

        int ftr = 0, x = rt1;
        vector<int> ff(n + 5, 0);
        while (x) ff[x] = 1, x = fa[x];
        while (G[rt1].size() <= 2) {
            ftr = rt1;
            rt1 = fa[rt1];
        }

        for (auto v : G[rt1]) {
            if (!ff[v]) {
                cout << ftr << ' ' << rt1 << ' ' << v << "\n";
                break;
            }
        }
    }

    return 0;
}
```

## [E - Power Boxes](https://codeforces.com/contest/2134/problem/E)

题目要求操作不超过 $\lceil \frac{3n}{2} \rceil$ ，可以初步判断为每三次询问确定两个数

倒数第二个位置是可以通过一次 `throw` 就判断出来的，跳了两次这个位置就是 1 ，只跳一次就是 2

我们先将数两两分一组，最后会剩下两个或者三个，先通过倒数第二个位置判断出这两个或者三个数的值。再从我们分出的最后一组开始倒序遍历。假设这一组的位置是 $i - 1$ 和 $i$ ，令 $dis_i$ 表示向第 $i$ 位扔球球跳的次数，这里看 $dis_{i + 1}$ 和 $dis_{i + 2}$ 的大小关系：

如果 $dis_{i + 1} \neq dis_{i + 2}$ ，通过询问第 $i$ 位，就可以判断这一位的大小，然后把 $i - 1$ 换过来再询问一次

如果 $dis_{i + 1} = dis_{i + 2}$ ，先询问 $i - 1$ ，再交换 $i - 1$ 和 $i$ ，再次询问 $i - 1$ ，假设后两位的 $dis$ 值都为 $x$

`1 1 x x` ： $x + 2, x + 2$

`2 2 x x` ： $x + 1, x + 1$

`1 2 x x` ： $x + 2, x + 1$

`2 1 x x` ： $x + 1, x + 2$

可以看到，所有的四种情况返回的两个值互不相同，就可以确定这两位的大小

然后按照当前的数维护这两个位置的 $dis$ 数组，继续向前遍历即可

无论哪种情况，都是三次操作确定两个位置，加上最开始询问的最后的几个位置（如果 $n$ 为偶数，就是三次确定两个位置，如果 $n$ 为奇数，就是五次确定三个位置（$\lceil \frac{3\times3}{2}\rceil = 5$）），正好符合操作次数要求。

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

int th(int x) {
    cout << "throw " << x << endl;
    int res;
    cin >> res;
    return res;
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
        vector<int> pos(n + 5, 0), ans(n + 5, 0);
        for (int i = 1; i <= n; i++) pos[i] = i;
        
        auto sw = [&] (int x) -> void {
            swap(pos[x], pos[x + 1]);
            cout << "swap " << x << endl;
        };

        int now = n;
        if (n & 1) {
            ans[pos[n - 1]] = 3 - th(n - 1);
            sw(n - 1);
            ans[pos[n - 1]] = 3 - th(n - 1);
            sw(n - 2);
            ans[pos[n - 1]] = 3 - th(n - 1);
            now = n - 3;
        } else {
            ans[pos[n - 1]] = 3 - th(n - 1);
            sw(n - 1);
            ans[pos[n - 1]] = 3 - th(n - 1);
            now = n - 2;
        }

        vector<int> dis(n + 5, 0);
        for (int i = n; i > now; i--) {
            int x = ans[pos[i]];
            dis[i] = dis[i + x] + 1;
        }

        for (int i = now; i >= 1; i -= 2) {
            int x = dis[i + 1], y = dis[i + 2];
            int q1, q2;
            if (x != y) {
                q2 = th(i);
                if (q2 - 1 == x) ans[pos[i]] = 1;
                    else ans[pos[i]] = 2;
                sw(i - 1);
                q1 = th(i);
                if (q1 - 1 == x) ans[pos[i]] = 1;
                    else ans[pos[i]] = 2;
            } else {
                q1 = th(i - 1);
                sw(i -1 );
                q2 = th(i - 1);

                if (q1 == x + 2 && q2 == x + 2) {
                    ans[pos[i]] = ans[pos[i - 1]] = 1;
                } else if (q1 == x + 1 && q2 == x + 1) {
                    ans[pos[i]] = ans[pos[i - 1]] = 2;
                } else if (q1 == x + 2 && q2 == x + 1) {
                    ans[pos[i]] = 1;
                    ans[pos[i - 1]] = 2;
                } else { // q1 == x + 1 && q2 == x + 2
                    ans[pos[i]] = 2;
                    ans[pos[i - 1]] = 1;
                }
            }

            x = ans[pos[i]];
            dis[i] = dis[i + x] + 1;
            x = ans[pos[i - 1]];
            dis[i - 1] = dis[i - 1 + x] + 1;
        }

        cout << "! ";
        for (int i = 1; i <= n; i++)
            cout << ans[i] << ' ';
        cout << endl;
    }

    return 0;
}
```
