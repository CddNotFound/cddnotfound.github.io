---
title: 'VP 2025 CCPC Harbin Site ABGIJKLM'
publishDate: 2025-11-21
description: "''"
tags:
  - ACM
  - 正赛
heroImage: {src: './cover.jpeg', color: '#216662ff' }
---

## [A - k-子集和最大公约数问题](https://qoj.ac/contest/2575/problem/14814)

如果只有一种数，答案为 `infinite`

否则最大 $f(k)$ 为每两个数差的 gcd

$k$ 每增大 1 ，模意义下的和就会增加 gcd ，$k$ 为 $\frac{g}{gcd(g, g - (a[1] \% g))}$ 时变为 0 

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
        
        if (a[1] == a[n]) {
            cout << "infinite\n";
            continue;
        }

        int g = 0;
        for (int i = 2; i <= n; i++) {
            g = gcd(g, a[i] - a[i - 1]);
        }

        cout << g << ' ' << g / gcd(g, g - (a[1] % g)) << "\n";
    }

    return 0;
}
```

## [G - 扫雪](https://qoj.ac/contest/2575/problem/14820)

雪只能从左上往右下扫，先考虑最后一行和最后一列，这部分的雪只能往右/往下扫，最后剩余的雪汇总到最后一行最后一列这一个格子，中间如果有没能填平的坑，可以等价合并到上一行或者上一列，这样从后往前扫一遍就能计算合并了多少

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
        vector<vector<int>> a(n + 5, vector<int>(m + 5, 0));

        int neg = 0, pos = 0;
        for (int i = 1; i <= n; i++)
            for (int j = 1; j <= m; j++) {
                cin >> a[i][j];
                if (a[i][j] > 0)
                    pos += a[i][j];
                else
                    neg += a[i][j];
            }

        int i = n, j = m;
        int ans = 0;
        while (i >= 1 && j >= 1) {
            int tot = 0, res = 0;
            for (int k = 1; k < j; k++) {
                if (a[i][k] > 0) {
                    tot += a[i][k];
                } else {
                    int tmp = min(tot, -a[i][k]);
                    ans -= tmp;
                    tot -= tmp;
                    a[i][k] += tmp;
                    if (a[i][k] < 0) {
                        if (a[i - 1][k] > 0) ans -= min(a[i - 1][k], -a[i][k]);
                        a[i - 1][k] += a[i][k];
                    }
                }
            }
            res += tot;
            tot = 0;
            for (int k = 1; k < i; k++) {
                if (a[k][j] > 0) {
                    tot += a[k][j];
                } else {
                    int tmp = min(tot, -a[k][j]);
                    ans -= tmp;
                    tot -= tmp;
                    a[k][j] += tmp;
                    if (a[k][j] < 0) {
                        if (a[k][j - 1] > 0) ans -= min(-a[k][j], a[k][j - 1]);
                        a[k][j - 1] += a[k][j];
                    }
                }                
            }
            res += tot;
            if (a[i][j] < 0) {
                ans -= min(res, -a[i][j]);
                a[i][j] += min(res, -a[i][j]);
                if (a[i][j] < 0) {
                    if (a[i - 1][j - 1] > 0) ans -= min(a[i - 1][j - 1], -a[i][j]);
                    a[i - 1][j - 1] += a[i][j];
                }
            }

            --i, --j;
        }

        ans = pos - neg + ans * 2;
        cout << ans << "\n";
    }

    return 0;
}
```

## [L - 网格避障](https://qoj.ac/contest/2575/problem/14825)

