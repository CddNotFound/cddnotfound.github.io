---
title: 'Codeforces Round 931 (Div. 2) A - D2'
description: ''
publishDate: 2024-03-02
tags:
  - ACM
  - CF
---
<!-- more -->
## A.Too Min Too Max
选最小  次小  最大  次大
### Code
```cpp
int main()
{
    T = read();
    while (T--) {
        n = read();
        for (int i = 1; i <= n; i++) a[i] = read();
        sort(a + 1, a + 1 + n);
        int ans = 0;
        ans = abs(a[n] - a[1]) + abs(a[n - 1] - a[1]) + abs(a[n - 1] - a[2]) + abs(a[n] - a[2]);
        printf("%d\n", ans);
    }

    return 0;
}
```

## B.Yet Another Coin Problem
懒得动脑直接打表了（

部分15+的数不用15的硬币可能更优，撤回一个15块比一下
### Code
```cpp
int a[31] = {0, 1, 2, 1, 2, 3, 1, 2, 3, 2, 1, 2, 2, 2, 3, 1, 2, 3, 2, 3, 2, 2, 3, 3, 3, 2, 3, 3, 3, 4, 2};
int main()
{
    T = read();
    while (T--) {
        n = read();
        if (n <= 30) {
            printf("%d\n", a[n]);
            continue;
        }
        int ans = n / 30 * 2 + a[n % 30];
        if (n % 30 <= 15) ans = min(ans, n / 30 * 2 - 1 + a[n % 30 + 15]);
        printf("%d\n", ans);
    }

    return 0;
}
```

## C.Find a Mine
一开始思路跑偏浪费了好多时间，后面改的有点急所以代码看着有点乱（

------
首先询问 $(1,1)$ ，离该点最近的地雷位于 $l:x + y = d + 2$ 这条线上，再分别询问这条线与矩阵边界的两个交点，我们会得到另外两个距离

因为只有两个地雷， 这两个距离至少有一个指向的是 $l$ 上的地雷，把两个指向的位置询问一个，距离是0就找到了，不是0就是另外一个

而且因为在同一条线上，如果距离为奇数就肯定是线外的另一个雷在干扰，可以直接输出另一个距离指向的位置
### Code
```cpp
#include<bits/stdc++.h>
#define LL long long
using namespace std;
int T;
int n, m;
int x;
int l[5];
int posx[5], posy[5];
int ansx[5], ansy[5];
int main()
{
    cin >> T;
    while (T--) {
        int X = 0, Y = 0;
        for (int i = 1; i <= 4; i++) posx[i] = posy[i] = 0;

        cin >> n >> m;
        cout << "? 1 1"<<endl;
        cin >> x;
        l[1] = x + 2;

        posx[1] = 1, posy[1] = l[1] - 1;
        posx[2] = l[1] - 1, posy[2] = 1;
        if (posy[1] > m) {
            posy[1] = m, posx[1] = l[1] - m;
        }
        if (posx[2] > n) {
            posx[2] = n, posy[2] = l[1] - n;
        }

            int d1, d2;
            cout<<"? "<<posx[1]<<' '<<posy[1]<<endl;
            cin>>d1;
            cout<<"? "<<posx[2]<<' '<<posy[2]<<endl;
            cin>>d2;

            if (d1 & 1) {
                d1 /= 2, d2 /= 2;
                ansx[1] = posx[2] - d2, ansy[1] = posy[2] + d2;
                cout<<"! "<<ansx[1]<<' '<<ansy[1]<<endl;
                continue;
            }
            if (d2 & 1) {
                d1 /= 2, d2 /= 2;
                ansx[1] = posx[1] + d1, ansy[1] = posy[1] - d1;
                cout<<"! "<<ansx[1]<<' '<<ansy[1]<<endl;
                continue;
            }
            d1 /= 2, d2 /= 2;
            ansx[1] = posx[2] - d2, ansy[1] = posy[2] + d2;
            ansx[2] = posx[1] + d1, ansy[2] = posy[1] - d1;
            cout<<"? "<<ansx[1]<<' '<<ansy[1]<<endl;
            int d3;
            cin>>d3;
            if (!d3) cout<<"! "<<ansx[1]<<' '<<ansy[1]<<endl;
                else cout<<"! "<<ansx[2]<<' '<<ansy[2]<<endl;
            continue;
    }

    return 0;
}
```

## D1.XOR Break --- Solo Version
首先 $m > n$ 的话肯定不行


对于一个需要将1变成0的位置，可以同时把低位所有的0变成1

所以找到最高的需要变成1的位置 $pos$ ，看高位（除了最高位）有没有1变0的，如果有第一步就可以把除了最高位全部变成 $y$ 的样子，第二步变最高位。

（不动最高位的话 $y$ 一定小于 $x$ ）

如果只有 $n$ 最高位是1变0，那就看 $pos$ 到次高位有没有1，没有1的话选择的 $y$ 一定会大与当前的 $x$ ，无解，有的话就可以一步到位。

