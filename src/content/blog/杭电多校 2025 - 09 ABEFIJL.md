---
title: '杭电多校 2025 - 09 ABEFIJL'
description: ''
publishDate: 2025-08-15
tags:
  - 多校
  - ACM
---
<!-- more -->

## [A - 数字卡片](https://acm.hdu.edu.cn/contest/problem?cid=1180&pid=1001)

0, 4, 8 单独组成倍数

2, 6 可以和 1, 3, 5, 7, 9 组合成 4 的倍数

6, 9 可以随意转换，所以哪边少放哪边，相等之后一起放

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
        int n = 9;
        vector<int> a(15, 0);
        for (int i = 0; i <= n; i++) cin >> a[i];

        int ans = a[0] + a[4] + a[8];
        int x = a[2], y = a[1] + a[3] + a[5] + a[7];
        int res = a[6] + a[9];
        if (abs(x - y) <= res) {
            ans += (x + y + res) / 2;
        } else {
            ans += min(x, y) + res;
        }
        
        cout << ans << "\n";
    }

    return 0;
}
```

## [B - 预处理器](https://acm.hdu.edu.cn/contest/problem?cid=1180&pid=1002)

参考题解

将 $S_i$ 分成三种

- 纯数字
- 形如 $(b, x, c)$ 的加乘混合式，表示 $b + x + c$，其中 $b$ 和 $c$ 为仅包含乘法的式子，$x$ 是一个加乘混合式
- 形如 $(l, r)$ 的左移式，表示 $l << r$， 其中 $l$ 和 $r$ 为加乘混合式或者纯数字

先考虑仅有加法和乘法的情况：

$a_1 + a_2 = (a_1, 0, a_2),\ \ \ a_1 + (b, x, c) = (a_1, b + x, c),\ \ \ (b, x, c) + a_1 = (b, x + c, a_1)$

$(b_1, x_1, c_1) + (b_2, x_2, c_2) = (b_1, x_1 + c_1 + b_2 + x_2, c_2)$

$a_1 \times a_2 = a_1 a_2,\ \ \ a_1 \times (b, x, c) = (a_1b, x, c),\ \ \ (b, x, c) \times a_1 = (b, x, ca_1)$

$(b_1, x_1, c_1) \times (b_2, x_2, c_2) = (b_1, x_1 + c_1 \times b_2 + x_2, c_2)$

然后考虑左移：

首先 $a << b << c = a << b + c$ ，根据费马小定理， $(a << b)\ \%\ mod = (a \times 2^b)\ \%\ mod = a \times 2^{b\ \%\ (mod - 1)} = a << (b\ \%\ (mod - 1))$

当一个数字某一边有 << 的时候，要么这个 << 会保留到最后，要么会变成 + ，所以这个数字可以转换为 $(0, 0, x)$ 或者 $(x, 0, 0)$ （ $x$ 在远离 << 的那一侧）

$(l_1, r_1) + (l_2, r_2) = (l_1, r_1) << (l_2, r_2) = (l_1, r_1 + l_2 + r_2),\ \ \ (l_1, r_1) \times (l_2, r_2) = (l_1, r_1 \times l_2 + r_2)$

$a_1 << a_2 = ((a_1, 0, 0), (0, 0, a_2))$

加乘混合式与左移的计算同理即可（才不是懒得写了）（具体见程序

然后 << 左边的需要模 mod ，右边的需要模 mod - 1，对每个式子需要储存对两个模数取模的结果

---

好像写得很麻烦，变成超级大分讨了

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

const int mod1 = 1e9 + 7;
const int mod2 = 1e9 + 6;

int qpow(int x, int a, int mod) {
    int res = 1;
    while (a) {
        if (a & 1) res = (res * x) % mod;
        x = (x * x) % mod;
        a >>= 1;
    }
    return res;
}

struct node {
    int mod1x, mod1y, mod1z, mod2x, mod2y, mod2z;
    node() : mod1x(0), mod1y(0), mod1z(0), mod2x(0), mod2z(0), mod2y(0) {}
    node(int a, int b, int c, int d, int e, int f) : mod1x(a), mod1y(b), mod1z(c), mod2x(d), mod2z(f), mod2y(e) {}
    int gets(int id) {
        if (id == 1) return (mod1x + mod1y + mod1z) % mod1;
        return (mod2x + mod2y + mod2z) % mod2;
    }
};

struct lr {
    node l, r;
    lr() : l(node()), r(node()) {}
    lr(node a, node b) : l(a), r(b) {}
    int gets() {
        int a, b;
        a = l.gets(1), b = r.gets(2);
        b = qpow(2, b, mod1);
        return a * b % mod1;
    }
};

node operator + (node x, node y) {
    node tmp = node();
    tmp.mod1x = x.mod1x, tmp.mod1z = y.mod1z;
    tmp.mod1y = (x.mod1y + x.mod1z + y.mod1x + y.mod1y) % mod1;
    tmp.mod2x = x.mod2x, tmp.mod2z = y.mod2z;
    tmp.mod2y = (x.mod2y + x.mod2z + y.mod2x + y.mod2y) % mod2;
    return tmp;
}
node operator + (pii x, node y) {
    node tmp = node();
    tmp.mod1x = x.first, tmp.mod1y = (y.mod1x + y.mod1y) % mod1, tmp.mod1z = y.mod1z;
    tmp.mod2x = x.second, tmp.mod2y = (y.mod2x + y.mod2y) % mod2, tmp.mod2z = y.mod2z;
    return tmp;
}
node operator + (node x, pii y) {
    node tmp = node();
    tmp.mod1x = x.mod1x, tmp.mod1y = (x.mod1y + x.mod1z) % mod1, tmp.mod1z = y.first;
    tmp.mod2x = x.mod2x, tmp.mod2y = (x.mod2y + x.mod2z) % mod2, tmp.mod2z = y.second;
    return tmp;
}
node add(pii x, pii y) {
    return {x.first, 0, y.first, x.second, 0, y.second};
}

node operator * (node x, node y) {
    node tmp = node();
    tmp.mod1x = x.mod1x, tmp.mod1z = y.mod1z;
    tmp.mod1y = (x.mod1y + x.mod1z * y.mod1x + y.mod1y) % mod1;
    tmp.mod2x = x.mod2x, tmp.mod2z = y.mod2z;
    tmp.mod2y = (x.mod2y + x.mod2z * y.mod2x + y.mod2y) % mod2;
    return tmp;
}
node operator * (pii x, node y) {
    node tmp = node();
    tmp.mod1x = (x.first * y.mod1x) % mod1, tmp.mod1y = y.mod1y, tmp.mod1z = y.mod1z;
    tmp.mod2x = (x.second * y.mod2x) % mod2, tmp.mod2y = y.mod2y, tmp.mod2z = y.mod2z;
    return tmp;
}
node operator * (node x, pii y) {
    node tmp = node();
    tmp.mod1x = x.mod1x, tmp.mod1y = x.mod1y, tmp.mod1z = (x.mod1z * y.first) % mod1;
    tmp.mod2x = x.mod2x, tmp.mod2y = x.mod2y, tmp.mod2z = (x.mod2z * y.second) % mod2;
    return tmp;
}
pii operator * (pii x, pii y) {
    return {x.first * y.first % mod1, x.second * y.second % mod2};
}

signed main()
{
    cin.tie(0); cout.tie(0);
    ios::sync_with_stdio(0);

    int T = 1;
    // cin >> T;
    while (T--) {
        int n;
        cin >> n;
        vector<int> a(n + 5, 0), b(n + 5, 0);
        vector<pii> val(n + 5, {0, 0});
        vector<char> ch(n + 5, 0);

        vector<node> res1(n + 5);
        vector<lr> res2(n + 5);
        vector<int> ff(n + 5, 0), num(n + 5, 0);
        for (int i = 1; i <= n; i++) {
            cin >> ch[i] >> a[i];
            if (ch[i] != 'N') cin >> b[i];
                else num[i] = 1, val[i].first = a[i] % mod1, val[i].second = a[i] % mod2;
        }

        vector<int> vis(n + 5, 0);
        function<void(int)> dfs = [&] (int x) -> void {
            if (vis[x]) {
                return ;
            }

            vis[x] = 1;
            if (ch[x] != 'N') {
                dfs(a[x]);
                dfs(b[x]);
            }

            if (ch[x] == 'N') {
                num[x] = 1;
                ff[x] = 0;
            } else if (ch[x] == '+') {
                if (ff[a[x]] && ff[b[x]]) {
                    ff[x] = 1;
                    res2[x] = {res2[a[x]].l, res2[a[x]].r + res2[b[x]].l + res2[b[x]].r};
                } else if (ff[a[x]] && !ff[b[x]]) {
                    ff[x] = 1;
                    if (!num[b[x]]) {
                        res2[x] = {res2[a[x]].l, res2[a[x]].r + res1[b[x]]};
                    } else {
                        res2[x] = {res2[a[x]].l, res2[a[x]].r + val[b[x]]};
                    }
                } else if (!ff[a[x]] && ff[b[x]]) {
                    ff[x] = 1;
                    if (!num[a[x]]) {
                        res2[x] = {res1[a[x]] + res2[b[x]].l, res2[b[x]].r};
                    } else {
                        res2[x] = {val[a[x]] + res2[b[x]].l, res2[b[x]].r};
                    }
                } else { // !ff[a[x]] && !ff[b[x]]
                    ff[x] = 0;
                    if (!num[a[x]] && !num[b[x]]) {
                        res1[x] = res1[a[x]] + res1[b[x]];
                    } else if (num[a[x]] && !num[b[x]]) {
                        res1[x] = val[a[x]] + res1[b[x]];
                    } else if (!num[a[x]] && num[b[x]]) {
                        res1[x] = res1[a[x]] + val[b[x]];
                    } else {
                        res1[x] = add(val[a[x]], val[b[x]]);
                    }
                }
            } else if (ch[x] == '*') {
                if (ff[a[x]] && ff[b[x]]) {
                    ff[x] = 1;
                    res2[x] = {res2[a[x]].l, (res2[a[x]].r * res2[b[x]].l) + res2[b[x]].r};
                } else if (ff[a[x]] && !ff[b[x]]) {
                    ff[x] = 1;
                    if (!num[b[x]]) {
                        res2[x] = {res2[a[x]].l, res2[a[x]].r * res1[b[x]]};
                    } else {
                        res2[x] = {res2[a[x]].l, res2[a[x]].r * val[b[x]]};
                    }
                } else if (!ff[a[x]] && ff[b[x]]) {
                    ff[x] = 1;
                    if (!num[a[x]]) {
                        res2[x] = {res1[a[x]] * res2[b[x]].l, res2[b[x]].r};
                    } else {
                        res2[x] = {val[a[x]] * res2[b[x]].l, res2[b[x]].r};
                    }
                } else { // !ff[a[x]] && !ff[b[x]]
                    ff[x] = 0;
                    if (!num[a[x]] && !num[b[x]]) {
                        res1[x] = res1[a[x]] * res1[b[x]];
                    } else if (num[a[x]] && !num[b[x]]) {
                        res1[x] = val[a[x]] * res1[b[x]];
                    } else if (!num[a[x]] && num[b[x]]) {
                        res1[x] = res1[a[x]] * val[b[x]];
                    } else {
                        val[x] = val[a[x]] * val[b[x]];
                        num[x] = 1;
                    }
                }
            } else { // <<
                ff[x] = 1;
                if (ff[a[x]] && ff[b[x]]) {
                    res2[x] = {res2[a[x]].l, res2[a[x]].r + res2[b[x]].l + res2[b[x]].r};
                } else if (ff[a[x]] && !ff[b[x]]) {
                    if (!num[b[x]]) {
                        res2[x] = {res2[a[x]].l, res2[a[x]].r + res1[b[x]]};
                    } else {
                        res2[x] = {res2[a[x]].l, res2[a[x]].r + val[b[x]]};
                    }
                } else if (!ff[a[x]] && ff[b[x]]) {
                    if (!num[a[x]]) {
                        res2[x] = {res1[a[x]], res2[b[x]].l + res2[b[x]].r};
                    } else {
                        res2[x] = {{val[a[x]].first, 0, 0, val[a[x]].second, 0, 0}, res2[b[x]].l + res2[b[x]].r};
                    }
                } else { // !ff[a[x]] && !ff[b[x]]
                    if (!num[a[x]] && !num[b[x]]) {
                        res2[x] = {res1[a[x]], res1[b[x]]};
                    } else if (num[a[x]] && !num[b[x]]) {
                        res2[x] = {{val[a[x]].first, 0, 0, val[a[x]].second, 0, 0}, res1[b[x]]};
                    } else if (!num[a[x]] && num[b[x]]) {
                        res2[x] = {res1[a[x]], {0, 0, val[b[x]].first, 0, 0, val[b[x]].second}};
                    } else {
                        res2[x] = {{val[a[x]].first, 0, 0, val[a[x]].second, 0, 0}, {0, 0, val[b[x]].first, 0, 0, val[b[x]].second}};
                    }
                }
            }
        };
        for (int i = 1; i <= n; i++) dfs(i);

        for (int i = 1; i <= n; i++) {
            if (num[i]) {
                cout << val[i].first << "\n";
            } else if (ff[i]) {
                cout << res2[i].gets() << "\n";
            } else {
                cout << res1[i].gets(1) << "\n";
            }
        }
    }

    return 0;
}
```