注意到 $k \leq 10$ ，最多有 1024 种状态，考虑 $dp_{i, j, k}$ 表示机器人到第 $i$ 行第 $j$ 列，经过障碍的状态为 $k$ 的最小步数，每次更新直接向下一列移动，然后从上往下从下往上各扫一遍更新即可

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
        int n, m, k;
        cin >> n >> m;
        cin >> k;
        vector<int> block(m + 5, 0);
        // vector<vector<int>> a(n + 5, vector<int>(m + 5, 0));
        for (int i = 1; i <= k; i++) {
            int x, y;
            cin >> x >> y;
            block[y] = x;
        }

        vector<array<int, 1025>> dp(n + 5);

        auto upd = [&] (int block) -> void {
            for (int i = 1; i <= n; i++)
                for (int x = 0; x < (1 << k); x++)
                    dp[i][x] = min(inf64, dp[i][x] + 1);
            for (int i = 2; i <= n; i++)
                for (int x = 0; x < (1 << k); x++) {
                    if (i == block || i - 1 == block) continue;
                    dp[i][x] = min(dp[i - 1][x] + 1, dp[i][x]);
                }
                for (int i = n - 1; i; i--)
                for (int x = 0; x < (1 << k); x++) {
                    if (i == block || i + 1 == block) continue;
                    dp[i][x] = min(dp[i + 1][x] + 1, dp[i][x]);
                }
        };

        int cnt = -1;
        for (int j = 2; j <= m; j++) {
            if (!block[j]) {
                upd(0);
                continue;
            }

            int row = block[j];
            ++cnt;
            for (int x = 0; x < (1 << k); x++) dp[row][x] = inf64;
            for (int i = row - 1; i; i--)
                for (int x = 0; x < (1 << k); x++) {
                    if (x >> cnt & 1) dp[i][x] = inf64;
                }
            for (int i = row + 1; i <= n; i++)
                for (int x = 0; x < (1 << k); x++) {
                    if (!(x >> cnt & 1)) dp[i][x] = inf64;
                }
            
            upd(row);
        }

        vector<int> ans((1 << k) + 5, inf64);
        for (int x = 0; x < (1 << k); x++)
            for (int i = 1; i <= n; i++)
                ans[x] = min(ans[x], dp[i][x]);
                
        for (int i = 0; i < (1 << k); i++)
            cout << (ans[i] == inf64 ? -1 : ans[i]) << ' ';
        cout << "\n";
    }

    return 0;
}
```

## [K - 01 背包](https://qoj.ac/contest/2575/problem/14824)

先考虑 $W = 2$ 的情况，很容易构造出 $(1, 2), (2, 3)$ 的方案，不难想到一个构造方法为使用重量从 1 到 $W$ 的每个重量，让重量为 1 的物品性价比最大，然后 $W$ 到 $2$ 性价比以此递减，但 $V_1 + V_i > V_{i + 1}$ ，这样就能保证对于每个重量 $k$ 都是填入一个 1 和 $k - 1$，并且价值不如只填一个 $k$

根据上面的公式二分出 $V_1$ 的最小价值，然后构造就是答案

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
    // cin >> T;
    while (T--) {
        int W;
        cin >> W;

        auto check = [&] (int x) -> int {
            vector<pii> tmp(W + 5, {0, 0});
            tmp[1] = {1, x}, tmp[2] = {2, x + 1};
            for (int i = 3; i <= W; i++)
                tmp[i] = {i, tmp[1].second + tmp[i - 1].second + 1};
            sort(tmp.begin() + 1, tmp.begin() + 1 + W, [&](pii x, pii y) {
                if (x.second * y.first != y.second * x.first) return x.second * y.first > y.second * x.first;
                return x.first > y.first;
            });
            if (tmp[1].first != 1) return 0;
            for (int i = 2; i <= W; i++)
                if (tmp[i].first != W - i + 2) return 0;
            return 1;
        };

        int l = 1, r = 1e9, res = 0;
        while (l <= r) {
            int mid = (l + r) >> 1;
            if (check(mid)) r = mid - 1, res = mid;
                else l = mid + 1;
        }
        
        vector<pii> ans(W + 5, {0, 0});
        ans[1] = {1, res}, ans[2] = {2, res + 1};
        for (int i = 3; i <= W; i++)
            ans[i] = {i, ans[i - 1].second + res + 1};

        cout << W << "\n";
        for (int i = 1; i <= W; i++)
            cout << ans[i].first << ' ';
        cout << "\n";
        for (int i = 1; i <= W; i++)
            cout << ans[i].second << ' ';
        cout << "\n";
    }

    return 0;
}
```

