---
title: 'Codeforces Round 1023 (Div. 2) A - F1, F2(Fake)'
description: ''
publishDate: 2025-05-07
tags:
  - ACM
  - CF
---
<!-- more -->

## [A - LRC and VIP](https://codeforces.com/contest/2107/problem/A)

如果全相同无解

否则最大数放在一组，剩下的放在另一组一定不相等

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

        int ff = 0, mx = a[1];
        for (int i = 2; i <= n; i++) {
            if (a[i] != a[1]) ff = 1;
            mx = max(mx, a[i]);
        }

        if (!ff) {
            cout << "No\n";
            continue;
        }

        cout << "Yes\n";
        for (int i = 1; i <= n; i++) {
            if (a[i] == mx) {
                cout << "2 ";
                mx = -1;
            } else {
                cout << "1 ";
            }
        }
        cout << "\n";
    }

    return 0;
}
```

## [B - Apples in Boxes](https://codeforces.com/contest/2107/problem/B)

如果 Tom 取完第一颗石头无法满足约束条件则 Jerry 胜

否则二人一定是轮流取直到全部取完，根据石头总数奇偶判断

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
        int tot = 0, mx = 0;
        for (int i = 1; i <= n; i++) cin >> a[i], tot += a[i], mx = max(mx, a[i]);
        
        for (int i = 1; i <= n; i++)
            if (a[i] == mx) {
                --a[i];
                break;
            }

        int mn = inf32;
        mx = 0;
        for (int i = 1; i <= n; i++) mx = max(mx, a[i]), mn = min(mn, a[i]);
        if (mx - mn > k) {
            cout << "Jerry\n";
            continue;
        }

        cout << (tot & 1 ? "Tom\n" : "Jerry\n");
    }

    return 0;
}
```

## [C - Maximum Subarray Sum](https://codeforces.com/contest/2107/problem/C)

我们发现 $k \geq 1$ ，令所有不确定的数值均为 $-\infty$ ，此时如果最大子数组和超过了 $k$ 则无解

此时说明每一块的大小均未到达 $k$

我们选择一个不确定的位置进行二分，将剩下的置为 $-\infty$ 

这里二分是正确的，假设选择位置左边的和为 $x$ ，右边的和为 $y$ ，中间不属于最大子数组的部分为 $z$。当 $z < -min(x, y)$ 时，这部分的贡献为 $max(x, y) < k$ ，当 $z  \geq -min(x, y)$ 时，这部分的贡献为 $x + y + z$ ，是一个不减的连续函数

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
        string tag;
        cin >> tag; tag = " " + tag;
        vector<int> a(n + 5, 0);
        for (int i = 1; i <= n; i++) cin >> a[i];

        int mx = 0, tmp = 0;
        for (int i = 1; i <= n; i++) {
            tmp += a[i];
            if (tag[i] == '0') tmp = 0;
            if (tmp < 0) tmp = 0;
            mx = max(mx, tmp);
        }

        int ff = 0;
        for (int i = 1; i <= n; i++)
            if (tag[i] == '0') {
                ff = i;
                break;
            }
        
        if ((!ff && mx != k) || mx > k) {
            cout << "No\n";
            continue;
        }

        for (int i = 1; i <= n; i++)
        if (tag[i] == '0' && i != ff) {
            a[i] = -inf64;
        }
        
        int l = -inf64, r = inf64, ans = 0;
        auto check = [&] (int x) -> int {
            i128 tot = 0, res = 0;
            a[ff] = x;
            for (int i = 1; i <= n; i++) {
                tot += a[i];
                tot = max((i128)0, tot);
                res = max(res, tot);
            }
            if (res > inf64) res = inf64;
            if (res < -inf64) res = -inf64;
            return res;
        };
        while (l <= r) {
            int mid = (l + r) >> 1;
            int s = check(mid);
            if (s > k) r = mid - 1;
            if (s < k) l = mid + 1;
            if (s == k) {ans = mid; break;}
        }
        // a[ff] = ans;
        
        cout << "Yes\n";
        for (int i = 1; i <= n; i++)
            cout << a[i] << ' ';
        cout << "\n";
    }

    return 0;
}
```

## [D - Apple Tree Traversing](https://codeforces.com/contest/2107/problem/D)



我们从 1 到 n 遍历，如果当前点已经被摘掉了就 continue，否则就一直寻找当前点所在树的直径进行切割。最后对所有三元组排序即可

感觉复杂度是对的但是不会证明（

---

证明（来源题解）：

**性质1** ：如果树有多条直径，那么这些直径至少有一个公共点，所以切割后形成的森林的直径一定是小于当前切掉的这个直径的

**性质2**：对于一个序列 $a$ ，若满足 $a_1 + a_2 + ... + a_k \leq n$ 且对于任意 $1 \leq i < j \leq k$ 有 $a_i < a_j$ ，则 $k$ 的复杂度为 $O(\sqrt{n})$

所以我们最多会切割 $\sqrt{n}$ 次，时间复杂度 $O(n\sqrt{n})$

事实上每次只在子树而不是整个森林中寻找直径，复杂度会远远小于 $O(n\sqrt{n})$

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

struct node {
    int cnt, u, v;
};

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

        vector<int> tag(n + 5, 0), fa(n + 5, 0), dep(n + 5, 0);
        queue<int> q;
        vector<node> ans;
        for (int i = 1; i <= n; i++) q.push(i);

        int id = 0;
        function<void(int, int, int)> dfs = [&] (int cur, int ftr, int depth) -> void {
            fa[cur] = ftr, dep[cur] = depth;
            for (auto v : G[cur]) {
                if (v == ftr || tag[v]) continue;
                dfs(v, cur, depth + 1);
            }
            if (dep[cur] > dep[id] || (dep[cur] == dep[id] && cur > id)) id = cur;
        };
        auto gets = [&] (int rt) -> void {
            id = 0;
            dfs(rt, 0, 1);
            rt = id;
            id = 0;
            dfs(rt, 0, 1);
            ans.push_back({dep[id], max(id, rt), min(id, rt)});
            tag[id] = 1;
            while (id != rt) {
                id = fa[id];
                tag[id] = 1;
            }
        };
        while (!q.empty()) {
            auto x = q.front();
            if (tag[x]) {
                q.pop();
                continue;
            }

            gets(x);
        }

        ranges::sort(ans, [&](node x, node y){
            if (x.cnt != y.cnt) return x.cnt > y.cnt;
            if (x.u != y.u) return x.u > y.u;
            return x.v > y.v;
        });

        for (auto [cnt, u, v] : ans)
            cout << cnt << ' ' << u << ' ' << v << ' ';
        cout << "\n";
    }

    return 0;
}
```

