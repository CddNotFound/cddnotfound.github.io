---
title: 'Codeforces Round 958 (Div. 2) A - E'
description: ''
publishDate: 2024-07-20
tags:
- ACM
- CF
---
<!-- more -->

## [A - Split the Multiset](https://codeforces.com/contest/1988/problem/A)

每次操作可以增加 $k - 1$ 个数字，答案就是 $n - 1$ 对 $k - 1$ 上取整

### Code

```cpp
#include<bits/stdc++.h>
#define LL long long
using namespace std;
int read() {
    int x = 0, w = 1;
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
int T;
int n, k;
int main()
{
    T = read();
    while (T--) {
        n = read(); k = read();
        printf("%d\n", (n - 1 + k - 2) / (k - 1));
    }

    return 0;
}
```

## [B - Make Majority](https://codeforces.com/contest/1988/problem/B)

连续的 $0$ 可以合并，最后看 $1$ 的数量是不是严格大于 $0$

### Code

```cpp
#include<bits/stdc++.h>
#define LL long long
using namespace std;
int read() {
    int x = 0, w = 1;
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

const int maxn = 2e5 + 5;

int T;
int n;
int a[maxn];

int main()
{
    T = read();
    while (T--) {
        n = read();
        int cnt = 0;
        a[cnt] = 1;
        for (int i = 1; i <= n; i++) {
            int x;
            scanf("%1d", &x);
            if (x == 0) {
                if (a[cnt] == 1) a[++cnt] = x;
            } else a[++cnt] = x;
        }
        int s0 = 0, s1 = 0;
        for (int i = 1; i <= cnt; i++)
            if (a[i]) s1++;
                else s0++;
        printf(s1 > s0 ? "Yes\n" : "No\n");
    }

    return 0;
}
```

## [C - Increasing Sequence with Fixed OR](https://codeforces.com/contest/1988/problem/C)

$a_i$ 的二进制下的 $1$ 肯定是 $a_{i + 1}$ 的一个真子集，所以每往前一个数二进制下 $1$ 的个数最少减一，那么贪心的构造就是每次只将一位变成 $0$

序列的最后一个数肯定是 $n$

### Code

```cpp
#include<bits/stdc++.h>
#define LL long long
using namespace std;
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

const int maxn = 5e5 + 5;
const int maxm = 1e2 + 5;

int T;
LL n;
LL t[maxm], a[maxm];

int main()
{
    T = read();
    while (T--) {
        n = read();

        t[0] = 1;
        a[0] = 0;
        for (int i = 1; i <= 61; i++) t[i] = t[i - 1] * 2, a[i] = 0;

        int cnt = 0;
        for (int i = 0; i <= 61; i++)
            if (n >> i & 1) a[i] = 1, cnt++;

        if (cnt == 1) {
            printf("%d\n", cnt);
            printf("%lld\n", n);
            continue;
        }
        printf("%d\n", cnt + 1);
        for (int i = 61; i >= 0; i--)
            if (a[i]) {
                printf("%lld ", n - t[i]);
            }
        printf("%lld\n", n);
    }

    return 0;
}
```

## [D - The Omnipotent Monster Killer](https://codeforces.com/contest/1988/problem/D)

首先最多只会操作 $logn$ 次，此时这颗树的构造方法差不多是：

初始一个节点，给当前的所有节点都连上一个比它权值大到一定程度的点，此时是最坏情况（这个能很好的解释为什么 $3$ 次删不完

然后跑 `dp` 就行了，令 $dp_{i, j}$ 表示在第 $j$ 次操作删除第 $i$ 个节点，以 $i$ 为根的子树的贡献，转移就是 $dp_{i, j} = \sum_{v \in son[i]}\ (\min_{k \neq j}\ dp_{v, k})$

### Code