## [E - 计算几何](https://acm.hdu.edu.cn/contest/problem?cid=1180&pid=1005)

通过判断对两条边 ($p_{i - 1} \to p_i, p_i \to p_{i + 1}$) 叉乘结果的正负，可以判断这个钉子是处于当前方向的左边还是右边，结果为 0 则说明这个点的角度为 180° 不需要放置钉子

接下来只需要判断左边和右边哪边在图形内侧，假设我们用一个最小的矩形将整个图形围起来，与矩形接触，也就是最外侧的点构成的角一定是一个凸角（或者平角），这个位置放置的钉子一定处于图形内侧，找到任意一个点（不能是平角），这个点放钉子的那一侧对应的答案就是 `YES`

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

struct point {
    int x, y;
};

typedef point vec;

int cross(point x, point y) {
    return x.x * y.y - x.y * y.x;
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
        vector<point> p(2 * n + 5, {0, 0});
        for (int i = 1; i <= n; i++) cin >> p[i].x >> p[i].y, p[i + n] = p[i];
        int st = 0;
        int tx = inf64, ty = inf64;
        for (int i = 1; i <= n; i++) {
            if (p[i].x < tx || (p[i].x == tx && p[i].y < ty)) {
                st = i;
                tx = p[i].x, ty = p[i].y;
            }
        }
        p[0] = p[n];
        
        vector<pair<point, int>> ans;
        int std = 0;
        for (int i = st; i < st + n; i++) {
            vec a = {p[i].x - p[i - 1].x, p[i].y - p[i - 1].y};
            vec b = {p[i + 1].x - p[i].x, p[i + 1].y - p[i].y};
            int tmp = cross(a, b);
            if (!tmp) continue;
            if (i == st) std = tmp / abs(tmp);
            ans.push_back({{p[i].x, p[i].y}, tmp / abs(tmp)});
        }

        sort(ans.begin(), ans.end(), [&](pair<point, int> x, pair<point, int> y) {
            if (x.first.x != y.first.x) return x.first.x < y.first.x;
            return x.first.y < y.first.y;
        });
        
        cout << ans.size() << "\n";
        for (auto [tmp, typ] : ans) {
            auto [x, y] = tmp;
            cout << x << ' ' << y << ' ' << (typ == std ? "YES" : "NO") << "\n";
        }
    }

    return 0;
}
```

## [F - 括号匹配](https://acm.hdu.edu.cn/contest/problem?cid=1180&pid=1006)

前置芝士：马拉车

对于一个合法的括号序来说，里面一定包含一个 `()` ，所以第二个条件等价于串中包含 `()`

用两个 `set` 分别保存所有 `()` 中 `(` 和 `)` 的位置，然后用马拉车计算出以每个字符为中心的最长回文串半径 $p_i$ ，在 `set` 中找到离中心最近的括号（在中心左侧的 `(` 或在中心右侧的 `)`）的距离，这个中心的贡献就是 $\frac{p_i - dis}{2}$

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

string t = "#";

vector<int> manacher(string s) {
    for (auto c : s) {
        t += c;
        t += '#';
    }
    int n = t.size();
    vector<int> r(n);
    for (int i = 0, j = 0; i < n; i++) {
        if (2 * j - i >= 0 && j + r[j] > i) {
            r[i] = min(r[2 * j - i], j + r[j] - i);
        }
        while (i - r[i] >= 0 && i + r[i] < n && t[i - r[i]] == t[i + r[i]]) {
            r[i] += 1;
        }
        if (i + r[i] > j + r[j]) {
            j = i;
        }
    }
    return r;
}

signed main()
{
    cin.tie(0); cout.tie(0);
    ios::sync_with_stdio(0);

    int T = 1;
    // cin >> T;
    while (T--) {
        string str;
        cin >> str;
        int n = str.length() * 2 - 1, len = str.length();
        auto p = manacher(str);

        set<int> left, right;
        for (int i = 0; i < n; i++) {
            if (t[i] == '(' && i + 2 < n && t[i + 2] == ')') {
                left.insert(i);
            }
            if (t[i] == ')' && i >= 2 && t[i - 2] == '(') {
                right.insert(i);
            }
        }

        int ans = 0;
        for (int i = 0; i < n; i++) {
            auto it = left.lower_bound(i);
            int dis = inf64;
            if (!left.empty() && it != left.begin()) {
                --it;
                dis = min(dis, i - *it + 1);
            }
            it = right.upper_bound(i);
            if (it != right.end()) {
                dis = min(dis, *it - i + 1);
            }

            ans += max(0ll, (p[i] - dis + 1) / 2);
        }

        cout << ans << "\n";
    }

    return 0;
}
```

