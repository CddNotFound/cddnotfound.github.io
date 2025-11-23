---
title: 'Codeforces Round 1039 (Div. 2)  A - E2'
description: ''
publishDate: 2025-08-25
heroImage: {src: './cover.jpeg', color: '#7d98d6ff' }
tags:
  - ACM
  - CF
---
## [A - Recycling Center](https://codeforces.com/contest/2128/problem/A)

记录每堆垃圾离 $c$ 还有多少秒，从小到大遍历，如果当前时间这堆垃圾还没有膨胀到 $c$ 就可以免费清理掉这堆垃圾，否则忽略继续向后

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
        int n, c;
        cin >> n >> c;
        vector<int> a(n + 5, 0), t(n + 5, 0);
        for (int i = 1; i <= n; i++) {
            cin >> a[i];
            while (a[i] <= c) {
                t[i] += 1;
                a[i] <<= 1;
            }
        }
        sort(t.begin() + 1, t.begin() + 1 + n);

        int now = 1, cnt = 0;
        for (int i = 1; i <= n; i++) {
            if (!t[i]) continue;
            if (t[i] >= now) {
                cnt += 1;
                now += 1;   
            }
        }

        cout << n - cnt << "\n";
    }

    return 0;
}
```

## [B - Deque Process](https://codeforces.com/contest/2128/problem/B)

每次取头尾的两个数比较大小，按照 `大小、小大、大小` 的顺序插入，这样最多连续四个单调

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

        string ans;
        int way = 1;
        for (int l = 1, r = n; l < r; ++l, --r) {
            if ((a[l] > a[r]) == way) {
                ans.push_back('L'), ans.push_back('R');
            } else {
                ans.push_back('R'), ans.push_back('L');
            }
            way ^= 1;
        }

        if (n & 1) ans.push_back('L');
        cout << ans << "\n";
    }

    return 0;
}
```

## [C - Leftmost Below](https://codeforces.com/contest/2128/problem/C)

按照这个操作，数一定是一位一位按顺序被填充好的

当我们正在填充第 $i$ 个数字时，为它增加的数不能超过前面的最小值，否则就加到其他地方去了，并且增加的数还要大于当前的数。所以 $a_i$ 最大为 $2 \times \min_{j = 1}^{i - 1}\{a_j\} - 1$ ，扫一遍判断即可

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

        int ff = 1, mx = a[1];
        for (int i = 2; i <= n; i++) {
            if (a[i] >= mx * 2) {ff = 0; break;}
            mx = min(mx, a[i]);
        }
        
        cout << (ff ? "YES\n" : "NO\n");
    }

    return 0;
}
```

## [D - Sum of LDS](https://codeforces.com/contest/2128/problem/D)

令 $dp_i$ 表示以 $i$ 为结尾的最长递减序列的最大长度，根据题目条件 $max(p_i,p_{i + 1}) > p_{i + 2}$ ， $dp_i$ 要么由 $i - 1$ 转移过来，要么由 $i - 2$ 转移过来，转移方程为：

$$
\begin{equation}
dp_i = 
\begin{cases}
dp_{i - 1} + 1 & p_{i - 1} > p_i\\
dp_{i - 1} & p_{i - 1} < p_i
\end{cases}
\end{equation}
$$

当 $p_{i - 1} < p_i$ 时，一定是 $p_{i - 1} < p_i < p_{i - 2}$ ，此时 $dp_i$ 和 $dp_{i - 1}$ 都是从 $dp_{i - 2}$ 转移，所以 $dp_i = dp_{i - 1}$

当 $p_{i - 1} < p_i$ 时，无论 $p_{i - 1}$ 和 $p_{i - 2}$ 的大小关系如何，一定是从 $dp_{i - 1}$ 转移更优

当有了这个 $dp$ 数组后，一段区间 $[l, r]$ 的贡献就是 $\max_{i = l}^{r}\left(dp_{i}\right) - \min_{i = l}^{r}\left(dp_{i}\right) + 1$ ，并且由于 $dp$ 单调不减，以 $l$ 开头的区间，它的 $\min$ 部分一定是 $dp_l$ ，同理以 $r$ 结尾的区间，它的 $\max$ 部分一定是 $dp_r$ ，分开累和后一减就是答案

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
        dp[1] = 1;
        for (int i = 2; i <= n; i++) {
            if (a[i] > a[i - 1]) dp[i] = dp[i - 1];
                else dp[i] = dp[i - 1] + 1;
        }

        int sumMin = 0, sumMax = 0;
        for (int i = 1; i <= n; i++) {
            sumMin += (n - i + 1) * (dp[i] - 1);
            sumMax += i * dp[i];
        }
        
        cout << sumMax - sumMin << "\n";
    }

    return 0;
}
```

