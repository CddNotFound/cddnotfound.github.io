---
title: 'Educational Codeforces Round 163 (Div. 2) A - E'
description: ''
publishDate: 2024-03-16
tags:
  - ACM
  - CF
---

<!-- more -->

## A.Special Characters

### Code

```cpp
int main()
{
    T = read();
    while (T--) {
        n = read();
        if (n & 1) {
            printf("NO\n");
            continue;
        }
        printf("YES\n");
        for (int i = 1; i <= n / 2; i++)
            if (i & 1) printf("AA");
                else printf("BB");
        putchar('\n');
    }

    return 0;
}
```

## B.Array Fix

```cpp
int main()
{
    T = read();
    while (T--) {
        n = read();
        for (int i = 1; i <= n; i++) a[i] = read();

        int ff = 1, succ = a[n];
        for (int i = n - 1; i; i--) {
            if (a[i] <= succ) {succ = a[i]; continue;}
            int t = a[i];
            while (t) {
                int now = t % 10;
                if (now <= succ) succ = now;
                    else {ff = 0; break;}
                t /= 10;
            }
            if (!ff) break;
        }
        printf(ff ? "YES\n" : "NO\n");
    }

    return 0;
}
```

## C.Arrow Path

### Code

```cpp
void dfs(int x, int y) {
    if (v[x][y]) return ;
    v[x][y] = 1;
    if (x == 2 && y == n) return ;
    for (int k = 1; k <= 4; k++) {
        int tx = x + dx[k], ty = y + dy[k];
        if (tx <= 0 || tx >= 3 || ty <= 0 || ty >= n + 1) continue;
        ty += d[tx][ty];
        if (tx <= 0 || tx >= 3 || ty <= 0 || ty >= n + 1) continue;
        if (!v[tx][ty]) dfs(tx, ty);
    }
}
int main()
{
    T = read();
    while (T--) {
        ans = 0;
        n = read();
        for (int i = 1; i <= 2; i++) {
            scanf("%s", &ch);
            for (int j = 0; j < n; j++) {
                if (ch[j] == '>') d[i][j + 1] = 1;
                    else d[i][j + 1] = -1;
                v[i][j + 1] = 0;
            }
        }
        dfs(1, 1);
        printf(v[2][n] ? "YES\n" : "NO\n");
    }

    return 0;
}
```

## D.Tandem Repeats?

从1到 $\frac{n}{2}$ 枚举长度 $len$ ，对于每个长度从第一个字母开始比较，如果连续匹配 $len$ 次则合法

### Code

```cpp
#include<bits/stdcpp.h>
#define LL long long
using namespace std;
const int maxn = 5e3 + 5;
int T;
int n;
char ch[maxn];
int ans;
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
        ans = 0;
        scanf("%s", &ch);
        n = strlen(ch);
        for (int i = n; i; i--) ch[i] = ch[i - 1];

        for (int len = 1; len <= n / 2; len++) {//应该倒着写找到直接break来着
            int cnt = 0;
            for (int i = 1; i <= n - len; i++) {
                if (ch[i] == ch[i + len] || ch[i] == '?' || ch[i + len] == '?') cnt++;
                    else cnt = 0;
                if (cnt >= len) {ans = max(ans, len << 1); break;}
            }
        }
        printf("%d\n", ans);
    }

    return 0;
}
```

## E.Clique Partition

首先暴搜模拟一下可以发现对于一个 $k$ 最多能让 $k$ 个点构成一个团

构造方法是

$$a[] = {st, st - 1, ..., 1, k, k - 1, ..., st + 1}$$
其中 $st = (k - 1) / 2$
然后每连续 $k$ 个分一组就行了

### Code

```cpp
#include<bits/stdcpp.h>
#define LL long long
using namespace std;
const int maxn = 1e2 + 5;
int T;
int n, k;
int a[maxn], col[maxn], colcnt;
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
void ini(int x) {
    if (x == 1) s[1] = 1;
    if (x == 2) s[1] = 1, s[2] = 2;
    if (x >= 3) {
        int st = (x - 1) / 2;
        for (int i = 1; i <= x; i++) {
            s[i] = st, st--;
            if (st <= 0) st = x;
        }
    }
}
int main()
{
    T = read();
    while (T--) {
        colcnt = 0;
        n = read(); k = read();
        ini(k);
        for (int i = 1; i <= n; i += k) {
            int l = i, r = min(n, i + k - 1);
            colcnt++;
            if (r - l + 1 != k) ini(r - l + 1);
            for (int j = l; j <= r; j++)
                a[j] = s[j - l + 1] + l - 1, col[j] = colcnt;
        }

        for (int i = 1; i <= n; i++)
            printf("%d ", a[i]);
        putchar('\n');
        printf("%d\n", colcnt);
        for (int i = 1; i <= n; i++)
            printf("%d ", col[i]);
        putchar('\n');
    }

    return 0;
}
```