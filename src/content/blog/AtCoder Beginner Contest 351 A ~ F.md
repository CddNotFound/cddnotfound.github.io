---
title: 'AtCoder Beginner Contest 351 A - F'
description: ''
publishDate: 2024-04-28
tags:
  - ACM
  - Atc
---
<!-- more -->
lazy一下， $G$ 以后再补吧（
## [A. The bottom of the ninth](https://atcoder.jp/contests/abc351/tasks/abc351_a)
### Code
```cpp
int main()
{
    int s = 0;
    for (int i = 1; i <= 9; i++) s += read();
    for (int i = 1; i <= 8; i++) s -= read();

    printf("%d\n", s + 1);

    return 0;
}
```

## [B. Spot the Difference](https://atcoder.jp/contests/abc351/tasks/abc351_b)
### Code
```cpp
int main()
{
    n = read();
    for (int i = 0; i < n; i++) {
        scanf("%s", &a[i]);
    }
    for (int i = 0; i < n; i++) {
        scanf("%s", &b[i]);
    }
    int x = -1, y = -1;
    for (int i = 0; i < n; i++)
        for (int j = 0; j < n; j++)
            if (a[i][j] != b[i][j]) {x = i + 1, y = j + 1; break;}
    printf("%d %d\n", x, y);

    return 0;
}
```

## [C. Merge the balls](https://atcoder.jp/contests/abc351/tasks/abc351_c)
### Code
```cpp
int main()
{
    n = read();
    for (int i = 1; i <= n; i++) a[i] = read();

    for (int i = 1; i <= n; i++) {
        l[++top] = a[i];
        if (top <= 1) continue;
        while (top > 1 && l[top] == l[top - 1]) top--, l[top]++;
    }
    printf("%d\n", top);

    return 0;
}
```

## [D. Grid and Magnet](https://atcoder.jp/contests/abc351/tasks/abc351_d)
就BFS

写的有点丑陋（
### Code
```cpp
#include<bits/stdc++.h>
#define LL long long
using namespace std;
const int maxn = 1e3 + 5;
const int dx[5] = {0, 1, 0, -1, 0};
const int dy[5] = {0, 0, 1, 0, -1};
int n, m;
char ch[maxn];
int a[maxn][maxn], v[maxn][maxn], id;
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
int dfs(int x, int y) {
    if (a[x][y] == 0 && v[x][y] == id) return 0;
    if (a[x][y] == 2) {
        int t = v[x][y] != id;
        v[x][y] = id;
        return t;
    }
    v[x][y] = id;
    int s = 1;

    for (int k = 1; k <= 4; k++) {
        int tx = x + dx[k], ty = y + dy[k];
        if (tx <= 0 || tx > n || ty <= 0 || ty > m || a[tx][ty] == 1 || (a[tx][ty] == 0 && v[tx][ty])) continue;
        s += dfs(tx, ty);
    }

    return s;
}
int main()
{
    n = read(); m = read();
    for (int i = 1; i <= n; i++) {
        scanf("%s", &ch);
        for (int j = 1; j <= m; j++)
            if (ch[j - 1] == '#') a[i][j] = 1;
                else a[i][j] = 0;
    }

    for (int i = 1; i <= n; i++)
        for (int j = 1; j <= m; j++) {
            if (a[i][j] != 1) continue;
            for (int k = 1; k <= 4; k++) {
                int tx = i + dx[k], ty = j + dy[k];
                if (tx <= 0 || tx > n || ty <= 0 || ty > m || a[tx][ty] != 0) continue;
                a[tx][ty] = 2;
            }
        }
    int ans = 0;
    for (int i = 1; i <= n; i++)
        for (int j = 1; j <= m; j++) {
            if (a[i][j] == 1) continue;
            ++id;
            if (a[i][j] == 0 || a[i][j] == 2) ans = max(ans, dfs(i, j));
        }
    printf("%d\n", ans);

    return 0;
}
```

## [E. Jump Distance Sum](https://atcoder.jp/contests/abc351/tasks/abc351_e)
#### #1
首先我们对一个点画线，斜率分别是 $1$ 和 $-1$ ，可以分成四个区域，正下方的区域的距离是 $y$ 的差，右侧的距离是 $x$ 的差

那么处理出每个点对应的两条线 $y = x + b_1$ 和 $y = -x + b_2$ 的两个 $b$ ，按照 $b_1$ 升序处理，那么只要用一个数据结构维护按 $b_2$ 排序的 $x, y$ 的区间和即可 