## [E - Ain and Apple Tree](https://codeforces.com/contest/2107/problem/E)

不难发现一棵树权重最大的情况是一条链，权重为 $C(n, 3)$，最小情况是菊花图，权重为 $0$

考虑从 $n$ （n为叶子）开始将节点向上移动，假设将边 $(n - 1, n)$ 变为 $(x, n)$ ，那么树的权重会减少 $\sum_{i = 1}^{n - x - 1}i$，也就是 $C(n - x, 2)$。就可以通过贪心从 $n$ 开始依次移动，每次二分找到移动的最大距离。

可以保证每次移动经过的点一定是一条链，不会出现上一个点连到了 $x$ ，这个点连到了 $x$ 的祖先

若当前点不是叶子，则说明上一个点一步也没有动，也就是说我们需要减少的权重已经小于 $C(2, 2) = 1$ ，此时应该已经 break，所以不会出现这种情况

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

        int mn = 0, mx = 0;
        for (int i = 1; i < n; i++) mx += i * (n - i - 1);

        if (k < mn - 1 || k > mx + 1) {
            cout << "No\n";
            continue;
        }

        vector<int> fa(n + 5, 0);
        for (int i = 1; i <= n; i++) fa[i] = i - 1;

        auto C2 = [&] (int a) -> int {
            return a * (a - 1) / 2;
        };
        int del = mx - k;
        for (int i = n; i; i--) {
            if (abs(del) <= 1) break;
            int l = 2, r = n, tag = 2;
            while (l <= r) {
                int mid = (l + r) >> 1;
                if (C2(mid) <= del + 1 && mid < i) l = mid + 1, tag = mid;
                    else r = mid - 1;
            }
            fa[i] = i - tag;
            del -= C2(tag);
        }


        cout << "Yes\n";
        for (int i = 2; i <= n; i++)
            cout << fa[i] << ' ' << i << "\n";
    }

    return 0;
}
```

## [F1 - Cycling (Easy Version)](https://codeforces.com/contest/2107/problem/F1)

首先若我们遇到了一个更小的节点 $x$ ，之后的每次移动最多耗费 $x + 1$

如果到了某一步要将前面更小的节点移动过来，将交换的花费均摊到每一个节点上，每次移动的花费会变为 $x, x + 2, x + 2, x + 2, ..., x + 2$ （ $x$ 是要交换过来的节点）

所以从 $n$ 倒序遍历要移动的节点，若这样移动后花费更少，则进行交换并修改数组

数组的和就是答案

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

        for (int i = n; i; i--)
            for (int j = i - 1; j; j--)
                a[j] = min(a[j], a[i] + 1);

        for (int i = n; i; i--) {
            int add = 0, del = 0, ed = 0;
            for (int j = i + 1; j <= n; j++) {
                add += a[i] + 2, del += a[j];
                if (del >= add) ed = j;
            }
            if (!ed) continue;
            for (int j = i + 1; j <= ed; j++) a[j] = a[i] + 2;
        }
        
        int ans = 0;
        for (int i = 1; i <= n; i++) ans += a[i];

        cout << ans << "\n";
    }

    return 0;
}
```

