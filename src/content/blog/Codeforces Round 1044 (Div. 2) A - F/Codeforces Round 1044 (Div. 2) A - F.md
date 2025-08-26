---
title: Codeforces Round 1044 (Div. 2) A - F
description: ''
tags:
  - ACM
  - CF
publishDate: 2025-08-26
heroImage: {src: './cover.jpeg', color: '#e3e17aff' }
---
## [A - Redstone?](https://codeforces.com/contest/2133/problem/A)

最后的转速为 $\frac{a_1}{a_2} \times \frac{a_3}{a_2} \times ... \times \frac{a_n}{a_{n - 1}} = \frac{a_1}{a_n}$

存在两个齿数相等的齿轮就是 yes

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
        sort(a.begin() + 1, a.begin() + 1 + n);
        
        int ff = 0;
        for (int i = 2; i <= n; i++)
            if (a[i] == a[i - 1]) {ff = 1; break;}

        cout << (ff ? "YES\n" : "NO\n");
    }

    return 0;
}
```

## [B - Villagers](https://codeforces.com/contest/2133/problem/B)

类似于并查集合并的过程。对于单独的点， $g_i$ 最大的那个点无论和谁合并都会产生 $g_i$ 的代价，所以我们让它和次大的合并，尽可能降低每次合并的最大值，我们会获得若干个大小为 2 的连通块（可能还有一个单独的点），并且每个连通块里至少有一个 $g_i$ 为 0

这个时候用 $g_i = 0$ 的点将每个连通块合并，这部分代价是 $0$ ，如果还剩一个单独点的话合并代价就是它自己的大小

答案就是从最大值开始隔一个取一个的和

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
        sort(a.begin() + 1, a.begin() + 1 + n);

        int ans = 0;
        for (int i = n; i >= 1; i -= 2) ans += a[i];
        cout << ans << "\n";
    }

    return 0;
}
```

## [C - The Nether](https://codeforces.com/contest/2133/problem/C)

令 $len_i$ 表示以 $i$ 为起点能到达的最远距离，先花 $n$ 次询问找出在整个图中长度最长的起点

然后从这个起点的距离 $maxDis = \max_{i = 1}^{n}({len_i})$ 开始，先从图中删除所有距离为 $maxDis - 1$ 的点，分别查询图中距离为 $maxDis - 1$ 的点在只有某个点的情况下，长度是多少。如果长度与 $maxDis$ 相等，说明这个点作为路径的第二个点是可以达成最长长度的，以此类推遍历每个长度，最坏情况下每个距离都是问到最后一个点才找到，最多 $n$ 次询问，总和 $2n$ ，满足题目要求

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

int query(int st, set<int>& list) {
    cout << "? " << st << ' ' << list.size();
    for (auto x : list)
        cout << ' ' << x;
    cout << endl;
    int res;
    cin >> res;
    return res;
}

void answer(vector<int>& list) {
    cout << "! " << list.size();
    for (auto x : list)
        cout << ' ' << x;
    cout << endl;
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

        vector<int> dis(n + 5, 0);
        vector<vector<int>> id(n + 5);
        set<int> st;
        int maxDis = 0, S = 0;
        for (int i = 1; i <= n; i++) st.insert(i);
        for (int i = 1; i <= n; i++) {
            dis[i] = query(i, st);
            if (dis[i] > maxDis) {
                maxDis = dis[i];
                S = i;
            }
            id[dis[i]].push_back(i);
        }

        vector<int> ans;
        ans.push_back(S);
        for (int i = maxDis - 1; i; i--) {
            int nxt = 0;
            for (auto x : id[i]) st.erase(x);
            for (auto x : id[i]) {
                st.insert(x);
                if (query(S, st) == maxDis) {
                    nxt = x;
                    break;
                }
            }
            ans.push_back(nxt);
        }

        answer(ans);
    }

    return 0;
}
```

## [D - Chicken Jockey](https://codeforces.com/contest/2133/problem/D)

会受到摔落伤害的鸡骑士（x

---

首先，如果我们按照顺序攻击，除了第一个鸡骑士都会受到 1 点摔落伤害，假设我们需要让某个鸡骑士 $i$ 摔下来，那么我们就要手动击杀它下面一位（所以没法让连续两个鸡骑士摔下来），原来这两位受到的摔落伤害总和应该是 2 ，现在会变成 $max(a_i, i - 1)$ ，就增加了 $max(0, max(a_i, i - 1) - 2)$ 的伤害

令 $dp_i$ 表示在前 $i$ 位鸡骑士中，让第 $i$ 位鸡骑士摔下来能产生的最大额外伤害的和。因为不能连续摔落，所以只能从 $i - 2$ 和 $i - 3$ 处转移，记录最大值，去减按顺序攻击需要的回合数就是答案

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
        int mx = 0;
        for (int i = 3; i <= n; i++) {
            int val = max(0ll, min(a[i], i - 1) - 2);
            dp[i] = max(dp[i - 2], dp[i - 3]) + val;
            mx = max(mx, dp[i]);
        }

        int tot = a[1];
        for (int i = 2; i <= n; i++) tot += a[i] - 1;

        cout << tot - mx << "\n";
    }

    return 0;
}
```

