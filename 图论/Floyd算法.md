# Floyd的应用

- 最短路
- 传递闭包
- 找最小环
- 恰好经过k条边的最短路（倍增）

## 问题1

问题描述：Farmer John 的农场里有很多 **牧区**。有的路径连接一些特定的牧区。**一片所有连通的牧区** 称为一个 **牧场**。但是就目前而言，你能看到至少有两个牧区通过任何路径都不连通。这样，Farmer John 就有 **多个** 牧场了。

John 想在牧场里添加 **恰好** 一条路径。对这条路径有以下限制：

一个牧场的 **直径** 就是牧场中 **最远** 的两个牧区的距离（本题中所提到的所有距离指的都是 **最短的距离**）。考虑如下的有 5 个牧区的牧场，牧区用 `*` 表示，路径用直线表示。每一个牧区都有自己的坐标

- 用floyd求任意两点的距离
- 求maxd[i]表示和i连同且距离i最远的点的距离
- 分两种情况
  - 所有maxd[i]最大值
  - 枚举在哪两个点之间连边。i，j满足di，j = INF；

```cpp
#include <iostream>
#include <algorithm>
#include <cstring>
#include <cmath>

#define x first
#define y second

using namespace std;

typedef pair<int, int> PII;

const int N = 160;
const double INF = 1e20;

char g[N][N];
double dist[N][N];
int n;
PII ver[N];
double maxd[N];

double get(PII s, PII t)
{
	int x1 = s.x, y1 = s.y, x2 = t.x, y2 = t.y;
	return sqrt((x1 - x2) * (x1 - x2) + (y1 - y2) * (y1 - y2));
}

int main()
{
	cin >> n;

	for(int i = 0; i < n; i ++)
	{
		int x, y;
		cin >> x >> y;
		ver[i] = {x, y};
	}

	for(int i = 0; i < n; i ++) cin >> g[i];

	for(int i = 0; i < n; i ++)
		for(int j = 0; j < n; j ++)
			if(i != j)
			{
				if(g[i][j] == '1') dist[i][j] = get(ver[i], ver[j]);
				else dist[i][j] = INF;
			}
			

	for(int k = 0; k < n; k ++)
		for(int i = 0; i < n; i ++)
			for(int j = 0; j < n; j ++)
				dist[i][j] = min(dist[i][j], dist[i][k] + dist[k][j]);

	for(int i = 0; i < n; i ++)
		for(int j = 0; j < n; j ++)
			if(dist[i][j] < INF)
				maxd[i] = max(maxd[i], dist[i][j]);

	double res1 = 0.0;
	for(int i = 0; i < n; i ++) res1 = max(res1, maxd[i]);

	double res2 = INF;
	for(int i = 0; i < n; i ++)
		for(int j = 0; j < n; j ++)
			if(dist[i][j] >= INF)
				res2 = min(res2, maxd[i] + maxd[j] + get(ver[i], ver[j]));

	
	printf("%lf\n", max(res1, res2));

	return 0;
}
```

## 问题2（传递闭包）

传递闭包：将所有间接到达的点变成直接到达的边

```
传递闭包：
如果i - > j右边则d[i][j] = 1
for(k)
	for(i)
		for(j)
			若i，k连，k，j连
				则d[i][j] = 1;//说明能到达
				通常用|=来计算
```

问题描述：给定 n个变量和 m 个不等式。其中 n 小于等于 2626，变量分别用前 n 的大写英文字母表示。

不等式之间具有传递性，即若 A>B 且 B>C，则 A>C。

请从前往后遍历每对关系，每次遍历时判断：

- 如果能够确定全部关系且无矛盾，则结束循环，输出确定的次序；
- 如果发生矛盾，则结束循环，输出有矛盾；
- 如果循环结束时没有发生上述两种情况，则输出无定解。

**看成是传递闭包的问题**，将小于看成前到后有边，进行传递闭包

- 若di,i有边则无解
- 若di,j为1则确定关系
- 都不满足则是无法判断

```cpp
#include <iostream>
#include <cstring>
#include <algorithm>

using namespace std;

const int N = 30;

int g[N][N], d[N][N];
bool st[N];
int n, m;

void floyd()
{
    memcpy(d, g, sizeof g);

    for(int k = 0; k < n; k ++)
        for(int i = 0; i < n; i ++)
            for(int j = 0; j < n; j ++)
                d[i][j] |= d[i][k] && d[k][j];
}

int check()
{
    for(int i = 0; i < n; i ++) 
        if(d[i][i]) return 1;//有矛盾
    for(int i = 0; i < n; i ++)
        for(int j = 0; j < i; j ++)
            if(!d[i][j] && !d[j][i])
                return 0;//还没排好
    return 2;//都排好了
}

char get_char()
{
    for(int i = 0; i < n; i ++)
        if(!st[i])
        {
            bool flag = true;//可以选
            for(int j = 0; j < n; j ++)
                if(!st[j] && d[j][i])
                {
                    flag = false;
                    break;
                }
            if(flag)
            {
                st[i] = true;
                return i + 'A';
            }
        }
    return 'x';
}

int main()
{
    while(cin >> n >> m, n || m)
    {
        memset(g, 0, sizeof g);
        int type = 0, t;
        for(int i = 1; i <= m; i ++)
        {
            char c[5];
            cin >> c;
            int x = c[0] - 'A', y = c[2] - 'A';
            if(!type)
            {
                g[x][y] = 1;
                floyd();
                type = check();
                if(type) t = i;
            }
        }
        if(!type) puts("Sorted sequence cannot be determined.");
        else if(type == 1) printf("Inconsistency found after %d relations.\n", t);
        else if(type == 2) 
        {
            memset(st, 0, sizeof st);
            printf("Sorted sequence determined after %d relations: ", t);
            for(int i = 0; i < n; i ++) printf("%c", get_char());
            printf(".\n");
        }
    }

    return 0;
}
```

