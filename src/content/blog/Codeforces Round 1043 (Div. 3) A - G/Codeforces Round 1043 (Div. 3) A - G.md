---
title: 'Codeforces Round 1043 (Div. 3) A - G'
description: ''
publishDate: 2025-08-23
tags:
  - ACM
  - CF
heroImage: {src: './cover.jpeg', color: '#8de2cdff' }
---

## [A - Homework](https://codeforces.com/contest/2132/problem/A)

倒着输出 b 串对应的 V ，输出 a ，正着输出 b 串对应的 D

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
        string a, b, opt;
        cin >> n;
        cin >> a;
        cin >> m;
        cin >> b;
        cin >> opt;
        
        for (int i = m; i >= 1; i--)
            if (opt[i - 1] == 'V') cout << b[i - 1];
            cout << a;
        for (int i = 1; i <= m; i++)
            if (opt[i - 1] == 'D') cout << b[i - 1];
        cout << "\n";
    }

    return 0;
}
```

## [B - The Secret Number](https://codeforces.com/contest/2132/problem/B)

假设增加了 $t$ 个 0 ，那么 $n = x + y = x + x \times 10^t = (10^t + 1) \times x$ ，枚举这个 $10^t + 1$ 看能不能除尽 $n$ 即可

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

        i128 v = 1;
        vector<int> ans;
        while (1) {
            v *= 10;
            if (v + 1 > n) break;
            if (n % (v + 1) == 0) ans.push_back(n / (v + 1));
        }

        cout << ans.size() << "\n";
        if (!ans.empty()) {
            ranges::sort(ans);
            for (auto x : ans)
                cout << x << ' ';
            cout << "\n";
        }

    }

    return 0;
}
```

## [C1 - The Cunning Seller (easy version)](https://codeforces.com/contest/2132/problem/C1)

要求够买次数最少，即为三进制下每一位的和，预处理一下每个 $x$ 的花费，加起来即可

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

int t[30];

void init(int n) {
    int x = 0, std = 1;
    t[0] = 3;
    for (int i = 1; i <= n; i++) {
        x += 1, std *= 3;
        t[i] = std * 3 + x * std / 3;
    }
}
signed main()
{
    cin.tie(0); cout.tie(0);
    ios::sync_with_stdio(0);

    init(20);

    int T = 1;
    cin >> T;
    while (T--) {
        int n;  
        cin >> n;

        int id = 0, ans = 0;
        while (n) {
            ans += (n % 3) * t[id];
            ++id;
            n /= 3;
        }

        cout << ans << "\n";
    }

    return 0;
}
```

## [C2 - The Cunning Seller (hard version)](https://codeforces.com/contest/2132/problem/C2)

先计算出 `C1` 中的最小购买次数，若小于 $k$ 则无解。每次操作可以将一个 $3^x$ 变成三个 $3^{x - 1}$ ，即通过两次额外的购买将一个 $x$ 分解为三个 $x - 1$

观察一下购买的花费，一定是优先分解较大的 $x$ 更优，用一个数组 $cnt$ 记录一下每个 $x$ 出现的次数，扫一遍模拟分解即可

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

int t[30];

void init(int n) {
    int x = 0, std = 1;
    t[0] = 3;
    for (int i = 1; i <= n; i++) {
        x += 1, std *= 3;
        t[i] = std * 3 + x * std / 3;
    }
}
signed main()
{
    cin.tie(0); cout.tie(0);
    ios::sync_with_stdio(0);

    init(20);

    int T = 1;
    cin >> T;
    while (T--) {
        int n, k;
        cin >> n >> k;

        int id = 0, mn = 0, tn = n, ans = 0;
        vector<int> cnt(25, 0);
        while (tn) {
            mn += (tn % 3);
            ans += (tn % 3) * t[id];
            cnt[id] += tn % 3;
            ++id;
            tn /= 3;
        }

        if (mn > k) {
            cout << "-1\n";
            continue;
        }

        k -= mn;
        for (int i = 20; i >= 1; i--) {
            if (k < 2) break;
            int times = min(k / 2, cnt[i]);
            ans -= times * (t[i] - 3 * t[i - 1]);
            cnt[i - 1] += times * 3;
            k -= times * 2;
        }

        cout << ans << "\n";
    }

    return 0;
}
```

## [D - From 1 to Infinity](https://codeforces.com/contest/2132/problem/D)

我觉得是全场最难（x

---

