# spfa求负环

**trick做法：当所有点的入队次数超过2n时，我们就认为图中是有很大可能是存在负环的**

注：spfa求负环时负权边之add一次

## eg.虫洞

```cpp
#include <bits/stdc++.h>

#define x first
#define y second

using namespace std;

typedef long long ll;
typedef pair<int, int> PII;

const int N = 5e2 + 10, M = 5210;

int n, m, k;
int h[N], w[M], ne[M], e[M], idx;
int q[N], dist[N], cnt[N], st[N];

void add(int a, int b, int c) {
    e[idx] = b, w[idx] = c, ne[idx] = h[a], h[a] = idx++;
}

bool spfa() {
    memset(dist, 0, sizeof dist);
    memset(cnt, 0, sizeof cnt);
    memset(st, 0, sizeof st);

    int hh = 0, tt = 0;

    for (int i = 1; i <= n; i++) {
        q[tt++] = i;
        st[i] = true;
    }

    while (tt != hh) {
        int t = q[hh++];
        if (hh == N) hh = 0;
        st[t] = false;

        for (int i = h[t]; ~i; i = ne[i]) {
            int j = e[i];
            if(dist[j] > dist[t] + w[i]) {
                dist[j] = dist[t] + w[i];
                cnt[j] = cnt[t] + 1;
                if (cnt[j] >= n) return true;
                if (!st[j]) {
                    st[j] = true;
                    q[tt++] = j;
                    if (tt == N) tt = 0;
                }
            }
        }
    }
    return false;
}

int main() {

    ios::sync_with_stdio(false);
    cin.tie(nullptr);

    int T;
    cin >> T;
    while (T--) {
        cin >> n >> m >> k;

        memset(h, -1, sizeof h);
        idx = 0;

        while (m--) {
            int a, b, c;
            cin >> a >> b >> c;
            add(a, b, c), add(b, a, c);
        }

        while (k--) {
            int a, b, c;
            cin >> a >> b >> c;
            add(a, b, -c);
        }

        if (spfa()) puts("YES");
        else puts("NO");
    }

    return 0;
}
```

## eg.观光奶牛

问题描述：给定一张 L 个点、P 条边的有向图，每个点都有一个权值 f[i]，每条边都有一个权值 t[i]。

求图中的一个环，使“环上各点的权值之和”除以“环上各边的权值之和”最大。

输出这个最大值。

### 01分数规划

做法使用二分，若sumf / sumt > mid  -> sum (f - mid) > 0 -> 图中是否有正环（把距离判断符号转变就行）

细节：dist要用double要不然在比较的时候精度不够，其次二分的时候精度要比答案的要求高**两**次

```cpp
#include <cstring>
#include <algorithm>
#include <iostream>

using namespace std;

const int N = 1e3 + 10, M = 5010;

int n, m;
int pw[N];
int h[N], e[M], ne[M], w[M], idx;
int cnt[N];
double dist[N];
bool st[N];

void add(int a, int b, int c) {
    e[idx] = b, w[idx] = c, ne[idx] = h[a], h[a] = idx++;
}

bool check(double x) {
    memset(st, 0, sizeof st);
    memset(cnt, 0, sizeof cnt);

    int q[N];
    int hh = 0, tt = 0;
    for (int i = 1; i <= n; i ++) {
        q[tt++] = i;
        st[i] = true;
    }

    while (hh != tt) {
        int t = q[hh++];
        if (hh == N) hh = 0;
        st[t] = false;

        for (int i = h[t]; ~i; i = ne[i]) {
            int j = e[i];
            if (dist[j] < dist[t] + pw[j] - x * w[i]) {
                cnt[j] = cnt[t] + 1;
                if (cnt[j] >= n) return true;
                dist[j] = dist[t] + pw[j] - x * w[i];
                if (st[j]) continue;
                st[j] = true;
                q[tt++] = j;
                if (tt == N) tt = 0;
            }
        }
    }
    return false;
}

int main() {

    ios::sync_with_stdio(false);
    cin.tie(nullptr);

    cin >> n >> m;

    for (int i = 1; i <= n; i++) cin >> pw[i];

    memset(h, -1, sizeof h);

    while (m--) {
        int a, b, c;
        cin >> a >> b >> c;
        add(a, b, c);
    }
    
    double l = 0.0, r = 1010.0;

    while (r - l > 1e-4) {//精度一般比题目要求高2次
        double mid = (l + r) / 2;
        if (check(mid)) l = mid;
        else r = mid;
    }

    printf("%.2lf\n", l);

    return 0;
}
```