### 优化成二维写法的传递背包

```cpp
d[x][y] = 1;
for(int i = 0; i < n; i ++)
{
    if(d[i][x]) d[i][y] = 1;
    if(d[y][i]) d[x][i] = 1;
    for(int j = 0; j < n; j ++)
        if(d[i][x] && d[y][j])
            d[i][j] = 1;
}
```

### 使用bitsset进行优化

```cpp
bitset<N> b[N];
for (int i = 1; i <= n; i++) 
    for (int j = 1; j <= n; j++)
        if (b[j][i])
            b[j] |= b[i];
```



## 问题3     求最小环问题

给定一张无向图，求图中一个至少包含 3 个点的环，环上的节点不重复，并且环上的边的长度之和最小。

该问题称为无向图的最小环问题。

你需要输出最小环的方案，若最小环不唯一，输出任意一个均可。

- 把这个问题当作dp问题来求解，dp[i]的含义是以i为最大节点的最小环
- 因此在用floyd的时候更新第k次之前，我们就知道前k-1个点的最小值，我们就可以用它来更新第k类的答案
- 在这个过程中用pos记录更新最小值的位置，并用path记录最小环的形成

```cpp
#include <iostream>
#include <algorithm>
#include <cstring>

using namespace std;

const int N = 110, INF = 0x3f3f3f3f;

int g[N][N], d[N][N];
int n, m;
int pos[N][N], path[N], cnt;
int res = INF;

void find_path(int x, int y)
{
    if(pos[x][y] == 0) return;//x, y之间直接连着一条边

    int k = pos[x][y];

    find_path(x, k);
    path[cnt ++] = k;
    find_path(k, y);
}

int main()
{
    cin >> n >> m;

    memset(g, 0x3f, sizeof g);

    for(int i = 1; i <= n; i ++) g[i][i] = 0;

    while(m --)
    {
        int a, b, c;
        cin >> a >> b >> c;
        g[a][b] = g[b][a] = min(g[a][b], c);
    }

    memcpy(d, g, sizeof d);

    for(int k = 1; k <= n; k ++)
    {
        
        for(int i = 1; i < k; i ++)
            for(int j = 1; j < i; j ++)
                if(res > (long long)g[j][k] + g[k][i] + d[i][j])
                {
                    res = g[j][k] + g[k][i] + d[i][j];
                    cnt = 0;
                    path[cnt ++] = k;
                    path[cnt ++] = j;
                    find_path(j, i);
                    path[cnt ++] = i;
                }

        for(int i = 1; i <= n; i ++)
            for(int j = 1; j <= n; j ++)
                if(d[i][j] > d[i][k] + d[k][j])
                {
                    d[i][j] = d[i][k] + d[k][j];
                    pos[i][j] = k;
                }
    }

    if(res == INF) puts("No solution.");
    else    
        for(int i = 0; i < cnt; i ++) cout << path[i] << ' ';

    return 0;
}
```

## 问题4 倍增算法（求经过n条边的最短路）

问题描述：

牛站：给定一张由 T 条边构成的无向图，点的编号为 1∼10001∼1000 之间的整数。

求从起点 S 到终点 E 恰好经过 N 条边（可以重复经过）的最短路。

- d k, i, j : 经过k条边后i ， j的最短路径
- d (（a + b）i j)  =  min( d(a, i, k) + d( b ,k, j))
- a + b用倍增来求
- 用类floyd求解，建立一个新的数组，**这个数组不会用自己更新别人**，这样就产生了边数上的限制

<img src="C:\Users\24996\AppData\Roaming\Typora\typora-user-images\image-20231220210125474.png" alt="image-20231220210125474" style="zoom:67%;" />

tips：要离散化点数1000，边数100，所以要把点数离散化在100以内这样在n^3的算法复杂度内不会超时

```cpp
#include <iostream>
#include <cstring>
#include <algorithm>
#include <map>

using namespace std;

const int N = 110;

int g[N][N];
int k, t, s, e;
map <int, int> id;
int cnt;//用于离散化

int res[N][N];//存取快速幂的答案

void mul(int a[][N], int b[][N], int c[][N])
{
    static int temp[N][N];

    memset(temp, 0x3f, sizeof temp);

    for(int k = 1; k <= cnt; k ++)
        for(int i = 1; i <= cnt; i ++)
            for(int j = 1; j <= cnt; j ++)
                temp[i][j] = min(b[i][k] + c[k][j], temp[i][j]);

    memcpy(a, temp, sizeof temp);

}

void qmi()
{
    memset(res, 0x3f, sizeof res);

    for(int i = 1; i <= cnt; i ++) res[i][i] = 0;

    while(k)
    {
        if(k & 1) mul(res, res, g);//res = res * g
        mul(g, g, g);
        k >>= 1;
    }
}

int main()
{
    cin >> k >> t >> s >> e;

    memset(g, 0x3f, sizeof g);

    id[s] = ++ cnt, id[e] = ++ cnt;
    s = id[s], e = id[e];

    while(t --)
    {
        int c, a, b;
        cin >> c >> a >> b;
        if(!id.count(a)) id[a] = ++ cnt;
        if(!id.count(b)) id[b] = ++ cnt;
        a = id[a], b = id[b];
        g[a][b] = g[b][a] = min(g[a][b], c);
    }

    qmi();

    cout << res[s][e] << endl;

    return 0;
}
```