## [E1 - Submedians (Easy Version)](https://codeforces.com/contest/2128/problem/E1)

对中位数有一个很经典的处理方法就是，当 $a_i \geq x$ 记为 1 ，当 $a_i < x$ 记为 -1 ，当某一段区间的和为非负数时，这段区间的中位数大于等于 $x$

考虑二分这个最大中位数，按照上述方法处理并计算前缀和，当 $i >= k$ 时，将 $s_{i - k}$ 的值加入集合中，若集合中存在小于等于 $s_i$ 的值，这一段的和就是 $s_i$ 减去这个数大于等于 0，说明存在一个长度至少为 $k$ 的区间的中位数大于等于 $x$ ，并返回这个区间

最后输出最大值和对应的区间
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
    int x, y, z;
};

signed main()
{
    cin.tie(0); cout.tie(0);
    ios::sync_with_stdio(0);

    int T = 1;
    cin >> T;
    while (T--) {
        int n, k;
        cin >> n >> k;
        vector<int> a(n + 5, 0);
        for (int i = 1; i <= n; i++) cin >> a[i];

        int l = 1, r = n, ans = 0, ansl = 0, ansr = 0;
        auto check = [&] (int x) -> node {
            vector<int> v(n + 5, 0), sum(n + 5, 0);
            for (int i = 1; i <= n; i++)
                if (a[i] >= x) v[i] = 1;
                    else v[i] = -1;
            
            map<int, int> mp;
            for (int i = 1; i <= n; i++) {
                if (i >= k) mp[-sum[i - k]] = i - k;

                sum[i] = sum[i - 1] + v[i];
                auto it = mp.lower_bound(-sum[i]);
                if (it != mp.end()) {
                    return {it -> second + 1, i, 1};
                }
            }

            return {0, 0, 0};
        };

        while (l <= r) {
            int mid = (l + r) >> 1;
            auto [x, y, z] = check(mid);
            if (z) {
                ans = mid;
                ansl = x, ansr = y;
                l = mid + 1;
            } else {
                r = mid - 1;
            }
        }

        cout << ans << ' ' << ansl << ' ' << ansr << "\n";
    }

    return 0;
}
```

## [E2 - Submedians (Hard Version)](https://codeforces.com/contest/2128/problem/E2)

一个重要的结论就是：对于一个区间 $[l, r]$ ，如果 $l$ 或者 $r$ 移动了一位，中位数和原来相比是连续的

比如目前的区间为 $[a_1, a_2, ..., x, ..., a_{n}]$ （$x$ 为中位数），当我们添加一位进来，会变成 $[a_1, a_2, ..., x_1, x_2, ..., a_n]$ 的样子，这个时候原来的 $x$ 要么变成新的 $x_1$， 要么变成新的 $x_2$ ，仍然是中位数。其他情况同理，就不一一列举了

所以我们可以按照 `E1` 的方法，二分找到最大和最小的中位数及其对应的区间，从一个区间一点一点移动到另一个区间，每移动一步（包括初始区间），就算出当前的中位数范围，和当前的区间坐标一起存到数组里，排序后按顺序输出即可

---

我偷懒直接在树状数组上二分了，时间复杂度为 $O(nlog^2n)$

两个 `set` 按照对顶堆的思路维护可以做到单 log

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
    int x, y, z;
};

struct segment {
    int v1, v2, l, r;
};

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
    int find(int x) {
        int l = 1, r = n, res = 0;
        while (l <= r) {
            int mid = (l + r) >> 1;
            if (query(mid) >= x) res = mid, r = mid - 1;
                else l = mid + 1;
        }
        return res;
    }
};

signed main()
{
    cin.tie(0); cout.tie(0);
    ios::sync_with_stdio(0);

    int T = 1;
    cin >> T;
    while (T--) {
        int n, k;
        cin >> n >> k;
        vector<int> a(n + 5, 0);
        for (int i = 1; i <= n; i++) cin >> a[i];

        int l = 1, r = n;
        int ansMin = 0, lMin = 0, rMin = 0;
        int ansMax = 0, rMax = 0, lMax = 0;
        auto check1 = [&] (int x) -> node {
            vector<int> v(n + 5, 0), sum(n + 5, 0);
            for (int i = 1; i <= n; i++)
                if (a[i] >= x) v[i] = 1;
                    else v[i] = -1;
            
            map<int, int> mp;
            for (int i = 1; i <= n; i++) {
                if (i >= k) mp[-sum[i - k]] = i - k;

                sum[i] = sum[i - 1] + v[i];
                auto it = mp.lower_bound(-sum[i]);
                if (it != mp.end()) {
                    return {it -> second + 1, i, 1};
                }
            }

            return {0, 0, 0};
        };
        auto check2 = [&] (int x) -> node {
            vector<int> v(n + 5, 0), sum(n + 5, 0);
            for (int i = 1; i <= n; i++)
                if (a[i] <= x) v[i] = 1;
                    else v[i] = -1;
            
            map<int, int> mp;
            for (int i = 1; i <= n; i++) {
                if (i >= k) mp[-sum[i - k]] = i - k;

                sum[i] = sum[i - 1] + v[i];
                auto it = mp.lower_bound(-sum[i]);
                if (it != mp.end()) {
                    return {it -> second + 1, i, 1};
                }
            }

            return {0, 0, 0};
        };

        while (l <= r) {
            int mid = (l + r) >> 1;
            auto [x, y, z] = check1(mid);
            if (z) {
                ansMax = mid;
                lMax = x, rMax = y;
                l = mid + 1;
            } else {
                r = mid - 1;
            }
        }
        l = 1, r = n;
        while (l <= r) {
            int mid = (l + r) >> 1;
            auto [x, y, z] = check2(mid);
            if (z) {
                ansMin = mid;
                lMin = x, rMin = y;
                r = mid - 1;
            } else {
                l = mid + 1;
            }
        }

        vector<node> ans;
        vector<segment> seg;

        Fenwick t(n);
        int tot = 0;
        auto upd = [&] (int l, int r) -> void {
            if (r - l + 1 < k) return ;
            if (tot & 1) {
                int x = t.find(tot / 2 + 1);
                seg.push_back({x, x, l, r});
            } else {
                int x = t.find(tot / 2), y = t.find(tot / 2 + 1);
                seg.push_back({x, y, l, r});
            }
        };
        for (int i = lMin; i <= rMin; i++) t.add(a[i], 1), tot += 1;
        upd(lMin, rMin);
        
        while (lMin > lMax) lMin -= 1, t.add(a[lMin], 1), tot += 1, upd(lMin, rMin);
        while (rMin < rMax) rMin += 1, t.add(a[rMin], 1), tot += 1, upd(lMin, rMin);
        while (lMin < lMax) t.add(a[lMin], -1), tot += -1, lMin += 1, upd(lMin, rMin);
        while (rMin > rMax) t.add(a[rMin], -1), tot += -1, rMin -= 1, upd(lMin, rMin);

        int lst = ansMin - 1;
        ranges::sort(seg, [&](segment x, segment y) {
            if (x.v1 != y.v1) return x.v1 < y.v1;
            return x.v2 < y.v2;
        });

        cout << ansMax - ansMin + 1 << "\n";
        for (auto [v1, v2, l, r] : seg) {
            if (v2 <= lst) continue;
            for (int i = lst + 1; i <= v2; i++)
                cout << i << ' ' << l << ' ' << r << "\n";
            lst = v2;
        }

        cerr << "\n";
    }

    return 0;
}
```