先预处理出全部长度为 $i$ 的数组需要占用多少位，和为多少，记 $tot_i$ 为从 $0$ 到 $10^i - 1$ 所有数每一位的和，占用位数 $cnt_i = 9 \times 10^{i - 1}$ ，和 $sum_i$ = $9 \times tot_{i - 1} + 45 \times 10^{i - 1}$ （最高位从 `1xxx` 到 `9xxx` 会经历 $10^{i - 1}$ 组 `123456789` ，每一组的和为 45 ，较低位则会完整经历 9 次 $tot_{i - 1}$

然后我们找到 $n$ 不能完全包含的数字长度，考虑枚举每一位的贡献。

令剩下的还能完整遍历的数字个数为 $res$ ，枚举到第 $i$ 位时，我们将每 $10^{i - 1}$ 个数分为一组（这一位每经过 $10^{i - 1}$ 个数后会变化一次），每十组的贡献是一个 $45 \times 10^{i - 1}$ ，对于剩下的凑不成十组的，按从 0 开始的等差数列求和即可。再考虑剩下的凑不出一组的部分，这一部分在这一位的数字是不会变化的，计算出应该是哪个数字然后乘上个数

p.s. 在枚举到最高位前，数字是从 0 开始的，在最高位是从 1 开始的

剩下的不能完整包含一个数字的，就从高位一位一位加到答案里
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

i128 qpow(int x, int a) {
    i128 res = 1;
    while (a) {
        if (a & 1) res *= x;
        x *= x;
        a >>= 1;
    }
    return res;
}

i128 cnt[20], sum[20], hun[20];

void init(int n) {
    cnt[1] = 9, sum[1] = 45, hun[1] = 45;
    i128 v = 10;
    for (int i = 2; i <= n; i++) {
        cnt[i] = (v * 9ll) * i;
        sum[i] = 9ll * hun[i - 1] + 45ll * v;
        hun[i] = sum[i] + hun[i - 1];
        v *= 10;
    }
}

signed main()
{
    cin.tie(0); cout.tie(0);
    ios::sync_with_stdio(0);

    init(15);

    int T = 1;
    cin >> T;
    while (T--) {
        int n;
        cin >> n;

        if (n <= 9) {
            cout << n * (n + 1) / 2 << "\n";
            continue;
        }

        int id = 0, ans = 0;
        for (int i = 1; i <= 18; i++) {
            if (n >= cnt[i]) {
                n -= cnt[i];
                ans += sum[i];
            } else {
                id = i;
                break;
            }
        }

        if (!n) {
            cout << ans << "\n";
            continue;
        }

        // n, id
        int res = n / id, len = 1;
        int pos = n % id;
        for (int i = 1; i <= id; i++) {
            if (i != id) {
                int cnt = res / len, tail = res % len, lst = cnt % 10;
                ans += len * (45 * (cnt / 10) + (cnt % 10) * (cnt % 10 - 1) / 2) + lst * tail;
            } else {
                int cnt = res / len, tail = res % len, lst = (cnt + 1) % 10;
                ans += len * (45 * (cnt / 10) + (cnt % 10) * (cnt % 10 + 1) / 2) + lst * tail;
            }
            len *= 10;
        }

        int fir = qpow(10, id - 1);
        int num = fir - 1 + res + 1;
        for (int i = 1; i <= pos; i++) {
            ans += num / fir % 10;
            fir /= 10;
        }

        cout << ans << "\n";
    }

    return 0;
}
```

## [E - Arithmetics Competition](https://codeforces.com/contest/2132/problem/E)

暴力的方法就是看 a, b 队头哪个数大，若还能取就加到答案里。所以我们可以二分加进去的最小的数，再用一次二分计算能从 a, b 里各取多少个数，看能不能填满 $z$ 个需求，能填满就加大，不能就减小

这里最终记录的是不能填满的最大的最小值，这样剩下的空位全部填上这个最小值就行了

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

signed main()
{
    cin.tie(0); cout.tie(0);
    ios::sync_with_stdio(0);

    int T = 1;
    cin >> T;
    while (T--) {
        int n, m, Q;
        cin >> n >> m >> Q;
        vector<int> a(n + 5, 0), b(m + 5, 0);
        for (int i = 1; i <= n; i++) cin >> a[i];
        for (int i = 1; i <= m; i++) cin >> b[i];
        sort(a.begin() + 1, a.begin() + 1 + n, greater());
        sort(b.begin() + 1, b.begin() + 1 + m, greater());
        vector<int> sumA(n + 5, 0), sumB(m + 5, 0);
        for (int i = 1; i <= n; i++) sumA[i] = sumA[i - 1] + a[i];
        for (int i = 1; i <= m; i++) sumB[i] = sumB[i - 1] + b[i];

        auto count = [&] (vector<int> &t, int lim, int x) -> int {
            int l = 1, r = lim, aim = 0;
            while (l <= r) {
                int mid = (l + r) >> 1;
                if (t[mid] > x) l = mid + 1, aim = mid;
                    else r = mid - 1; 
            }
            return aim;
        };

        while (Q--) {
            int x, y, z;
            cin >> x >> y >> z;

            int l = 1, r = 1e9, aim = 0, ans = 0, num = 0;
            while (l <= r) {
                int mid = (l + r) >> 1;
                int cntA = min(x, count(a, n, mid)), cntB = min(y, count(b, m, mid));
                if (cntA + cntB < z) {
                    r = mid - 1;
                    aim = mid;
                    num = cntA + cntB;
                    ans = sumA[cntA] + sumB[cntB];
                } else {
                    l = mid + 1;
                }
            }

            ans += (z - num) * aim;

            cout << ans << "\n";
        }
    }

    return 0;
}
```

