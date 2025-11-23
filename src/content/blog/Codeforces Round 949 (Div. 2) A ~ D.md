---
title: 'Codeforces Round 949 (Div. 2) A - D'
description: ''
publishDate: 2024-06-01
tags:
- ACM
- CF
---
<!-- more -->

最恶心的一集，不知道 `C` 的意义在哪里

## [A - Turtle and Piggy Are Playing a Game](https://codeforces.com/contest/1981/problem/A)

答案为 $r$  的二进制位数

### Code
```cpp
#include<bits/stdc++.h>
#define LL long long
using namespace std;
int T;
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
int main()
{
    T = read();
    while (T--) {
        int l, r;
        l = read(); r = read();
        int ans = 0;
        while (r >= 2) {
            ans++, r >>= 1;
        }
        printf("%d\n", ans);
    }

    return 0;
}
```

## [B - Turtle and an Infinite Sequence](https://codeforces.com/contest/1981/problem/B)

二进制下每一位从 $0$ 开的规律是：$2^i$ 个 $0$ ， $2^i$ 个 $1$ ， $2^i$ 个 $0$ ，...

找一下每位离 $n$ 的最小距离即可

---

又笨比连WA三次过的比 `C` 还晚，我不说是谁

### Code

```cpp
#include<bits/stdc++.h>
#define LL long long
using namespace std;
int T;
LL n, m;
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
int main()
{
    T = read();
    while (T--) {
        n = read(); m = read();
        if (!m) {
            printf("%d\n", n);
            continue;
        }

        LL ans = n;
        for (int i = 0; i <= 32; i++) {
            LL now = 1ll << i;
            LL num = n / now;
            if (num & 1) continue;
                else {
                    LL dis = 1e18;
                    if (num >= 2) {
                        dis = (n) % (2 * now) + 1;
                        dis = min(dis, now - dis + 1);
                    }
                    else {
                        dis = now - n;
                    }
                    if (dis <= m) ans |= now;
                }
        }

        printf("%lld\n", ans);
    }

    return 0;
}
```

## [C - Turtle and an Incomplete Sequence](https://codeforces.com/contest/1981/problem/C)

思路及其简单，实现及其麻烦的恶心模拟

---

对于两个确定的数字，如果二进制位数差值比距离大，或者奇偶性不同，则结果为 -1

对两个相邻的确定的数，先通过对较大的数整除2使两个数二进制长度相等，然后两个数同时整除2，如果先除到 1 了，那么接下来每步 2 1 2 1 交替即可

如果先填到中间只剩下一个 -1 ，那么填入左右任意一个数整除2的结果，看和另外一个是否满足条件即可

对于第一个和最后一个位置，* 2 , / 2 交替填即可

### Code

