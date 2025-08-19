---
title: 'JSCPC 2024'
description: ''
publishDate: 2024-05-14
tags:
  - ACM
  - 游记
  - 总结
  - 正赛
---
又是大脑宕机的一天（悲
<!-- more -->
## 总结

理论上是不是要写点 `Day0` , `Day1` 什么的，还没写过这种（

南大食堂名不虚传，第一次见粉皮能成糊糊的肠粉

--- 

热身赛签了 `A` ， 然后贡献了下 `D` 的思路

一开始机房还没开交换机，整个机房没一个登得上去的（

--- 

过了签到 `K` 

`E` 思路出的挺快的，但是好几个 $i$ 打成 $j$ ，抄的板子写的第 $k$ 大实际上是第 $k$ 小，然后就调了挺久的

中途下机几次让队友调 `A` ， 最后2h多一点过的

大概 2.5h 注意力就涣散了，然后成功贡献了一发错的 `A` 思路

3h开始进入宕机状态， `J` 看半天也没想出来怎么压掉一维，出去才发现是插头板子题，不过我确实不会（

最后应该去帮忙看 `B` 和 `C` 的，至少能多过一题

不过过了应该还是银也没什么区别就是了

## 题解

### [B - Area of the Devil](https://codeforces.com/gym/105161/problem/B)

#### 思路：

第 $i$ 个点起始位置记为 $point_{i, 1}$ , 终位置记为 $point_{i, 2}$

以序号 1, 2, 3, 4四个点为例，$point_{2, 2}$ 和 $point_{4, 1}$ ， $point_{3, 1}$ 和 $point_{1, 2}$ 两条线段的交点就是五角星能到的最远位置，这个交点和 $point_{2, 2}$ , $point_{3, 1}$ 围成的三角形是不可能覆盖到的，然后每个 $t_i$ 和 $s_{i +1}$ 的那段弧所对的弓形也围不到，把这两部分删了就行了

唯一的坑点应该是可能会出现竖线

赛时几乎没看这题不知道是不是错在这（
#### Code
```cpp
#include<bits/stdc++.h>
#define LL long long
using namespace std;
const int maxn = 1e1 + 5;
const double pi = 3.14159265358979;
int T;
int n, R;
double s[maxn], t[maxn];
struct poi {
    double x, y;
}point[maxn][3];
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
double dis(poi x, poi y) {
    return sqrt((x.x - y.x) * (x.x - y.x) + (x.y - y.y) * (x.y - y.y));
}
double getS(poi x, poi y, poi z) {
    double a = dis(x, y), b = dis(y, z), c = dis(x, z);
    double p = (a + b + c) / 2;
    return sqrt(p * (p - a) * (p - b) * (p - c));
}
double cal(poi p1, poi p2, poi p3, poi p4) {
    int ff1 = 0, ff2 = 0;
    double k1 = 0, k2 = 0, b1 = 0, b2 = 0;
    if (abs(p1.x - p2.x) < 1e-9) k1 = p1.x, ff1 = 1;
        else k1 = (p1.y - p2.y) / (p1.x - p2.x), b1 = p1.y - k1 * p1.x;
    if (abs(p3.x - p4.x) < 1e-9) k2 = p3.x, ff2 = 1;
        else k2 = (p3.y - p4.y) / (p3.x - p4.x), b2 = p3.y - k2 * p3.x;
    double x = 0, y = 0;
    if (ff1) x = k1;
    if (ff2) x = k2;
    if (!ff1 && !ff2) x = (b2 - b1) / (k1 - k2);
    if (!ff1) y = k1 * x + b1;
    if (!ff2) y = k2 * x + b2;
    return getS(p1, p3, {x, y});
}
int main()
{
    T = read();
    while (T--) {
        R = read();
        double r = R;
        double ans = r * r * pi, S = ans;
        int n = 5;

        for (int i = 1; i <= n; i++) {
            s[i] = 1.0 * read();
            point[i][1] = {r * cos(s[i] / 180 * pi), r * sin(s[i] / 180 * pi)};
        }
        for (int i = 1; i <= n; i++) {
            t[i] = 1.0 * read();
            point[i][2] = {r * cos(t[i] / 180 * pi), r * sin(t[i] / 180 * pi)};
        }

        for (int i = 1; i <= n; i++) {
            int nxt = i < 5 ? i + 1 : 1;
            double th = (s[nxt] + 360 - t[i]);
            while (th > 360) th -= 360;
            ans -= S * th / 360;
            ans += r * r * sin(th / 180 * pi) / 2;
        }

        for (int i = 1; i <= n; i++) {
            int a1 = i, a2 = i + 1, b1 = i - 1, b2 = i + 2;
            if (a2 > 5) a2 -= 5;
            if (b2 > 5) b2 -= 5;
            if (b1 < 1) b1 += 5;
            poi p1 = point[a1][2], p2 = point[a2][1], p3 = point[b1][2], p4 = point[b2][1];
            ans -= cal(p1, p4, p2, p3);
        }

        printf("%.9lf\n", ans);
        
    }

    return 0;
}
```