```cpp
#include<bits/stdc++.h>
#define LL long long
using namespace std;
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

const int maxn = 3e5 + 5;
const LL inf = 1e18;

int T;
int n;
LL a[maxn];
vector<int> G[maxn];

LL dp[maxn][35];

void dfs(int cur, int ftr) {
    for (int i = 1; i <= 25; i++) dp[cur][i] = a[cur] * i;

    for (auto v : G[cur])
        if (v != ftr) {
            // ff = 1;
            dfs(v, cur);
            for (int i = 1; i <= 25; i++) {
                LL mn = inf;
                for (int j = 1; j <= 25; j++) {
                    if (i == j) continue;
                    mn = min(mn, dp[v][j]);
                }
                dp[cur][i] += mn;
            }
        }
}

int main()
{
    T = read();
    while (T--) {
        n = read();
        for (int i = 1; i <= n; i++) a[i] = read();
        for (int i = 1; i < n ; i++) {
            int x, y;
            x = read(); y = read();
            G[x].push_back(y), G[y].push_back(x);
        }

        dfs(1, 0);

        LL ans = inf;
        for (int i = 1; i <= 25; i++) ans = min(ans, dp[1][i]);
        printf("%lld\n", ans);

        for (int i = 1; i <= n; i++) G[i].clear();
    }

    return 0;
}
```

## [E - Range Minimum Sum](https://codeforces.com/contest/1988/problem/E)

差点就赛时A了，`st` 结束后两发就过了，悲

第一次是没开 `long long` ，然而懒得一个个看直接 define 就过了，四舍五入也是一发A （bushi

---

令 $L_i, R_i$ 表示第一个更小的数字，$LL_i, RR_i$ 表示第二个更小的数字

初始序列的答案就是 $\sum_{} a_i \times (i - L_i) \times (R_i - i)$ ，即在 $(L_i, R_i)$ 内的经过 $i$ 的区间个数乘上 $a_i$

再看删除一个数对答案的贡献，假设删掉的是 $a_i$ 

1. 以 $i$ 为边界的区间会被直接删掉

2. 对于经过 $i$ 但是边界不在 $i$ 的区间，如果它的最小值是 $a_i$ ，那么会变成一个更大的数

第一部分比较好算，在单调栈上二分就能处理出$L_i$ 和 $R_i$ ，以及以 $i$ 为左 / 右边界的区间贡献，这部分时间复杂度是 $O(nlogn)$ 

第二部分，对每个 $\{L_i,\ i\}$ 先按照 $L_i$ 排序，在 $[1, L_i - 1]$ 的单调栈上二分就能找到 $LL_i$ ， $RR_i$ 同理，这部分复杂度也是 $O(nlogn)$ 

在删除 $a_i$ 时，对于一个以 $i$ 为 $L_i$ 的数 $a_j$ ， $(LL_j, R_j)$ 这个区间里面，没有比 $a_j$ 更小的数，所以其中所有经过 $i$ 的所有区间贡献都将变为 $a_j$ 。也就是左边界在 $(LL_j, L_j)$ ，右边界在 $[j, R_j)$ 的区间贡献会从 $a_i$ 上升到 $a_j$ ，计算为 $(L_j - LL_j - 1) \times (R_j - j) \times (a_j - a_i)$ ，对 $R_i$ 同理

删除某个数就是原答案减去第一部分再加上第二部分

### Code