## [F2 - Cycling (Hard Version)](https://codeforces.com/contest/2107/problem/F2)

做法假了，hack数据为

```
1
5
1 1 2 3 5

1 2 4 7 11
```

当二分条件的 $tmp \leq i$ 改为 $tmp < i$ 时，结果理应不变，但是第五个数输出为 $12$

最后加了一个 50 次的循环偷鸡偷过去了（

---

说说思路，维护一个单调栈，因为交换会把每个节点的花费变为 $x + 2$，那么这个单调栈一定是连续的，因为差大于等于 2 时，用前面的节点刷过去一定不会更劣。那么要交换一个节点 $x$ 的条件即为后面 **$x+1$ 的数的个数**小于等于 **$x + 3$及以上的数的个数** ，这个思路的立足点是后面每个数都可以被 $x + 1$ 刷成 $x+3$ ，所以**每个 $x+3$ 及以上的数**最多只会产生一点贡献。

根据 F1 的思路，我们不难发现如果要把某个点向后交换，那么一定是交换到最后面。维护一下每个大小的数字满足条件需要的数的个数，和前缀最小值，就可以二分找到需要交换的节点大小。

但是问题在于 hack 数据中，实际上 5 对 1 产生了两点贡献。如果考虑 2 将 3 5 刷成 4 4，虽然把 5 的贡献减为了 1 ，但是也让 3 产生了一点贡献，但是没有考虑这种情况，所以 $tmp < i$ 的写法会导致错误，最终 WA on test 15

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

        vector<int> p;
        for (int i = 1; i <= n; i++) p.push_back(a[i]);
        ranges::sort(p);
        int id = 0;
        unordered_map<int, int> mp;
        for (int i = 0; i < n; i++) {
            if (!i || p[i] != p[i - 1]) {
                ++id;
                if (i && abs(p[i] - p[i - 1]) > 1) ++id;
                mp[p[i]] = id;
            }
        }

        vector<int> stk, s, cnt(id + 5, 0), mn(id + 5, inf32), ini(id + 5, inf32);
        int ans = 0;
        map<int, int> pos;
        for (int i = 1; i <= n; i++) {
            while (!stk.empty() && a[i] < a[stk.back()]) {
                --cnt[mp[a[stk.back()]]];
                if (!cnt[mp[a[stk.back()]]]) {
                    mn[mp[a[stk.back()]]] = inf32;
                    ini[mp[a[stk.back()]]] = inf32;
                }
                stk.pop_back(), s.pop_back();
            }
            if (stk.empty() || a[i] <= a[stk.back()] + 1) {
                int pre = stk.empty() ? 0 : stk.back();
                stk.push_back(i);
                pos[a[i]] = (int)stk.size() - 1;

                s.push_back((i - pre - 1) * (a[i] + 1) + a[i] + (s.empty() ? 0 : s.back()));

                ++cnt[mp[a[i]]];
                mn[mp[a[i]]] = inf32;
                ini[mp[a[i]]] = inf32;
                if (cnt[mp[a[i]] - 1]) {
                    if (cnt[mp[a[i]] - 2]) {
                        if (mp[a[i]] >= 3) {
                            ini[mp[a[i]] - 2] += 1;
                            mn[mp[a[i]] - 2] = min(mn[mp[a[i]] - 3], ini[mp[a[i]] - 2]);
                        }
                    }
                    ini[mp[a[i]] - 1] = i + cnt[mp[a[i]]];
                    mn[mp[a[i]] - 1] = min(mn[mp[a[i]] - 2], ini[mp[a[i]] - 1]);
                }
            }
            
            int l = 0, r = (int)stk.size() - 1, tag = (int)stk.size() - 1;
            while (l <= r) {
                int mid = (l + r) >> 1;
                int tmp = mn[mp[a[stk[mid]]]];
                if (a[stk[mid]] == a[stk.back()] || tmp <= i) r = mid - 1, tag = mid;
                    else l = mid + 1;
            }
            tag = pos[a[stk[tag]]];

            int res = s[tag] + (i - stk[tag]) * (a[stk[tag]] + 2);
            for (int k = 1; k <= 50; k++) {
                if (a[stk[tag]] == a[*stk.begin()]) break;
                tag = pos[a[stk[tag]] - 1];
                res = min(res, s[tag] + (i - stk[tag]) * (a[stk[tag]] + 2));
            }
            cout << res << ' ';
        }
        cout << "\n";
    }

    return 0;
}
```
