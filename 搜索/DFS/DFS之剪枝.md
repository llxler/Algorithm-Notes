# DFS之剪枝

## 常用的剪枝方式

- 优化搜索顺序
  - 大部分情况下，我们应该优先搜索分支较少的节点
- 排除等效冗余（如排序->组合）
- 可行性剪枝
- 最优性剪枝（如何都更新不了最好的答案则return)
- 记忆化搜索DP

## 例题

### eg.小猫爬山

翰翰和达达饲养了 N 只小猫，这天，小猫们要去爬山。

经历了千辛万苦，小猫们终于爬上了山顶，但是疲倦的它们再也不想徒步走下山了（呜咕>_<）。

翰翰和达达只好花钱让它们坐索道下山。

索道上的缆车最大承重量为 W，而 N 只小猫的重量分别是 C1、C2……CN

当然，每辆缆车上的小猫的重量之和不能超过 W。

每租用一辆缆车，翰翰和达达就要付 11 美元，所以他们想知道，最少需要付多少美元才能把这 N 只小猫都运送下山？

剪枝方法：

- 优化搜索顺序：先放重猫这样带来的分支数量最少
- 最优性剪枝：当前大于ANS直接return

```cpp
#include <iostream>
#include <cstring>
#include <algorithm>

using namespace std;

const int N = 20;

int w[N];
int sum[N];
int n, m;
int ans = N;

void dfs(int u, int k)//现在是第u只小猫，分好了k组
{
    if(k >= ans) return;//最优性剪枝
    if(u == n)
    {
        ans = k;
        return;
    }
    
    for(int i = 0; i < k; i ++)
        if(sum[i] + w[u] <= m)//可行性剪枝
        {
            sum[i] += w[u];
            dfs(u + 1, k);
            sum[i] -= w[u];
        }
    
    sum[k] = w[u];
    dfs(u + 1, k + 1);
    sum[k] = 0;
}

int main()
{
    cin >> n >> m;
    
    for(int i = 0; i < n; i ++) cin >> w[i];
    
    sort(w, w + n);
    
    //优化搜索顺序剪枝
    reverse(w, w + n);
    
    dfs(0, 0);//当前第0只猫，组数为0
    
    cout << ans << endl;
    
    return 0;
}
```

### eg.吃奶酪

#### **借助状态压缩进行冗余优化 + 最优剪枝**

```cpp
#include <iostream>
#include <cstring>
#include <algorithm>
#include <cmath>

#define x first
#define y second

using namespace std;

const int N = 15, M = 1 << 15;
const double eps = 1e-6;

typedef pair<double, double> PDD;

int n;
double ans = 2000000000.0;
PDD ver[N];
int map[M];
double f[M][N];//优化数组，记录状态i,终点为j的点的最小值

int lowbit(int x)
{
    return x & -x;
}

int cmp(double x, double y)
{
    if(fabs(x - y) < eps) return 0;
    else if(x > y) return 1;
    else return -1;
}

double f_dis(double x1, double y1, double x2, double y2)
{
    return sqrt((x1 - x2) * (x1 - x2) + (y1 - y2) * (y1 - y2));
}

void dfs(int u, int state, double s)
{
    if(cmp(s, ans) != -1) return;
    if(!state)
    {
        ans = s;
        return;
    }
    for(int i = state; i; i -= lowbit(i))//取出第一个1
    {
        int k = map[lowbit(i)];
        double distance = f_dis(ver[u].x, ver[u].y, ver[k].x, ver[k].y);
        int state_now = state;
        int state_next = state - (1 << k);
        if(cmp(f[state_next][k], s + distance) != 1) continue;//冗余性剪枝
        f[state_next][k] = s + distance;
        dfs(k, state_next, s + distance);
    }
    return;
}

int main()
{
    for(int i = 0; i < N; i ++) map[1 << i] = i;

    for(int i = 0; i < M; i ++)
        for(int j = 0; j < N; j ++)
            f[i][j] = 2000000000.0;

    scanf("%d", &n);

    for(int i = 0; i < n; i ++)
    {
        double x, y;
        scanf("%lf%lf", &x, &y);
        ver[i] = {x, y};
    }

    for(int i = 0; i < n; i ++)
    {
        auto t = ver[i];
        double x1 = t.x, y1 = t.y;
        double distance = f_dis(x1, y1, 0, 0);
        int u = i;
        int state = ((1 << n) - 1) - (1 << u);
        f[state][u] = distance;
        dfs(u, state, distance);
    }

    printf("%.2lf\n", ans);

    return 0;
}
```