## [I - 乘法逆元](https://acm.hdu.edu.cn/contest/problem?cid=1180&pid=1009)

将原式乘开得到：


$$\oplus_{i = 1}^{p - 1}(i \times inv(i) + i * 2^k + inv(i) * 2^{2k} + 2^{3k})$$

当 $2^k \geq p^2 \geq i \times inv(i)$ 时，即 $p \geq 2618$ 时， 这四个和在二进制下不会相交，式子就可以转化为

$$\oplus_{i = 1}^{p - 1}(i \times inv(i)) + \oplus_{i = 1}^{p - 1}(i * 2^k) + \oplus_{i = 1}^{p - 1}(inv(i) * 2^{2k}) + \oplus_{i = 1}^{p - 1}(2^{3k})$$

令 $a, b, c, d$ 分别表示上面的不包含 $2^x$ 的四个异或和

$p$ 为质数， $i \times inv(i)$ 两两对称，所以 $a = inv(i) \oplus (p - 1) \times inv(p - 1)$

从 0 开始每 4 个数为一组，异或和为 0 

$$
b = \begin{cases}
x & x = 4t\\
1 & x = 4t + 1\\
x + 1 & x = 4t + 2\\
0 & x = 4t + 3
\end{cases}
$$

对第三部分打表发现，当 $p\ \%\ 4 = 3$ 时，$c = p$，否则 $c = p - 1$

