---
title: 'Codeforces Round 979 (Div. 2) A - E'
description: ''
publishDate: 2024-10-20
tags:
  - ACM
  - CF
---
<!-- more --> 

不该用小号打的，再再再痛失上大分场😢

## [A - A Gift From Orangutan](https://codeforces.com/contest/2030/problem/A)

第一个放最大值，第二个放最小值

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
        for (int i = 1; i <= n; i++) a[i] = read();
        sort(a.begin() + 1, a.begin() + 1 + n);

        int ans = 0;
        ans = (n - 1) * (a[n] - a[1]);
        cout << ans << "\n";
    }

    return 0;
}
```

## [B - Minimise Oneness](https://codeforces.com/contest/2030/problem/B)

$f(t) = 2 ^ {cnt0} - 1$

$g(t) = 2 ^ {cnt0} * (2 ^ {cnt1} - 1)$

只有一个 1 时最优

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

int main()
{
    cin.tie(0); cout.tie(0);
    ios::sync_with_stdio(0);

    int T = 1;
    T = read();
    while (T--) {
        int n;
        n = read();

        cout << 1;
        for (int i = 1; i < n; i++) cout << 0;
        cout << "\n";
    }

    return 0;
}
```

## [C - A TRUE Battle](https://codeforces.com/contest/2030/problem/C)

优先运算 and ，就相当于把整个数组用 or 分成若干块，只要有一块里全是 1 就是 Alice 赢

如果边界有 1 ，Alice 可以直接把它分成单独的一块

如果有两个相邻的 1 ，Alice 也可以用两步分出一块

其他情况对于每个 1 Alice 来不及隔开两边，都能被 Bob 用 and 消掉 ，Bob 赢

### Code

```cpp
#include<bits/stdc++.h>
using namespace std;

typedef pair<int, int> pii;
typedef long long LL;
typedef unsigned long long uLL;
#define mp make_pair

int main()
{
    int T = 1;
    scanf("%d", &T);
    while (T--) {
        int n;
        scanf("%d", &n);
        vector<int> a(n + 5, 0);
        for (int i = 1; i <= n; i++) scanf("%1d", &a[i]);

        if (n == 2) {
            cout << (a[1] || a[2] ? "YES\n" : "NO\n");
            continue;
        }

        if (a[1] || a[n]) {
            cout << "YES\n";
            continue;
        }

        int ff = 0;
        for (int i = 1; i <= n; i++)
            if (a[i] && a[i - 1]) {ff = 1; break;}

        cout << (ff ? "YES\n" : "NO\n");
    }

    return 0;
}
```

## [D - QED's Favorite Permutation](https://codeforces.com/contest/2030/problem/D)

只有当第 i 格为 L，第 i + 1 格为 R，i 和 i + 1 不能交换数字，相当于 i 到 i + 1 的这个间隔被封上了

若 x 当前位置在 y，那从 x 到 y ，中间的所有间隔都不能被封上

先用差分算一下每个间隔能不能被封，统计一下最初不合法的数量

然后每次查询最多改变两个间隔的状态，挨个更新一下不合法数量即可

### Code

```cpp
#include<bits/stdc++.h>
using namespace std;

typedef pair<int, int> pii;
typedef long long LL;
typedef unsigned long long uLL;
#define mp make_pair

const int maxn = 2e5 + 5;

char ch[maxn];

int main()
{
    cin.tie(0); cout.tie(0);
    ios::sync_with_stdio(0);

    int T = 1;
    cin >> T;
    while (T--) {
        int n, Q;
        cin >> n >> Q;
        vector<int> p(n + 5, 0);
        for (int i = 1; i <= n; i++) cin >> p[i];
        cin >> ch;
        for (int i = n; i; i--) ch[i] = ch[i - 1];

        vector<int> v(n + 5, 0);
        for (int i = 1; i <= n; i++) {
            int x = i, y = p[i];
            if (x == y) continue;
            if (x > y) swap(x, y);
            ++v[x + 1], --v[y + 1];
        }
        for (int i = 1; i <= n; i++) v[i] += v[i - 1];


        int cnt = 0;
        auto add = [&] (int x) -> void {
            if (v[x]) ++cnt; 
        };
        auto del = [&] (int x) -> void {
            if (v[x]) --cnt;
        };
        for (int i = 2; i <= n; i++) {
            if (ch[i - 1] == 'L' && ch[i] == 'R') add(i);
        }

        while (Q--) {
            int x;
            cin >> x;
            if (x >= 2) {
                if (ch[x - 1] == 'L' && ch[x] == 'R') del(x);
            }
            if (x < n) {
                if (ch[x] == 'L' && ch[x + 1] == 'R') del(x + 1);
            }

            ch[x] = ch[x] == 'L' ? 'R' : 'L';
            if (x >= 2) {
                if (ch[x - 1] == 'L' && ch[x] == 'R') add(x);
            }
            if (x < n) {
                if (ch[x] == 'L' && ch[x + 1] == 'R') add(x + 1);
            }

            cout << (cnt ? "NO\n" : "YES\n");
        }
    }

    return 0;
}
```

## [E - MEXimize the Score](https://codeforces.com/contest/2030/problem/E)