#### TSP模板写法

```cpp
#include <iostream>
#include <cstring>
#include <algorithm>
#include <cmath>

#define x first
#define y second

using namespace std;

const int N = 15, M = 1 << 15;

typedef pair<double, double>PDD;

int n;
PDD ver[N];
double f[M][N];//走到状态i，到达j 

double f_dis(double x1, double y1, double x2, double y2)
{
    return sqrt((x1 - x2) * (x1 - x2) + (y1 - y2) * (y1 - y2));
}

int main()
{
    scanf("%d", &n);

    for(int i = 0; i < n; i ++)
    {
        double x, y;
        scanf("%lf%lf", &x, &y);
        ver[i] = {x, y};
    }
    
    for(int i = 0; i < M; i ++)
		for(int j = 0; j < N; j ++)
			f[i][j] = 2000000000.0; 
    
	for(int i = 0; i < n; i ++) f[1 << i][i] = f_dis(ver[i].x, ver[i].y, 0.0, 0.0);
	
	for(int i = 1; i < (1 << n); i ++)
		for(int j = 0; j < n; j ++)
		{
			if((i >> j & 1) == 0) continue;
			for(int k = 0; k < n; k ++)
			{
				if(k == j || (i >> k & 1) == 0) continue;
				f[i][j] = min(f[i][j], f[i - (1 << j)][k] + f_dis(ver[j].x, ver[j].y, ver[k].x, ver[k].y));
			}
		}
	
	double ans = f[(1 << n) - 1][0];
	
	for(int i = 1; i < n; i ++) ans = min(ans, f[(1 << n) - 1][i]);
	
    printf("%.2lf\n", ans);

    return 0;
}
```



### eg.数独(重复训练)

- 优化搜索顺序：优先选择分支最少的格子，如果相同就搜索前面一个

- 可行性剪枝：只能填满足的数字

- 特性，运用到了位运算优化:行，列，九宫格状态相与，这样可以得到可以填的数字

  结合LOWBIT运算取出其中的1（不用循环9次了）

```cpp
#include <iostream>
#include <cstring>
#include <algorithm>

using namespace std;

const int N = 9, M = 1 << N;

int ones[M], map[M];//存储每个数字对应1的状态，以及这个数对应的位数是多少
int row[N], col[N], cell[3][3];
char str[100];
int cnt;

void init()
{
    for(int i = 0; i < N; i ++) row[i] = col[i] = (1 << N) - 1;
    
    for(int i = 0; i < 3; i ++)
        for(int j = 0; j < 3; j ++)
            cell[i][j] = (1 << N) - 1;
}

int lowbit(int x)
{
    return x & -x;
}

void draw(int i, int j, int t, bool set)
{
    if(set)str[i * N + j] = '1' + t;
    else str[i * N + j] = '.';
    
    int v = 1 << t;
    if(!set) v = -v;
    
    row[i] -= v;
    col[j] -= v;
    cell[i / 3][j / 3] -= v;
}

int get(int x, int y)
{
    return row[x] & col[y] & cell[x / 3][y / 3];
}

bool dfs(int cnt)
{
    if(!cnt) return true;
    
    int minv = 10;
    int x, y;
    
    for(int i = 0; i < N; i ++)
        for(int j = 0; j < N; j ++)
            if(str[i * N + j] == '.')
            {
                int state = get(i, j);
                if(minv > ones[state])
                {
                    minv = ones[state];
                    x = i, y = j;
                }
            }
    
    int state = get(x, y);//这里的state得是x ，y对应的state
    
    for(int i = state; i; i -= lowbit(i))
    {
        int t = map[lowbit(i)];
        draw(x, y, t, true);
        if(dfs(cnt - 1)) return true;
        draw(x, y, t, false);
    }
    
    return false;
}

int main()
{
    for(int i = 0; i < M; i ++)
        for(int j = 0; j < N; j ++)
            ones[i] += i >> j & 1;
    
    for(int i = 0; i < N; i ++) map[1 << i] = i;//求log2的作用,配合lowbit快速求位数
    
    while(cin >> str, str[0] != 'e')
    {
        init();
        cnt = 0;//空格的数量
        for(int i = 0, k = 0; i < N; i ++)
            for(int j = 0; j < N; j ++, k ++)
                if(str[k] != '.')//这里是数字
                {
                    int t = str[k] - '1';
                    draw(i, j, t, true);
                }
                else cnt ++;
                
        dfs(cnt);
        puts(str);
    }
}
```

