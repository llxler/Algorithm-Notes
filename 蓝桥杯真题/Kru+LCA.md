# [蓝桥杯 2023 省 A] 网络稳定性

## 题目描述

有一个局域网，由 $n$ 个设备和 $m$ 条物理连接组成，第 $i$ 条连接的稳定性为 $w_i$。

对于从设备 $A$ 到设备 $B$ 的一条经过了若干个物理连接的路径，我们记这条路径的稳定性为其经过所有连接中稳定性最低的那个。

我们记设备 $A$ 到设备 $B$ 之间通信的稳定性为 $A$ 至 $B$ 的所有可行路径的稳定性中最高的那一条。

给定局域网中的设备的物理连接情况，求出若干组设备 $x_i$ 和 $y_i$ 之间的通信稳定性。如果两台设备之间不存在任何路径，请输出 $-1$。

## 输入格式

输入的第一行包含三个整数 $n,m,q$，分别表示设备数、物理连接数和询问数。

接下来 $m$ 行，每行包含三个整数 $u_i,v_i,w_i$，分别表示 $u_i$ 和 $v_i$ 之间有一条稳定性为 $w_i$ 的物理连接。

接下来 $q$ 行，每行包含两个整数 $x_i,y_i$，表示查询 $x_i$ 和 $y_i$ 之间的通信稳定性。

## 输出格式

输出 $q$ 行，每行包含一个整数依次表示每个询问的答案。

## 样例 #1

### 样例输入 #1

```
5 4 3
1 2 5
2 3 6
3 4 1
1 4 3
1 5
2 4
1 3
```

### 样例输出 #1

```
-1
3
5
```

## 提示

【评测用例规模与约定】

对于 $30 \%$ 的评测用例，$n,q \leq 500$，$m \leq 1000$；

对于 $60 \%$ 的评测用例，$n,q \leq 5000$，$m \leq 10000$；

对于所有评测用例，$2 \leq n,q \leq 10^5$，$1 \leq m \leq 3 \times 10^5$，$1 \leq u_i,v_i,x_i,y_i \leq n$，$
1 \leq w_i \leq 10^6$，$u_i \neq v_i$，$x_i \neq y_i$。

```cpp
#include <bits/stdc++.h>

using namespace std;

const int N = 3e5 + 10, M = 6e5 + 10;

int n, m, q;
struct Edge {
    int a, b, w;
    friend bool operator<(Edge A, Edge B) { return A.w > B.w; }
} edge[M / 2];
int p[N];
int h[N], e[M], ne[M], w[M], idx;

int find(int x) { return (p[x] == x) ? x : p[x] = find(p[x]); }
void add(int a, int b, int c) {
    e[++idx] = b, w[idx] = c, ne[idx] = h[a], h[a] = idx;
}

void kruskal() {
    for (int i = 1; i <= m; ++i) {
        auto t = edge[i];
        int a = t.a, b = t.b, w = t.w;
        int pa = find(a), pb = find(b);
        if (pa != pb) {
            add(a, b, w), add(b, a, w);
            p[pa] = pb;
        }
    }
}

bool st[N];
int fa[N][30], nw[N][30], dep[N];

void dfs(int u) {
	st[u] = true;
    for (int i = h[u]; i; i = ne[i]) {
        int j = e[i];
        if (st[j]) continue;
        dep[j] = dep[u] + 1;
        fa[j][0] = u;
        nw[j][0] = w[i];
        dfs(j);
    }
}

int LCA(int a, int b) {
    int res = 2e9;
    if (find(a) != find(b))
        return -1;
    if (dep[a] < dep[b])
        swap(a, b);
    for (int i = 20; i >= 0; i--) {
        if (dep[fa[a][i]] >= dep[b]) {
            res = min(res, nw[a][i]);
            a = fa[a][i];
        }
    }
    if (a == b) return res;
    for (int i = 20; i >= 0; i--) {
    	if (fa[a][i] != fa[b][i]) {
    		res = min({res, nw[a][i], nw[b][i]});
    		a = fa[a][i], b = fa[b][i];
    	}
    }
    res = min({res, nw[a][0], nw[b][0]});
    return res;
}

int main() {
    ios::sync_with_stdio(false);
    cin.tie(nullptr);
    for (int i = 1; i < N; ++i) p[i] = i;
    cin >> n >> m >> q;
    for (int i = 1; i <= m; ++i) {
        int a, b, w;
        cin >> a >> b >> w;
        edge[i] = {a, b, w};
    }
    sort(edge + 1, edge + 1 + m);
    kruskal();
    memset(nw, 0x3f, sizeof nw);
    for (int i = 1; i <= n; ++i) {
        if (!st[i]) {
            dep[i] = 1;
            fa[i][0] = i;
            dfs(i);
        }
    }
    for (int i = 1; i <= 20; ++i) {
        for (int j = 1; j <= n; ++j) {
            fa[j][i] = fa[fa[j][i - 1]][i - 1];
            nw[j][i] = min(nw[j][i - 1], nw[fa[j][i - 1]][i - 1]);
        }
    }
    while (q--) {
        int a, b;
        cin >> a >> b;
        int t = LCA(a, b);
        cout << t << '\n';
    }

    return 0;
}
```

