---
title: 'Graph Theory'
description: 'b'
order: 2
---

### 2-SATtmp
```cpp
#include<bits/stdc++.h>
#define LL long long
using namespace std;
const int maxn = 2e6 + 5;
int n, m;
int head[maxn], to[maxn << 2], nxt[maxn << 2], cnt;
int dfn[maxn], low[maxn], ins[maxn], l[maxn], top, id;
int col[maxn], colcnt;
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
void in(int x, int y) {
    nxt[++cnt] = head[x], head[x] = cnt, to[cnt] = y;
}
void tarjan(int cur) {
    dfn[cur] = low[cur] = ++id;
    l[++top] = cur, ins[cur] = 1;
    for (int i = head[cur]; i; i = nxt[i])
        if (!dfn[to[i]]) tarjan(to[i]), low[cur] = min(low[to[i]], low[cur]);
            else if (ins[to[i]]) low[cur] = min(low[cur], dfn[to[i]]);
    if (low[cur] == dfn[cur]) {
        ++colcnt;
        while (l[top] != cur) {
            col[l[top]] = colcnt;
            ins[l[top]] = 0;
            top--;
        }
        col[cur] = colcnt;
        ins[cur] = 0;
        top--;
    }
}
int main()
{
    n = read(); m = read();
    while (m--) {
        int x, a, y, b;
        x = read(); a = read(); y = read(); b = read();
        in(x + n - a * n, y + b * n); in(y + n - b * n, x + a * n);
    }
    for (int i = 1; i <= 2 * n; i++)
        if (!dfn[i]) tarjan(i);
    for (int i = 1; i <= n; i++)
        if (col[i] == col[i + n]) return printf("IMPOSSIBLE\n"), 0;
    printf("POSSIBLE\n");

    for (int i = 1; i <= n; i++)
        printf("%d ", col[i] > col[i + n]);
    putchar('\n');

    return 0;
}
```

### 强连通分量缩点

```cpp
struct SCC {
    int n;
    vector<vector<int>> G;
    vector<int> dfn, low, ins, stk;
    vector<int> col, siz;
    int id, colcnt;

    SCC() {}
    SCC(int x) {
        init(x);
    }
    void init(int x) {
        n = x;
        G.assign(n + 5, {});
    }
    void add_edge(int x, int y) {
        G[x].push_back(y);
    }
    void dfs(int cur) {
        dfn[cur] = low[cur] = ++id;
        stk.push_back(cur), ins[cur] = 1;
        for (auto v : G[cur]) {
            if (!dfn[v]) {
                dfs(v);
                low[cur] = min(low[cur], low[v]);
            } else if (ins[v]) {
                low[cur] = min(low[cur], dfn[v]);
            }
        }
        
        if (dfn[cur] == low[cur]) {
            ++colcnt;
            int x = stk.back();
            while (x != cur) {
                col[x] = colcnt;
                ins[x] = 0;
                ++siz[colcnt];
                stk.pop_back();
                x = stk.back();
            }
            col[cur] = colcnt;
            ins[cur] = 0;
            ++siz[colcnt];
            stk.pop_back();
        }
    }
    void gets() {
        dfn.assign(n + 5, 0);
        low.assign(n + 5, 0);
        ins.assign(n + 5, 0);
        col.assign(n + 5, 0);
        siz.assign(n + 5, 0);
        stk.clear();
        id = colcnt = 0;

        for (int i = 1; i <= n; i++)
            if (!dfn[i]) dfs(i);
    }
    int diff(int x, int y) {
		return col[x] != col[y];
	}
};
```


### 割边与割点

#### 割边

```cpp
struct EBCC
{
    int n;
    vector<vector<int>> G;
    vector<int> dfn, low, ff, fa;
    int id;

    EBCC() {};
    EBCC(int x) {
        init(x);
    }
    void init(int x) {
        n = x;
        G.assign(n + 5, {});
    }
    void add_edge(int x, int y) {
        G[x].push_back(y);
        G[y].push_back(x);
    }

    void dfs(int cur, int ftr) {
        dfn[cur] = low[cur] = ++id;
        fa[cur] = ftr;
        for (auto v : G[cur]) {
            if (!dfn[v]) {
                dfs(v, cur);
                low[cur] = min(low[cur], low[v]);
                if (low[v] > dfn[cur]) {
                    ff[v] = 1;
                }
            } else {
                if (dfn[v] < dfn[cur] && v != ftr) {
                    low[cur] = min(low[cur], dfn[v]);
                }
            }
        }
    }

    void gets() {
        dfn.assign(n + 5, 0);
        low.assign(n + 5, 0);
        ff.assign(n + 5, 0);
        fa.assign(n + 5, 0);
        id = 0;

        for (int i = 1; i <= n; i++)
            if (!dfn[i]) dfs(i, 0);
    }
};
```