##  [E - I Yearned For The Mines](https://codeforces.com/contest/2133/problem/E)

根据样例 1 可以发现，对于一条长度为 $x$ 的链，可以用 $x$ 次操作 1 按顺序查询，从而确定 him 是否在这条链上，所以当最终图为若干条链时，只需要 $n$ 次操作，题目就转变为如何用 $\lfloor \frac{n}{4} \rfloor$ 次操作 2 断开所有的岔路口

考虑树上 dp ，假设这个点当前有 $cnt$ 个没有断开的儿子：

如果 $cnt \geq 3$ ，那么这个点必须断开，并且向上的边也会断开，返回 0

如果 $cnt = 2$ ，加上父节点的话会形成一个岔路，但是断开父节点更有可能影响到更多的岔路口，所以返回一个较大的数，确保在父节点处断开

如果 $cnt \leq 1$ ，这个点目前是处在链的状态，返回 1 即可

对于两种需要断开的情况，每次断开至少会让 $4$ 个节点不是岔路口，并且 3 个点无法添加出一个与任何岔路口完全没有共用节点的的岔路口，所以总操作数不超过 $\lfloor \frac{n}{4} \rfloor$ 

对于断开后的图，按顺序遍历每条链即可

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
    int x, y;
    bool operator < (const node& a) const {
        if (x != a.x) return x < a.x;
        return y < a.y;
    }
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
        vector<pii> e;
        vector<int> in(n + 5);
        for (int i = 1; i < n; i++) {
            int x, y;
            cin >> x >> y;
            G[x].push_back(y), G[y].push_back(x);
            e.push_back({x, y});
            ++in[x], ++in[y];
        }

        vector<pii> ans;
        function<int(int, int)> dfs = [&] (int cur, int ftr) -> int {
            if (G[cur].size() == 1 && cur != 1) return 1;
            int cnt = 0;
            for (auto v : G[cur]) {
                if (v == ftr) continue;
                cnt += dfs(v, cur);
            }
            if (cnt <= 1) return 1;
            if (cnt == 2) return 3;

            ans.push_back({2, cur});
            in[cur] = 0;
            for (auto v : G[cur]) --in[v];
            return 0;
        };
        dfs(1, 0);

        vector<int> vis(n + 5, 0);
        for (int i = 1; i <= n; i++)
            if (in[i] <= 0) vis[i] = 1, ans.push_back({1, i});
        
        function<void(int)> dfs2 = [&] (int cur) -> void {
            ans.push_back({1, cur});
            vis[cur] = 1;
            for (auto v : G[cur]) {
                if (!vis[v]) dfs2(v);
            }
        };
        for (int i = 1; i <= n; i++) {
            if (!vis[i] && in[i] == 1) {
                dfs2(i);
            }
        }

        cout << ans.size() << "\n";
        for (auto [x, y] : ans)
            cout << x << ' ' << y << "\n";
    }

    return 0;
}
```

## [F - Flint and Steel](https://codeforces.com/contest/2133/problem/F)

令 $dp_i$ 表示引爆第 $i$ 只 creeper ，并且保证前 $i$ 只全被炸死的最少次数，转移方程如下：

$$dp_i = \min_{j < i, \left|i - j\right| \geq min(e_i, e_j)}(dp_j) + 1$$

即只要两只 creeper 不会互相炸到就可以进行转移。

可以在每只 creeper 爆炸范围右端点 + 1 的位置储存操作次数以及这只 creeper 的位置，用线段树维护操作次数尽可能少，上一只爆炸位置尽可能靠左的区间最值。对于 $[i - e_i + 1, i]$ 的部分，上一只的爆炸不会波及到这一只，可以直接转移。对于 $[i + 1, i + e_i - 1]$ 的部分，采取另一种维护方式，当前 $i - e_i$ 只 creeper 爆炸结束后，这只的爆炸不会波及到前面的任何一只，从 $[i - e_i + 1, i + e_i - 1]$ 转移即可

因为选择的 creeper 不存在两两会互相炸到的情况，所以按照 $e_i$ 从小到大引爆就不会出现引爆前被炸死的情况

此外 0 不能引爆，需要特判跳过

---

数据感觉不是很强，原做法假了也过了，写题解写一半发现证不出正确性搞了个样例把自己 hack 了（

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
    int mn, pos;
};

const int maxn = 5e5 + 5;

node t[maxn << 2];

void pushup(int p) {
    int ls = p << 1, rs = p << 1 | 1;
    if (t[ls].mn < t[rs].mn) t[p] = t[ls];
        else if (t[ls].mn > t[rs].mn) t[p] = t[rs];
        else t[p] = {t[ls].mn, min(t[ls].pos, t[rs].pos)};
}
void build(int l, int r, int p) {
    t[p] = {inf64, 0};
    if (l == r) return ;
    int mid = (l + r) >> 1;
    build(l, mid, p << 1);
    build(mid + 1, r, p << 1 | 1);
    pushup(p);
}
void upd(int l, int r, int x, node k, int p) {
    if (l == r && l == x) {
        if (k.mn < t[p].mn || (k.mn == t[p].mn && k.pos < t[p].pos)) t[p] = k;
        return ;
    }
    int mid = (l + r) >> 1;
    if (x <= mid) upd(l, mid, x, k, p << 1);
        else upd(mid + 1, r, x, k, p << 1 | 1);
    pushup(p);
}
node query(int l, int r, int x, int y, int p) {
    if (x <= l && r <= y) {
        return t[p];
    }
    node res = {inf64, 0};
    int mid = (l + r) >> 1;
    if (x <= mid) {
        auto tmp = query(l, mid, x, y, p << 1);
        if (res.mn > tmp.mn || (res.mn == tmp.mn && res.pos > tmp.pos)) res = tmp;
    }
    if (y > mid) {
        auto tmp = query(mid + 1, r, x, y, p << 1 | 1);
        if (res.mn > tmp.mn || (res.mn == tmp.mn && res.pos > tmp.pos)) res = tmp;
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
        int n;
        cin >> n;
        vector<int> a(n + 5, 0);
        for (int i = 1; i <= n; i++) cin >> a[i];

        build(1, n + 1, 1);
        upd(1, n + 1, 1, {0, 0}, 1);

        vector<int> dp(n + 5, inf64), pre(n + 5, 0);
        vector<vector<int>> id(n + 5);
        for (int i = 1; i <= n; i++) id[max(0ll, i - a[i] + 1)].push_back(i); 
        for (int i = 0; i <= n; i++) {
            for (auto x : id[i]) {
                if (!a[x]) continue;
                int l = max(1ll, x - a[x] + 1), r = min(n, x + a[x] - 1);
                auto tmp = query(1, n + 1, l, r, 1);
                if (tmp.mn < dp[x]) {
                    dp[x] = tmp.mn + 1;
                    pre[x] = tmp.pos;
                }    
            }

            if (!a[i]) continue;
            int l = max(1ll, i - a[i] + 1), r = min(n, i + a[i] - 1);
            auto tmp = query(1, n + 1, l, i, 1);
            if (tmp.mn < dp[i]) {
                dp[i] = tmp.mn + 1;
                pre[i] = tmp.pos;
            }
            if (dp[i] == inf64) continue;

            upd(1, n + 1, r + 1, {dp[i], i}, 1);
        }

        node ans = query(1, n + 1, n + 1, n + 1, 1);
        if (ans.mn == inf64) {
            cout << "-1\n";
            continue;
        }
        priority_queue<pii> seq;
        while (ans.pos) {
            seq.push({-a[ans.pos], ans.pos});
            ans.pos = pre[ans.pos];
        }
        cout << seq.size() << "\n";
        while (!seq.empty()) {
            cout << seq.top().second << ' ';
            seq.pop();
        }
        cout << "\n";
    }

    return 0;
}
```