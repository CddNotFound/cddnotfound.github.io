---
title: 'Codeforces Global Round 25 A - F'
description: ''
publishDate: 2024-04-10
tags:
    - ACM
    - CF
---

<!-- more -->

## [A. Dual Trigger](https://codeforces.com/contest/1951/problem/A)

目标状态有偶数个 $1$ 且不为 $2$ 就可以

只有两个的时候要看是否相邻

### Code

```cpp
int main()
{
    T = read();
    while (T--) {
        n = read();
        scanf("%s", &ch);
        int s = 0;
        for (int i = 0; i < n; i++)
            s += ch[i] - 48;
        if (s & 1) {
            printf("NO\n");
            continue;
        }
        if (s > 2 || !s) {
            printf("YES\n");
            continue;
        }
        int ff = 1;
        for (int i = 1; i < n; i++)
            if (ch[i] == '1' && ch[i - 1] == '1') ff = 0;
        printf(ff ? "YES\n" : "NO\n");
    }

    return 0;
}
```

## [B. Battle Cows](https://codeforces.com/contest/1951/problem/B)

考虑两种情况：

第一种将 $k$ 移到第一位开始模拟

第二种将 $k$ 移动到在 $k$ 前面的第一个比 $a_k$ 大的位置模拟，如果此时位置不在首位，因为还要和前面的牛比一次，答案要$+ 1$ 

两种情况取最优即可

### Code

```cpp
int main()
{
    T = read();
    while (T--) {
        n = read(); k = read();
        for (int i = 1; i <= n; i++) a[i] = read();
        mx = ans = s = 0;
        for (int i = 1; i <= n; i++)
            if (a[i] > a[k]) {mx = i; break;}
        s = 0;
        swap(a[1], a[k]);
        for (int i = 2; i <= n; i++)
            if (a[i] < a[1]) s++;
                else break;   
        ans = max(ans, s);
        swap(a[1], a[k]);

        if (mx && mx < k) {
            s = 0;
            swap(a[mx], a[k]);
            for (int i = mx + 1; i <= n; i++)
                if (a[i] < a[mx]) s++;
                    else break;
            if (mx > 1) s++;
            ans = max(ans, s);
        }
        printf("%d\n", ans);
    }

    return 0;
}
```

## [C. Ticket Hoarding](https://codeforces.com/contest/1951/problem/C)

对于一个买票序列 $[c_1, c_2, ..., c_l]$ ( $c[i]$ 代表某一天买的票数)，无论顺序怎么变化，产生的额外价格都是一样的

当每天都尽可能买满，产生的额外价格是最小的

所以贪心选价格最小的若干天买票即可

### Code

```cpp
int main()
{
    T = read();
    while (T--) {
        n = read(); m = read(); k = read();
        ans = 0;
        for (int i = 1; i <= n; i++) a[i] = read();
        sort(a + 1, a + 1 + n);

        for (int i = 1; i <= n; i++) {
            LL now = min(k, m);
            k -= now;
            ans += a[i] * now + now * k;
        }
        printf("%lld\n", ans);
    }

    return 0;
}
```

## [D. Buying Jewels](https://codeforces.com/contest/1951/problem/D)

首先不难推出当 $k \leq \lfloor \frac {n} {2} + 1\rfloor$ 或者 $n = k$ 时才有可能（不过也没什么用

当 $n = k$ ，设一个价格为 $1$ 的摊就行

对于 $2 \times (n - (k - 1)) > n$ 即 $k \leq \lfloor \frac {n} {2} + 1\rfloor$，可以先按 $n - k + 1$ 的价格买一个，然后一元一个买 $k - 1$ 个

剩下的情况 $k$ 肯定超过 $n$ 的一半，所以不考虑 $3$ 及以上的价格

此时买的最多的组合就是先 $2$ 后 $1$ ，为 $\lfloor \frac {n + 1} {2} \rfloor \leq \lfloor \frac {n} {2} + 1 \rfloor$，所以无解

注意特判 $n < k$ 的情况（应该是有的，因为没写RE了

### Code

```cpp
int main()
{
    T = read();
    while (T--) {
        n = read(); k = read();
        if (k > n) {
            printf("NO\n");
            continue;
        }
        LL p1 = n - (k - 1), p2 = 1; //n = k时凑巧也成立就不特判了
        if (n / p1 + n % p1 / p2 == k) printf("YES\n2\n%lld %lld\n", p1, p2);
            else printf("NO\n");
    }

    return 0;
}
```

## [E. No Palindromes](https://codeforces.com/contest/1951/problem/E)

首先只有三种情况时无解：

形如aaaaaaa...

形如abababa...

形如aa...aabaa...aa

其中第二三种可能长度均为奇数

然后如果不是回文串就不用割了

是的话一定能分成两个非回文，用字符串哈希扫一遍就行

---

数据好像有点水，不特判只用单哈希扫一遍也能过（

### Code

