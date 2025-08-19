---
title: 'Educational Codeforces Round 166 (Rated for Div. 2) A - D'
description: ''
publishDate: 2024-05-31
tags:
  - ACM
  - CF
---

<!-- more -->

半小时才进去，罚时最多的一集

这次先交了发 `A` WA了，又交了发 `B` 又WA了，然后还卡了将近半小时才进去，整个人就非常慌，代码打得乱的要死（

## [A - Verify Password](https://codeforces.com/contest/1976/problem/A)

先找字符+数字

连续字符和数字判断大小

---

$i - 1$ 打成 $i + 1$ ，Penalty += 10

### Code

```cpp
#include<bits/stdc++.h>
#define LL long long
using namespace std;
const int maxn = 2e1 + 5;
int T;
int n;
char ch[maxn];
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
int isnum(char c) {
    return c >= '0' && c <= '9';
}
int islet(char c) {
    return c >= 'a' && c <= 'z';
}
int main()
{
    T = read();
    while (T--) {
        n = read();
        scanf("%s", ch);
        int ff = 1;
        for (int i = 0; i < n - 1; i++) {
            if (islet(ch[i]) && isnum(ch[i + 1])) ff = 0;
        }
        for (int i = 1; i < n; i++) {
            if ((islet(ch[i]) && islet(ch[i - 1])) || (isnum(ch[i]) && isnum(ch[i - 1]))) {
                if (ch[i] < ch[i - 1]) ff = 0;
            }
        }
        printf(ff ? "YES\n" : "NO\n");
    }


    return 0;
}
```

## [B - Increase/Decrease/Copy](https://codeforces.com/contest/1976/problem/B)

每一位的操作数是固定的

如果修改过程中 $a_i$ 经过 $b_{n + 1}$ ，最后一位只需要复制一次

不然还要加上离 $b_{n + 1}$ 最近的距离

---

$a_i$ 打成 $b_i$ ，Penalty += 20

### Code

```cpp
#include<bits/stdc++.h>
#define LL long long
using namespace std;
const int maxn = 2e5 + 5;
int T;
int n, m;
LL a[maxn], b[maxn];
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
    T = read();
    while (T--) {
        n = read();
        for (int i = 1; i <= n; i++) a[i] = read();
        for (int i = 1; i <= n + 1; i++) b[i] = read();

        LL ans = 0, res = 1e18;
        for (int i = 1; i <= n; i++) {
            ans += abs(a[i] - b[i]);
            if (b[i] > a[i]) {
                if (b[i] >= b[n + 1] && b[n + 1] >= a[i]) res = 0;
                    else res = min(res, 1ll * min(abs(b[i] - b[n + 1]), abs(a[i] - b[n + 1])));
            }
            else {
                if (a[i] >= b[n + 1] && b[n + 1] >= b[i]) res = 0;
                    else res = min(res, 1ll * min(abs(b[i] - b[n + 1]), abs(a[i] - b[n + 1])));
            }
        }
        printf("%lld\n", ans + res + 1);
    }

    return 0;
}
```

## [C - Job Interview](https://codeforces.com/contest/1976/problem/C)

二分找到第一个满足 $n$ 或者 $m$ 的位置，一直到这个位置的所有人都会被分到特长的职位上，后面的人都会分到没满的职位上

然后根据 $i$ 和这个位置的大小关系，减去 $i$ 产生的贡献即可

---

前缀和没开 `long long` ，Penalty += 10

### Code

