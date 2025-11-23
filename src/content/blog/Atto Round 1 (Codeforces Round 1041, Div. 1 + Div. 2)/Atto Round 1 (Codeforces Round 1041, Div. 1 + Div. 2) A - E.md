---
title: 'Atto Round 1 (Codeforces Round 1041, Div. 1 + Div. 2) A - E'
description: ''
publishDate: 2025-08-20
tags:
  - ACM
  - CF
heroImage: {src: './cover.jpeg', color: '#D58388' }
---
## [A - Mix Mex Max](https://codeforces.com/contest/2127/problem/A)

只有当所有数都相等且不为 0 时才符合要求

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

        int std = 0, ff = 1;
        for (int i = 1; i <= n; i++) {
            if (a[i] == -1) continue;
            if (!a[i]) {ff = 0; break;}
            if (!std) 
                std = a[i];
            else if (std != a[i]) {
                ff = 0;
                break;
            }
        }

        cout << (ff? "YES\n" : "NO\n");
    }

    return 0;
}
```

## [B - Hamiiid, Haaamid... Hamid?](https://codeforces.com/contest/2127/problem/B)

如果 Hamid 在最边上，那么一回合就可以跑出去

如果我们要让 Hamid 在逃脱的方向走最多的步数，肯定在他的左边或者右边放墙

假设我们在他左边放墙， Hamid 从左边跑出去的的花费就是 $x - 1$ （撞碎一堵放一堵），从右边跑出去就是在他右边最近的墙到 $n$ 的长度 $dis$，也是撞碎一堵放一堵，这个时候需要的步数就是 $min\left(x - 1, dis\right) + 1$

起手在右边也是同理，在两种情况中取 max 即可

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
        string str;
        cin >> str;
        str = " " + str;

        if (k == 1 || k == n) {
            cout << "1\n";
            continue;
        }

        int leftCnt = 0, rightCnt = 0, leftPos = 0, rightPos = n + 1;
        for (int i = 1; i < k; i++) if (str[i] == '#') leftPos = i;
        for (int i = n; i > k; i--) if (str[i] == '#') rightPos = i;

        cout << max(min(k - 1, n - rightPos + 1), min(n - k, leftPos)) + 1 << "\n";
    }

    return 0;
}
```

## [C - Trip Shopping](https://codeforces.com/contest/2127/problem/C)

一对 $(i, j)$ 被操作后，再进行操作也不会使答案变大，所以后续肯定是一直选择这一对

令 $a_i, a_j, b_i, b_j$ 为选择的四个数，只有当 $a_i \leq a_j < b_i \leq b_j$ 时进行一次操作才会让最终答案增加 $b_i - a_j$，对每个位置 $i$ 计算出 $[min\left(a_i, b_i\right), max\left(a_i, b_i\right)]$ 这个区间，若区间两两不交，则会让答案增加最小的一个 $min_{i} - max_{i - 1} (1 < i \leq n)$ ，否则答案就是初始序列的值

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

        vector<pii> p(n + 5, {0, 0});
        for (int i = 1; i <= n; i++) p[i] = {min(a[i], b[i]), max(a[i], b[i])};
        sort(p.begin() + 1, p.begin() + 1 + n, [&](pii x, pii y){
            if (x.first != y.first) return x.first < y.first;
            return x.second < y.second;
        });

        int v = inf64;
        for (int i = 2; i <= n; i++) {
            if (p[i].first > p[i - 1].second) {
                v = min(v, (p[i].first - p[i - 1].second) << 1);
            } else {
                v = 0;
                break;
            }
        }

        int ans = 0;
        for (int i = 1; i <= n; i++) ans += abs(a[i] - b[i]);

        cout << ans + v << "\n";
    }

    return 0;
}
```

## [D - Root was Built by Love, Broken by Destiny](https://codeforces.com/contest/2127/problem/D)

题目要求桥不能相交，那么说明这个图中肯定不能存在环，此时是一定满足二分图定义的

再考虑这个树的贡献，先取一个非叶节点作为根节点，假设它一共有 $tot$ 个儿子，并且他的儿子中有 $cnt$ 个度大于 1 的，即还会往回连桥的

若 $cnt = 2$ ，这两个一定是放在最两边的，且顺序随意，中间的可以随意排列，贡献为 $2 \times (tot - 2)!$

若 $cnt = 1$，这个可以放在最左边或最右边，其余部分可以随意排列，贡献为 $2 \times (tot - 1)!$

若 $cnt = 0$，所有儿子都可以随意排列，贡献为 $tot!$

对于一个非根节点，它的父节点占用了一个边界位，并且指定了方向（即这个点的度大于 1 的节点只能放置在某一边，不能两边随意放）

若 $cnt = 1$，贡献为 $(tot - 1)!$

若 $cnt = 0$ ，贡献为 $tot!$

对于其他 $cnt$ ，都会导致中间的节点需要往回连接桥梁导致相交，答案为 0

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

const int maxn = 2e5 + 5;

int t[maxn];

void init(int n) {
    t[0] = 1;
    for (int i = 1; i <= n; i++)
        t[i] = mul(t[i - 1], i);
}

signed main()
{
    cin.tie(0); cout.tie(0);
    ios::sync_with_stdio(0);

    init(2e5);

    int T = 1;
    cin >> T;
    while (T--) {
        int n, m;
        cin >> n >> m;
        vector<vector<int>> G(n + 5);
        for (int i = 1; i <= m; i++) {
            int x, y;
            cin >> x >> y;
            G[x].push_back(y), G[y].push_back(x);
        }

        vector<int> col(n + 5, 0);
        int ff = 1;
        function<void(int, int, int )> dfs = [&] (int cur, int ftr, int color) -> void {
            col[cur] = color;
            for (auto v : G[cur]) {
                if (v == ftr) continue;
                if (col[v]) ff = 0;
                    else dfs(v, cur, color ^ 1);
            }
        };
        dfs(1, 0, 2);

        if (!ff) {
            cout << "0\n";
            continue;
        }

        int ans = 1, st = 0;
        for (int i = 1; i <= n; i++) 
            if (G[i].size() >= 2) {st = i; break;}

        if (!st) {
            cout << "2\n";
            continue;
        }

        function<void(int, int, int)> dfs2 = [&] (int cur, int ftr, int isRoot) -> void {
            int cnt = 0;
            for (auto v : G[cur]) {
                if (v == ftr) continue;
                if (G[v].size() >= 2) ++cnt;
            }
            if (cnt >= 3 || (!isRoot && cnt >= 2)) return ans = 0, void();

            if (isRoot) {
                if (cnt == 0) {
                    ans = mul(ans, t[G[cur].size()]);
                } else if (cnt == 1) {
                    ans = mul(ans, mul(2, t[G[cur].size() - 1]));
                } else { // cnt == 2
                    ans = mul(ans, mul(2, t[G[cur].size() - 2]));
                }
            } else {
                if (cnt == 0) {
                    ans = mul(ans, t[G[cur].size() - 1]);
                } else if (cnt == 1) {
                    ans = mul(ans, t[G[cur].size() - 2]);
                }
            }

            for (auto v : G[cur]) {
                if (v == ftr) continue;
                dfs2(v, cur, 0);
            }
        };
        dfs2(st, 0, 1);

        cout << mul(2, ans) << "\n";
    }

    return 0;
}
```

