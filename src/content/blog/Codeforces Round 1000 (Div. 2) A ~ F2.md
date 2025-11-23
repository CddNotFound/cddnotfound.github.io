---
title: 'Codeforces Round 1000 (Div. 2) A - F2'
description: ''
publishDate: 2025-01-22
tags:
  - ACM
  - CF
---
<!-- more -->

## [A - Minimal Coprime](https://codeforces.com/contest/2063/problem/A)

相邻的数互质，最小互质就是 $[1, 1], [2, 3], [3, 4]...$

答案为 $r - l$ 

1 1 的时候要特判一下

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
        int l, r;
        cin >> l >> r;
        if (l == 1 && r == 1) {
            cout << "1\n";
            continue;
        }
        cout << r - l << "\n";
    }

    return 0;
}
```

## [B - Subsequence Update](https://codeforces.com/contest/2063/problem/B)

颠倒的是子序列，所以可以将 $[l, r]$ 中的任何数要么替换为区间左边的数，要么是区间右边的数

答案就是 $[1, r]$ 中前 $len$ 小的数的和与 $[l, n]$ 中前 $len$ 小的数的和取 min

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
        int n, l, r;
        cin >> n >> l >> r;
        vector<int> a(n + 5, 0);
        for (int i = 1; i <= n; i++) cin >> a[i];

        int ans = inf64, tmp = 0;
        vector<int> p;
        for (int i = 1; i <= r; i++) p.push_back(a[i]);
        ranges::sort(p);
        for (int i = 1; i <= r - l + 1; i++) tmp += p[i - 1];
        ans = min(ans, tmp);

        p.clear(), tmp = 0;
        for (int i = n; i >= l; i--) p.push_back(a[i]);
        ranges::sort(p);
        for (int i = 1; i <= r - l + 1; i++) tmp += p[i - 1];
        ans = min(ans, tmp);

        cout << ans << "\n";
    }

    return 0;
}
```

## [C - Remove Exactly Two](https://codeforces.com/contest/2063/problem/C)

令 $cnt_i$ 表示第 $i$ 个节点连接的边数

初始连通块个数为 1 ，每删除一个点，连通块的数量就增加 $cnt_i - 1$ 个，当删除的两个点相邻时，这条边不会产生额外的连通块，要额外减 1

所以对每个点找到除它自己以外边最多的点，用 $cnt_x + cnt_y - 2$ 更新答案，再删去所有相邻的点，找到边最多的，用 $cnt_x + cnt_y - 1$ 更新答案

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
            cout << n - 2 << "\n";
            continue;
        }

        map<int, int> mp;
        auto add = [&] (int x) -> void {
            ++mp[x];
        };  
        auto del = [&] (int x) -> void {
            --mp[x];
            if (!mp[x]) mp.erase(x);
        };

        int ans = 0;
        for (int i = 1; i <= n; i++) add(G[i].size());
        for (int i = 1; i <= n; i++) {
            del(G[i].size());
            if (!mp.empty()) ans = max(ans, (LL)G[i].size() + mp.rbegin() -> first - 2);
            for (auto v : G[i])
                del(G[v].size());
            if (!mp.empty()) ans = max(ans, (LL)G[i].size() + mp.rbegin() -> first - 1);

            add(G[i].size());
            for (auto v : G[i])
                add(G[v].size());
        }

        cout << ans << "\n";
    }

    return 0;
}
```

## [D - Game With Triangles](https://codeforces.com/contest/2063/problem/D)

当 $n, m$ 中较大值超过另一个的两倍时， $k_{max}$ 为 $min(n, m)$，否则为 $\lfloor \frac{n + m}{3} \rfloor$

作为三角形顶点的点是可以随便选择的，不会影响面积，所以先确定底边，底边一定是先选择最靠边的两个点最优，可以先预处理出在这两条线上取第 $i$ 次底边的面积

然后考虑怎么选点，当两条线都可以作为底边（即两条线上至少都还剩 2 个点没选）时，选较大值，当某一条边 x 不能作为底边（即只剩下 0 或 1 个点时），选另外一条线 y 为底边，若边 x 剩下 0 个点，说明如果要接着往下取，必须撤回一条 x 上的底边，然后在 y 上连取两条底边

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
        int n, m;
        cin >> n >> m;
        vector<int> a(n + 5, 0), b(m + 5, 0);
        for (int i = 1; i <= n; i++) cin >> a[i];
        for (int i = 1; i <= m; i++) cin >> b[i];

        int kmax = 0;
        if (max(n, m) >= min(n, m) * 2) kmax = min(n, m);
            else kmax = (n + m) / 3;
        
        sort(a.begin() + 1, a.begin() + 1 + n);
        sort(b.begin() + 1, b.begin() + 1 + m);
        vector<int> tota(n + 5, 0), totb(m + 5, 0);
        int l = 1, r = n, cnt = 0;
        while (r - l >= 1) {
            tota[++cnt] = a[r] - a[l];
            ++l, --r;
        }
        l = 1, r = m, cnt = 0;
        while (r - l >= 1) {
            totb[++cnt] = b[r] - b[l];
            ++l, --r;
        }

        cout << kmax << "\n";

        int cnta = 0, cntb = 0, ans = 0;
        for (int i = 1; i <= kmax; i++) {
            if (cnta * 2 + cntb + 2 <= n && cntb * 2 + cnta + 2 <= m) {
                if (tota[cnta + 1] > totb[cntb + 1]) ans += tota[++cnta];
                    else ans += totb[++cntb];
            } else if (cnta * 2 + cntb + 2 > n) {
                if (cnta * 2 + cntb + 1 <= n) {
                    ans += totb[++cntb];
                } else {
                    ans -= tota[cnta--];
                    ans += totb[++cntb];
                    ans += totb[++cntb];
                }
            } else {
                if (cntb * 2 + cnta + 1 <= m) {
                    ans += tota[++cnta];
                } else {
                    ans -= totb[cntb--];
                    ans += tota[++cnta];
                    ans += tota[++cnta];
                }
            }
            
            cout << ans << ' ';
        }
        
        cout << "\n";
    }

    return 0;
}
```