## eg.word rings

问题描述：

如果字符串A的**结尾两个**字符与字符串B的**开头两个**字符相匹配，我们称A与B能 **“ 相连 ”** ( 注意：A与B能相连，不代表B与A能相连 )

当若干个串首尾 “ 相连 ” 成一个环时，我们称之为一个环串（一个串首尾相连也算）

我们希望从给定的全小写字符串中找出一个环串，使这个环串的平均长度最长

- 将每个单词看成边，首位两个字母为点
- 01分数规划：sum(w) / sum(1) > mid
  - sum(w)  - sum(1) * mid > 0
  - sum (w - mid) > 0
  - 转化成有无正环边
- **trick做法：当所有点的入队次数超过2n时，我们就认为图中是有很大可能是存在负环的**



### trick1

```cpp
#include <cstring>
#include <algorithm>
#include <iostream>

using namespace std;

const int N = 700, M = 100010;
int h[N], e[M], ne[M], w[M], idx;

int n, m;
int cnt[N];
double dist[N];
bool st[N];

void add(int a, int b, int c) {
    e[idx] = b, w[idx] = c, ne[idx] = h[a], h[a] = idx++;
}

bool check(double x) {
    memset(cnt, 0, sizeof cnt);
    memset(st, 0, sizeof st);
    memset(dist, 0, sizeof dist);

    int q[N], hh = 0, tt = 0;
    for (int i = 0; i < 676; i++) {
        q[tt++] = i;
        st[i] = true;
    }

    int count = 0;

    while (hh != tt) {
        int t = q[hh++];
        if (hh == N) hh = 0;
        st[t] = false;

        for (int i = h[t]; ~i; i = ne[i]) {
            int j = e[i];
            if (dist[j] < dist[t] + w[i] - x) {
                if (++count > 2 * N) return true;
                cnt[j] = cnt[t] + 1;
                if (cnt[j] >= N) return true;
                dist[j] = dist[t] + w[i] - x;
                if (st[j]) continue;
                st[j] = true;
                q[tt++] = j;
                if (tt == N) tt = 0;
            }
        }
    }
    return false;
}

int main() {

    ios::sync_with_stdio(false);
    cin.tie(nullptr);

    while (cin >> n, n) {
        memset(h, -1, sizeof h);
        idx = 0;
        string s;
        for (int i = 0; i < n; i++) {
            cin >> s;
            int len = s.size();
            if (len >= 2) {
                int left = (s[0] - 'a') * 26 + s[1] - 'a';
                int right = (s[len - 2] - 'a') * 26 + s[len - 1] - 'a';
                add(left, right, len);
            }
        }

        if (!check(0)) puts("No solution.");
        else {
            double l = 0.0, r = 1010.0;
            while (r - l > 1e-5) {
                double mid = (r + l) / 2;
                if (check(mid)) l = mid;
                else r = mid;
            }
            printf("%.2lf\n", l);
        }
    }

    return 0;
}
```

### trick2

把其中的循环队列换成栈，这样在找环的时候更容易找到 （效率普遍偏高）

```cpp
int q[N], hh = 0, tt = 0;
for (int i = 0; i < 676; i++) {
    q[tt++] = i;
    st[i] = true;
}

int count = 0;

while (hh != tt) {
    int t = q[--tt];//换成栈，只需要将hh--换成--tt
    st[t] = false;

    for (int i = h[t]; ~i; i = ne[i]) {
        int j = e[i];
        if (dist[j] < dist[t] + w[i] - x) {
            if (++count > 2 * N) return true;
            cnt[j] = cnt[t] + 1;
            if (cnt[j] >= N) return true;
            dist[j] = dist[t] + w[i] - x;
            if (st[j]) continue;
            st[j] = true;
            q[tt++] = j;
        }
    }
}
return false;
```



# 优化模板：

```cpp
bool dfs(int x, double v) { //dfs优化的spfa算法（模板）
    vis[x] = 1;
    for (int j = lnk[x]; j; j = la[j]) {
        if (dis[ne[j]] < dis[x] + co[j] - v) {
            dis[ne[j]] = dis[x] + co[j] - v;
            if (vis[ne[j]])return 1;
            else {
                if (dfs(ne[j], v))return 1;
            }
        }
    }
    vis[x] = 0;
    return 0;
}
bool check(double mid) {//检查是否成环
    memset(vis, 0, sizeof(vis));
    memset(dis, 0, sizeof(dis));
    for (int i = 0; i <= 5000; ++i)
        if (dfs(i, mid))return 1;
    return 0;
}
```