## [E - Ancient Tree](https://codeforces.com/contest/2127/problem/E)

前置芝士：`Dsu on tree`

假设对每个节点都记录了以这个节点为根的子树拥有的颜色集合，通过遍历某个点的儿子储存的信息，并将信息合并到这个节点，可以获得以这个点为 lca ，有多少个颜色不同的满足条件的 $(x, y)$

同时我们可以发现，一个点向上传递的颜色数量越少越好。若颜色个数大于 2 ，则无论这个点是什么颜色都会被计入代价中，从子树拥有的颜色中任取一个染色；若个数为 1 ，令其与这个颜色相同，这个点就不会被计入代价；若个数为 0 ，则先不进行染色，并且忽略这个节点的颜色

这个过程结束后某些节点可能还未染色，直接染上离它最近的父节点的颜色即可。从它父节点向上传递的颜色数量不会增加，从这个点到父节点的所有节点颜色都相同，所以不会产生任何代价。

这个颜色集合可以用树上启发式合并维护，时间复杂度 $O(nlogn)$

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
        vector<int> w(n + 5, 0), c(n + 5, 0);
        vector<vector<int>> G(n + 5);
        for (int i = 1; i <= n; i++) cin >> w[i];
        for (int i = 1; i <= n; i++) cin >> c[i];
        for (int i = 1; i < n; i++) {
            int x, y;
            cin >> x >> y;
            G[x].push_back(y), G[y].push_back(x);
        }

        vector<int> siz(n + 5, 0), dep(n + 5, 0), son(n + 5, 0);
        function<void(int, int, int)> dfs1 = [&] (int cur, int ftr, int depth) -> void {
            dep[cur] = depth, siz[cur] = 1;
            for (auto v : G[cur]) {
                if (v == ftr) continue;
                dfs1(v, cur, depth + 1);
                if (siz[v] > siz[son[cur]]) son[cur] = v;
                siz[cur] += siz[v];
            }
        };

        vector<int> col(k + 5, 0), tag(k + 5, 0);
        vector<vector<int>> st(n + 5);
        int ans = 0, id = 0;
        function<void(int, int, int)> dfs2 = [&] (int cur, int ftr, int keep) -> void {
            for (auto v : G[cur]) {
                if (v == ftr || v == son[cur]) continue;
                dfs2(v, cur, 0);
            }
            if (son[cur]) dfs2(son[cur], cur, 1);

            
            if (!son[cur]) {
                if (c[cur]) st[cur].push_back(c[cur]);
                if (keep) col[c[cur]] += 1;
                return ;
            } 

            int c1 = 0, c2 = 0;
            swap(st[cur], st[son[cur]]);
            for (auto v : G[cur]) {
                if (v == ftr || v == son[cur]) continue;
                ++id;
                for (auto x : st[v]) {
                    st[cur].push_back(x);
                    if (col[x] && tag[x] != id) {
                        if (!c1) c1 = x;
                            else if (c1 != x && !c2) c2 = x;
                    }
                    col[x] += 1;
                    tag[x] = id;
                }
                st[v].clear();
            }

            if (c[cur]) {
                if ((c1 && c[cur] != c1) || (c2 && c[cur] != c2)) {
                    ans += w[cur];
                }
            } else {
                if (c1 && c2) {
                    ans += w[cur];
                }
                c[cur] = c1;
            }
            col[c[cur]] += 1;
            st[cur].push_back(c[cur]);

            if (!keep) {
                for (auto x : st[cur]) {
                    col[x] -= 1;
                }
            }
        };
        dfs1(1, 0, 1);
        dfs2(1, 0, 1);

        function<void(int, int)> dfs3 = [&] (int cur, int ftr) -> void {
            for (auto v : G[cur]) {
                if (v == ftr) continue;
                if (!c[v]) c[v] = c[cur];
                dfs3(v, cur);
            }
        };
        if (!c[1]) c[1] = 1;
        dfs3(1, 0);

        cout << ans << "\n";
        for (int i = 1; i <= n; i++)
            cout << c[i] << ' ';
        cout << "\n";
    }

    return 0;
}
```