---
title: 'Educational Codeforces Round 170 (Rated for Div.2) C - E'
description: ''
publishDate: 2024-10-15
tags:
  - ACM
  - CF
---

<!-- more -->

感觉补题速度还行，如果赛时没看错题应该能出到E

## [C - New Game](https://codeforces.com/contest/2025/problem/C)

拿的牌是连续的一段，那排序后双指针就行了

到断开的地方清空计数器然后把 r 扔到 i - 1 的位置

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
        int n, k;
        n = read(); k = read();
        vector<int> a(n + 5, 0);
        for (int i = 1; i <= n; i++) a[i] = read();
        sort(a.begin() + 1, a.begin() + 1 + n);

        int ans = 0, r = 0, cnt = 0;
        a[0] = -1;
        for (int i = 1; i <= n; i++) {
            if (abs(a[i] - a[i - 1]) == 1) --cnt;
                else if (abs(a[i] - a[i - 1]) > 1) cnt = 0, r = i - 1;
            if (i > r) cnt = 0, r = i - 1;
            while ((r < i || abs(a[r] - a[r + 1]) <= 1) && r < n && (cnt < k || (cnt == k && a[r] == a[r + 1]))) {
                ++r;
                if (a[r] != a[r - 1]) ++cnt;
            }
            ans = max(ans, r - i + 1);
        }
        cout << ans << "\n";
    }

    return 0;
}
```

## [D - Attribute Checks](https://codeforces.com/contest/2025/problem/D)

假设当前有 $cnt$ 个技能点，属性的状态可以用一个数字表示，智力为 $x$ ，力量就是 $cnt - x$

并且对一个检查点能通过的属性值在这种表示下为连续的一段

$r_i > 0$ 为 $r_i$ 到 $m$ ， $r_i < 0$ 为 $0$ 到 $m - |r_i|$ 

那就可以用一个一维数组表示每种状态对应的检查点通过数，遇到检查点差分修改，遇到技能点将差分值加到进去并且推到下一层 $dp_i = max(lst_i, lst_{i - 1})$

输出前记得再刷新一次

检查点查分修改 $O(1)$ ，技能点扫一遍复杂度 $O(m)$

时间复杂度 $O(max(n, m^2))$

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
    // T = read();
    while (T--) {
        int n, m;
        n = read(); m = read();
        vector<int> a(n + 5, 0);
        for (int i = 1; i <= n; i++) a[i] = read();

        vector<int> dp(m + 5, -inf), lst(m + 5, -inf);
        vector<int> v(m + 5, 0);
        int cnt_now = 0 , cnt_lst = 0;

        auto upd = [&] () -> void {
            for (int i = 0; i <= cnt_now; i++) {
                if (i) v[i] += v[i - 1];
                dp[i] += v[i];
            }
            for (int i = 0; i <= cnt_now; i++) v[i] = 0;
        };
        
        dp[0] = 0;
        for (int i = 1; i <= n; i++) {
            if (!a[i]) {
                upd();

                lst = dp;
                ++cnt_now;
                for (int i = 0; i <= cnt_now; i++) {
                    dp[i] = lst[i];
                    if (i) dp[i] = max(dp[i], lst[i - 1]);
                }
            } else {
                if (abs(a[i]) > cnt_now) continue;
                if (a[i] > 0) {
                    ++v[a[i]];
                } else {
                    ++v[0], --v[cnt_now + a[i] + 1];
                }
            }
        }
        upd();
        
        int ans = 0;
        for (int i = 0; i <= cnt_now; i++)
            ans = max(ans, dp[i]);
        cout << ans << "\n";
    }

    return 0;
}
```

## [E - Card Game](https://codeforces.com/contest/2025/problem/E)

花色1看成大小1了，已经是不知道第多少次不看样例理解错题就开写，写半天然后发现假了，还好这次错的不多还比较好改（

---

题目要求就是每组内的选择方案要是一个合法的括号序列，如果有未匹配的左括号，可以和花色1中任意多余的右括号匹配

先考虑只有一组的情况

用 $dp_{i, j}$ 表示 A 当前选了 $i$ 张牌，有 $j$ 张 B 选的牌没匹配的方案数。每添加一张牌，如果这张牌给 A ，那么A选牌数量加一，未匹配数量减一；如果给 B ，A选牌不变， 未匹配数量加一

此时 $dp_{\frac {m} {2},\ 0}$ 就是答案

那么接着考虑多组，因为第一组的牌相当于万能牌，所以后面每组可以有未匹配的B的牌。但是不能有未匹配的A的牌，因为要求两两匹配，如果有A的牌用不掉，最后肯定匹配不上，满足这种条件的 $dp_{i, j}$ 需要满足 $j + i * 2 = m$ （A 选 i 张 ，匹配 i 张， 未匹配 j 张）

令 $f_{i, j}$ 表示选完第 i 组，还剩 j 张万能牌的情况，枚举新的一组有 $k$ 张未匹配的牌，转移就是 $f_{i, j}\ = \sum \ f_{i - 1, j + k} \times s_{k}$  ，$s_k$ 为满足条件的有 k 张未匹配的方案数

时间复杂度 $O(n^3)$

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

int main()
{
    cin.tie(0); cout.tie(0);
    ios::sync_with_stdio(0);

    int T = 1;
    // cin >> T;
    while (T--) {
        int n, m;
        cin >> n >> m;
        
        vector<vector<mint>> dp(m + 5, vector<mint>(m + 5, 0)); //dp[i][j] 选了i张，有j张没匹配
        auto lst = dp;

        dp[0][0] = 1;
        for (int i = 1; i <= m; i++) {
            swap(dp, lst);
            dp.assign(m + 5, vector<mint>(m + 5, 0));
            for (int j = 0; j <= i; j++)
                for (int k = 0; k <= i - j; k++) {
                    dp[j + 1][max(0, k - 1)] += lst[j][k];
                    dp[j][k + 1] += lst[j][k];
                }
        }
        
        if (n == 1) {
            cout << dp[m / 2][0] << endl;
            continue;
        }

        vector<mint> s(m + 5, 0);
        lst = dp;
        for (int i = 0; i <= m; i++) {
            for (int j = 0; j <= m; j++)
                if (m - j * 2 == i) s[i] += lst[j][i];
        }

        dp.assign(n + 5, vector<mint>(m + 5, 0)); // 前i组，多j张牌
        for (int i = m / 2; i <= m; i++)
            dp[1][m - (m - i) * 2] = lst[i][0];

        for (int i = 2; i <= n; i++) {
            for (int j = 0; j <= m; j++) // 当前多 j 张
                for (int k = 0; k <= j; k++) { // 新选一组 k 张未匹配
                    dp[i][j - k] += dp[i - 1][j] * s[k];
                }
        }
        cout << dp[n][0] << "\n";
    }

    return 0;
}
```