## [E - Triangle Tree](https://codeforces.com/contest/2063/problem/E)

树上启发式合并板子题了

令两个 dist 分别为 x, y，三角形的个数就是 $2\cdot min(x, y) - 1$

假设当前的子树里有若干个数字，新插入一个深度 $x$ 时，若比它小的深度有 $cnt_{less}$ 个，深度的和为 $sum_{less}$，比它大的深度有 $cnt_{more}$ 个，深度的和为 $sum_{more}$，贡献分别为 $(sum_{less} - cnt_{less} * dep_{cur}) * 2 - cnt_{less}$ 和 $cnt_{more} * (x - dep_{cur}) * 2 - cnt_{more}$ 。这里 $dep_{cur}$ 是当前递归节点的深度，不是新插入的深度，因为 dist 为到 lca 的距离，所以要减去当前节点的深度

时间复杂度 $O(nlog^2n)$

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
        if (r < l) return 0;
        return query(r) - query(l - 1);
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
        for (int i = 1; i < n; i++) {
            int x, y;
            cin >> x >> y;
            G[x].push_back(y), G[y].push_back(x);
        }

        vector<int> siz(n + 5, 0), son(n + 5, 0), dep(n + 5, 0);
        function<void(int, int, int)> dfs1 = [&] (int cur, int ftr, int depth) -> void {
            siz[cur] = 1, dep[cur] = depth;
            for (auto v : G[cur]) {
                if (v == ftr) continue;
                dfs1(v, cur, depth + 1);
                siz[cur] += siz[v];
                if (siz[v] > siz[son[cur]]) son[cur] = v;
            }
        };
        dfs1(1, 0, 1);

        Fenwick sum(n), cnt(n);
        vector<vector<int>> st(n + 5);
        int ans = 0;
        function<void(int, int, int)> dfs2 = [&] (int cur, int ftr, int kep) -> void {
            for (auto v : G[cur]) {
                if (v == ftr || v == son[cur]) continue;
                dfs2(v, cur, 0);
            }
            if (son[cur]) dfs2(son[cur], cur, 1);

            if (!son[cur]) {
                st[cur].push_back(dep[cur]);
                if (kep) {
                    sum.add(dep[cur], dep[cur]), cnt.add(dep[cur], 1);
                }
                return ;
            }

            swap(st[cur], st[son[cur]]);
            for (auto v : G[cur]) {
                if (v == ftr || v == son[cur]) continue;
                vector<int> tmp;
                for (auto x : st[v]) {
                    ans += (sum.query(1, x) - cnt.query(1, x) * dep[cur]) * 2 - cnt.query(1, x);
                    ans += cnt.query(x + 1, n) * (x - dep[cur]) * 2 - cnt.query(x + 1, n);

                    tmp.push_back(x);
                }

                for (auto x : tmp) {
                    cnt.add(x, 1), sum.add(x, x), st[cur].push_back(x);
                }
                st[v].clear();
            }

            cnt.add(dep[cur], 1), sum.add(dep[cur], dep[cur]);
            st[cur].push_back(dep[cur]);

            if (!kep) {
                for (auto x : st[cur]) {
                    cnt.add(x, -1), sum.add(x, -x);
                }
            }
        };
        dfs2(1, 0, 1);

        cout << ans << "\n";    
    }

    return 0;
}
```

## [F2 - Counting Is Not Fun (Hard Version)](https://codeforces.com/contest/2063/problem/F2)

括号之间不会相互干扰，即不管一个括号内部如何排列，都不会影响到外面，所以答案应为每个括号内方案个数的乘积

有 $2n$ 个格子的括号排列方案数为卡特兰数的第 $n$ 项 $\frac{\binom{2n}{n}}{n + 1}$

递推公式为 $f_n = f_{n - 1} * (4n - 2) / (n + 1)$

其实不知道也没事，在 F1 的范围可以 $O(n^2)$ 内算出来：第一个左括号肯定放在第一个，枚举对应右括号的位置，加上这对括号间隔长度的方案数与外面剩下的格子长度的方案数的乘积

```cpp
void init(int n) {
    dp[0] = dp[2] = 1;
    for (int i = 4; i <= n; i += 2) {
        dp[i] = 0;
        for (int j = 0; j <= i - 2; j += 2) {
            dp[i] += dp[j] * dp[i - j - 2];
        }
    }
}
```

每新增一对括号 $[l, r]$ ，只会影响最小的包含这对括号的括号，假设新增括号内已经有 $x$ 格被占用（被一对括号覆盖的格子数量，不是填了括号的格子数量），那么最小包含该括号的括号内部长度就增加 $x$ 减少 $r - l + 1$ ，新增括号的内部长度就是 $r - l - 1 - x$ ，只更新这两对括号即可

接下来首先考虑如何找到那个最小括号，用线段树存储当前每个左括号对应右括号的位置，没有左括号则为 0 ，二分即可找到左括号在 $[l, r]$ 左边的最后一个右括号大于 $r$ 的位置

再看如何计算新增括号内被占用的格子数量，假设每新增一对括号，就对其区间整体加 1 ，因为括号不会部分覆盖，所以新增括号内没有被占用的格子的数值，与 l 和 r 的数值大小应该相同，均为 $[l, r]$ 的区间最小值，所以统计 $[l, r]$ 的区间最小值的数量和 $sum$ ，占用格数即为 $r - l + 1 - sum$

时间复杂度 $O(nlog^2n)$

---

写麻烦了，对括号序建树就行了（

### Code

```cpp
#include<bits/stdc++.h>
using namespace std;