首先如果 b 确定了，最优方案是每组分成尽可能长的连续的不重复的数，或者说就是有几个 0 就分成几组，然后剩下的数尽可能往上叠，比如 {0, 0, 0, 1, 1, 2, 3, 3, 3} 最终的贡献长这样

![[./picture/Pasted image 20241020004804.png]]
所以考虑枚举 MEX 算对应的组数

假设当前的 MEX 枚举到了 i ，前面的数中从 0 连续到 i - 1 的最多有 k 组。

先考虑前面的方案数，令 $dp_{i, j}$ 表示第 i 个数选了大于等于 j 个的方案数，为 $\sum_{t = j} ^ k \binom{t}{cnt_i}$，对第一维求前缀积，就是从 1 选到 i，每个数都不低于 j 个的方案数。那么最多有 k 组的方案数，也就是 1 到 i 至少有一个数只选了 k 个，为 $prod_{i, k} - prod_{i, k + 1}$ ，记为 $C1$ 

再考虑 MEX + 1 到最后的方案数。选出的 k 组中能有几组最终的 MEX 为 i 仅和选了几个 i 有关，所以后面的数怎么选都不会有影响（假设某一组的 MEX 为 4，大于 4 的数无论选多少个都让它变大；同理，大于等于 4 的数无论少选多少个都不会让它变小 ），方案数就是 $2^{cnt_n - cnt_{i + 1}}$ ，记为 $C2$

最后考虑如何计算 i + 1 的影响。

先考虑枚举 i + 1 的个数，假设为 t，此时的贡献数就是 $C1 * C2 * (k - t) * \binom{t}{cnt_{i + 1}}$，加起来就是 

$\sum_{t = 0}^{k} C1 * C2 * (k - t) * \binom{t}{cnt_{i + 1}} = C1 * C2 * k * \sum_ {t = 0}^{k} \binom{t}{cnt_{i + 1}} - \sum_{t = 0}^{k}t * \binom{t}{cnt_{i + 1}}$

两个 $\sum$ 可以通过前缀和预处理出来，就可以 $O(1)$ 计算了

枚举的组数的这个 k 不会超过 $min\{cnt_0, cnt_1, ..., cnt_{i - 1}\}$ ，所以总共要计算的 dp 也不会超过 n 个，时间复杂度 $O(n)$ 

### Code

```cpp
#include<bits/stdc++.h>
using namespace std;

typedef pair<int, int> pii;
typedef long long LL;
typedef unsigned long long uLL;
#define mp make_pair

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

const int maxn = 1e6 + 5;
mint fact[maxn], invfact[maxn];
mint t[maxn];
void init(int n) {
    fact[0] = invfact[0] = 1;
    for (int i = 1; i <= n; i++) fact[i] = fact[i - 1] * i;
    
    invfact[n] = fact[n].inv();
    for (int i = n - 1; i; i--)
        invfact[i] = invfact[i + 1] * (i + 1);

    t[0] = 1;
    for (int i = 1; i <= n; i++) t[i] = t[i - 1] * 2;
}
mint C(int a, int b) {
    if (a == b || !b) return 1;
    if (a < b) return 0;
    return fact[a] * invfact[b] * invfact[a - b];
}

const int inf = 1e9;

int main()
{
    cin.tie(0); cout.tie(0);
    ios::sync_with_stdio(0);

    init(1e6);

    int T = 1;
    cin >> T;
    while (T--) {
        int n;
        cin >> n;
        vector<int> a(n + 5, 0), cnt(n + 5, 0);
        vector<int> s_cnt(n + 5, 0);
        for (int i = 1; i <= n; i++) cin >> a[i], ++cnt[a[i]];
        for (int i = 1; i <= n + 2; i++) s_cnt[i] = s_cnt[i - 1] + cnt[i];

        vector<vector<mint>> sumC(n + 5), low(n + 5);
        for (int i = 0; i <= n; i++) {
            sumC[i].assign(cnt[i] + 1, 0);
            sumC[i][0] = 0;
            for (int j = 1; j <= cnt[i]; j++)
                sumC[i][j] = sumC[i][j - 1] + C(cnt[i], j) * j;
        }
        for (int i = 0; i <= n; i++) {
            low[i].assign(cnt[i] + 1, 0);
            low[i][0] = 1;
            for (int j = 1; j <= cnt[i]; j++)
                low[i][j] = low[i][j - 1] + C(cnt[i], j);
        }

        vector<mint> dp(cnt[0] + 5, 1), lst;

        mint ans = 0;
        int mn = inf;
        for (int i = 0; i <= n; i++) {
            int x = cnt[i], y = cnt[i + 1];

            swap(dp, lst);
            mn = min(mn, cnt[i]);
            dp.assign(mn + 5, 0);
            mint tot = t[cnt[i]];
            dp[0] = lst[0] * tot;
            for (int j = 1; j <= mn; j++) {
                tot -= C(cnt[i], j - 1);
                dp[j] = lst[j] * tot;
            }

            for (int k = 1; k <= min(x, mn); k++) {
                mint tmp = dp[k] - dp[k + 1];
                tmp *= t[s_cnt[n] - s_cnt[i + 1]];
                mint t2 = k * low[i + 1][min(k, y)] - sumC[i + 1][min(k, y)];
                ans += tmp * t2 * (i + 1);
            }
        }
        
        cout << ans << "\n";
    }

    return 0;
}
```