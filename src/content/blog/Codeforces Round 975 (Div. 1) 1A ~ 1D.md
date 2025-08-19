---
title: 'Codeforces Round 975 (Div. 1) 1A - 1D'
description: ''
publishDate: 2024-09-28
tags:
  - ACM
  - CF
---
<!-- more -->

这场是不是有点偏简单了（

## [A - Cards Partition](https://codeforces.com/contest/2018/problem/A)

每组最多只能有 $n$ 张牌，至少要分为 $max\{a_i\}$ 组

假设当前我们要给每一组分 cnt 张牌，那么至少要分成 $\lceil \frac {tot}{cnt} \rceil$ 组，最多能分成 $\lfloor \frac {tot + k} {cnt} \rfloor$ 组

只要最多的组数大于最多的牌数，并且 k 足够将 tot 补成 cnt 的倍数即可

所以从 n 到 1 枚举 cnt ，找到合法答案就 break 输出

### Code

```cpp
#include<bits/stdc++.h>
using namespace std;

#define int long long

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

signed main()
{
    cin.tie(0); cout.tie(0);
    ios::sync_with_stdio(0);

    int T = 1;
    T = read();
    while (T--) {
        int n, k;
        n = read(); k = read();
        vector<int> a(n + 5, 0);
        int tot = 0;
        for (int i = 1; i <= n; i++) a[i] = read(), tot += a[i];

        sort(a.begin() + 1, a.begin() + 1 + n);
        int ans = 1;
        int now = 1, mx = n, group = (tot + mx - 1) / mx, mxgroup = (tot + k) / mx;
        while (mx > 1) {
            int ned = group * mx - tot;
            if (mxgroup >= a[n] && k >= ned) {ans = mx; break;}
            mx--;
            group = (tot + mx - 1) / mx, mxgroup = (tot + k) / mx;
        }

        cout << ans << "\n";
    }

    return 0;
}

```

## [B - Speedbreaker](https://codeforces.com/contest/2018/problem/B)

当我们要把时限为 $i$ 的城市全部占领时，如果时限小于等于 $i$ 最左边的城市为 $L$ ，最右边的城市为 $R$ ，那么整个区间 $[L, R]$ 都应该被占领

假设这个区间长度为 $len_i$ ，那么当 $len_i > i$ 时，说明不可能，否则若还剩下 $res$ 秒，那么区间 $[L - res, R + res]$ 都有可能成为起点

答案就是对每个时限都能成为起点的点的个数，差分数组统计一下就好了

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
    cin.tie(0); cout.tie(0);
    ios::sync_with_stdio(0);

    int T = 1;
    T = read();
    while (T--) {
        int n;
        n = read();
        vector<int> a(n + 5, 0);
        vector<int> l(n + 5, n), r(n + 5, 0);
        for (int i = 1; i <= n; i++) {
            a[i] = read();
            l[a[i]] = min(l[a[i]], i);
            r[a[i]] = max(r[a[i]], i);
        }
        for (int i = 2; i <= n; i++)
            l[i] = min(l[i], l[i - 1]), r[i] = max(r[i], r[i - 1]);

        int ff = 0, cnt = 0;
        Fenwick t(n);
        for (int i = 1; i <= n; i++) {
            if (!r[i]) continue;
            int len = r[i] - l[i] + 1;
            if (len > i) {ff = -1; break;}
            int res = i - len;
            t.add(max(1, l[i] - res), min(n, r[i] + res), 1);
            ++cnt;
        }

        if (ff == -1) {
            cout << "0\n";
            continue;
        }

        int ans = 0;
        for (int i = 1; i <= n; i++)
            if (t.query(i) == cnt) ans++;
        cout << ans << "\n";
    }

    return 0;
}
```

## [C - Tree Pruning](https://codeforces.com/contest/2018/problem/C)

在脑子大概想象一下，若指定深度为 depth ，那么深度大于 depth 的点都要被删掉，对于深度小于depth的点，如果它的子树中没有深度大于等于 depth 的也要被删掉，算一下 mxdep 和 dep ，枚举深度统计一下就好了

---

这个当 1C 是不是有点太简单了（

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

const int inf = 1e9;

int main()
{
    cin.tie(0); cout.tie(0);
    ios::sync_with_stdio(0);

    int T = 1;
    T = read();
    while (T--) {
        int n;
        n = read();
        vector<vector<int>> G(n + 5);
        for (int i = 1; i < n; i++) {
            int x, y;
            x = read(); y = read();
            G[x].push_back(y), G[y].push_back(x);
        }

        vector<int> dep(n + 5, 0), mxdep(n + 5, 0);
        auto dfs = [&] (auto dfs, int cur, int ftr, int depth) -> void {
            dep[cur] = mxdep[cur] = depth;
            for (auto v : G[cur]) {
                if (v == ftr) continue;
                dfs(dfs, v, cur, depth + 1);
                mxdep[cur] = max(mxdep[cur], mxdep[v]);
            }
        };
        dfs(dfs, 1, 0, 1);
        
        vector<int> cnt_dep(n + 5, 0), cnt_mxdep(n + 5, 0);
        for (int i = 1; i <= n; i++) {
            cnt_dep[dep[i]]++;
            cnt_mxdep[mxdep[i]]++;
        }
        for (int i = 1; i <= n; i++)
            cnt_dep[i] += cnt_dep[i - 1], cnt_mxdep[i] += cnt_mxdep[i - 1];

        int ans = inf;
        for (int i = 1; i <= n; i++) {
            ans = min(ans, cnt_dep[n] - cnt_dep[i] + cnt_mxdep[i - 1]);
        }
        cout << ans << "\n";
    }

    return 0;
}
```