## [I - 六边形翻转](https://qoj.ac/contest/2575/problem/14822)

先对两个网格同时异或某一个网格，得到一个纯白网格和一个新的网格，和原问题是等价的

对于每次操作，对涉及的每条线都会更改两个格子的颜色，也就是说如果某一条线上出现奇数个黑色格子则无解

如果每条线上都是偶数个黑色，最外层的线应该围成一个等边六边形，可以处理边上的黑色，一层一层往里缩，因为每条线上都是偶数个格子，缩到边长为 2 时一次操作就可以变成全白

开三个 `map` 统计一下每条线的奇偶性即可
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

struct position {
    int x, y, z;
    bool operator == (const position& a) const {
        return x == a.x && y == a.y && z == a.z;
    };
};

signed main()
{
    cin.tie(0); cout.tie(0);
    ios::sync_with_stdio(0);

    int T = 1;
    cin >> T;
    while (T--) {
        int n, m;
        cin >> n >> m;
        vector<position> p(n + m + 5, {0, 0, 0});
        for (int i = 1; i <= n + m; i++) cin >> p[i].x >> p[i].y >> p[i].z;
        sort(p.begin() + 1, p.begin() + 1 + n + m, [&](position x, position y){
            if (x.x != y.x) return x.x < y.x;
            if (x.y != y.y) return x.y < y.y;
            return x.z < y.z;
        });
        int cnt = 0;
        p[n + m + 1] = {1, 1, 1};
        for (int i = 1; i <= n + m; i++) {
            if (!(p[i] == p[i + 1])) p[++cnt] = p[i];
                else ++i;
        }
        n = cnt;

        map<int, int> X, Y, Z;
        for (int i = 1; i <= n; i++) {
            auto [x, y, z]  = p[i];
            ++X[x], ++Y[y], ++Z[z];
        }

        int ff = 1;
        for (auto x : X) if (x.second & 1) {ff = 0; break;}
        for (auto x : Y) if (x.second & 1) {ff = 0; break;}
        for (auto x : Z) if (x.second & 1) {ff = 0; break;}

        cout << (ff ? "YES\n" : "NO\n");
    }

    return 0;
}
```

## [J - 幻想乡的裁判长](https://qoj.ac/contest/2575/problem/14823)

首先把所有的 w 拆成两个 v 跑一遍马拉车

然后对于一个回文区间，如果两段到离它们各自最近的 o 这段上的 v 奇偶性不同，则说明有一边把 w 拆开来用了，合法区间应该缩短到最近的奇偶性相同的位置

时间复杂度和空间复杂度看上去会有一个不是很对，但是最后卡卡卡在 `TLE` 和 `MLE` 中找到了一个巧妙的位置过了）

---

原来题解就是这个思路， 那就是我写太麻烦了）

### Code

```cpp
#include<bits/stdc++.h>
using namespace std;

