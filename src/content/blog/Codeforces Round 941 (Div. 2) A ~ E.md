---
title: 'Codeforces Round 941 (Div. 2) A - E'
description: ''
publishDate: 2024-04-28
tags:
  - ACM
  - CF
---

<!-- more -->

## [A. Card Exchange](https://codeforces.com/contest/1966/problem/A)

按牌的数量排序，大于等于 $k$ 张就能转换成 $k - 1$ 张下一张牌

### Code

```cpp
bool cmp(int x, int y) {
    return x > y;
}
int main()
{
    T = read();
    while (T--) {
        for (int i = 1; i <= 100; i++) s[i] = 0;
        n = read(); k = read();
        for (int i = 1; i <= n; i++) s[a[i] = read()]++;

        sort(s + 1, s + 1 + 100, cmp);
        int ans = 0;
        for (int i = 1; i <= 101; i++) {
            if (s[i] >= k) s[i] = 0, s[i + 1] += k - 1;
                else if (s[i]) ans += s[i];
        }
        printf("%d\n", ans);
    }

    return 0;
}
```

## [B. Rectangle Filling](https://codeforces.com/contest/1966/problem/B)

四条边上均出现某种颜色即可

很朴素的写法（

### Code

```cpp
int main()
{
    T = read();
    while (T--) {
        n = read(); m = read();
        for (int i = 1; i <= n; i++) {
            scanf("%s", &ch);
            for (int j = 1; j <= m; j++)
                if (ch[j - 1] == 'W') a[i][j] = 1;
                    else a[i][j] = 0;
        }

        int b = 0, c = 0, d = 0, e = 0, ff = 0;
        for (int j = 1; j <= m; j++) {
            if (a[1][j] == 1) b = 1;
            if (a[n][j] == 1) d = 1;
        }
        for (int i = 1; i <= n; i++) {
            if (a[i][1] == 1) c = 1;
            if (a[i][m] == 1) e = 1;
        }
        if (b && c && d && e) ff = 1;
        b = c = d = e = 0;
        for (int j = 1; j <= m; j++) {
            if (a[1][j] == 0) b = 1;
            if (a[n][j] == 0) d = 1;
        }
        for (int i = 1; i <= n; i++) {
            if (a[i][1] == 0) c = 1;
            if (a[i][m] == 0) e = 1;
        }
        if (b && c && d && e) ff = 1;

        printf(ff ? "YES\n" : "NO\n");
    }

    return 0;
}
```

## [C. Everything Nim](https://codeforces.com/contest/1966/problem/C)

题目等效于石子排序后求差分，然后按顺序每次取一个或多个

有个结论就是谁先碰到大于等于 $2$ 的石堆谁就赢

通过全取或者取到只剩一个将可以保证每次都先碰到后续的 $2$ ，从而达到必胜

全是 $1$ 的话模拟一下就好了

### Code

```cpp
int main()
{
    T = read();
    while (T--) {
        n = read();
        for (int i = 1; i <= n; i++) a[i] = read();

        sort(a + 1, a + 1 + n);
        a[0] = 0;

        int ff = 1, f2 = 0;
        for (int i = 1; i <= n; i++) {
            v[i] = min(2, a[i] - a[i - 1]);
            if (v[i] == 2) f2 = 1;
        }

        for (int i = 1; i <= n; i++) {
            if (v[i] == 1) ff ^= 1;
                else {
                    if (v[i] == 2) break;
                }
        }
        if (!f2) ff ^= 1;
        printf(ff ? "Alice\n" : "Bob\n");
    }

    return 0;
}
```

## [D. Missing Subsequence Sum](https://codeforces.com/contest/1966/problem/D)

首先当 $k = 1$ 时， 一个可行的序列是 $2, 3, 4, 8, 16, 32, ...$

那么假设 $k$ 在二进制最高位是 $2 ^ m$，我们可以先将 $2 ^ 0$ 到 $2 ^ {m - 1}$ 塞进去，然后再用一个 $k - 2 ^ m$ 就可以构造出 $1$ 到 $k - 1$ 的所有数

所以说我们接下来只要能构造出除了 $k$ 外所有 $k$ 的倍数即可，所以再塞 $k + 1, 2k, 3k, 4k, 8k, ...$ 

### Code