## [D - Max Plus Min Plus Size](https://codeforces.com/contest/2018/problem/D)

首先最大的数字一定可以被选择，考虑有一种选择方案，不选择最大的数字，如果此时所有最大的数字左右两边都被选了，可以通过少选一个数字选上最大的数字，此时长度减一，最大值至少增加一，答案一定不会更差；如果有最大的数字左右两边没全被选，可以通过替换，不减少长度增加最大值的大小。所以一定有一种包含最大数的方案，他的答案是最优解。

知道这点后，我们按照数的大小从大往小插入，每次插入时维护存在可选区间，并且维护一下包含最大数的偶数长度区间个数和包含存在奇偶性和 L 相同的最大数的奇数长度区间个数，能选择的最大长度就是 $\sum \lceil \frac {len_i} {2} \rceil$ ，如果上述的两种区间至少存在一个，那么一定有种选择最多数字的选择方案能选到最大数，假设当前刚插入完为 $a_i$ 的数字 ，那么直接用 $mx + a_i + \sum$ 更新答案。如果上述两种区间都不存在，说明需要通过减少一个长度去选择最大的数字，那就减一再更新答案

那么这个时候如果加完了 $a_i$ ，但是最后选的方案里如果存在一种即选择了最大数，又没选 $a_i$ 还长度最长的选择方案，那答案不就算少了吗。 假设这种方案中选的最小数为 $a_j$ ，那么当我们插入 $a_j$ 时，也能得到相同的选择方案，答案在这个时候就被更新过了。

---

一看到区间加减上来就开 Fenwick，B也是，完全忘记不用更新用差分就行（
### Code

```cpp
#include<bits/stdc++.h>
using namespace std;

typedef pair<int, int> pii;
typedef long long LL;
typedef unsigned long long uLL;
// #define mp make_pair

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
    int l, r, len;
};

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
        int cnt = 0;
        map<int, int> mp;
        vector<int> pos(n + 5, 0), p;
        for (int i = 1; i <= n; i++) {
            a[i] = read();
            p.push_back(a[i]);
        }
        sort(p.begin(), p.end());
        for (int i = 0; i < n; i++) {
            if (!i || p[i] != p[i - 1]) {
                ++cnt;
                mp[p[i]] = cnt;
                pos[cnt] = p[i];
            }
        }
        int mx = 0;
        vector<vector<int>> e(n + 5);
        for (int i = 1; i <= n; i++) {
            a[i] = mp[a[i]], mx = max(mx, a[i]);
            e[a[i]].push_back(i);
        }

        int ans = 0, lst = -1, cnt_mx = 0;
        Fenwick t0(n), t1(n);
        
        for (int i = 1; i <= n; i++) {
            if (a[i] != mx || lst == i - 1) continue;
            lst = i, cnt_mx++;
        }
        for (int i = 1; i <= n; i++) {
            if (a[i] != mx) continue;
            if (i & 1) t1.add(i, 1);
                else t0.add(i, 1);
        }

        vector<int> l(n + 5, 0), r(n + 5, 0);
        int double_mx = 0, same_mx = 0, nowcnt = 0;
        auto del = [&] (int L, int R) -> void {
            int len = R - L + 1;
            nowcnt -= (len + 1) / 2;
            if (len % 2 == 0 && (t0.query(L, R) || t1.query(L, R))) double_mx--;
            if (len & 1) {
                if (L & 1) {
                    if (t1.query(L, R)) same_mx--;
                } else {
                    if (t0.query(L, R)) same_mx--;
                }
            }
        };
        auto add = [&] (int L, int R) -> void {
            int len = R - L + 1;
            nowcnt += (len + 1) / 2;
            if (len % 2 == 0 && (t0.query(L, R) || t1.query(L, R))) double_mx++;
            if (len & 1) {
                if (L & 1) {
                    if (t1.query(L, R)) same_mx++;
                } else {
                    if (t0.query(L, R)) same_mx++;
                }
            }
        };

        for (int i = cnt; i; i--) {
            for (auto x : e[i]) {
                if (l[x - 1] && r[x + 1]) {
                    int L = l[x - 1], R = r[x + 1];
                    del(l[x - 1], x - 1), del(x + 1, r[x + 1]);
                    l[x - 1] = r[x + 1] = 0;
                    r[L] = R, l[R] = L;
                    add(L, R);
                } else if (l[x - 1]) {
                    int L = l[x - 1], R = x;
                    del(l[x - 1], x - 1);
                    l[x - 1] = 0;
                    r[L] = R, l[R] = L;
                    add(L, R);
                } else if (r[x + 1]) {
                    int L = x, R = r[x + 1];
                    del(x + 1, r[x + 1]);
                    r[x + 1] = 0;
                    r[L] = R, l[R] = L;
                    add(L, R);
                } else {
                    l[x] = r[x] = x;
                    add(x, x);
                }
            }

            int tmp = pos[mx] + pos[i] + nowcnt;
            if (!double_mx && !same_mx) tmp--;
            ans = max(ans, tmp);
        }

        cout << ans << "\n";
    }

    return 0;
}
```