// #define int long long

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
        string str;
        cin >> str;

        string s;
        int cnt = 1;
        for (int i = 0; i < n; i++) {
            if (str[i] == 'w') cnt += 4;
                else cnt += 2;
        }
        s += '#';
        vector<int> pos(cnt + 5, 0);
        cnt = 0;
        for (int i = 0; i < n; i++) {
            if (str[i] == 'w') {
                s += 'v';
                s += '#';
                s += 'v';
                s += '#';
                pos[++cnt] = i;
                pos[++cnt] = -1;
                pos[++cnt] = i;
                pos[++cnt] = -1;
            } else {
                s += str[i];
                s += '#';
                pos[++cnt] = i;
                pos[++cnt] = -1;
            }
        }
        
        vector<int> v(cnt + 5, 0);
        for (int i = 0; i <= cnt; i++) {
            if (s[i] == 'v' && str[pos[i]] == 'v') v[i] = 1;
        }
        set<int> O, V;
        for (int i = 0; i <= cnt; i++) {
            if (s[i] == 'v' && str[pos[i]] == 'v') V.insert(i);
        }
        V.insert(inf32), V.insert(-inf32);

        for (int i = 1; i <= cnt; i++) 
            if (i) v[i] ^= v[i - 1];

        vector<int> preO(cnt + 5, 0), succO(cnt + 5, 0);
        int lst = -inf32;
        for (int i = 0; i < s.size(); i++) {
            if (s[i] == 'o') lst = i;
            preO[i] = lst;
        }
        lst = inf32;
        for (int i = s.size() - 1; i >= 0; i--) {
            if (s[i] == 'o') lst = i;
            succO[i] = lst;
        }

        auto _xor = [&] (int l, int r) -> int {
            return v[r] ^ v[l - 1];
        };

        vector<int> p(cnt + 5, 0);
        for (int i = 0, j = 0; i < cnt; i++) {
            if (2 * j - i >= 0 && j + p[j] > i) {
                p[i] = std::min(p[2 * j - i], j + p[j] - i);
            }
            while (i - p[i] >= 0 && i + p[i] < cnt && s[i - p[i]] == s[i + p[i]]) {
                p[i] += 1;
            }
            if (i + p[i] > j + p[j]) {
                j = i;
            }
        }
        auto succV = [&] (int x) -> int {
            return *V.lower_bound(x);
        };

        auto preV = [&] (int x) -> int {
            auto it = V.lower_bound(x);
            if (*it == x) return x;
            --it;
            return *it;
        };

        int ans = 0, st = -1, ed = -1;
        for (int i = 0; i <= cnt; i++) {
            int l = i - p[i] + 1, r = i + p[i] - 1;
            if (s[l] == '#' && r > l) ++l, --r;
            if (r == l && s[l] == '#') continue;

            int len = pos[r] - pos[l] + 1;
            if (len >= 2) {
                while (succO[l] <= i && _xor(l, min(i, succO[l])) != _xor(max(i, preO[r]), r)) {
                    int dis = min(succV(l) - l, r - preV(r)) + 2;
                    l += dis, r -= dis;
                    if (l > r) break;
                }
            }
            if (l > r) continue;

            len = pos[r] - pos[l] + 1;
            if (len > ans) {
                ans = len;
                st = pos[l], ed = pos[r];
            }
        }

        for (int i = st; i <= ed; i++)
            cout << str[i];
        cout << "\n";
    }

    return 0;
}
```

## [B - 液压机](https://qoj.ac/contest/2575/problem/14815)

纯粹模拟

根据当前的移动方向计算出下一次碰撞是和哪根线模拟就能过了

当左右的竖线不移动时，模拟可能超时，但是直接计算出最终的 $x$ 坐标

怎么我去的站计算几何都没这么简单过）

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

const long double eps = 1e-6;

signed main()
{
    cin.tie(0); cout.tie(0);
    ios::sync_with_stdio(0);

    int T = 1;
    cin >> T;
    while (T--) {
        long double x, y, vx, vy;
        long double left, right, leftSpeed, rightSpeed;
        long double bottom, top, bottomSpeed, topSpeed;
        cin >> x >> y >> vx >> vy;
        cin >> bottom >> top >> bottomSpeed >> topSpeed;
        cin >> left >> right >> leftSpeed >> rightSpeed;

        long double totTime = (top - bottom) / (topSpeed + bottomSpeed);
        if (leftSpeed < eps && rightSpeed < eps) {
            long double lr = (right - left) * 2 / vx;
            totTime -= (int)(totTime / lr) * lr;
        }
        while (totTime > eps) {
            long double timeX = 0;
            if (vx > 0) {
                if (vx <= rightSpeed) {
                    timeX = inf32;
                } else {
                    timeX = (right - x) / (vx - rightSpeed);
                }
            } else {
                if (-vx <= leftSpeed) {
                    timeX = inf32;
                } else {
                    timeX = (x - left) / (-vx - leftSpeed);
                }
            }
            long double time = min(totTime, timeX);
            totTime -= time;
            x += time * vx;
            left -= time * leftSpeed;
            right += time * rightSpeed;
            vx = -vx;
        }

        long double timeY = (top - bottom) / (topSpeed + bottomSpeed);
        top -= timeY * topSpeed;
        y = top;

        cout << fixed << setprecision(12) << x << ' ' << y << "\n";

    }

    return 0;
}
```