### eg.木棒（涉及到剪枝的方向很多）

问题描述：乔治拿来一组等长的木棒，将它们随机地砍断，使得每一节木棍的长度都不超过 50 个长度单位。

然后他又想把这些木棍恢复到为裁截前的状态，但忘记了初始时有多少木棒以及木棒的初始长度。

请你设计一个程序，帮助乔治计算木棒的可能最小长度。

每一节木棍的长度都用大于零的整数表示。

- 优化顺序-先搜大长度的
- 可行性 搜满足长度限制的
- 冗余性
  - 第一根不满足return false
  - 最后一格放入的不满足return false
  - 同样长度的跳过
  - 采用组合型枚举

```cpp
#include <iostream>
#include <algorithm>

using namespace std;

const int N = 70;

bool st[N];
int w[N];
int n, length, sum;

bool dfs(int u, int s, int start) //第u组木棒，目前s的重量，从start开始枚举
{
	if(u * length == sum) return true;
	if(s == length) return dfs(u + 1, 0, 0);
	
	for(int i = start; i < n; i ++)//剪枝2-冗余剪枝-组合型枚举 
	{
		if(st[i]) continue;
		if(w[i] + s > length) continue;//剪枝3-可行性剪枝-只枚举正确的选择 
		
		st[i] = true;
		if(dfs(u, s + w[i], i)) return true;
		st[i] = false;
		
		if(!s) return false;//剪枝4-冗余剪枝-第一根都不行，后面肯定也不行
		
		if(s + w[i] == length) return false;//剪枝5-冗余剪枝-最后一根正好不行，那肯定不行
		
		int j = i;
		while(j < n && w[j] == w[i]) j ++;//剪枝6-冗余剪枝-同样长度肯定不行
		i = j - 1;//因为程序结束之后i还会++，一个重要的细节 
	}   
	
	return false;
} 

int main()
{
	while(scanf("%d", &n), n)
	{
		length = 1, sum = 0;
		for(int i = 0; i < N; i ++) st[i] = false;
		
		for(int i = 0; i < n; i ++)
		{
			scanf("%d", &w[i]);
			sum += w[i]; 
		}
        
        sort(w, w + n);//剪枝7-优化搜索顺序-每次搜节点数少的分支
        reverse(w, w + n);
        
		while(length <= sum)
		{
			if(sum % length != 0)//剪枝1-可行性剪枝-只取sum约数 
			{
			    length ++;
			}
			else
			{
			    if(dfs(0, 0, 0)) printf("%d\n", length);
			    else length ++;
			}
		}
	}
	
	return 0;
} 
```