树状数组线段树都行

懒得离散化用 `Splay` 偷鸡也行（
### Code
```cpp
#include<bits/stdc++.h>
#define LL long long
using namespace std;
const int maxn = 2e5 + 5;
const int inf = 1e9;
int n;
struct point {
    LL x, y, t, b;
}a[maxn], s1[maxn], s2[maxn];
int cnt1, cnt2;
struct _Splay {
    int rt;
    int val[maxn], cnt[maxn], ch[maxn][2], fa[maxn], siz[maxn], tot = 0;
    LL sum[maxn], a[maxn];

    void pushup(int cur) {return (void)(siz[cur] = siz[ch[cur][1]] + siz[ch[cur][0]] + cnt[cur], sum[cur] = sum[ch[cur][1]] + sum[ch[cur][0]] + a[cur]);}
    int chk(int cur) {return cur == ch[fa[cur]][1];}
    void rotate(int x) {
        int y = fa[x], z = fa[y], k = chk(x), w = ch[x][k ^ 1];
        ch[y][k] = w, fa[w] = y;
        ch[z][chk(y)] = x, fa[x] = z;
        ch[x][k ^ 1] = y, fa[y] = x;
        pushup(y); pushup(x);
    }
    void splay(int cur, int g = 0) {
        while (fa[cur] != g) {
            int y = fa[cur], z = fa[y];
            if (z != g)
                if (chk(y) == chk(z)) rotate(y);
                    else rotate(cur);
            rotate(cur); 
        }
        if (!g) rt = cur;
    }
    void ins(int x, LL y) {
        int cur = rt, ftr = 0;
        while (cur && val[cur] != x) ftr = cur, cur = ch[cur][x > val[cur]]; 
        if (cur) return (void)(cnt[cur]++, a[cur] += y, splay(cur));
        cur = ++tot;
        val[cur] = x, a[cur] = y;
        cnt[cur] = siz[cur] = 1;
        fa[cur] = ftr;
        if (ftr) ch[ftr][x > val[ftr]] = cur;
        ch[cur][0] = ch[cur][1] = 0;
        splay(cur);
        return ;
    }
    void find(int x) {
        int cur = rt;
        while (ch[cur][x > val[cur]] && x != val[cur]) cur = ch[cur][x > val[cur]];
        splay(cur);
        rt = cur;
    }
    int pre(int x) {
        find(x);
        if (val[rt] < x) return rt;
        int cur = ch[rt][0];
        while (ch[cur][1]) cur = ch[cur][1];
        return splay(cur), cur;
    }
    int succ(int x) {
        find(x);
        if (val[rt] > x) return rt;
        int cur = ch[rt][1];
        while (ch[cur][0]) cur = ch[cur][0];
        return splay(cur), cur;
    }
    void del(int x) {
        int lst = pre(x), nxt = succ(x), cur = 0;
        splay(lst);
        splay(nxt, rt);
        cur = ch[nxt][0];
        if (cnt[cur] > 1) {
            cnt[cur]--;
            splay(cur);
        } else ch[nxt][0] = 0, fa[cur] = 0;
        pushup(cur); pushup(nxt);
    }
    int kth(int k) {
        k++;
        int cur = rt;
        while (1)
            if (ch[cur][0] && k <= siz[ch[cur][0]]) cur = ch[cur][0];
                else if (k > cnt[cur] + siz[ch[cur][0]]) k -= cnt[cur] + siz[ch[cur][0]], cur = ch[cur][1];
                    else return splay(cur), cur;
    }
    int rk(int x) {
        find(x);
        return val[rt] >= x ? siz[ch[rt][0]] : siz[ch[rt][0]] + cnt[rt];
    }
    LL query(LL x) {
        int nxt = succ(x);
        splay(nxt);
        return sum[ch[rt][0]];
    }
    void clear() {
        for (int i = 0; i <= tot; i++) val[i] = cnt[i] = siz[i] = fa[i] = ch[i][0] = ch[i][1] = a[i] = sum[i] = 0;
        rt = 0;
        return ;
    }
}X, Y, num;

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
bool cmp1(point x, point y) {
    return x.t < y.t;
}
int main()
{
    n = read();
    for (int i = 1; i <= n; i++) {
        a[i] = (point){read(), read(), 0, 0};
        a[i].x++, a[i].y++;
        a[i].b = a[i].x + a[i].y;
        a[i].t = a[i].y - a[i].x;
        if ((a[i].x + a[i].y) & 1) s1[++cnt1] = a[i];
            else s2[++cnt2] = a[i];
    }
    LL ans = 0;
    X.ins(inf, 0), Y.ins(inf, 0), num.ins(inf, 0);
    X.ins(-inf, 0), Y.ins(-inf, 0), num.ins(-inf, 0);
    sort(s1 + 1, s1 + 1 + cnt1, cmp1);
    for (int i = 1; i <= cnt1; i++) {
        auto [x, y, t, b] = s1[i];
        LL sum = num.query(b);
        ans += y * sum - Y.query(b);
        sum = num.query(2e8 + 5) - num.query(b);
        ans += (X.query(2e8 + 5) - X.query(b)) - sum * x;
        num.ins(b, 1);
        X.ins(b, x);
        Y.ins(b, y);
    }

    X.clear(); Y.clear(); num.clear();
    X.ins(inf, 0), Y.ins(inf, 0), num.ins(inf, 0);
    X.ins(-inf, 0), Y.ins(-inf, 0), num.ins(-inf, 0);

    sort(s2 + 1, s2 + 1 + cnt2, cmp1);
    for (int i = 1; i <= cnt2; i++) {
        auto [x, y, t, b] = s2[i];  
        LL sum = num.query(b);
        ans += y * sum - Y.query(b);
        sum = num.query(2e8 + 5) - num.query(b);
        ans += (X.query(2e8 + 5) - X.query(b)) - sum * x;
        num.ins(b, 1);
        X.ins(b, x);
        Y.ins(b, y);
    }
    printf("%lld\n", ans);

    return 0;
}
```
#### #2
先根据奇偶性分组