```cpp
#include<bits/stdc++.h>
#define LL long long
using namespace std;
const int maxn = 2e5 + 5;
int T;
int n;
int a[maxn], b[maxn];
int s[maxn];
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
int check(int l, int r) {
    if (b[l] / 2 == b[r] || b[r] / 2 == b[l]) return 1;
    return 0;
}
int main()
{
    T = read();
    while (T--) {
        n = read();
        for (int i = 1; i <= n; i++) a[i] = read(), s[i] = 0;

        vector<int> p;
        for (int i = 1; i <= n; i++) {
            if (a[i] != -1) p.push_back(i), b[i] = a[i];
            int t = a[i];
            if (t == -1) continue;
            while (t) {
                s[i]++, t >>= 1;
            }
        }
        if (p.empty()) {
            for (int i = 1; i <= n; i++)
                printf(i & 1 ? "1 " : "2 ");
            putchar('\n');
            continue;
        }
        int ff = 1, len = p.size();
        for (int i = 0; i < len - 1; i++) {
            int l = p[i], r = p[i + 1];
            if (abs(s[r] - s[l]) <= r - l && ((abs(s[r] - s[l]) & 1) == ((r - l) & 1))) {
                if (r - l == 1) {
                    if (check(l, r)) continue;
                        else {ff = 0; break;}
                }
                if (a[l] > a[r]) {
                    int t = abs(s[l] - s[r]);
                    while (l < r - 1 && t) {
                        l++;
                        b[l] = b[l - 1] >> 1;
                        t--;
                    }
                } else {
                    int t = abs(s[l] - s[r]);
                    while (l < r - 1 && t) {
                        r--;
                        b[r] = b[r + 1] >> 1;
                        t--;
                    }
                }
                while (r - l >= 3) {
                    if (b[r] >= 2 && b[l] >= 2) {
                        r--;
                        l++;
                        b[r] = b[r + 1] >> 1, b[l] = b[l - 1] >> 1;
                    } else if (b[r] == 1 && b[l] == 1) {
                        r--, l++;
                        b[r] = 2, b[l] = 2;
                    } else {ff = 0; break;}
                }
                if (r - l == 1 && !check(l, r)) {ff = 0; break;}
                if (r - l == 2) {
                    b[r - 1] = b[r] >= 2 ? b[r] / 2 : b[r] * 2;
                    if (!(check(l, r - 1) && check(r - 1, r))) {ff = 0; break;}
                } 
                if (!ff) break;
            } else {
                ff = 0;
                break;
            }
        }
        if (!ff) {
            printf("-1\n");
            continue;
        }
        int now = p[0], tmp = 1;
        for (int i = now - 1; i; i--)
            b[i] = tmp ? b[i + 1] * 2 : (b[i + 1] >> 1), tmp ^= 1;
        tmp = 1, now = p[len - 1];
        for (int i = now + 1; i <= n; i++)
            b[i] = tmp ? b[i - 1] * 2 : (b[i - 1] >> 1), tmp ^= 1;

        for (int i = 1; i <= n; i++)
            printf("%lld ", b[i]);
        putchar('\n');
    }

    return 0;
}
```

## [D - Turtle and Multiplication](https://codeforces.com/contest/1981/problem/D)

首先如果全取用质数，那么任意两个不同的数的积一定不同，之后问题就转变为在 $k$ 个点的无向图里找一条欧拉回路（存在自边

对于无向图，只有当每个点的度数均为偶数才存在能遍历每条边的欧拉回路

所以当 $k$ 为奇数时，为完全图有效边为 $\frac {k \times (k - 1)} {2}$ 条

当 $k$ 为偶数时，需要在完全图的基础上删除 $\frac {k} {2} - 1$ 条边

先找到最小的 $k$ 然后建图跑一边欧拉回路即可

### Code

```cpp
#include<bits/stdc++.h>
#define LL long long
using namespace std;
const int maxn = 1e6 + 5;
int T;
int n, m;
int ss;
int a[maxn];
int ans[maxn];
int sum[maxn];
int edge[2005][2005];
int ant[maxn], tot;
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
void dfs(int cur) {
    for (int v = 1; v <= ss; v++) {
        if (edge[cur][v]) {
            edge[cur][v] = edge[v][cur] = 0;
            dfs(v);
        }
    }
    if (tot < n) ans[++tot] = cur;
}
int gets(int x) {
    int t = 1;
    while (t * (t + 1) / 2 < m) t += 2;
    if (t > 1 && t * (t - 1) / 2 + 1 - ((t - 1) / 2 - 1) >= n) t--;
    return t;
}
int main()
{
    vector<int> v(100001, 0);
    int cnt = 0;
    for (int i = 2; i <= 100000; i++) {
        if (!v[i]) a[++cnt] = i;
        for (int j = 1; j <= cnt; j++) {
            if (a[j] * i > 100000) break;
            v[a[j] * i] = 1;
            if (i % a[j] == 0) break;
        }
    }

    int id = 1;
    T = read();
    while (T--) {
        id++;
        n = read(); m = n - 1;

        ss = gets(m);
        for (int i = 1; i <= ss; i++)
            for (int j = 1; j <= ss; j++)
                edge[i][j] = 1;
        if (ss % 2 == 0) {
            for (int i = 3; i <= ss; i += 2)
                edge[i][i + 1] = edge[i + 1][i] = 0;
        }

        dfs(1);
        for (int i = 1; i <= n; i++)
            printf("%d ", a[ans[i]]);
        putchar('\n');

        tot = 0;
    }
    return 0;
}
```