### [C - Radio Direction Finding](https://codeforces.com/gym/105161/problem/C)

#### 思路：

令 $dis(x)$ 表示询问点为 $x$ 时返回的距离

然后用 $dis(x + 1) - dis(x)$ 对每条边赋值，可以发现这个环一定是一段 $0$ ，一段$+2$ ，一个 $+1$ ，一段 $0$ ，一个 $-1$ ，一段 $-2$ 

其中 $\pm 1$ 正对的点就是隐藏点，且当两个隐藏点相邻时 $\pm 1$ 中间没有 $0$ 

然后先用两次询问计算 $|(dis(1) - dis(2)|$ ，有三种可能的取值：

为 $1$ ：一个隐藏点就是 $2 + \lfloor \frac {n} {2} \rfloor$ 

为 $0$ ：此时第一个点往后转半圈包含的所有边只会有一个 $\pm 1$ ，可以通过二分奇偶性找到这个 $1$ ，然后计算出其中一个隐藏点

为 $2$ ：说明转半圈内的边的数值应该是一段 $2$ ，一段 $0$ ， 一段 $2$ 或是 一段 $2$ ，一个 $1$ ，一段 $0$ ，一个 $1$ ，一段 $2$

此时如果查询位置和 $dis(1)$ 的差值为 $2k$ 或是 $2k - 1$ ，则说明还没有越过 $0$ ，否则已经越过峰（ $k$ 为查询位置和 $1$ 的距离），可以进行二分找到第一个 $0$ 的位置

然后查询一下这个位置前一条边是 $1$ 还是 $2$ 就可以确定是隐藏点还是隐藏点对应的边

此时已经找到一个点，另一个点要么在 $1$ 左边，要么在 $1$ 右边，两种情况分别跟 $dis(2)$ 比较找到匹配的那个就行
#### Code
```cpp
#include<bits/stdc++.h>
#define LL long long
using namespace std;
int T;
int n;
int dis(int x, int y) {
    if (x == y) return 0;
    int d1 = y + n - x;
    int d2 = x + n - y;
    if (d1 > n) d1 -= n;
    if (d2 > n) d2 -= n;
    return min(d1, d2);
}
int main()
{
    cin >> T;
    while (T--) {
        cin >> n;
        
        int d1, d2;
        int p1 = 0, p2 = 0;
        cout << "? " << 1 << endl;
        cin >> d1;
        cout << "? " << 2 << endl;
        cin >> d2;

        if (abs(d2 - d1) == 1) p1 = 1 + 1 + n / 2;
        if (abs(d2 - d1) == 0) {
            int l = 1, r = 2 + n / 2, tag = 0;
            while (l <= r) {
                int mid = (l + r) >> 1, dd = 0;
                cout << "? " << mid << endl;
                cin >> dd;
                if ((dd - d1) & 1) tag = mid, r = mid - 1;
                    else l = mid + 1;
            }
            p1 = tag + n / 2;
            if (p1 > n) p1 -= n;
        }
        if (abs(d2 - d1) == 2) {
            int l = 1, r = 1 + n / 2, tag = 0, dtag = 0;
            while (l <= r) {
                int mid = (l + r) >> 1, dd;
                cout << "? " << mid << endl;
                cin >> dd;
                if (abs(d1 - dd) == dis(1, mid) * 2 || abs(d1 - dd) == dis(1, mid) * 2 - 1) l = mid + 1, tag = mid, dtag = dd;
                    else r = mid - 1;
            }
            int pre = tag == 1 ? n : tag - 1;
            int dispre;
            cout << "? " << pre << endl;
            cin >> dispre;
            if (abs(dispre - dtag) == 2) p1 = tag;
                else p1 = tag + n / 2;
        }

        int res = d1 - dis(1, p1);
        p2 = 1 + res;
        if (p2 > n) p2 -= n;
        if (dis(2, p1) + dis(2, p2) != d2) {
            p2 = 1 - res;
            if (p2 < 1) p2 += n;
        }
        if (p1 > p2) swap(p1, p2);
        cout << "! " << p1 << ' ' << p2 << endl;
    }


    return 0;
}
```

### [E - Divide](https://codeforces.com/gym/105161/problem/E)

#### 思路：

通过前缀和，每个询问可以 $logV$ 内判断出最终答案的二进制长度，然后问题就转变成对每个询问求 $[l, r]$ 的区间第 $k'$ 大，其中二进制长度超过答案长度的要统一长度

空间其实开一颗主席树就行， 对答案长度排序后建一棵树处理对应长度，到下一个长度可以直接把原树清了

所以 $n$ 其实能上到 $1e6$ 的（bushi

### [H - Real Estate Is All Around](https://codeforces.com/gym/105161/problem/H)
#### 思路：

按题解思路写的

最重要的地方应该是两个：

- I. 卖不完的房子一定是最便宜的，所以可以扔给小绿，题目就变成了可卖可不卖
- II.小红小蓝可以合并当作同一个人，一轮只卖一套房子贡献是 $a_i$ ，卖两套就是 $a_i + a_i' - 1$ 

剩下的看题解吧，都一样就不写了（躺
#### Code
```cpp
#include<bits/stdc++.h>
#define LL long long
using namespace std;
const int maxn = 2e2 + 5;
int T;
int n;
struct opt {
    int tag, x;
}q[maxn];
int f[2][maxn][maxn];
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
        for (int i = 1; i <= n; i++) {
            q[i].tag = read();
            if (q[i].tag == 1) q[i].x = read();
        }
        for (int i = 0; i <= 1; i++)
            for (int j = 0; j <= n; j++)
                for (int k = 0; k <= n; k++)
                    f[i][j][k] = 0;

        int now = 0, lst = 1;
        for (int t = 1; t <= n; t++) {
            auto [tag, x] = q[t];
            swap(now, lst);
            for (int j = 0; j <= n; j++)
                for (int k = 0; k <= n; k++)
                    f[now][j][k] = f[lst][j][k];
            if (tag == 1) {
                for (int j = 0; j <= n; j++)
                    for (int k = 0; k <= n; k++) {
                        f[now][j + 1][k] = max(f[now][j + 1][k], f[lst][j][k] + x - (x + 9) / 10);
                        f[now][j][k + 1] = max(f[now][j][k + 1], f[lst][j][k] + x);
                    }
            }
            if (tag == 2) {
                for (int j = 0; j <= n; j++)
                    for (int k = 0; k <= n; k++) {
                        f[now][max(0, j - 1)][max(0, k - 1)] = max(f[now][max(0, j - 1)][max(0, k - 1)], f[lst][j][k]);
                        f[now][max(0, j - 1)][max(0, k - 2)] = max(f[now][max(0, j - 1)][max(0, k - 2)], f[lst][j][k] - 1);
                    }
            }
        }
        printf("%d\n", f[now][0][0]);
    }

    return 0;
}
```

### [J - Tile Covering](https://codeforces.com/gym/105161/problem/J)

#### 思路：

当时不会 `轮廓线dp` ，现在看其实是有点板的（

---

考虑如何转移

如果要覆盖当前格有三种可能：

1.上面和左边没有牌，这个点是一块新的骨牌的起点

2.左上和上方没有牌，左边有，从左边的骨牌延伸过来

3.左边、左上和右上没有牌，上面有，从上面的骨牌延伸过来

一般的轮廓线dp是不保存左上角信息的，所以要额外开一维记录左上有没有被覆盖

特殊的，覆盖每行第一位的转移时，不用考虑左上的覆盖状态，无论是否被覆盖都可以转移

比如前一个点的轮廓线状态为 $k$ ，不覆盖当前点就是 $f_{i, j, k \oplus ((col == 1) * 2 ^ {j - 1}), col}$ ，覆盖就是 $f_{i, j, k | 2 ^ {j - 1}, col}$ ，col 表示这个点上方的覆盖状态，lst 表示左上方的覆盖状态，为了方便表示第三维后面统一用 $nk$ 代替

不覆盖：$f_{i, j, nk, col} = max(f_{i, j - 1, k, 0}, f_{i, j - 1, k, 1})$

覆盖：

$j = 1$ ：$f_{i, j, nk, col} = max(max(f_{i - 1, m, k, 0}, f_{i - 1, m, k, 1}) + a_{i, j})$

$j \neq 1$ ：$f_{i, j, nk, col} = max(f_{i, j - 1, k, lst} + a_{i, j})$

然后每行的状态只用从前一个点转移，可以用滚动数组优化掉两维，不过优化一维就能过了
#### Code
```cpp
#include<bits/stdc++.h>
#define LL long long
using namespace std;
const int maxn = 2e1 + 5;
const int maxm = (1 << 18) + 5;
int n, m;
int a[maxn][maxn], f[2][maxn][maxm][2];
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
    n = read(); m = read();
    for (int i = 1; i <= n; i++)
        for (int j = 1; j <= m; j++)
            a[i][j] = read();

    int nowrow = 0, lstrow = 1;
    f[1][1][1][0] = a[1][1];
    for (int i = 1; i <= n; i++) {
        swap(nowrow, lstrow);
        for (int j = 1; j <= m; j++) {
            for (int k = 0; k < (1 << m); k++) 
                for (int lst = 0; lst <= 1; lst++) {
                    if ((j == 1 || i == 1) && lst) continue;
                    int row, col, nxt;
                    if (j == 1) row = 0;
                        else row = k & (1 << (j - 2));
                    if (j == m) nxt = 0;
                        else nxt = k & (1 << j);
                    if (i == 1) col = nxt = 0;                
                        else col = k & (1 << (j - 1));
                    row = row != 0;
                    col = col != 0;
                    nxt = nxt != 0;
                    int prei = j == 1 ? lstrow : nowrow;
                    int prej = j == 1 ? m : j - 1;
                    
                    int nk = k ^ ((col == 1) * (1 << (j - 1)));
                    f[nowrow][j][nk][col] = max(f[prei][prej][k][0], f[prei][prej][k][1]);
                    if ((!row && !col) || (row && !lst && !col) || (col && !lst && !nxt && !row)) {
                        nk = k | (1 << (j - 1));
                        f[nowrow][j][nk][col] = max(f[nowrow][j][nk][col], f[prei][prej][k][lst] + a[i][j]);
                        if (j == 1) f[nowrow][j][nk][col] = max(f[nowrow][j][nk][col], f[prei][prej][k][lst ^ 1] + a[i][j]);
                    }
            }
        }
    }
    int ans = 0;
    for (int i = 0; i < (1 << m); i++)
        for (int lst = 0; lst <= 1; lst++)
            ans = max(ans, f[nowrow][m][i][lst]);
    printf("%d\n", ans);

    return 0;
}
```