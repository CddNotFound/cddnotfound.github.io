---
title: 'AtCoder Beginner Contest 352 A - F'
description: ''
publishDate: 2024-05-05
tags:
- ACM
- Atc
---

<!-- more -->

## [A - AtCoder Line](https://atcoder.jp/contests/abc352/tasks/abc352_a)

$z$ 在不在 $x, y$ 中间

### Code

```cpp
#include<bits/stdc++.h>
#define LL long long
using namespace std;
int n, x, y, z;
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
    n = read();
    x = read(); y = read(); z = read();
    printf(((x <= z && z <= y) || (y <= z && z <= x)) ? "Yes\n" : "No\n");

    return 0;
}
```

## [B - Typing](https://atcoder.jp/contests/abc352/tasks/abc352_b)

弄个指针，每匹配一位就输出

### Code

```cpp
#include<bits/stdc++.h>
#define LL long long
using namespace std;
const int maxn = 2e5 + 5;
char s[maxn], t[maxn];
int p;
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
    scanf("%s", &s);
    scanf("%s", &t);

    p = 0;
    int lens = strlen(s), lent = strlen(t);
    for (int i = 0; i < lent; i++) {
        if (p < lens && t[i] == s[p]) {
            printf("%d ", i + 1);
            p++;
        }
    }

    return 0;
}
```

## [C - Standing On The Shoulders](https://atcoder.jp/contests/abc352/tasks/abc352_c)

把一个 $a$ 换成 $b$ 后的最大值

### Code

```cpp
#include<bits/stdc++.h>
#define LL long long
using namespace std;
const int maxn = 2e5 + 5;
int n;
LL a[maxn], b[maxn];
LL s;
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
    n = read();
    for (int i = 1; i <= n; i++) a[i] = read(), b[i] = read(), s += a[i];

    LL ans = 0;
    for (int i = 1; i <= n; i++) {
        ans = max(ans, s - a[i] + b[i]);
    }

    printf("%lld\n", ans);
    return 0;
}
```

## [D - Permutation Subsequence](https://atcoder.jp/contests/abc352/tasks/abc352_d)

就是个滑动窗口

直接用 `set` 偷鸡了（

### Code

```cpp
#include<bits/stdc++.h>
#define LL long long
using namespace std;
const int maxn = 2e5 + 5;
const int inf = 1e9;
int n, k;
int a[maxn], pos[maxn];
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
    n = read(); k = read();
    for (int i = 1; i <= n; i++) a[i] = read(), pos[a[i]] = i;

    set<int> s;
    int ans = inf;
    for (int i = 1; i < k; i++) s.insert(pos[i]);
    for (int i = k; i <= n; i++) {
        s.insert(pos[i]);
        ans = min(ans, *s.rbegin() - *s.begin());
        s.erase(pos[i - k + 1]);
    }
    printf("%d\n", ans);

    return 0;
}
```

## [E - Clique Connect](https://atcoder.jp/contests/abc352/tasks/abc352_e)

一个点如果能被更便宜的边连接那其他更贵的边就没有贡献了

那么按照 $c$ 升序合并

用并查集维护，每次合并连边操作的所有点

### Code

```cpp
#include<bits/stdc++.h>
#define LL long long
using namespace std;
const int maxn = 2e5 + 5;
int n, m;
int f[maxn], v[maxn];
int d[maxn];
struct line {
    int k, c;
    vector<int> x;
}a[maxn];
bool cmp(line x, line y) {
    return x.c < y.c;
}
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
int find(int x) {
    return f[x] == x ? x : f[x] = find(f[x]);
}
int main()
{
    n = read(); m = read();
    for (int i = 1; i <= n; i++) f[i] = i;

    for (int i = 1; i <= m; i++) {
        a[i].k = read(); a[i].c = read();
        for (int j = 1; j <= a[i].k; j++) a[i].x.push_back(read());
    }
    sort(a + 1, a + 1 + m, cmp);
    LL ans = 0;
    for (int i = 1; i <= m; i++) {
        auto [k, c, x] = a[i];
        int fir = *x.begin();
        for (auto j : x) {
            if (j == fir) continue;
            if (find(fir) != find(j)) ans += c, f[find(j)] = find(fir);
        }
    }

    for (int i = 2; i <= n; i++)
        if (find(1) != find(i)) return printf("-1\n"), 0;

    printf("%lld\n", ans);

    return 0;
}
```