```cpp
#include<bits/stdc++.h>
#define LL long long
#define int long long
using namespace std;
int read() {
    int x = 0, w = 1;
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

const int maxn = 5e5 + 5;
const int inf = 1e9;

int T;
int n;
int a[maxn];
int L[maxn], R[maxn];
int LLL[maxn], RR[maxn];
LL del[maxn];
LL del1[maxn], del2[maxn];


struct node {
    int nxt, id;
};
bool operator < (node x, node y) {
    if (x.nxt != y.nxt) return x.nxt > y.nxt;
    return x.id > y.id;
}
struct node2 {
    int nxt, id;
};
bool operator < (node2 x, node2 y) {
    if (x.nxt != y.nxt) return x.nxt < y.nxt;
    return x.id > y.id;
}
signed main()
{
    T = read();
    while (T--) {
        n = read();
        for (int i = 1; i <= n; i++) a[i] = read();

        vector<int> pos;
        priority_queue<node> q;
        pos.push_back(0);
        a[0] = 0;
        LL sum = 0;
        for (int i = 1; i <= n; i++) {
            while (!pos.empty() && a[*pos.rbegin()] > a[i]) {
                if (a[*pos.rbegin()]) sum -= 1ll * a[*pos.rbegin()] * (pos[pos.size() - 1] - pos[pos.size() - 2]);
                pos.pop_back();
            }
            pos.push_back(i);
            sum += a[i] * (i - pos[pos.size() - 2]);
            del[i] += sum;
            L[i] = pos[pos.size() - 2];
            if (L[i] >= 1) q.push({L[i], i});
        }

        pos.clear();
        pos.push_back(0);
        for (int i = 1; i <= n; i++) {
            while (!q.empty() && q.top().nxt == i) {
                auto [nxt, id] = q.top();
                q.pop();

                int len = pos.size();
                int l = 0, r = len - 1, tag = 0;
                while (l <= r) {
                    int mid = (l + r) >> 1;
                    if (a[pos[mid]] < a[id]) tag = mid, l = mid + 1;
                        else r = mid - 1;
                }
                LLL[id] = pos[tag];
            }

            while (!pos.empty() && a[*pos.rbegin()] > a[i]) {
                pos.pop_back();
            }
            pos.push_back(i);
        } //这里和下面求LL, RR的复杂度写高了

        priority_queue<node2> q1;
        pos.clear();
        pos.push_back(n + 1);
        a[n + 1] = 0;
        sum = 0;
        for (int i = n; i; i--) {
            while (!pos.empty() && a[*pos.rbegin()] > a[i]) {
                if (a[*pos.rbegin()]) sum -= 1ll * a[*pos.rbegin()] * (pos[pos.size() - 2] - pos[pos.size() - 1]);
                pos.pop_back();
            }
            pos.push_back(i);
            sum += a[i] * (pos[pos.size() - 2] - i);
            del[i] += sum - a[i];
            R[i] = pos[pos.size() - 2];
            if (R[i] <= n) q1.push({R[i], i});
        }

        pos.clear();
        pos.push_back(n + 1);
        for (int i = n; i; i--) {
            while (!q1.empty() && q1.top().nxt == i) {
                auto [nxt, id] = q1.top();
                q1.pop();

                int len = pos.size();
                int l = 0, r = len - 1, tag = n + 1;
                while (l <= r) {
                    int mid = (l + r) >> 1;
                    if (a[pos[mid]] < a[id]) tag = mid, l = mid + 1;
                        else r = mid - 1;
                }
                RR[id] = pos[tag];
            }

            while (!pos.empty() && a[*pos.rbegin()] > a[i]) {
                pos.pop_back();
            }
            pos.push_back(i);
        }

        for (int i = 1; i <= n; i++) {
            if (L[i] != LLL[i] )  {
                int l = L[i];
                del1[l] += abs(a[i] - a[l]) * (abs(L[i] - LLL[i]) - 1) * abs(R[i] - i);
            }
        }
        for (int i = n; i; i--) {
            if (R[i] != RR[i] ) {
                int r = R[i];
                del2[r] += abs(a[i] - a[r]) * (abs(RR[i] - R[i]) - 1) * abs(L[i] - i);
            }
        }

        LL tot = 0;
        for (int i = 1; i <= n; i++) {
            int sl = i - L[i], sr = R[i] - i;
            tot += 1ll * sl * sr * a[i];
        }

        for (int i = 1; i <= n; i++)
            printf("%lld ", tot - del[i] + del1[i] + del2[i]);
        putchar('\n');

        for (int i = 0; i <= n + 1; i++) del[i] = del1[i] = del2[i] = L[i] = R[i] = LLL[i] = RR[i] = 0;
    }

    return 0;
}
```