## [M - 连通的正三角形](https://qoj.ac/contest/2575/problem/14826)

假设选取的三个方向的编号依次为 $i, j, k$ ，能构成一个合法的正三角形当且仅当：
- $i + j \geq n$ , $i + k \geq n$ , $j + k \geq n$
- $i + j + k \neq 2n$
- $a_i = b_j = c_k$

第三个条件把 01 分开算即可

先计算第一种情况，令 $s_x$ 表示 $j, k >= x$ 时的方案数，可以用树状数组计算，从大到小插入，对每一位查询 $[n - x, n]$ 的区间和，然后枚举 $i$ ，对于这个 $i$ 的方案数就是 $s_{n - i}$

题解用 FFT，我决定直接用 `bitset` 偷鸡）

再考虑如何去除第二种情况，因为 $i, j, k \leq n$ ，所以当 $i + j + k = 2n$ 时，必然满足第一个条件，用 `bitset` 维护，令 $B$ 表示 $b$ 的哪几位是当前计算的方向， $C$ 表示 $c$ 中当前计算的方向与 $2n$ 的差的集合，假设我们枚举到了 $i$ , $B\ \&\ (C >> i)$ 就是和为 $2n$ 的情况（如果 $C >> i$ 的某一位为 1 ，并且这一位 $b$ 也是 1 ，说明存在一个 $b_j$ 和 $c_k$ 与 $a_i$ 的和为 $2n$）

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
        if (pos <= 0) return 0;
        LL sum = 0;
        for (int i = pos; i; i -= lowbit(i)) sum += t[i];
        return sum;
    }
    LL query(int l, int r) {
        return query(r) - query(l - 1);
    }
};

const int maxn = 2e5 + 5;

bitset<maxn> C, B;

signed main()
{
    cin.tie(0); cout.tie(0);
    ios::sync_with_stdio(0);

    int T = 1;
    cin >> T;
    while (T--) {
        int n;
        cin >> n;
        vector<int> a(n + 5, 0), b(n + 5, 0), c(n + 5, 0);
        string str;
        cin >> str;
        for (int i = 1; i <= n; i++) a[i] = str[i - 1] ^ 48;
        cin >> str;
        for (int i = 1; i <= n; i++) b[i] = str[i - 1] ^ 48;
        cin >> str;
        for (int i = 1; i <= n; i++) c[i] = str[i - 1] ^ 48;

        if (n == 1) {
            cout << (a[1] == b[1] && b[1] == c[1]) << "\n";
            continue;
        }

        int ans = 0;
        auto calc = [&] (int x) -> void {
            Fenwick t1(n), t2(n);
            vector<int> s(n + 5, 0);
            for (int i = n; i; i--) {
                if (b[i] == x) {
                    t1.add(i, 1);
                    s[i] += t2.query(n - i, n);
                }
                if (c[i] == x) {
                    t2.add(i, 1);
                    s[i] += t1.query(n - i, n);
                }
            }
            for (int i = n; i >= 0; i--)
                s[i] += s[i + 1];

            for (int i = 1; i <= n; i++)
                if (a[i] == x) {
                    ans += s[n - i];
                }

            C.reset(), B.reset();
            for (int i = 1; i <= n; i++) {
                if (b[i] == x) B[i] = 1;
                if (c[i] == x) C[2 * n - i] = 1;
            }
            for (int i = 1; i <= n; i++)
                if (a[i] == x) {
                    ans -= (B & (C >> i)).count();
                }
        };
        
        calc(0);
        calc(1);
        
        cout << ans << "\n";
    }

    return 0;
}
```