```cpp
#include <bits/stdc++.h>

using namespace std;

const int N = 100;

int n, a[N], len, sum;
bool st[N];

bool dfs(int u, int cnt, int s) {
	if (cnt * len == sum) return true;
	if (s == len) return dfs(1, cnt + 1, 0);
	for (int i = u; i <= n; ++i) {
		if (a[i] + s > len) continue;
		if (st[i]) continue;
		st[i] = true;
		if (dfs(u + 1, cnt, s + a[i])) return true;
		st[i] = false;
		if (!s || s + a[i] == len) return false;
		int j = i;
		while (j <= n && a[j] == a[i]) j++;
		i = j - 1;
	}
	return false;
}

int main() {
	ios::sync_with_stdio(false);
	cin.tie(nullptr);
	while (cin >> n, n) {
		memset(st, 0, sizeof st);
		len = 1, sum = 0;
		for (int i = 1; i <= n; ++i) {
			cin >> a[i];
			sum += a[i];
		}
		sort(a + 1, a + n + 1, greater<int>());
		while (true) {
			if (sum % len == 0 && dfs(1, 0, 0)) {
				cout << len << '\n';
				break;
			} else {
				len++;
			}
		}
	}
	return 0;
}
```



### eg.生日蛋糕

问题描述：

77 月 1717 日是 Mr.W 的生日，ACM-THU 为此要制作一个体积为 Nπ 的 M 层生日蛋糕，每层都是一个圆柱体。

设从下往上数第 i 层蛋糕是半径为 Ri，高度为 Hi 的圆柱。

当 i<M 时，要求 Ri>Ri+1 且 Hi>Hi+1

由于要在蛋糕上抹奶油，为尽可能节约经费，我们希望蛋糕外表面（最下一层的下底面除外）的面积 Q 最小。

令 Q=Sπ=π ，请编程对给出的 N 和 M，找出蛋糕的制作方案（适当的 Ri和 Hi 的值），使 S 最小。

除 Q 外，以上所有数据皆为正整数。

- 优化搜索顺序

  - 自底向上搜索
  - 先枚举R，再枚举H（半径是平方级别占的更大）
  - 从大到小枚举参数

- 可行性剪枝

  - u <= R(u) <= min(R(u + 1) - 1, sqrt(n - v))
  - minv(u), mins(u)
    - v + min(u) <= n
    - s + mins(u) < ans // 最优性剪枝

- **数学推导剪枝**（也属于可行性）

  s(u) > 2(n - v) / r(u + 1) (表面积和体积之间满足这个关系)

  s + 2 * (n - v) / R[u + 1] >= ans

```cpp
#include <iostream>
#include <cmath>
#include <cstring>
#include <algorithm>

using namespace std;

const int N = 25, INF = 1e9;

int R[N], H[N];
int minv[N], mins[N];
int n, m, ans = INF;

void dfs(int u, int v, int s)//当前第u层，体积为v，表面积为s
{
    if(v + minv[u] > n) return;
    if(s + mins[u] >= ans) return;
    if(s + 2 * (n - v) / R[u + 1] >= ans) return;//数学推导，如果当前不能更新，则直接结束
    
    if(!u)
    {
        if(v == n) ans = s;
        return;
    }
    
    for(int r = min(R[u + 1] - 1, (int)sqrt(n - v)); r >= u; r --)
        for(int h = min(H[u + 1] - 1, (n - v) / r / r); h >= u; h --)
        {
            int t = 0;
            if(u == m) t = r * r;//加上上面那层所有的面积
            R[u] = r, H[u] = h;
            dfs(u - 1, v + r * r * h, s + 2 * r * h + t);
        }
}

int main()
{
    scanf("%d%d", &n, &m);
    
    for(int i = 1; i <= m; i ++)
    {
        minv[i] = minv[i - 1] + i * i * i;
        mins[i] = mins[i - 1] + 2 * i * i;
    }
    
    R[m + 1] = H[m + 1] = INF;//为了第m层做准备
    
    dfs(m, 0, 0);
    if(ans == 1e9)
    {
        cout << 0 << endl;
        return 0;
    }
    
    cout << ans << endl;
    
    return 0;
}
```