## [F - Rada and the Chamomile Valley](https://codeforces.com/contest/2132/problem/F)

必经之路就是从 1 到 n 必须经过的“桥”

我们先找出任意一条从 1 到 n 的路径，在这条路径上的桥就是必经之路，然后把每个符合要求的桥的端点加入队列进行一次 `bfs` 即可

每个点在不经过必经之路的情况下最多到达两个必经之路，所以一个点最多只会被遍历两次

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

struct EBCC
{
    int n;
    vector<vector<int>> G;
    vector<int> dfn, low, ff, fa;
    int id;

    EBCC() {};
    EBCC(int x) {
        init(x);
    }
    void init(int x) {
        n = x;
        G.assign(n + 5, {});
    }
    void add_edge(int x, int y) {
        G[x].push_back(y);
        G[y].push_back(x);
    }

    void dfs(int cur, int ftr) {
        dfn[cur] = low[cur] = ++id;
        fa[cur] = ftr;
        for (auto v : G[cur]) {
            if (!dfn[v]) {
                dfs(v, cur);
                low[cur] = min(low[cur], low[v]);
                if (low[v] > dfn[cur]) {
                    ff[v] = 1;
                }
            } else {
                if (dfn[v] < dfn[cur] && v != ftr) {
                    low[cur] = min(low[cur], dfn[v]);
                }
            }
        }
    }

    void gets() {
        dfn.assign(n + 5, 0);
        low.assign(n + 5, 0);
        ff.assign(n + 5, 0);
        fa.assign(n + 5, 0);
        id = 0;

        for (int i = 1; i <= n; i++)
            if (!dfn[i]) dfs(i, 0);
    }
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
        vector<vector<int>> G(n + 5);
        EBCC TG(n);
        map<pii, int> mp;
        for (int i = 1; i <= m; i++) {
            int x, y;
            cin >> x >> y;
            G[x].push_back(y), G[y].push_back(x);
            TG.add_edge(x, y), TG.add_edge(y, x);
            mp[{x, y}] = mp[{y, x}] = i;
        }

        vector<int> fa(n + 5, 0);
        function<void(int, int)> dfs = [&] (int cur, int ftr) -> void {
            fa[cur] = ftr;
            for (auto v : G[cur]) {
                if (fa[v] || v == ftr) continue;
                dfs(v, cur);
            }
        };
        dfs(1, -1);

        map<pii, int> idx;
        int now = n;
        while (now != 1) {
            idx[{now, fa[now]}] = idx[{fa[now], now}] = mp[{fa[now], now}];
            now = fa[now];
        }
        TG.gets();

        vector<int> bridge(n + 5, inf64);
        for (int i = 1; i <= n; i++) {
            if (!TG.ff[i]) continue;
            int x = i, y = TG.fa[i];
            if (idx.count({x, y})) {
                int tmp = idx[{x, y}];
                bridge[x] = min(bridge[x], tmp);
                bridge[y] = min(bridge[y], tmp);
            }
        }

        vector<int> dis(n + 5, inf64), ansid(n + 5, -1);
        queue<int> q;
        for (int i = 1; i <= n; i++)
            if (bridge[i] != inf64) q.push(i), dis[i] = 0, ansid[i] = bridge[i];
        
        while (!q.empty()) {
            auto cur = q.front();
            q.pop();

            for (auto v : G[cur]) {
                if (dis[cur] + 1 < dis[v] || (dis[cur] + 1 == dis[v] && ansid[cur] < ansid[v])) {
                    dis[v] = dis[cur] + 1;
                    ansid[v] = ansid[cur];
                    q.push(v);
                }
            }
        }

