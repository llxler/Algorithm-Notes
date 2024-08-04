# Flood Fill 模板

八方格类型泛洪 + **只统计连通块个数**

eg.池塘计数

```cpp
#include <iostream>

using namespace std;

#define x first
#define y second

typedef pair <int, int> PII;

const int N = 110, M = N * N;

char g[N][N];//*注意这里可能是char数组
bool st[N][N];
int n, m;

void bfs(int sx, int sy)
{
    st[sx][sy] = true;
    int hh = 0, tt = 0;//初始时队列有值为sx, sy
    PII q[M];//因为记录的是点的数量所以是N * N
    q[0] = {sx, sy};

    while(hh <= tt)
    {
        PII t = q[hh ++];

        for(int i = t.x - 1; i <= t.x + 1; i ++)//遍历八方向方法
            for(int j = t.y - 1; j <= t.y + 1; j ++)
            {
                if(i == t.x && j == t.y) continue;
                if(i < 0 || i >= n || j < 0 || j >= m) continue;
                if(st[i][j] || g[i][j] == '.') continue;
                q[++ tt] = {i, j};
                st[i][j] = true;
            }
    }

}

int main()
{
    scanf("%d%d", &n, &m);

    for(int i = 0; i < n; i ++) scanf("%s", &g[i]);


    int cnt = 0;

    for(int i = 0; i < n; i ++)
        for(int j = 0; j < m; j ++)
            if(g[i][j] == 'W' && !st[i][j])
            {
                bfs(i, j);
                cnt ++;
            }

    printf("%d\n", cnt);

    return 0;
}
```

四方格类型泛洪 + **统计最大格子数量**

eg：城堡问题

```cpp
#include <iostream> 

using namespace std;

#define x first
#define y second

typedef pair<int, int> PII;

const int N = 55, M = N * N;

int g[N][N];
bool st[N][N];
PII q[M];
int n, m;

int bfs(int sx, int sy)
{
	st[sx][sy] = true;
	int hh = 0, tt = 0;
	q[0] = {sx, sy};
	
	int area = 0;
	int dx[4] = {0, -1, 0, 1}, dy[4] = {-1, 0, 1, 0};
		    
	while(hh <= tt)
	{
		PII t = q[hh ++];
		area ++;//每个点都在这里入队一次 
		
		for(int i = 0; i < 4; i ++)
		{
			int nx = t.x + dx[i], ny = t.y + dy[i];
			if(st[nx][ny]) continue;
			if(nx < 0 || nx >= n || ny < 0 || ny >= m) continue;
			if(g[t.x][t.y] >> i & 1) continue;//这里是相对与t而不是nx,ny 
			
			st[nx][ny] = true;
			q[++ tt] = {nx, ny};
		}	
	}
	
	return area;	
}

int main()
{
	scanf("%d%d", &n, &m);
	
	for(int i = 0; i < n; i ++)
		for(int j = 0; j < m; j ++)
			scanf("%d", &g[i][j]);
	
	int cnt = 0, area = 0;
	
	for(int i = 0; i < n; i ++)
		for(int j = 0; j < m; j ++)
		if(!st[i][j]) 
		{
			area = max(area, bfs(i, j));
			cnt ++;
		}
	
	printf("%d\n%d\n", cnt, area);
	
	return 0;
}
```

**遍历边界情况**

eg.山峰和山谷

问题描述：周围的方格（即不属于山峰或山谷但与山峰或山谷相邻的格子）高度均大于山谷的高度，或小于山峰的高度。

求地图内山峰和山谷的数量。特别地，如果整个地图方格的高度均相同，则整个地图既是一个山谷，也是一个山峰。

```cpp
#include <iostream>

#define x first
#define y second

using namespace std;

typedef pair <int, int> PII;

const int N = 1010, M = N * N;

int h[N][N];
int n;
bool st[N][N];
PII q[M];

void bfs(int sx, int sy, bool &has_higher, bool &has_lower)
{
	st[sx][sy] = true;
	int hh = 0, tt = 0;
	q[0] = {sx, sy};
	
	while(hh <= tt)
	{
		PII t = q[hh ++];
		
		for(int i = t.x - 1; i <= t.x + 1; i ++)
			for(int j = t.y - 1; j <= t.y + 1; j ++)
			{
				if(t.x == i && t.y == j) continue;//挖掉中间的格子 
				if(i < 1 || i > n || j < 1 || j > n) continue;
//		易错	if(st[i][j]) continue; 在这里就continue是错误的，因为还要通过这里来判断外界情况 
				if(h[i][j] != h[t.x][t.y]) // ***边界情况 (再外一层即不满足) 
				{
					if(h[i][j] > h[t.x][t.y]) has_higher = true;
					else has_lower = true;
				}
				else if(!st[i][j])//相等则加入队列 
				{
					st[i][j] = true;
					q[++ tt] = {i, j};
				}
			}
	}
}

int main()
{
	scanf("%d", &n);
	
	for(int i = 1; i <= n; i ++)
		for(int j = 1; j <= n; j ++)
			scanf("%d", &h[i][j]);
			
	int valley = 0, peak = 0;
	
	for(int i = 1; i <= n; i ++)
		for(int j = 1; j <= n; j ++)
			if(!st[i][j])
			{
				bool has_higher = false;
				bool has_lower = false;
				bfs(i, j, has_higher, has_lower);
				if(!has_higher) peak ++;
				if(!has_lower) valley ++;
			}
	
	printf("%d %d\n", peak, valley);
	
	return 0;
}
```