第四部分固定，$d = [p\ \%\ 2 == 0]$

将每个部分乘上幂再加起来即可

当 $p < 2618$ 时，$max(k) = 22$，不会溢出 `long long` ，直接模拟计算即可

---

惨痛教训：$(p - 1) * inv(p - 1)$ 会溢出 `long long`，记得开 `i128`

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

int mod = 998244353;

const int maxn = 1e4 + 5;

int INV[maxn];

void init(int n) {
    INV[1] = 1;
    for (int i = 2; i <= n; i++)
        INV[i] = 1ll * INV[mod % i] * (mod - mod / i) % mod;
}

i128 qpow(i128 x, i128 a) {
    i128 res = 1;
    while (a) {
        if (a & 1) res = res * x;
        x *= x;
        a >>= 1;
    }
    return res;
}

const int mod99 = 998244353;

i128 mypow(i128 x, i128 a) {
    i128 res = 1;
    while (a) {
        if (a & 1) res = (res * x) % mod99;
        x = (x * x) % mod99;
        a >>= 1;
    }
    return res;
}

LL inv(LL a) {
    if (a == 1) return 1;
    return inv(mod % a) * (mod - mod / a) % mod;
}

i128 ii(int x) {
    if (x % 4 == 0) return x;
    if (x % 4 == 1) return 1;
    if (x % 4 == 2) return x + 1;
    if (x % 4 == 3) return 0;   
}

