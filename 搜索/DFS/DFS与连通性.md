# 	DFS解决连通性

优点：快，缺点：无法知道最短路，只知道能不能走到

==**如果是状态之间要转化则要考虑回溯，如果是一个状态内部枚举状态则一般不需要考虑回溯**==

eg.迷宫问题 输入两个点，问能否相互走到

```cpp
#include <iostream>
#include <cstring>

using namespace std;

const int N = 110;

int n, m;
int sx, sy, gx, gy;
char g[N][N];
bool st[N][N];

int dx[4] = {0, 1, -1, 0}, dy[4] = {1, 0, 0, -1};

bool dfs(int x, int y)
{
	if(x == gx && y == gy) return true;
	st[x][y] = true;
	for(int i = 0; i < 4; i ++)
	{
		int nx = x + dx[i], ny = y + dy[i];
		if(nx < 0 || nx >= n || ny < 0 || ny >= n) continue;
		if(g[nx][ny] == '#') continue;
		if(st[nx][ny]) continue;
		if(dfs(nx, ny)) return true;
	}
	return false;
}

int main()
{
	int T;
	cin >> T;
	while(T --)
	{
		cin >> n;
		for(int i = 0; i < n; i ++) cin >> g[i];
		memset(st, false, sizeof st);
		cin >> sx >> sy >> gx >> gy;
		if(dfs(sx, sy)) puts("YES");
		else puts("NO");
	}
	
	return 0;
} 
```



eg.红与黑

输出连通块的**数量**

```cpp
#include <iostream>
#include <cstring>

using namespace std;

const int N = 25;

char g[N][N];
int n, m;
bool st[N][N];
int dx[4] = {-1, 0, 1, 0}, dy[4] = {0, 1, 0, -1};

int dfs(int x, int y)
{
    int cnt = 1;
    st[x][y] = true;
    
    for(int i = 0; i < 4; i ++)
    {
        int a = x + dx[i], b = y + dy[i];
        if(a < 0 || a >= n || b < 0 || b >= m) continue;
        if(st[a][b]) continue;
        if(g[a][b] == '#') continue;
        cnt += dfs(a, b);
    }
    
    return cnt;
}

int main()
{
    while(cin >> m >> n, m || n)
    {
        memset(st, 0, sizeof st);
        
        for(int i = 0; i < n; i ++) cin >> g[i];
        
        int x, y;
        for(int i = 0; i < n; i ++)
            for(int j = 0; j < m; j ++)
                if(g[i][j] == '@')x = i, y = j;
        
        cout << dfs(x, y) << endl;        
    }
    
    return 0;
}
```



## eg. cf maze

# Maze

## 题面翻译

题目描述：

小P非常的喜欢方格迷宫。方格迷宫是一个n*m的由墙和空地构成的长方形方阵。只有当两点满足四联通条件时才能走过去。

小P画了一个迷宫，里面所有的空地都是四连通的。但~~闲着没事干的~~小P认为自己画的迷宫里小墙太多了很难看，所以他希望能够通过把迷宫中k个格子从空地变成墙但不破坏整张图的连通性（即仍然保持所有空地在一个四连通块中）

但是小P太蠢了做不来，请你帮助他。

输入：
第一行n,m,k（描述如题）
第二到n+1行：
每行m个字符，分别是'.'或'#'。 '.'表示空地，'#'表示墙。
输出：
把整张图原样打出来，但是被你修改的格子输出'X'。

（题意基本正确）

## 解题思路：

反向思考，记录联通的area - k个点，反向把其他k个点变成X 

```cpp
#include <bits/stdc++.h>

using namespace std;

const int N = 520;

char g[N][N];
int n, m, k, area;

struct Node {
    int x, y;
};

vector<Node> points;

int dx[] = {0, 1, 0, -1}, dy[] = {1, 0, -1, 0};

bool st[N * N];

void dfs(int x, int y) {
	++area;
    st[x * m + y] = true;
    points.push_back({x, y});
    for (int i = 0; i < 4; ++i) {
        int a = x + dx[i], b = y + dy[i];
        if (a < 0 || a >= n || b < 0 || b >= m) continue;
        if (g[a][b] == '#' || st[a * m + b]) continue;
        dfs(a, b);
    }
}

signed main() {
    cin.tie(nullptr)->ios::sync_with_stdio(false);
    cin >> n >> m >> k;
    for (int i = 0; i < n; ++i)
        cin >> g[i];
    for (int i = 0; i < n; ++i) {
        bool ok = false;
        for (int j = 0; j < m; ++j)
            if (g[i][j] == '.') {
                dfs(i, j);
                ok = true;
                break;
            }
        if (ok) break;
    }
    map<pair<int, int>, bool> state;
    for (int i = 0; i < area - k; i++) {
    	auto it = points[i];
    	state[{it.x, it.y}] = true;
    }
    for (int i = 0; i < n; i++) {
    	for (int j = 0; j < m; j++) {
    		if (g[i][j] == '#') cout << g[i][j];
    		else {
    			if (state[{i, j}]) cout << '.';
    			else cout << 'X';
    		}
    	}
    	cout << '\n';
    }
    return 0;
}
```