typedef long long LL;
const int P = 998244353;
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
        if (x < 0) x += P;
        return x;
    }
};
using mint = ModInt<P>;

#define int long long

typedef pair<int, int> pii;
typedef unsigned long long uLL;
typedef __int128_t i128;

const int inf32 = 1e9;
const LL inf64 = 1e18;

const int maxn = 6e5 + 5;

mint dp[maxn];

void init(int n) {
    dp[0] = dp[2] = 1;
    for (int i = 4; i <= n; i += 2) {
        dp[i] = dp[i - 2] * (2 * i - 2) / (i / 2 + 1);
    }
}

template<class Info, class Tag>
struct _LazySegmentTree { // 区间 [0, n]
    int n;
    vector<Info> info;
    vector<Tag> tag;

    _LazySegmentTree(int x) { // 初始值
        vector<Info> a(x + 5, Info());
        init(x, a);
    }
    _LazySegmentTree(int x, vector<Info> a) { // a 赋值
        init(x, a);
    }

    void init(int x, vector<Info> a) {
        n = x;
        info.assign(4 * n + 5, Info());
        tag.assign(4 * n + 5, Tag());

        function<void(int, int, int)> build = [&] (int l, int r, int p) {
            if (l == r) return info[p] = a[l], void();
            int mid = (l + r) >> 1;
            build(l, mid, p << 1);
            build(mid + 1, r, p << 1 | 1);
            pushup(p);
        };
        build(0, n, 1);
    }

    void apply(int p, Tag t) {
        tag[p].apply(t);
        info[p].apply(t);
    }
    void pushup(int p) {
        info[p] = info[p << 1] + info[p << 1 | 1];
    }
    void pushdown(int p) {
        apply(p << 1, tag[p]);
        apply(p << 1 | 1, tag[p]);
        tag[p] = Tag();
    }

