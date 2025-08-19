---
title: 'VP 2024 CCPC Harbin Site - ABCEGJKLM'
description: ''
publishDate: 2024-11-15
tags:
  - ACM
  - CF
---


<!-- more -->

[VP排名](https://codeforces.com/gym/105459/standings/participant/196882790#p196882790)

[榜](https://board.xcpcio.com/ccpc/10th/harbin)

第一次 vp 罚时直接爆炸，感觉ccpc好难啊，大家都好会做😭

## [M - Weird Ceiling](https://codeforces.com/gym/105459/problem/M)

签到1

把因数分解出来每段计算

### Code

```cpp
#include<bits/stdc++.h>
using namespace std;

// #define int long long

typedef pair<int, int> pii;
typedef long long LL;
typedef unsigned long long uLL;

const int inf32 = 1e9;
const LL inf64 = 1e18;

LL read() {
    LL x = 0, w = 1;
    char ch = 0;
    while (ch < '0' || ch > '9') {
        if (ch == '-') w = 0;
        ch = getchar();
    }
    while (ch >= '0' && ch <= '9') {
        x = x * 10 + (ch ^ 48);
       ch = getchar();
    }
    return w ? x : -x;
}

int main()
{
    cin.tie(0); cout.tie(0);
    ios::sync_with_stdio(0);

    int T = 1;
    T = read();
    while (T--) {
        int n;
        n = read();
        vector<int> p;
        for (int i = 1; i * i <= n; i++) {
            if (n % i == 0) {
                p.push_back(i);
                if (i * i != n) p.push_back(n / i);
            }
        }

        sort(p.begin(), p.end());

        int len = p.size();
        LL ans = 0;
        for (int i = 0; i < len - 1; i++) {
            ans += 1ll * (p[i + 1] - p[i]) * (n / p[i]);
        }

        cout << ans + 1 << "\n";
    }

    return 0;
}
```

## [C - Giving Directions in Harbin](https://codeforces.com/gym/105459/problem/C)

签到2

每条指令分解成两个：转弯和直行

### Code

```cpp
#include<bits/stdc++.h>
using namespace std;

#define int long long

typedef pair<int, int> pii;
typedef long long LL;
typedef unsigned long long uLL;

const int inf32 = 1e9;
const LL inf64 = 1e18;

const char dx[5] = {0, 'N', 'E', 'S', 'W'};
const char opt[4] = {0, 'Z', 'L', 'R'};

signed main()
{
    cin.tie(0); cout.tie(0);
    ios::sync_with_stdio(0);

    map<char, int> mp;
    mp['N'] = 1;
    mp['E'] = 2;
    mp['S'] = 3;
    mp['W'] = 4;

    int T = 1;
    cin >> T;
    while (T--) {
        int n;
        cin >> n;

        string str;
        int x;
        cin >> str >> x;

        int way = mp[str[0]];
        int ini = way, tmp = x;
        vector<pii> ans;
        ans.push_back({1, x});
        for (int i = 2; i <= n; i++) {
            cin >> str >> x;
            int newway = mp[str[0]];
            if (newway - way == 1 || (newway == 1 && way == 4)) ans.push_back({3, 0});
                else ans.push_back({2, 0});
            way = newway;
            ans.push_back({1, x});
        }

        cout << ans.size() << ' ' << dx[ini] << "\n";
        for (auto [x, y] : ans) {
            if (x == 1) {
                cout << opt[x] << ' ' << y << "\n";
            } else {
                cout << opt[x] << "\n";
            }
        }
    }

    return 0;
}
```

## [G - Welcome to Join the Online Meeting!](https://codeforces.com/gym/105459/problem/G)

根据题意加边，只要一条边连接的两个不都是在忙的人就加边，然后从任意一个不在忙的人开始bfs，如果能全部邀请入会则有解，否则无解

### Code

```cpp
#include<bits/stdc++.h>
using namespace std;

#define int long long

typedef pair<int, int> pii;
typedef long long LL;
typedef unsigned long long uLL;

const int inf32 = 1e9;
const LL inf64 = 1e18;

LL read() {
    LL x = 0, w = 1;
    char ch = 0;
    while (ch < '0' || ch > '9') {
        if (ch == '-') w = 0;
        ch = getchar();
    }
    while (ch >= '0' && ch <= '9') {
        x = x * 10 + (ch ^ 48);
       ch = getchar();
    }
    return w ? x : -x;
}

struct DSU {
    vector<int> f, siz;
    int n;

    DSU() {};
    DSU(int x) {
        init(x);
    }
    void init(int x) {
        n = x;
        f.assign(n + 5, 0);
        iota(f.begin() + 1, f.end(), 1);
        siz.assign(n + 5, 1);
    }
    int find(int x) {
        return f[x] == x ? x : f[x] = find(f[x]);
    }
    bool same(int x, int y) {
        return find(x) == find(y);
    }
    void merge(int x, int y) {
        x = find(x); y = find(y);
        if (x == y) return ;
        siz[x] += siz[y];
        f[y] = x;
    }
    int size(int x) {
        return siz[find(x)];
    }
    int count() {
        vector<int> v(n + 5, 0);
        int cnt = 0;
        for (int i = 1; i <= n; i++) {
            int x = find(i);
            if (v[x]) continue;
            v[x] = 1; ++cnt;
        }
        return cnt;
    }
};

signed main()
{
    cin.tie(0); cout.tie(0);
    ios::sync_with_stdio(0);

    int T = 1;
    // T = read();
    while (T--) {
        int n, m, k;
        n = read(); m = read(); k = read();
        vector<int> a(k + 5, 0), ff(n + 5, 0);
        vector<vector<int>> G(n + 5);
        for (int i = 1; i <= k; i++) a[i] = read(), ff[a[i]] = 1;

        for (int i = 1; i <= m; i++) {
            int x, y;
            x = read(); y = read();
            if (!ff[x]) G[x].push_back(y);
            if (!ff[y]) G[y].push_back(x);
        }

        queue<int> q;
        vector<int> vis(n + 5, 0);
        vector<pair<int, vector<int>>> ans;
        int cnt = 0;
        for (int i = 1; i <= n; i++)
            if (!ff[i]) {
                q.push(i); 
                vis[i] = 1; 
                break;
            }

        while (!q.empty()) {
            auto cur = q.front();
            q.pop();

            vector<int> tmp;
            for (auto v : G[cur]) {
                if (vis[v]) continue;
                tmp.push_back(v);
                vis[v] = 1;
                q.push(v);
            }
            if (!tmp.empty()) {
                ans.push_back({cur, tmp});
                ++cnt;
            }
        }

        int fff = 0;
        for (int i = 1; i <= n; i++)
            if (!vis[i]) {fff = 1; break;}
        if (fff) {
            cout << "No\n";
            continue;
        }

        cout << "Yes\n";
        cout << cnt << "\n";
        for (auto &[x, S] : ans) {
            cout << x << ' ' << S.size() << ' ';
            for (auto y : S)
                cout << y << ' ';
            cout << "\n";
        }
    }

    return 0;
}
```

## [K - Farm Management](https://codeforces.com/gym/105459/problem/K)

首先先把每种作物的 $l_i$ 填满，然后按照价值从高到低用完剩下的活动时间。

接下来会有两种情况：

1. 解除某一种作物的限制，将它的时间尽可能给价值更高的作物，如果还有剩余时间就还给这个作物
2. 解除价值最高的作物的限制，其他作物处理时间全部是 $l_i$ ，剩下的时间全给价值最高的作物

从高到低预处理出每种作物还剩的多少处理时间的前缀和，就可以二分计算每一种作物的第一种情况。第二章可以直接计算

### Code

```cpp
#include<bits/stdc++.h>
using namespace std;

#define int long long

typedef pair<int, int> pii;
typedef long long LL;
typedef unsigned long long uLL;

const int inf32 = 1e9;
const LL inf64 = 1e18;

LL read() {
    LL x = 0, w = 1;
    char ch = 0;
    while (ch < '0' || ch > '9') {
        if (ch == '-') w = 0;
        ch = getchar();
    }
    while (ch >= '0' && ch <= '9') {
        x = x * 10 + (ch ^ 48);
       ch = getchar();
    }
    return w ? x : -x;
}

struct node {
    int w, l, r, val;
};

signed main()
{
    cin.tie(0); cout.tie(0);
    ios::sync_with_stdio(0);

    int T = 1;
    // T = read();
    while (T--) {
        int n, m;
        n = read(); m = read();
        int M = m;
        
        vector<node> a(n + 5, {0, 0, 0});
        LL ans = 0;
        for (int i = 1; i <= n; i++) {
            a[i] = {read(), read(), read(), 0};
            m -= a[i].l;
            ans += a[i].w * a[i].l;
            a[i].val = a[i].r - a[i].l;
        }
        sort(a.begin() + 1, a.begin() + 1 + n, [&](node x, node y){
            return x.w < y.w;
        });

        for (int i = n; i; i--) {
            if (!m) break;
            if (a[i].val > m) {
                ans += a[i].w * m;
                a[i].val -= m;
                m = 0;
            } else {
                ans += a[i].w * a[i].val;
                m -= a[i].val;
                a[i].val = 0;
            }
        }
        
        vector<int> stk(n + 5, 0);
        vector<pii> s(n + 5, {0, 0});
        int cnt = 0;
        for (int i = n; i; i--) {
            auto &[w, l, r, val] = a[i];
            if (!val) continue;

            stk[++cnt] = val;
            stk[cnt] += stk[cnt - 1];
            s[cnt] = {w, val * w};
            s[cnt].second += s[cnt - 1].second;
        }

        int base = ans;
        for (int i = 1; i <= n; i++) {
            auto &[w, l, r, val] = a[i];

            int tmp = base;
            int res = l;
            tmp -= res * w;

            int L = 1, R = cnt, tag = 0;
            while (L <= R) {
                int mid = (L + R) >> 1;
                if (stk[mid] >= res) R = mid - 1, tag = mid;
                    else L = mid + 1;
            }

            if (!tag) {
                res -= stk[cnt];
                tmp += s[cnt].second;
                tmp += res * w;
            } else {
                tmp += s[tag].second;
                tmp -= max(0ll, (stk[tag] - res) * s[tag].first);
            }

            ans = max(ans, tmp);
        }

        int tmp = 0;
        for (int i = 1; i < n; i++) {
            auto &[w, l, r, val] = a[i];
            M -= l;
            tmp += w * l;
        }
        tmp += a[n].w * M;
        ans = max(ans, tmp);

        cout << ans << "\n";
    }

    return 0;
}
```

## [J - New Energy Vehicle](https://codeforces.com/gym/105459/problem/J)

首先肯定是先用最近的加油站的油箱，其次是次进的，以此类推。考虑如何模拟这个过程

先用优先队列存储两个信息，加油站的位置和对应的油箱，以加油站的位置为关键字，每到一个加油站，将对应油箱加满，并且添加最近的同一油箱的加油站。

如果上个油箱没用完，就将当前的加油站位置和油箱编号重新添加回去。这样可能会出现当前油箱加油站位置在车前面的情况，这时直接 continue 即可

### Code

```cpp
#include<bits/stdc++.h>
using namespace std;

#define int long long

typedef pair<int, int> pii;
typedef long long LL;
typedef unsigned long long uLL;

const int inf32 = 1e9;
const LL inf64 = 1e18;

LL read() {
    LL x = 0, w = 1;
    char ch = 0;
    while (ch < '0' || ch > '9') {
        if (ch == '-') w = 0;
        ch = getchar();
    }
    while (ch >= '0' && ch <= '9') {
        x = x * 10 + (ch ^ 48);
       ch = getchar();
    }
    return w ? x : -x;
}

signed main()
{
    cin.tie(0); cout.tie(0);
    ios::sync_with_stdio(0);

    int T = 1;
    T = read();
    while (T--) {
        int n, m;
        n = read(); m = read();
        vector<int> a(n + 5, 0);
        for (int i = 1; i <= n; i++) a[i] = read();
        auto val = a;
        vector<pii> b(m + 5, {0, 0});
        for (int i = 1; i <= m; i++) b[i] = {read(), read()};
        b[m + 1] = {inf64, 0};

        vector<int> succ(m + 5, m + 1), pos(n + 5, m + 1);
        for (int i = m; i; i--) {
            succ[i] = pos[b[i].second];
            pos[b[i].second] = i;
        }

        priority_queue<pii> q;
        for (int i = 1; i <= m; i++) q.push({-i, b[i].second});
        for (int i = 1; i <= n; i++) q.push({-inf64, i});

        int now = 0, aim_id = 1;
        while (!q.empty()) {
            auto [x, y] = q.top();
            q.pop();

            if (-x < aim_id) continue;
            if (!val[y]) continue;

            int aim = b[aim_id].first;
            int del = aim - now;
            if (del > val[y]) {
                now += val[y];
                val[y] = 0;
            } else {
                val[y] -= del;
                now += del;
                int upd_id = b[aim_id].second;
                q.push({x, y});
                q.push({-inf64, y});
                val[upd_id] = a[upd_id];
                q.push({-succ[aim_id], upd_id});
                ++aim_id;
            }
        }

        cout << now << "\n";
    }

    return 0;
}
```

## [L - A Game On Tree](https://codeforces.com/gym/105459/problem/L)

将每条边的权值均设为 1 ，那么树上一条路径的贡献就是这条路径上的权值和的平方，即 $(\sum\limits_{i \in S}e_i)^2$ ，这样就可以发现，每个条边的贡献是经过这条边的路径长度和

令 $cnt_i$ 表示两条路径的一个端点都在当前子树的情况数量， $E_i$ 表示子树的期望，不难推出 $cnt_i = (siz_i) ^ 2$ , $E_i = E_v + cnt_v$ 即子树的期望和再加上两个端点都在子树里的情况数量

假设现在遍历到了某条边 $(u, v)$ ，对于子树 u 的所有情况， $E_v$ 始终是有贡献的，对于子树 v 的所有情况， $E_u$ 也始终有贡献，所以这条边的贡献就是 $cnt_u \times E_v + cnt_v \times E_u + (cnt_u * cnt_v)$ ，（这里的子树 u 指的是从节点 u 向上遍历的子树，在遍历时计算一些奇奇怪怪的遍历就能维护）

（关于写题解的时候距离vp已经过去两周了，记不起一些细节了，就写的比较简单，反正核心就是维护向上的子树的 $cnt$ 与 $E$）

### Code

```cpp
#include<bits/stdc++.h>
using namespace std;

// #define int long long

typedef long long LL;
typedef pair<LL, LL> pii;
typedef unsigned long long uLL;

const int inf32 = 1e9;
const LL inf64 = 1e18;

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
        if (x < 0) y += P;
        return x;
    }
};
using mint = ModInt<P>;

int main()
{
    cin.tie(0); cout.tie(0);
    ios::sync_with_stdio(0);

    int T = 1;
    cin >> T;
    while (T--) {
        LL n;
        cin >> n;
        vector<vector<LL>> G(n + 5);
        for (int i = 1; i < n; i++) {
            int x, y;
            cin >> x >> y;
            G[x].push_back(y), G[y].push_back(x);
        }

        vector<mint> siz(n + 5, 0);
        vector<mint> cnt(n + 5, 0), E(n + 5, 0);
        auto dfs1 = [&] (auto && dfs1, int cur, int ftr) -> void {
            siz[cur] = 1;
            for (auto v : G[cur]) {
                if (v == ftr) continue;
                dfs1(dfs1, v, cur);
                siz[cur] += siz[v];

                E[cur] += E[v] + cnt[v];
            }
            cnt[cur] += siz[cur] * siz[cur];
        };
        dfs1(dfs1, 1, 0);

        mint ans = 0;
        auto dfs = [&] (auto &&dfs, int cur, int ftr, mint ftr_cnt, mint ftr_E, mint ftr_sz) -> void {

            ftr_sz = n - siz[cur];
            mint tot_cnt = ftr_cnt, tot_E = ftr_E + ftr_cnt;
            mint cnt_sq = ftr_sz * ftr_sz;
            for (auto v : G[cur]) {
                if (v == ftr) continue;

                tot_E += E[v] + cnt[v];;
                tot_cnt += cnt[v];
                cnt_sq += siz[v] * siz[v];
            }

            for (auto v : G[cur]) {
                if (v == ftr) continue;

                tot_cnt += (n - siz[v]) * (n - siz[v]);
                tot_cnt -= cnt_sq;
                tot_cnt += siz[v] * siz[v];

                tot_E -= E[v] + cnt[v];
                tot_cnt -= cnt[v];

                dfs(dfs, v, cur, tot_cnt, tot_E, n - siz[cur] + 1);


                ans += tot_cnt * E[v] + cnt[v] * tot_E + (cnt[v] * tot_cnt);

                tot_cnt -= (n - siz[v]) * (n - siz[v]);
                tot_cnt += cnt_sq;
                tot_cnt -= siz[v] * siz[v];

                tot_E += E[v] + cnt[v];
                tot_cnt += cnt[v];
            }


        };
        dfs(dfs, 1, 0, 0, 0, 0);
        
        mint div = 1;
        div = 1ll * n * (n - 1) / 2;
        cout << ans / div / div << "\n";
    }

    return 0;
}
```




## [B - Concave Hull](https://codeforces.com/gym/105459/problem/B)

首先最大的凸包对应的点一定在点集内。

如果所有点都是最大凸包对应的点击，说明无法构成凹包，输出 -1

**证明：**

如果存在一种凹包的点集不完全包含最大凸包，这些点一定在凸包内，选上凸包的点一会让面积变大，而且新图形仍然是个凹包。

先计算出凸包的面积，此时再任选任一个点都会形成凹包。因为没选一个点都会让面积变小，所以肯定只选一个。选择的点对面积的影响是 $\left|AB\right| \times dis(C, AB)$ ，即对每条边应选离它最近的点

首先不难发现可能被选择的内层点一定在内层凸包上，如果点在内层凸包内，那么对每条边都有距离比它近的点。假设当前我们找到了一条边和里它最近的内层点，当切换到下一条边时，按同一方向在内层凸包上往后遍历直到新边距离比当前大即可，最后在所有情况中取最优。

### Code

```cpp
#include<bits/stdc++.h>
using namespace std;

#define int long long

typedef pair<int, int> pii;
typedef long long LL;
typedef unsigned long long uLL;

const int inf32 = 1e9;
const LL inf64 = 1e18;

LL read() {
    LL x = 0, w = 1;
    char ch = 0;
    while (ch < '0' || ch > '9') {
        if (ch == '-') w = 0;
        ch = getchar();
    }
    while (ch >= '0' && ch <= '9') {
        x = x * 10 + (ch ^ 48);
       ch = getchar();
    }
    return w ? x : -x;
}

struct node {
    int x, y; 
    node(int x = 0, int y = 0) : x(x), y(y) {}
};
struct _l {
    node a, b;
};

LL dot(node a, node b) {
    return a.x * b.x + a.y * b.y;
}
LL cross(node a, node b) {
    return a.x * b.y - a.y * b.x;
}
LL side(node a, node b, node c) {
    node A = node(b.x - a.x, b.y - a.y);
    node B = node(c.x - a.x, c.y - a.y);
    return cross(A, B);
}
LL S(node a, node b, node c) {
    node A = node(b.x - a.x, b.y - a.y);
    node B = node(c.x - a.x, c.y - a.y);
    return abs(cross(A, B));
}
double len(_l x) {
    node a = x.a, b = x.b;
    LL delx = abs(a.x - b.x);
    LL dely = abs(a.y - b.y);
    return sqrt(delx * delx + dely * dely);
}
node vec(node a, node b) {
    return node(b.x - a.x, b.y - a.y);
}
double dis(node a, _l line) {
    LL s = S(a, line.a, line.b);
    return s / len(line);
}

signed main()
{
    cin.tie(0); cout.tie(0);
    ios::sync_with_stdio(0);

    int T = 1;
    T = read();
    while (T--) {
        int n;
        n = read();
        vector<node> a(n + 5, {0, 0});
        for (int i = 1; i <= n; i++) a[i] = {read(), read()};
        sort(a.begin() + 1, a.begin() + 1 + n, [&](node x, node y){
            if (x.x != y.x) return x.x < y.x;
            return x.y < y.y;
        });

        vector<int> stk;
        vector<int> vis(n + 5, 0);
        int cnt = 1;
        stk.push_back(1), stk.push_back(2);
        for (int i = 3; i <= n; i++) {
            while (cnt && side(a[stk[cnt - 1]], a[stk[cnt]], a[i]) <= 0) {
                --cnt;
                stk.pop_back();
            }
            stk.push_back(i), ++cnt;
        }
        stk.push_back(n - 1), ++cnt;
        for (int i = n - 2; i; i--) {
            while (cnt && side(a[stk[cnt - 1]], a[stk[cnt]], a[i]) <= 0) {
                --cnt;
                stk.pop_back();
            }
            stk.push_back(i), ++cnt;
        }
    
        if (cnt == n) {
            cout << "-1\n";
            continue;
        }

        for (auto x : stk) 
            vis[x] = 1;
        
        int ans = 0;
        int l = 0, r = 0;
        for (int i = 2; i < cnt; i++)
            ans += S(a[stk[1]], a[stk[i]], a[stk[i + 1]]);

        int tmp = ans;
        ans = 0;
        vector<_l> line(cnt + 5, {node(), node()});
        for (int i = 1; i < cnt; i++)
            line[i] = {a[stk[i]], a[stk[i + 1]]};
        line[cnt] = {a[stk[cnt]], a[stk[1]]};

        if (n - stk.size() <= 10) {
            for (int i = 1; i <= n; i++) {
                if (vis[i]) continue;
                for (int j = 1; j <= cnt; j++)
                    ans = max(ans, tmp - S(a[i], line[j].a, line[j].b));
            }
            cout << ans << "\n";
            continue;
        }

        int cnt_line = cnt;

        vector<node> b(n + 5, node());
        int m = 0;
        for (int i = 1; i <= n; i++)
            if (!vis[i]) b[++m] = a[i];

        stk.clear();
        cnt = 1;
        stk.push_back(1), stk.push_back(2);
        for (int i = 3; i <= m; i++) {
            while (cnt && side(b[stk[cnt - 1]], b[stk[cnt]], b[i]) <= 0) {
                --cnt;
                stk.pop_back();
            }
            stk.push_back(i), ++cnt;
        }
        stk.push_back(m - 1), ++cnt;
        for (int i = m - 2; i; i--) {
            while (cnt && side(b[stk[cnt - 1]], b[stk[cnt]], b[i]) <= 0) {
                --cnt;
                stk.pop_back();
            }
            stk.push_back(i), ++cnt;
        }

        l = 1, r = 1;
        LL mn = inf64;

        for (int i = 1; i <= cnt; i++) {
            auto tmp = S(b[stk[i]], line[r].a, line[r].b);
            if (tmp < mn) mn = tmp, l = i;
        }
        ans = max(ans, tmp - S(b[stk[l]], line[r].a, line[r].b));

        int st = r;
        r = r == cnt_line ? 1 : r + 1;
        while (r != st) {
            int nxt = l == cnt ? 1 : l + 1;
            while (dis(b[stk[l]], line[r]) >= dis(b[stk[nxt]], line[r])) {
                ans = max(ans, tmp - S(b[stk[l]], line[r].a, line[r].b));
                l = nxt;
                nxt = l == cnt ? 1 : l + 1;
            }
            ans = max(ans, tmp - S(b[stk[l]], line[r].a, line[r].b));
            r = r == cnt_line ? 1 : r + 1;
        }

        cout << ans << "\n";
    }

    return 0;
}
```

## [E - Marble Race](https://codeforces.com/gym/105459/problem/E)

反悔dp

考虑每个弹珠作为中位数时的贡献，时间就是 $\frac{x_i}{v_i}$ ，对于剩下的球，分别计算此时剩下的每个球有多少个发射点在它之前到，多少个发射点在它之后到，令 $dp_i$ 表示在它之前到的球的个数情况数量，转移方法就是 $dp_{i + 1} += lst_x + left_k,\ \ dp_i += lst_i * (n - left_k)$ ，其中 $left_k$ 表示第 k 个球有落后于当前球的起点数量，这样我们就得到了一个 $n^4$ 的解法

考虑如何优化

将每个球在每个起点的时间记录下来，排序，挨个按顺序计算，就可以避免每次计算 left 数组的开销，此时每次计算下一个情况时，只会有一个球的 left 数量增加 1 ，剩余的 left 均不会改变，考虑用反悔dp。因为每次计算是线性推过去的，反悔时倒着推一编就可以回到未考虑某个球的状态，然后记录答案，更新当前节点的 left 再把它加回 dp 中

时间复杂度 $O(n^3)$
 
### Code

```cpp
#include<bits/stdc++.h>
using namespace std;

// #define int long long

typedef pair<int, int> pii;
typedef long long LL;
typedef unsigned long long uLL;

const int inf32 = 1e9;
const LL inf64 = 1e18;

const int P = 1e9 + 7;
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

const int mod = 1e9 + 7;

LL inv(LL c) {
    LL a = c, b = P, x = 1, y = 0;
    while (b) {
    LL t = a / b;
       a -= t * b; swap(a, b);
        x -= t * y; swap(x, y);
    }
    // if (x < 0) y += P;
    x = (x % mod + mod) % mod;
    return x;
}

LL add(LL a, LL b) {
    return (a + b) % mod;
}
LL mul(LL a, LL b) {
    return a * b % mod;
}
LL sub(LL a, LL b) {
    return add(a, mod - b);
}
LL qpow(LL x, LL a) {
    LL res = 1;
    while (a) {
        if (a & 1) res = mul(res, x);
        x = mul(x, x);
        a >>= 1;
    }
    return res;
}
LL d(LL a, LL b) {
    return a * inv(b) % mod;
}

int main()
{
    cin.tie(0); cout.tie(0);
    ios::sync_with_stdio(0);

    int st = clock();   


    int T = 1;
    // T = read();
    while (T--) {
        int n, m;
        cin >> n >> m;
        vector<LL> a(n + 5, 0), b(m + 5, 0);
        for (int i = 1; i <= n; i++) cin >> a[i], a[i] = abs(a[i]);
        for (int i = 1; i <= m; i++) cin >> b[i];
        sort(a.begin() + 1, a.begin() + 1 + n);

        vector<pii> p;
        for (int i = 1; i <= n; i++)
            for (int j = 1; j <= m; j++)
                p.push_back({i, j});

        sort(p.begin(), p.end(), [&](pii x, pii y) {
            return a[x.first] * b[y.second] < a[y.first] * b[x.second];
        });

        LL ans = 0;
        vector<LL> left(m + 5, 0);
        vector<LL> dp(m + 5, 0);
        dp[0] = 1;
        for (int i = 1; i <= m; i++)
            for (int j = m; j >= 0; j--) {
                dp[j + 1] = add(dp[j + 1], mul(dp[j], left[i]));
                dp[j] = mul(dp[j], (n - left[i]));
            }

        int cnt = 0;
        for (auto [st, ball] : p) {
            ++cnt;

            int INV = inv(sub(n, left[ball]));
            for (int i = 0; i <= m; i++) {
                dp[i] = mul(dp[i], INV);
                dp[i + 1] = sub(dp[i + 1], mul(dp[i], left[ball]));
            }

            ans = add(ans, mul(dp[m / 2], d(abs(a[st]), b[ball])));

            left[ball] += 1;

            for (int i = m; i >= 0; i--) {
                dp[i + 1] = add(dp[i + 1], mul(dp[i], left[ball]));
                dp[i] = mul(dp[i], sub(n, left[ball]));
            }
        }

        cout << d(ans, qpow(n, m)) << "\n";
    }

    return 0;
}
```

## [A - Build a Computer](https://codeforces.com/gym/105459/problem/A)

### Code

```cpp
#include<bits/stdc++.h>
using namespace std;

#define int long long

typedef pair<int, int> pii;
typedef long long LL;
typedef unsigned long long uLL;

const int inf32 = 1e9;
const LL inf64 = 1e18;

LL read() {
    LL x = 0, w = 1;
    char ch = 0;
    while (ch < '0' || ch > '9') {
        if (ch == '-') w = 0;
        ch = getchar();
    }
    while (ch >= '0' && ch <= '9') {
        x = x * 10 + (ch ^ 48);
       ch = getchar();
    }
    return w ? x : -x;
}

signed main()
{
    cin.tie(0); cout.tie(0);
    ios::sync_with_stdio(0);

    int T = 1;
    // T = read();
    while (T--) {
        int x, y;
        x = read(); y = read() + 1;

        int S = 1, T = 2;
        int tot = 2;
        vector<int> bit(100, 0);
        vector<vector<pii>> ans(100);
        bit[0] = 2;

        auto make = [&] (auto &&make, int l, int r) -> int {
            cerr << l << ' ' << r << endl;
            if (x <= l && r <= y) {
                int len = r - l;
                int i = 0;
                for (; (1 << i) < len; i++) {
                    if (!bit[i + 1]) {
                        int lst = bit[i];
                        bit[i + 1] = ++tot;
                        ans[tot].push_back({lst, 0});
                        ans[tot].push_back({lst, 1});
                    }
                }
                return bit[i];
            }

            int mid = (l + r) >> 1;
            int id = ++tot;
            if (x < mid) ans[id].push_back({make(make, l, mid), 0});
            if (y > mid) ans[id].push_back({make(make, mid, r), 1});
            return id;
        };
        
        for (int i = 0; (1 << i) < y; i++) {
            if (x >= (1 << i + 1)) continue;
            
            int id = make(make, 1 << i, 1 << i + 1);
            ans[S].push_back({id, 1});
        }

        cout << tot << "\n";
        for (int i = 1; i <= tot; i++) {
            cout << ans[i].size() << ' ';
            for (auto [v, num] : ans[i])
                cout << v << ' ' << num << ' ';
            cout << "\n";
        }
    }

    return 0;
}
```