        int Q;
        cin >> Q;
        while (Q--) {
            int x;
            cin >> x;
            
            cout << ansid[x] << ' ';
        }
        cout << "\n";
    }

    return 0;
}
```

## [G - Famous Choreographer](https://codeforces.com/contest/2132/problem/G)

根据题意，对称中心的范围应该是在 $(1, 1)$ 到 $(n, m)$ 这个矩形里，最小移动距离是 $0.5$ ，对称中心数量实际是 $O(nm)$ 级别的

考虑枚举每个对称中心，可以计算出对称后的位置仍在原方阵的范围，这个矩形范围应该满足旋转 $180\degree$ 后与旋转前相同，也就是这个矩形按从上到下从左往右的顺序遍历和按从下往上从右往左的顺序遍历应该完全一致

考虑字符串哈希，令 $a1_{i, j} = a_{i, j} \times p_1^i \times p_j^j$ ， $a2_{i, j} = a_{i, j} \times p_1^{n - i + 1} \times p_2^{m - j + 1}$ ，然后用二维前缀和分别从两个方向累加。提取出这个矩阵 $\{(lx, ly), (rx, ry)\}$ 的哈希值的和后，将 $h1$ 除以 $p_1^{lx - 1} \times p_2^{ly - 1}$ ，$h2$ 除以 $p_1^{n - rx} \times p_2^{m - ry}$ ，即可消除幂次的差异。若两个哈希值相等，则说明是一个合法的对称中心，计算出对称后方阵的大小减去 $nm$ 就是新增的人数，最终的答案就是取最小值

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

int p1 = 13131, p2 = 131;

const int mod = 998244353;
int inv(int a) {
  if (a == 1) return 1;
  return inv(mod % a) * (mod - mod / a) % mod;
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

const int maxn = 1e6 + 5;

int col[maxn], row[maxn];
int invcol[maxn], invrow[maxn];

void init(int n) {
    col[0] = row[0] = 1;
    int invfact1 = inv(p1), invfact2 = inv(p2);
    invcol[0] = invrow[0] = 1;
    for (int i = 1; i <= n; i++)
        col[i] = mul(col[i - 1], p1), row[i] = mul(row[i - 1], p2), 
        invcol[i] = mul(invcol[i - 1], invfact1), invrow[i] = mul(invrow[i - 1], invfact2);
}

signed main()
{
    cin.tie(0); cout.tie(0);
    ios::sync_with_stdio(0);    

    init(1e6);

    int T = 1;
    cin >> T;
    while (T--) {
        int n, m;
        cin >> n >> m;
        string str;
        vector<vector<int>> a(n + 5, vector<int>(m + 5, 0));
        for (int i = 1; i <= n; i++) {
            cin >> str;
            for (int j = 1; j <= m; j++) {
                a[i][j] = str[j - 1] - 96;
            }
        }
        
        vector<vector<int>> h1(n + 5, vector<int>(m + 5, 0));
        auto h2 = h1;
        auto getH1 = [&] (int lx, int ly, int rx, int ry) -> int {
            int res = add(sub(h1[rx][ry], add(h1[lx - 1][ry], h1[rx][ly - 1])), h1[lx - 1][ly - 1]);
            res = mul(res, mul(invrow[lx - 1], invcol[ly - 1]));
            return res;
        };
        auto getH2 = [&] (int lx, int ly, int rx, int ry) -> int {
            int res = add(sub(h2[lx][ly], add(h2[lx][ry + 1], h2[rx + 1][ly])), h2[rx + 1][ry + 1]);
            res = mul(res, mul(invrow[n - rx], invcol[m - ry]));
            return res;
        };
        int ans = inf64;
        for (int i = 1; i <= n; i++)
            for (int j = 1; j <= m; j++)
                h1[i][j] = add(mul(a[i][j], mul(row[i], col[j])), sub(add(h1[i - 1][j], h1[i][j - 1]), h1[i - 1][j - 1]));
        for (int i = n; i; i--)
            for (int j = m; j; j--)
                h2[i][j] = add(mul(a[i][j], mul(row[n - i + 1], col[m - j + 1])), sub(add(h2[i + 1][j], h2[i][j + 1]), h2[i + 1][j + 1]));


        for (int i = 1; i <= 2 * n - 1; i++) {
            for (int j = 1; j <= 2 * m - 1; j++) {
                int disRow = min(i, 2 * n - i), disCol = min(j, 2 * m - j);
                int lx = (i - disRow + 2) / 2, rx = (i + disRow) / 2, ly = (j - disCol + 2) / 2, ry = (j + disCol) / 2;
                
                int hash1 = getH1(lx, ly, rx, ry), hash2 = getH2(lx, ly, rx, ry);
                if (hash1 == hash2) {
                    int lenx = max(i, 2 * n - i), leny = max(j, 2 * m - j);
                    ans = min(ans, lenx * leny - n * m);
                }
            }
        }

        cout << ans << "\n";
    }

    return 0;
}
```