```cpp
#include<bits/stdc++.h>
#define LL long long
using namespace std;
const int maxn = 3e1 + 5;
int T;
int n, k;
int a[maxn], cnt;
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
        cnt = 0;
        n = read(); k = read();
        if (k == 1) {
            a[1] = 2, a[2] = 3;
            int now = 4;
            for (int i = 3; i <= 25; i++) {
                a[i] = now;
                now <<= 1;
            }
            printf("25\n");
            for (int i = 1; i <= 25; i++)
                printf("%d ", a[i]);
            putchar('\n');
            continue;
        }

        int now = 1, s = 1;
        while (s < k) {
            a[++cnt] = now;
            now <<= 1;
            s += now;
        }
        s -= now;
        if (k - s - 1) a[++cnt] = k - s - 1;

        if (n > k) a[++cnt] = k + 1;
        s = k << 1;
        while (s < n && n != k) {
            a[++cnt] = s;
            s += a[cnt];
        }
        if (3 * k <= n) a[++cnt] = 3 * k;

        printf("%d\n", cnt);
        for (int i = 1; i <= cnt; i++)
            printf("%d ", a[i]);
        putchar('\n');
    }

    return 0;
}
```

## [E. Folding Strip](https://codeforces.com/contest/1966/problem/E)

### #1

首先 $01$ 中间肯定是不能作为折痕的，然后我们按照将可能的折痕位置将纸条分割成若干个区间

那么对于一段区间，如果在边界的话就要看能不能向内折，在中间的话看能不能和左右两边同时构成镜像折三折

因为对折的顺序并不影响最终的结果，那么我们考虑按区间顺序挨个折

因为对折后对应位置数字相等，左边界向内折的话就是直接将这个区间删除，中间折三折就是把这个区间和左镜像删除

这个时候输出一下，发现折一次并不能完全对折成 $01010101$ 串，有的地方还能折，所以加个循环多折几次就能过了

这算不算某种偷鸡（

### Code

```cpp
#include<bits/stdc++.h>
#define LL long long
using namespace std;
const int maxn = 2e5 + 5;
int T;
int n;
int a[maxn], cnt;
int L[maxn], R[maxn];
struct lr {
    int l, r;
};
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
void del(int l, int r) {
    int pre = L[l], succ = R[r];
    R[pre] = succ, L[succ] = pre;
}
int main()
{
    T = read();
    while (T--) {
        n = read();
        for (int i = 1; i <= n; i++) scanf("%1d", &a[i]);

        int Time = 1000, len = n, s = 0;
        while (Time--) {
            queue<lr> q;
            int lst = 1;
            R[0] = 1, L[len + 1] = len;
            a[0] = -1;
            for (int i = 1; i <= len; i++) {
                if (a[i] == a[i - 1]) q.push((lr){lst, i - 1}), lst = i;
                L[i] = i - 1, R[i] = i + 1;
            }
            q.push((lr){lst, len});
            while (!q.empty()) {
                auto [l, r] = q.front();
                q.pop();

                int le = 1, ri = 1;
                int nowl = l, nowr = r;
                if (L[len + 1] != r) {
                    for (int i = r; i >= l; i--) {
                        nowr = R[nowr];
                        if (a[i] != a[nowr] || nowr == len + 1) {ri = 0; break;}
                    }
                }
                if (R[0] != l) {
                    for (int i = l; i <= r; i++) {
                        nowl = L[nowl];
                        if (a[i] != a[nowl] || nowl == 0) {le = 0; break;}
                    }
                }
                if (R[0] == l && L[len + 1] == r) le = ri = 0;
                if (le && ri) {
                    if (!nowl) nowl = R[0];
                    if (r != L[len + 1]) del(nowl, r);
                        else del(l, r);
                } 
            }
            s = 0;
            for (int now = R[0]; now <= len; now = R[now]) {
                a[++s] = a[now];
            }
            len = s;
        }        
        printf("%d\n", len);
    }

    return 0;
}
```

### #2

官解好抽象啊这么短（

已经知道折叠后的纸条必然是 $01$ 交替的串

那么相邻位置如果相同的话一定会被折叠

模拟一下这个折叠过程就行了

就是先顺着一个方向铺，遇到相同的数字就转向，记录一下往左往右能铺到最远的地方，这个距离就是答案

### Code

```cpp
#include<bits/stdc++.h>
#define LL long long
using namespace std;
const int maxn = 2e5 + 5;
int T;
int n;
int a[maxn];
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
        n = read();
        for (int i = 1; i <= n; i++) scanf("%1d", &a[i]);

        int l = 1, r = 1, now = 1, w = 1;
        a[0] = -1;
        for (int i = 1; i <= n; i++) {
            if (a[i] == a[i - 1]) w *= -1;
            now += w;
            l = min(l, now);
            r = max(r, now);
        }
        printf("%d\n", r - l);
    }

    return 0;
}
```