```cpp
#include<bits/stdc++.h>
#define LL long long
using namespace std;
const int maxn = 1e6 + 5;
const int base = 131;
const LL P = 998244353;
LL d1[maxn], d2[maxn];
int T;
int n;
char ch[maxn];
int s[30], cnt;
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
void add(int x) {
    if (!s[x]) cnt++;
    s[x]++;
}
void exgcd(LL a, LL b, LL &x, LL &y) {
    if (!b) return (void)(x = 1, y = 0);
    exgcd(b, a % b, y, x);
    y -= a / b * x;
}
LL gets(LL x, LL a) {
    LL sum = 1;
    while (a) {
        if (a & 1) (sum *= x) %= P;
        (x *= x) %= P;
        a >>= 1;
    }
    return sum;
}
int re(int l, int r) {
    LL x, y;
    exgcd(gets(base, l - 1), P, x, y);
    LL s1 = (d1[r] - d1[l - 1]) * x % P;
    exgcd(gets(base, n - r), P, x, y);
    LL s2 = (d2[l] - d2[r + 1]) * x % P;
    (s1 += P) %= P, (s2 += P) %= P;
    return s1 == s2;
}
int main()
{
    T = read();
    while (T--) {
        scanf("%s", &ch);
        n = strlen(ch);
        memset(s, 0, sizeof(s));
        cnt = 0;
        LL p = 1;
        for (int i = 0; i < n; i++) {
            int t = ch[i] - 96;
            add(ch[i] - 96);
            d1[i + 1] = (d1[i] + p * t % P) % P;
            (p *= base) %= P;
        }
        p = 1;
        for (int i = n - 1; i >= 0; i--) {
            int t = ch[i] - 96;
            d2[i + 1] = (d2[i + 2] + p * t % P) % P;
            (p *= base) %= P;
        }

        int ff = !(cnt == 1);
        if (cnt == 2 && (n & 1)) {
            int mid = n / 2 + 1;
            if (s[ch[mid - 1] - 96] == 1) ff = 0;
            int t = 1;
            for (int i = 1; i < n; i++)
                if (ch[i] == ch[i - 1]) {t = 0; break;}
            if (t) ff = 0;
        }
        if (!ff) {
            printf("NO\n");
            continue;
        }

        ff = 0;
        for (int i = 1; i <= n / 2; i++)
            if (ch[i - 1] != ch[n - i]) {ff = 1; break;}
        if (ff) {
            printf("YES\n");
            printf("1\n");
            printf("%s\n", ch);
            continue;
        }
        for (int i = 2; i <= n - 1; i++) {
            if (!re(1, i) && !re(i + 1, n)) {
                printf("YES\n");
                printf("2\n");
                for (int j = 0; j < i; j++) putchar(ch[j]);
                putchar(' ');
                for (int j = i; j < n; j++) putchar(ch[j]);
                putchar('\n');
                break;
            }
        }
    }

    return 0;
}
```

---

## [F. Inversion Composition](https://codeforces.com/contest/1951/problem/F)

设 $p$ 序列的逆序对个数为 $inv$，则 $k$ 的合法范围应该是 $[inv, n \times (n - 1) - inv]$，且奇偶性应一致。

证明：

对于一个 $w$ 上的一个位置 $(i, j)(i < j)$ ，如果 $p_i > p_j$ 它的贡献恒为 $1$ ，否则就可以通过交换在 $0$ 和 $2$ 中转换

所以 $k$ 和 $inv$ 奇偶性应该相同

而当 $w$ 递增时，贡献为 $0$ 或 $1$ 最小，$w$ 递减时，贡献为 $1$ 或 $2$ 最大，范围就是$[inv, n \times (n - 1) - inv]$

所以我们可以先把 $w$ 构造成 $i, i - 1, i - 2, ..., 2, 1, i + 1, i + 2, ... , n$ 并计算此时的贡献，就是 $p_1$ 到 $p_n$ 的顺序对数量 * 2，直到总和超过 $k$

然后我们考虑将 $[1, i]$ 中的一个数字 $j$ 移动到 $1$ 后面，此时将 $1$ ~ $j - 1$ 和 $j$ 的若干数对从逆序变为顺序，即把贡献为 $2$ 的置为 $0$ ，从而凑出 $k$ 

逆序/顺序对用树状数组维护一下就好了

### Code

```cpp
#include<bits/stdc++.h>
#define LL long long
using namespace std;
const int maxn = 3e5 + 5;
int T;
LL n, k;
LL p[maxn], q[maxn], w[maxn], pos[maxn];
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

LL t[maxn];
int lowbit(int x) {return x & -x;}
void clear() {
    for (int i = 0; i <= n; i++) t[i] = 0;
}
void add(int pos, int y) {
    for (int i = pos; i <= n; i += lowbit(i))
        t[i] += y;
}
LL gets(int pos) {
    LL sum = 0;
    for (int i = pos; i; i -= lowbit(i))
        sum += t[i];
    return sum;
}
int main()
{
    T = read();
    while (T--) {
        n = read(); k = read();
        clear();
        LL s = 0;
        for (int i = 1; i <= n; i++) {
            pos[p[i] = read()] = i;
            s += gets(n) - gets(p[i]);
            add(p[i], 1);
            w[i] = i;
        }
        if (k < s || n * (n - 1) - s < k || (k - s) % 2) {
            printf("NO\n");
            continue;
        }

        k = (k - s) >> 1;
        if (k) {
            clear();
            for (int i = 1; i <= n; i++) {
                LL d = gets(p[i]);
                add(p[i], 1);
                if (k > d) k -= d;
                    else {
                        int now = i;
                        for (int j = 1; j < i; j++) {
                            w[j] = now--;
                            if (k && p[j] < p[i]) {
                                k--;
                                if (!k) w[i] = now--;
                            }
                        }
                        break;
                    }
            }
        }
        printf("YES\n");
        for (int i = 1; i <= n; i++)
            printf("%lld ", w[pos[i]]);
        putchar('\n');
    }

    return 0;
}
```