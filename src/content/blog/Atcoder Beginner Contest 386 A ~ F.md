---
title: 'Atcoder Beginner Contest 386 A - F'
description: ''
publishDate: 2024-12-31
tags:
  - ACM
  - Atc
---
<!-- more -->

## [A - Full House 2](https://atcoder.jp/contests/abc386/tasks/abc386_a)

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
        vector<int> a(10, 0);
        for (int i = 1; i <= 4; i++) cin >> a[i];
        sort(a.begin() + 1, a.begin() + 1 + 4);

        int cnt = 1;
        for (int i = 2; i <= 4; i++)
            if (a[i] != a[i - 1]) ++cnt;
        
        cout << (cnt == 2 ? "Yes\n" : "No\n");
    }

    return 0;
}
```

## [B - Calculator](https://atcoder.jp/contests/abc386/tasks/abc386_b)

优先放 '00'

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
        string str;
        cin >> str;
        int n = str.length();
        str = " " + str;

        int st = 1, ans = 0;
        while (st <= n) {
            if (str[st] != '0') {
                ++st;
                ++ans;
                continue;
            }

            if (st < n && str[st + 1] == '0') {
                st += 2;
                ++ans;
                continue;
            }

            ++ans;
            ++st;
        }

        cout << ans << "\n";
    }

    return 0;
}
```

## [C - Operate 1](https://atcoder.jp/contests/abc386/tasks/abc386_c)

只能修改一次，那么只有三种情况：

a, b 长度相等，只有一位不相同

a 长度比 b 大 1，b 按顺序应该全部与 a 匹配

a 长度比 b 小 1，a 按顺序应该全部与 b 匹配

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
        int k;
        cin >> k;
        string a, b;
        cin >> a >> b;
        int lena = a.length(), lenb = b.length();
        a = " " + a, b = " " + b;

        if (abs(lena - lenb) > 1) {
            cout << "No\n";
            continue;
        }

        int ff = 1;
        if (lena == lenb) {
            int cnt = 0;
            for (int i = 1; i <= lena; i++) {
                if (a[i] != b[i]) ++cnt;
            }
            ff = cnt <= 1;
        } else if (lena - 1 == lenb) {
            int pos = 0;
            for (int i = 1; i <= lenb; i++)
                if (a[i] != b[i]) {pos = i; break;}
            if (pos) {
                for (int i = pos; i <= lenb; i++)
                    if (a[i + 1] != b[i]) {ff = 0; break;}
            }
        } else {
            int pos = 0;
            for (int i = 1; i <= lena; i++)
                if (a[i] != b[i]) {pos = i; break;}
            if (pos) {
                for (int i = pos; i <= lena; i++)
                    if (b[i + 1] != a[i]) {ff = 0; break;}
            }          
        }

        cout << (ff ? "Yes\n" : "No\n");
    }

    return 0;
}
```

## [D - Diagonal Separation](https://atcoder.jp/contests/abc386/tasks/abc386_d)

根据题意，白色格子的右下方（包括同行或同列）不能出现任何一个黑格子

先对点排序，遇到白色格子就更新这一行黑格纵坐标的最大值

遇到超过这个最大值的黑格则不行

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
    int x, y, col;
};

signed main()
{
    cin.tie(0); cout.tie(0);
    ios::sync_with_stdio(0);

    int T = 1;
    // cin >> T;
    while (T--) {
        int n, k;
        cin >> n >> k;
        vector<node> a(k + 5, {0, 0, 0});
        for (int i = 1; i <= k; i++) {
            int x, y;
            string str;
            cin >> x >> y >> str;
            a[i] = {x, y, str[0] == 'B'};
        }
        sort(a.begin() + 1, a.begin() + 1 + k, [&](node x, node y){
            if (x.x != y.x) return x.x < y.x;
            return x.y < y.y;
        });

        int mx = n + 1, ff = 1;
        for (int i = 1; i <= k; i++) {
            auto [x, y, col] = a[i];
            if (!col) {
                mx = min(mx, y);
            } else {
                if (y >= mx) {
                    ff = 0;
                    break;
                }
            }
        }

        cout << (ff ? "Yes\n" : "No\n");
    }

    return 0;
}
```