### Code
```cpp
#include<bits/stdc++.h>
#define LL long long
using namespace std;
const int maxn = 1e2 + 5;
LL T;
LL n, m, t;
LL a1[maxn], a2[maxn], cnt1, cnt2;
LL ans[maxn];
LL read() {
    LL x = 0, w = 1;
    char ch = 0;
    while (ch < '0' || ch > '9') {
        if (ch == '-') w = -1;
        ch = getchar();
    }
    while (ch >= '0' && ch <= '9') {
        x = x * 10 + ch - 48;
       ch = getchar();
    }
    return x * w;
}
void print() {
    LL now = 0;
    for (int i = 1; i <= cnt1; i++)
        now += (1ll << (i - 1)) * a1[i];
    ans[2] = now;
}
int check() {
    int pos = 0, sum = 0;
    for (int i = cnt2; i; i--)
        if (a2[i] && !a1[i]) {pos = i; break;}
    if (!pos) return 1; //不需要0变1
    for (int i = cnt1 - 1; i > pos; i--)
        if (a1[i] && !a2[i]) return 1; //非最高位有1变0
    for (int i = cnt1 - 1; i > pos; i--)
        if (a1[i]) sum++;
    if (sum && a1[cnt1] && !a2[cnt1]) return 2; //有1
    return 0;
}
int main()
{
    T = read();
    while (T--) {
        cnt1 = cnt2 = 0;
        for (int i = 1; i <= 64; i++) a1[i] = a2[i] = 0;

        n = read(); t = m = read();
        ans[1] = n, ans[3] = m;
        if (m > n) {
            printf("-1\n");
            continue;
        }
        while (n) {
            a1[++cnt1] = n & 1;
            n >>= 1;
        }
        while (m) {
            a2[++cnt2] = m & 1;
            m >>= 1;
        }
        int tag = check();
        if (!tag) {
            printf("-1\n");
            continue;
        }

        for (int i = cnt1 - 1; i; i--)
            a1[i] = a2[i];

        print();
        if (ans[1] == ans[2] || ans[2] == ans[3] || tag == 2) {
            printf("1\n%lld %lld\n", ans[1], ans[3]);
        }
        else printf("2\n%lld %lld %lld\n", ans[1], ans[2], ans[3]);
    }

    return 0;
}
```

## D2.XOR Break --- Game Version
PS：1的个数指在二进制下，奇偶是指1的个数的奇偶

首先如果可选择的数只有一位是1就输了

对于一个有偶数个1的 $p$ ，分出的 $p1$ 和 $p2$ 就是均为奇或均为偶

对于一个有奇数个1的 $p$ ，分出的就是一奇一偶

（$p$ 的1会分给 $p1$ 和 $p2$， 一个0如果构造成两个1会同时改变 $p1$ 和 $p2$ 的奇偶性）

注意到最高能操作63次， $p$ 最多有64，我们可以考虑每次分成 $2 ^ m$ 和 $p \oplus 2 ^ m$，其中 $m$ 是 $p$ 中为1的最高位，每次干掉最高位保证最多63次操作后一定能让 $p$ 变成1和一个 $2 ^ x$ （只有一个1的数）

所以对于初始的 $p$ ，为奇则后手，为偶则先手，后手时对手分出的数为一奇一偶，我们选择偶数的那个分为 $2 ^ m$ 和 $p \oplus 2 ^ m$ ，第一个对手选完必输所以只能选第二个，第二个又是奇数个1，进入循环。
### Code
```cpp
#include<bits/stdc++.h>
#define LL long long
using namespace std;
LL T;
LL n;
LL p1, p2;
LL read() {
    LL x = 0, w = 1;
    char ch = 0;
    while (ch < '0' || ch > '9') {
        if (ch == '-') w = -1;
        ch = getchar();
    }
    while (ch >= '0' && ch <= '9') {
        x = x * 10 + ch - 48;
       ch = getchar();
    }
    return x * w;
}
LL check(LL x) {
    LL s = 0;
    while (x) {
        s += x & 1;
        x >>= 1;
    }
    return s;
}
int main()
{
    T = read();
    while (T--) {
        n = read();
        LL s = 0, t = n;
        while (t) {
            s += t & 1;
            t >>= 1;
        }
        if (s & 1) cout << "second" << endl;
            else cout << "first" << endl, p1 = n, p2 = 1;
        if (s & 1) p1 = read(), p2 = read();
        while (p1 && p2) {
            if (p1 == -1 && p2 == -1) return 0;
            LL s1 = 0, s2 = 0;
            s1 = check(p1), s2 = check(p2);
            if (s1 & 1) p1 = p2, s1 = s2;

            LL cnt = 0;
            for (int i = 0; i <= 63; i++) if (p1 & (1ll << (i - 1))) cnt = i;
            cout << (1ll << (cnt - 1)) << ' ' << (p1 ^ (1ll << (cnt - 1))) << endl;

            p1 = read(); p2 = read();
        }
    }

    return 0;
}
```