### 网络流

#### Dinic最大流
```cpp
struct dinic {
    const LL inf = 1e18;

    struct edge {
        LL v, nxt, cap, flow;
    };

    vector<edge> e;
    int n, S, T;
    LL maxflow;
    vector<int> head, dep, cur;

    dinic(int x) {init(x);}

    void init(int x) {
        n = x;
        S = 0, T = n + 1, maxflow = 0;
        e.clear();
        head.assign(n + 5, -1);
        cur.assign(n + 5, -1);
    }
    void add_edge(int u, int v, LL w) {
        e.push_back({v, head[u], w, 0});
        head[u] = e.size() - 1;
        e.push_back({u, head[v], 0, 0});
        head[v] = e.size() - 1;
    }
    int bfs() {
        dep.assign(n + 5, 0);
        queue<int> q;
        q.push(S);
        dep[S] = 1;

        while (!q.empty()) {
            int u = q.front();
            q.pop();
            for (int i = head[u]; ~i; i = e[i].nxt) {
                int v = e[i].v;
                if (!dep[v] && (e[i].cap > e[i].flow)) {
                    dep[v] = dep[u] + 1;
                    q.push(v);
                }
            }
        }
        return dep[T];
    }
    LL dfs(int u, LL flow) {
        if (u == T || !flow) return flow;

        LL ret = 0;
        for (int i = cur[u]; ~i; i = e[i].nxt) {
            cur[u] = i;
            int v = e[i].v;
            LL d = 0;
            if (dep[v] == dep[u] + 1 && (d = dfs(v, min(flow - ret, e[i].cap - e[i].flow)))) {
                ret += d;
                e[i].flow += d;
                e[i ^ 1].flow -= d;
                if (ret == flow) return ret;
            }
        }
        return ret;
    }
    void gets_maxflow() {
        maxflow = 0;
        while (bfs()) {
            cur = head;
            maxflow += dfs(S, inf);
        }
    }
};
```

#### Dinic最小费用最大流
```cpp
struct dinic {
    const LL inf = 1e18;

    struct edge {
        LL v, nxt, cap, flow, cost;
    };

    vector<edge> e;
    int n, S, T;
    LL maxflow, mncost;
    vector<int> head, cur, vis;
    vector<LL> dis;

    dinic(int x) {init(x);}

    void init(int x) {
        n = x;
        S = 0, T = n + 1, maxflow = 0;
        e.clear();
        head.assign(n + 5, -1);
        cur.assign(n + 5, -1);
        vis.assign(n + 5, 0);
        dis.assign(n + 5, inf);
    }
    void add_edge(int u, int v, LL w, LL cost) {
        e.push_back({v, head[u], w, 0, cost});
        head[u] = e.size() - 1;
        e.push_back({u, head[v], 0, 0, -cost});
        head[v] = e.size() - 1;
    }
    int spfa() {
        dis.assign(n + 5, inf);
        vis.assign(n + 5, 0);
        queue<int> q;
        q.push(S);
        dis[S] = 0, vis[S] = 1;
        while (!q.empty()) {
            int u = q.front();
            q.pop();
            vis[u] = 0;

            for (int i = head[u]; ~i; i = e[i].nxt) {
                int v = e[i].v;
                if (e[i].cap > e[i].flow && dis[v] > dis[u] + e[i].cost) {
                    dis[v] = dis[u] + e[i].cost;
                    if (!vis[v]) q.push(v), vis[v] = 1;
                }
            }
        }
        return dis[T] != inf;
    }
    LL dfs(int u, LL flow) {
        if (u == T || !flow) return flow;
        vis[u] = 1;
        LL ret = 0;

        for (int i = cur[u]; ~i; i = e[i].nxt) {
            cur[u] = i;
            int v = e[i].v;
            LL d = 0;
            
            if (!vis[v] && dis[v] == dis[u] + e[i].cost && (d = dfs(v, min(flow - ret, e[i].cap - e[i].flow)))) {
                ret += d;
                e[i].flow += d;
                e[i ^ 1].flow -= d;
                if (ret == flow) return ret;
            }
        }
        vis[u] = 0;
        return ret;
    }
    void gets_maxflow() {
        maxflow = mncost = 0;
        int x;
        while (spfa()) {
            cur = head;
            vis.assign(n + 5, 0);
            while(x = dfs(S, inf)) {
                maxflow += x;
                mncost += x * dis[T];
            }
        }
    }
};
```