signed main()
{
    cin.tie(0); cout.tie(0);
    ios::sync_with_stdio(0);

    int T = 1;
    cin >> T;
    while (T--) {
        int p;
        cin >> p;
        mod = p;
        i128 ans = 0;
        if (p >= 3000) {
            i128 a, b, c, d;
            a = (inv(1)) ^ ((i128)(p - 1) * inv(p - 1)); // i * inv(i)
            b = ii(p - 1); // i * 2^k
            if (p % 4 == 3) c = p; // inv(i) * 4^k
                else c = p - 1;
            d = (p ^ 1) & 1; // 2^k * 4^k

            int k = (p + 118) / 119;
            i128 k2 = mypow(2, k), k4 = mypow(4, k);

            ans = a % mod99;
            ans = (ans + b * k2) % mod99;
            ans = (ans + c * k4) % mod99;
            ans = (ans + d * (k2 * k4) % mod99) % mod99;
        } else {
            init(p);
            i128 k = (p + 118) / 119;
            i128 k2 = qpow(2, k), k4 = qpow(4, k);
            
            for (int i = 1; i < p; i++) {
                ans ^= (INV[i] + k2) * (i + k4);
            }
        }

        ans %= 998244353;
        cout << (int)ans << "\n";
    }

    return 0;
}
```

## [J - 阿斯蒂芬](https://acm.hdu.edu.cn/contest/problem?cid=1180&pid=1010)

笑点解析：$1 \leq a_i \leq n$ 与 $a_i \leq 2$ 与 $0 \leq a_i \leq 2$

---

先把原图缩成一个 DAG ，对于一个大小大于 2 的点，这个点的初始能量和必须为 0 ，并且不能有能量输入，否则就要将这个点及其下游的所有点断开连接。

按照拓扑顺遍历即可，遇到大小大于 2 并且有能量或者有能力输入的点打上标记，传递给下游，最终答案为打上标记的点的大小和

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

struct SCC {
    int n;
    vector<vector<int>> G;
    vector<int> dfn, low, ins, stk;
    vector<int> col, siz;
    int id, colcnt;

    SCC() {}
    SCC(int x) {
        init(x);
    }
    void init(int x) {
        n = x;
        G.assign(n + 5, {});
    }
    void add_edge(int x, int y) {
        G[x].push_back(y);
    }
    void dfs(int cur) {
        dfn[cur] = low[cur] = ++id;
        stk.push_back(cur), ins[cur] = 1;
        for (auto v : G[cur]) {
            if (!dfn[v]) {
                dfs(v);
                low[cur] = min(low[cur], low[v]);
            } else if (ins[v]) {
                low[cur] = min(low[cur], dfn[v]);
            }
        }
        
        if (dfn[cur] == low[cur]) {
            ++colcnt;
            int x = stk.back();
            while (x != cur) {
                col[x] = colcnt;
                ins[x] = 0;
                ++siz[colcnt];
                stk.pop_back();
                x = stk.back();
            }
            col[cur] = colcnt;
            ins[cur] = 0;
            ++siz[colcnt];
            stk.pop_back();
        }
    }
    void gets() {
        dfn.assign(n + 5, 0);
        low.assign(n + 5, 0);
        ins.assign(n + 5, 0);
        col.assign(n + 5, 0);
        siz.assign(n + 5, 0);
        stk.clear();
        id = colcnt = 0;

        for (int i = 1; i <= n; i++)
            if (!dfn[i]) dfs(i);
    }
    int diff(int x, int y) {
		return col[x] != col[y];
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
        vector<int> a(n + 5, 0), b(n + 5, 0);
        vector<pii> e;
        SCC G(n);
        for (int i = 1; i <= n; i++) cin >> a[i];
        for (int i = 1; i <= n; i++) cin >> b[i];

        for (int i = 1; i <= n; i++) {
            int x;
            for (int j = 1; j <= b[i]; j++) {
                cin >> x;
                G.add_edge(i, x);
                e.push_back({i, x});
            }
        }
        G.gets();
        
        set<pii> st;
        vector<vector<int>> TG(G.colcnt + 5);
        vector<int> in(G.colcnt + 5, 0), ff(G.colcnt + 5, 0), val(G.colcnt + 5, 0), sum(G.colcnt + 5, 0);
        for (int i = 1; i <= n; i++) val[G.col[i]] += a[i];
        for (auto [x, y] : e) {
            x = G.col[x], y = G.col[y];
            if (x == y || st.count({x, y})) continue;
            st.insert({x, y});
            in[y] += 1;
            TG[x].push_back(y);
        }

        queue<int> q;
        for (int i = 1; i <= G.colcnt; i++) 
            if (!in[i]) q.push(i);
        
        int ans = 0;
        while (!q.empty()) {
            int cur = q.front();
            q.pop();
            
            sum[cur] += val[cur];
            if (G.siz[cur] > 1 && sum[cur]) ff[cur] = 1;
            for (auto v : TG[cur]) {
                ff[v] |= ff[cur];
                sum[v] += sum[cur];
                in[v] -= 1;
                if (!in[v]) q.push(v);
            }
            if (ff[cur]) ans += G.siz[cur];
        }

        cout << ans << "\n";
    }

    return 0;
}
```

## [L - 测试若歪](https://acm.hdu.edu.cn/contest/problem?cid=1180&pid=1012)

将一血顺序和通过量顺序挨个比较，题目不限制 $\sum n$ 的范围，用 `map` 保存即可

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
        cin >> m >> n;

        map<int, int> mp;
        vector<pii> a;
        vector<int> b;
        for (int i = 1; i <= n; i++) {
            int x;
            cin >> x;
            if (!mp.count(x)) {
                b.push_back(x);
            }
            ++mp[x];
        }
        for (auto [x, y] : mp)
            a.push_back({x, y});
        sort(a.begin(), a.end(), [&](pii x, pii y){
            if (x.second != y.second) return x.second > y.second;
            return x.first < y.first;
        });

        int ans = 0;
        for (int i = 0; i < mp.size(); i++)
            ans += (b[i] != a[i].first);
        cout << ans << "\n";
    }

    return 0;
}
```