## [E - Maximize XOR](https://atcoder.jp/contests/abc386/tasks/abc386_e)

注意到 $\binom{n}{k} \leq 10^6$ ，可以直接 dfs 找出所有可能

当 k 过大时递归层数可能过大导致 TLE，可以计算 dfs异或和 与 所有值的异或和的异或最大值

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
        int n, k;
        cin >> n >> k;

        vector<int> a(n + 5, 0);
        int tot = 0;
        for (int i = 1; i <= n; i++) cin >> a[i], tot ^= a[i];

        int tag = 0, ans = 0;
        if (n - k < k) {
            tag = 1;
            k = n - k;
        }

        function<void(int, int, int)> dfs = [&] (int t, int val, int lst) -> void {
            if (t > k) {
                if (tag) ans = max(ans, tot ^ val);
                    else ans = max(ans, val);
                return ;
            }

            for (int i = lst + 1; i <= n - (k - t + 1) + 1; i++) {
                dfs(t + 1, val ^ a[i], i);
            }
        };
        
        dfs(1, 0, 0);

        cout << ans << "\n";
    }

    return 0;
}
```

## [F - Operate K](https://atcoder.jp/contests/abc386/tasks/abc386_f)

C 的 hard ver.

因为 k 很小，可以考虑枚举 b 的每一位在 a 中的位置，不会超过 2k + 1 个

将第 i 位放在 j，考虑两种情况：

1.代价为 $dp_j = \min_{k = 0}^{j - 1}\{dp_k + j - k - 1\} + [a_k \neq b_j]$ ，即上一位放在 k ，将 k 到 j 中间的位置全删掉（对应更改和删除）

2.代价为 $dp_j = \min_{k = 0}^{j}\{dp_k\} + 1$，即上一位放在 k，将第 j 位插进去（对应插入）

每更新一位就更新答案，第 j 位放到 i 的位置的代价加上删除 i 后面所有的字符再插入 j 后面所有的字符的代价，$ans = \min\{ans, dp_j + lenb - i + lena - j\}$

最后比较 ans 和 k 的大小即可

因为代价随下标线性递增，可以减去下标找出最小值，时间复杂度 $O(nk)$

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
        int k;
        cin >> k;
        string a, b;
        cin >> a >> b;
        int lena = a.length(), lenb = b.length();
        a = " " + a, b = " " + b;

        vector<int> dp(lena + 5, -1), id(lena + 5, 0), mn(lena + 5, inf64), new_id(lena + 5, 0);
        int tag = 1;
        for (int i = 0; i <= lena; i++) dp[i] = i, id[i] = tag, mn[i] = -1;

        int ans = inf64;
        for (int i = 1; i <= lenb; i++) {
            for (int j = max(1ll, i - k - 1); j <= min(lena, i + k + 1); j++) {
                if (id[j - 1] == tag && (new_id[j] != tag + 1 || mn[j - 1] + j + (a[j] != b[i]) < dp[j])) dp[j] = mn[j - 1] + j + (a[j] != b[i]), new_id[j] = tag + 1;
                if (id[j] == tag && (new_id[j] != tag + 1 || mn[j] + 2 + j < dp[j])) dp[j] = mn[j] + 2 + j, new_id[j] = tag + 1;
            }
            for (int j = max(0ll, i - k - 2); j <= min(lena, i + k + 2); j++)
                mn[j] = inf64, id[j] = new_id[j];
            id[0] = tag + 1, mn[0] = i - 1;
            for (int j = max(1ll, i - k - 1); j <= min(lena, i + k + 1); j++) {
                mn[j] = min(mn[j], mn[j - 1]);
                if (new_id[j] == tag + 1) mn[j] = min(mn[j], dp[j] - j - 1); 
            }
            ++tag;

            for (int j = max(1ll, i - k - 2); j <= min(lena, i + k + 2); j++)
                if (id[j] == tag) {
                    ans = min(ans, dp[j] + lenb - i + lena - j);
                }
        }

        cout << (ans <= k ? "Yes\n" : "No\n");
    }

    return 0;
}
```