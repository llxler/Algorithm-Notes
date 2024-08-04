# 多源bfs

求解的问题：求一群点到另外一群点之间的最短距离

**做法**：将其中一群点的**距离初始为0并全加入到队列**中，剩下操作与基本bfs一致

eg.矩阵距离

求矩阵中0到1的各自最短距离

```cpp
#include <cstring>
#include <iostream>

#define x first
#define y second

using namespace std;

typedef pair <int, int> PII;

const int N = 1e3 + 10, M = N * N;

int n, m;
char g[N][N]; 
int dis[N][N];//充当st和记录距离的作用
PII q[M];

void bfs()
{
    int dx[4]= {0, 1, -1, 0}, dy[4] = {1, 0, 0, -1};
    
    memset(dis, -1, sizeof dis);
    
    int hh = 0, tt = -1;//初始的时候是空的
    for(int i = 0; i < n; i ++)
        for(int j = 0; j < m; j ++)
            if(g[i][j] == '1')
            {
                q[++ tt] = {i, j};
                dis[i][j] = 0;
            }
    while(hh <= tt)
    {
        PII t = q[hh ++];
        for(int i = 0; i < 4;i ++)
        {
            int a = t.x + dx[i], b = t.y + dy[i];
            if(a < 0 || a >= n || b < 0 || b >= m) continue;
            if(dis[a][b] != -1) continue;
            dis[a][b] = dis[t.x][t.y] + 1;
            q[++ tt] = {a, b};
        }
    }
}

int main()
{
	cin >> n >> m;
    for(int i = 0; i < n; i ++) cin >> g[i];
    
    bfs();
    
    for(int i = 0; i < n; i ++)
    {
        for(int j = 0; j < m; j ++)
            cout << dis[i][j] << ' ';
        cout << endl;
    }
    
    return 0;
}
```