然后坐标转换成 $(x + y, x - y)$ ，从切比雪夫距离转换为曼哈顿距离

最后整体除 $2$ 即可
### Code
```cpp
#include<bits/stdc++.h>
#define LL long long
using namespace std;
const int maxn = 2e5 + 5;
int n;
int s[5][maxn];
int cnt1, cnt2;
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
    for (int i = 1; i <= n; i++) {
        int x, y;
        x = read(); y = read();
        if ((x + y) & 1) ++cnt1, s[1][cnt1] = x + y, s[2][cnt1] = x - y;
            else ++cnt2, s[3][cnt2] = x + y, s[4][cnt2] = x - y;
    }
    LL ans = 0;
    for (int i = 1; i <= 4; i++) {
        int len = i <= 2 ? cnt1 : cnt2;
        sort(s[i] + 1, s[i] + 1 + len);
        for (int j = 1; j <= len; j++) 
            ans += 1ll * (j * 2 - 1 - len) * s[i][j];
    }
    printf("%lld\n", ans / 2);

    return 0;
}
```

## [F - Double Sum](https://atcoder.jp/contests/abc351/tasks/abc351_f)
树状数组板子，但我写了个归并（
### Code
```cpp
#include<bits/stdc++.h>
#define LL long long
using namespace std;
const int maxn = 4e5 + 5;
int n;
LL a[maxn], b[maxn];
LL sa[maxn], sb[maxn];
LL ans = 0;
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
void sort(int l, int r) {
    if (l == r) return ;
    int mid = (l + r) >> 1;
    sort(l, mid);
    sort(mid + 1, r);

    sb[r] = a[r];
    for (int i = r - 1; i >= mid + 1; i--) sb[i] = sb[i + 1] + a[i];
    sb[r + 1] = 0;
    int p1 = l, p2 = r;
    for (int i = mid; i >= l; i--) {
        while (p2 > mid + 1 && a[p2 - 1] >= a[i]) p2--;
        if (a[i] > a[r]) continue;
        ans += sb[p2] - a[i] * (r - p2 + 1);
    }
    p2 = mid + 1;
    for (int i = l; i <= r; i++) {
        if (p1 <= mid && p2 <= r) {
            if (a[p1] > a[p2]) b[i] = a[p2++];
                else b[i] = a[p1++];
        } else {
            if (p1 <= mid) b[i] = a[p1++];
            if (p2 <= r) b[i] = a[p2++];
        }
    }
    for (int i = l; i <= r; i++) a[i] = b[i];
}
int main()
{
    n = read();
    for (int i = 1; i <= n; i++) a[i] = read();
    sort(1, n);
    printf("%lld\n", ans);

    return 0;
}
```