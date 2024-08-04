# DFS之搜索顺序

搜索解的时候是为了求解所有对应的状态，因此**常常需要回溯**

## 例题

### eg.马走日

求dfs状态**达到终点的种数**

```cpp
#include <cstring>
#include <iostream>

using namespace std;

const int N = 15;

int res;
bool st[N][N];
int n, m, x, y;
int dx[8] = {-2, -1, 1, 2, 2, 1, -1, -2};
int dy[8] = {1, 2, 2, 1, -1, -2, -2, -1};

void dfs(int x, int y, int step)
{
	if(step == m * n)
	{
		res ++;
		return;
	}
	st[x][y] = true;
	for(int i = 0; i < 8; i ++)
	{
		int a = x + dx[i], b = y + dy[i];
		if(a < 0 || b < 0 || a >= n || b >= m) continue;
		if(st[a][b]) continue;
		dfs(a, b, step + 1);
	}
	st[x][y] = false;
}

int main()
{
	int T;
	cin >> T;
	while(T --)
	{
		cin >> n >> m >> x >> y;
		memset(st, 0, sizeof st);
		res = 0;
		dfs(x, y, 1);
		cout << res << endl;
	}
	
	return 0;
}
```

### eg.单词接龙

**dfs求解达到要求的最大值**

问题描述：单词接龙是一个与我们经常玩的成语接龙相类似的游戏，现在我们已知一组单词，且给定一个开头的字母，要求出以这个字母开头的最长的“龙”（每个单词都最多在“龙”中出现两次），在两个单词相连时，其重合部分合为一部分，例如 `beast` 和 `astonish`，如果接成一条龙则变为 `beastonish`，另外相邻的两部分不能存在包含关系，例如 `at` 和 `atide` 间不能相连。

解法：预处理出来**两两单词之间最小的重合长度**。

```cpp
#include <iostream> 
#include <algorithm>

using namespace std;

const int N = 25;

int g[N][N];//记录两个字母之间重合的数量
string w[N];//存储单词
int used[N];//存储单词对应下标使用次数
int n, res;

void dfs(string dragon, int last)
{
	res = max(res, (int)dragon.size());
	
	used[last] ++;
	
	for(int i = 0; i < n; i ++)
		if(used[i] < 2 && g[last][i])//last和i之间得有联系 
			dfs(dragon + w[i].substr(g[last][i]), i);
	
	used[last] --; 
}

int main()
{
	cin >> n;
	for(int i = 0; i < n; i ++) cin >> w[i];
	
	//初始化i，j对应长度 
	for(int i = 0; i < n; i ++)
		for(int j = 0; j < n; j ++)
			for(int k = 1; k < min(w[i].size(), w[j].size()); k ++)
				if(w[i].substr(w[i].size() - k, k) == w[j].substr(0, k))
					
					{
						g[i][j] = k;
						break;//break的目的是为了让衔接的长度尽可能小，这样拼起来尽可能长 
					}
	
	char start;
	cin >> start;
	for(int i = 0; i < n; i ++)
		if(w[i][0] == start) dfs(w[i], i);
		
	cout << res << endl;
	
	return 0;
} 
```

**能用组合类型枚举就不要用排列型枚举，这样能大幅度减少搜索数量**

**因此题目不讲究顺序的话，则选用组合型枚举（添加一个start参数控制添加的数是按照顺序的）**

### eg.质因数分组      

 **涉及到的枚举细节很多，可以仔细揣摩**

给定n个正整数，将它们分组，使得每组中任意两个数互质。至少要分成多少个组？n <= 10

- ==固定将每个数都加入到最后一个组中这样能大幅度减少搜索顺序==，这样也能枚举完所有情况
- 按照组合顺序枚举
- 如果当前组数大于则直接return，来剪枝

```cpp
#include <iostream>
#include <algorithm>

using namespace std;

const int N = 15;

int w[N];
bool st[N];
int g[N][N];//i组内第j个数
int n;
int res = N;

int gcd(int a, int b)
{
	return b ? gcd(b, a % b) : a;
}

bool check(int num[], int size, int x)//全部互质返回真 
{
	for(int i = 0; i < size; i ++)
		if(gcd(num[i], x) > 1) return false;
	return true;
}

void dfs(int u, int gc, int tc, int start)//第u组，组内有gc个数，一共用了tc个数，从start开始枚举
{
	if(u >= res) return;//剪枝
	if(tc == n) res = u;
	
	bool flag = true;
	for(int i = start; i < n; i ++)//开始顺序的枚举数字
	{
		if(st[i] || !check(g[u], gc, w[i])) continue;
		st[i] = true;
		g[u][gc] = w[i]; 
		dfs(u, gc + 1, tc + 1, i + 1);
		
		st[i] = false;
		flag = false;
	}
	if(true)//如果当前没有任何数能加入
	dfs(u + 1, 0, tc, 0);//重新开始start	
} 

int main()
{
	cin >> n;
	for(int i = 0; i < n; i ++) cin >> w[i];
	
	dfs(1, 0, 0, 0);
	
	cout << res << endl;
	
	return 0; 
}
```