## [F - Estimate Order](https://atcoder.jp/contests/abc352/tasks/abc352_f)

#### #1 DFS

对于每个联通块，只要有一个位置确定了其余所有位置都确定了，那就可以对所有大小大于 $1$ 的联通块跑dfs，发现位置冲突就回溯

如果合法排名空下的位置多于两个，剩下人的排名都不能确定，否则就是空出的那个排名

### Code

```cpp
#include<bits/stdc++.h>
#define LL long long
using namespace std;
const int maxn = 2e1 + 5;
const int maxm = 4e2 + 5;
int n, m;
int a[maxm], b[maxm], c[maxm];
int v[maxn], fir[maxn];
int l[maxn], cnt;
int rk[maxn], trk[maxn], vis[maxn];
vector<pair<int, int> > G[maxn], S[maxn];

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
void dfss(int x, int cur, int ftr, int s) {
    for (auto [v, w] : G[cur]) {
        if (v == ftr) continue;
        S[x].push_back({v, s + w});
        dfss(x, v, cur, s + w);
    }
}
void dfs(int t, int lst) {
    if (t > cnt) {
        int num = 0;
        for (int i = 1; i <= n; i++) {
            if (!trk[i]) num++;
        }
        if (num >= 2) {
            for (int i = 1; i <= n; i++)
                if (!trk[i]) rk[i] = -1;
        }
        if (num == 1) {
            int dd[maxn] = {0}, aim = 0;
            for (int i = 1; i <= n; i++)
                dd[trk[i]] = 1;
            for (int i = 1; i <= n; i++)
                if (!dd[i]) {aim = i; break;}
            for (int i = 1; i <= n; i++)
                if (!trk[i]) trk[i] = aim;
        }
        for (int i = 1; i <= n; i++) {
            if (!rk[i] && trk[i]) rk[i] = trk[i];
            if (rk[i] && trk[i] && trk[i] != rk[i]) rk[i] = -1;
        }
        return ;
    }
    int tmptrk[maxn], tmpvis[maxn];
    for (int i = 1; i <= n; i++) tmptrk[i] = trk[i], tmpvis[i] = vis[i];

    for (int i = n; i; i--) {
        if (vis[i]) continue;
        trk[l[t]] = i;
        vis[i] = l[t];
        int ff = 0;
        for (auto [v, w] : S[l[t]]) {
            int aim = w + i;
            if (aim <= 0 || aim > n || (vis[aim] && vis[aim] != v) || (trk[v] && trk[v] != aim)) {ff = 1; break;}
          vis[aim] = v;
          trk[v] = aim;
        }
        if (!ff) dfs(t + 1, i);
        for (int j = 1; j <= n; j++) trk[j] = tmptrk[j], vis[j] = tmpvis[j];
    }
}
int main()
{
    n = read(); m = read();
    if (!m) {
        if (n == 1) return printf("1\n"), 0;

        for (int i = 1; i <= n; i++)
            printf("-1 ");
        putchar('\n');
        return 0;
    }
    for (int i = 1; i <= m; i++) {
        a[i] = read(), b[i] = read(); c[i] = read();
        fir[a[i]] = 1, v[b[i]] = 1;
        G[a[i]].push_back({b[i], -c[i]});
    }

    for (int i = 1; i <= n; i++) {
        dfss(i, i, 0, 0);
    }

    for (int i = 1; i <= n; i++) 
        if (fir[i] && !v[i]) l[++cnt] = i;

    dfs(1, 0);

    for (int i = 1; i <= n; i++)
        if (!rk[i]) rk[i] = -1;
    for (int i = 1; i <= n; i++)
        printf("%d ", rk[i]);
    putchar('\n');

    return 0;
}
```