    void modify(int l, int r, int x, Info k, int p) {
        if (x <= l && r <= x) {
            info[p] = k;
            return ;
        }
        pushdown(p);
        int mid = (l + r) >> 1;
        if (x <= mid) modify(l, mid, x, k, p << 1);
            else modify(mid + 1, r, x, k, p << 1 | 1);
        pushup(p);
    }
    void modify(int x, Info k) {
        modify(0, n, x, k, 1);
    }

    void rangeApply(int l, int r, int x, int y, Tag k, int p) {
        if (x <= l && r <= y) {
            apply(p, k);
            return ;
        }
        pushdown(p);
        int mid = (l + r) >> 1;
        if (x <= mid) rangeApply(l, mid, x, y, k, p << 1);
        if (y > mid) rangeApply(mid + 1, r, x, y, k, p << 1 | 1);
        pushup(p);
    }   
    void rangeApply(int x, int y, Tag k) {
        rangeApply(0, n, x, y, k, 1);
    }

    Info rangeQuery(int l, int r, int x, int y, int p) {
        if (x <= l && r <= y) {
            return info[p];
        }
        pushdown(p);
        Info res = Info().iniQuery();
        int mid = (l + r) >> 1;
        if (x <= mid) res = res + rangeQuery(l, mid, x, y, p << 1);
        if (y > mid) res = res + rangeQuery(mid + 1, r, x, y, p << 1 | 1);
        pushup(p);
        return res;
    }
    Info rangeQuery(int x, int y) {
        return rangeQuery(0, n, x, y, 1);
    }
};

struct Tag {
    LL add;
    
    void apply(Tag t) {
        add += t.add;
    }
    
    Tag(LL x = 0) : add(x) {} // Lazytag 初值
};

struct Info {
    LL mn, s;

    void apply(Tag t) {
        mn += t.add;
    }

    Info(LL x = 0, LL y = 1) : mn(x), s(y) {} // 初始值
    Info iniQuery() { // Query 初值 
        return Info(inf64, 0); 
    }
};

Info operator + (Info a, Info b) {
    if (a.mn != b.mn) {
        return a.mn < b.mn ? a : b;
    } else {
        return Info(a.mn, a.s + b.s);
    }
}

using LazySegmentTree = _LazySegmentTree<Info, Tag>;

int tree[maxn << 2];

void pushup(int p) {
    tree[p] = max(tree[p << 1], tree[p << 1 | 1]);
}
void build(int l, int r, int p) {
    if (l == r) return tree[p] = 0, void();
    int mid = (l + r) >> 1;
    build(l, mid, p << 1);
    build(mid + 1, r, p << 1 | 1);
    pushup(p);
}
void update(int l, int r, int x, int k, int p) {
    if (l == r && l == x) {
        tree[p] = k;
        return ;
    }
    int mid = (l + r) >> 1;
    if (x <= mid) update(l, mid, x, k, p << 1);
        else update(mid + 1, r, x, k, p << 1 | 1);
    pushup(p);
}
int query(int l, int r, int x, int y, int p) {
    if (x <= l && r <= y) {
        return tree[p];
    }
    int res = 0, mid = (l + r) >> 1;
    if (x <= mid) res = max(res, query(l, mid, x, y, p << 1));
    if (y > mid) res = max(res, query(mid + 1, r, x, y, p << 1 | 1));
    return res;
}

signed main()
{
    cin.tie(0); cout.tie(0);
    ios::sync_with_stdio(0);

    init(6e5);

    int T = 1;
    cin >> T;
    while (T--) {
        int n;
        cin >> n;
        vector<pii> seg(n + 5, {0, 0});
        for (int i = 1; i <= n; i++) cin >> seg[i].first >> seg[i].second;

        mint ans = dp[2 * n];
        vector<int> val(2 * n + 5, 0);
        val[0] = 2 * n;
        cout << ans << ' ';

        build(1, 2 * n, 1);
        LazySegmentTree t(2 * n);
        for (int i = 1; i <= n; i++) {
            auto [x, y] = seg[i];
            int used = y - x + 1 - t.rangeQuery(x, y).s;

            int l = 1, r = x, tag = 0;
            while (l <= r) {
                int mid = (l + r) >> 1;
                if (query(1, 2 * n, mid, x, 1) > y) l = mid + 1, tag = mid;
                    else r = mid - 1;
            }
            ans /= dp[val[tag]];
            val[tag] -= (y - x + 1) - used;
            ans *= dp[val[tag]];

            val[x] = y - x + 1 - used - 2;
            ans *= dp[val[x]];

            update(1, 2 * n, x, y, 1), t.rangeApply(x, y, 1);

            cout << ans << ' ';
        }

        cout << "\n";
    }

    return 0;
}
```