```cpp
#include<bits/stdc++.h>
#define LL long long
#define int long long
using namespace std;
const int maxn = 2e5 + 5;
int T;
int n, m;
int a[maxn], b[maxn];
int va[maxn], vb[maxn], sa[maxn], sb[maxn];
int ssa[maxn], ssb[maxn];
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
signed main()
{
    T = read();
    while (T--) {
        n = read(); m = read();
        for (int i = 1; i <= n + m + 1; i++) a[i] = read();
        for (int i = 1; i <= n + m + 1; i++) b[i] = read();

        for (int i = 1; i <= n + m + 1; i++) {
            va[i] = va[i - 1] + (a[i] > b[i]);
            vb[i] = vb[i - 1] + (a[i] < b[i]);
            sa[i] = sa[i - 1], sb[i] = sb[i - 1];
            if (a[i] > b[i]) sa[i] += a[i];
                else sb[i] += b[i];
            ssa[i] = ssa[i - 1] + a[i];
            ssb[i] = ssb[i -1 ] + b[i];
        }

        for (int i = 1; i <= n + m + 1; i++) {
            int l = 1, r = n + m + 1, tag = 0, typ = 0;
            while (l <= r) {
                int mid = (l + r) >> 1;
                int suma = va[mid];
                if (mid >= i) suma -= a[i] > b[i];
                int sumb = vb[mid];
                if (mid >= i) sumb -= a[i] < b[i];
                if (suma >= n || sumb >= m) {
                    r = mid - 1, tag = mid;
                    if (suma >= n) typ = 1;
                    if (sumb >= m) typ = 2;
                } else l = mid + 1;
            }
            if (!m) tag = 0, typ = 2;
            if (!n) tag = 0, typ = 1;
            LL ans = 0;
            if (typ == 1) {
                ans = sa[tag] + sb[tag] + ssb[n + m + 1] - ssb[tag];
                if (tag >= i) ans -= max(a[i], b[i]);
                    else ans -= b[i];
            }
            if (typ == 2) {
                ans = sa[tag] + sb[tag] + ssa[n + m + 1] - ssa[tag];
                if (tag >= i) ans -= max(a[i], b[i]);
                    else ans -= a[i];
            }
            printf("%lld ", ans); 
        }
        putchar('\n');
    }

    return 0;
}
```

## [D - Invertible Bracket Sequences](https://codeforces.com/contest/1976/problem/D)

令 ( 表示 $1$ ， ) 表示 $-1$ ， $s_i$ 表示其前缀和

那么一个区间需要满足以下两个条件：

1. $s_r - s_{l - 1} = 0$

2. $2 * s_{l - 1} \geq max(s[l], s[l + 1], ..., s[r])$ 

然后推一下对于某个 $r$ ，从满足条件1的一个 $l$ 移动到 下一个 $l'$

$s_{l - 1}$ 不变，max不变或减少

所以随着 l 增加，条件2只会从不满足变满足，可以二分（其实双指针 + `set` 就行了，少个 log）

区间最大值用线段树维护一下即可

---

在循环里定义 maxn 个 `vector`， Penalty += 10

### Code

```cpp
#include<bits/stdc++.h>
#define LL long long
using namespace std;
const int maxn = 2e5 + 5;
int T;
char ch[maxn];
int a[maxn], s[maxn];
vector<int> p[maxn];
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

int t[maxn << 2];
void build(int l, int r, int p) {
    if (l == r) return t[p] = s[l], void();
    int mid = (l + r) >> 1;
    build(l, mid, p << 1);
    build(mid + 1, r, p << 1 | 1);
    t[p] = max(t[p << 1], t[p << 1 | 1]);
}
int query(int l, int r, int x, int y, int p) {
    if (x <= l && r <= y) return t[p];
    int mx = -1e9, mid = (l + r) >> 1;
    if (x <= mid) mx = max(mx, query(l, mid, x, y, p << 1));
    if (y > mid) mx = max(mx, query(mid + 1, r, x, y, p << 1 | 1));
    return mx;
}
int main()
{
    T = read();
    while (T--) {
        scanf("%s", ch);
        int n = strlen(ch);
        for (int i = 1; i <= n; i++) {
            if (ch[i - 1] == '(') a[i] = 1;
                else a[i] = -1;
            s[i] = s[i - 1] + a[i];
        }
        build(1, n, 1);

        vector<int> pos(n + 5, 0), cnt(n + 5, 0);
        int now = 0;
        for (int i = 1; i <= n; i++) {
            now += a[i];
            p[now].push_back(i);
            pos[i] = p[now].size() - 1;
            cnt[i] = now;
        }

        LL ans = 0;
        for (int i = 1; i <= n; i++) {
            int l = 0, r = pos[i] - 1, tag = -1;
            while (l <= r) {
                int mid = (l + r) >> 1;
                int posmid = p[cnt[i]][mid] + 1;
                if (s[posmid - 1] * 2 >= query(1, n, posmid, i, 1)) r = mid - 1, tag = mid;
                    else l = mid + 1;
            }
            if (tag != -1) ans += pos[i] - tag;
        }
        printf("%lld\n", ans);

        for (int i = 0; i <= n; i++) s[i] = 0, p[i].clear();
    }

    return 0;
}
```