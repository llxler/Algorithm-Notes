# DFS之迭代加深，双向dfs和IDA-star

## 迭代加深

**适用场景**：答案在**很浅**的地方，但是很容易探入到深入的地方

设计一个max_depth，每次搜索的深度控制为max_depth，超过的话则继续增大max_depth

为什么不停的重复还能比原先小的原本：节点是以指数级别增长的就算前面层数中节点全部加起来也会小于最后一层的数量

### eg.加成序列

你的任务是：给定一个整数 n，找出符合上述条件的长度 m 最小的“加成序列”。

如果有多个满足要求的答案，只需要找出任意一个可行解。

加成序列：递增且一个数能被它前面的数表示出

```cpp
#include <iostream>
#include <algorithm>
#include <cstring>

using namespace std;

const int N = 110;
int path[N];
int n;

int dfs(int u, int depth)//现在的深度为u，最大的深度为depth
{
    if(u > depth) return false;
    if(path[u - 1] == n) return true;
    
    bool st[N];
    memset(st, 0, sizeof st);
    for(int i = u - 1; i >= 0; i --)//优化搜索顺序，先枚举大的
        for(int j = i; j >= 0; j --)
        {
            int num = path[i] + path[j];
            if(st[num] || num > n || num <= path[u - 1]) continue;
            st[num] = true;
            path[u] = num;
            if(dfs(u + 1, depth)) return true;
        }
        
    return false;
}

int main()
{
    while(cin >> n, n)
    {
        int max_depth = 1;
        path[0] = 1;
        while(!dfs(1, max_depth)) max_depth ++;//迭代加深
        for(int i = 0; i < max_depth; i ++) cout << path[i] << ' ';
        cout << endl;
    }
    
    return 0;
}
```

## 双向dfs

### eg.送礼物

问题描述：达达帮翰翰给女生送礼物，翰翰一共准备了 N 个礼物，其中第 i 个礼物的重量是 G[i]。

达达的力气很大，他一次可以搬动重量之和不超过 W 的任意多个物品。

达达希望一次搬掉尽量重的一些物品，请你告诉达达在他的力气范围内一次性能搬动的最大重量是多少。

g的取值很大，如果用背包O（n * v）来解决的话会超时，但是n的范围很小，考虑可以用搜索来解决

==感悟==：参数u往往是总的个数，但由于从0开始，u这层枚举的其实是0-u-1下标的数

基本思路：

- 将所有物品按照重量从大到小排序

- 先将前K件物品凑出的重量打表

- 搜索剩下N-K的选择方式然后在表中二分找到Wsum的最大值

  ```cpp
  #include <iostream>
  #include <algorithm>
  
  using namespace std;
  
  typedef long long LL;
  
  const int N = 50, M = 1 << 25;
  int w[N], weight[M], cnt = 1;//0也是一种情况
  int m, n, k;
  int ans;
  
  void dfs1(int u, int s)
  {
      if(u == k)
      {
          weight[cnt ++] = s;
          return;
      }
      dfs1(u + 1, s);//不选当前这个数
      if((LL)s + w[u] <= m) dfs1(u + 1, s + w[u]);//选当前这个数
  }
  
  void dfs2(int u, int s)
  {
      if(u == n)//已经枚举了n个数，结束
      {
          if(s > m) return;
          int find = m - s;//要在weight中寻找的数字
          int l = 0, r = cnt - 1;
          while(l < r)
          {
              int mid = l + r + 1 >> 1;
              if(weight[mid] <= find) l = mid;
              else r = mid - 1;
          }
          ans = max(ans, s + weight[l]);
          return;
      }
      dfs2(u + 1, s);
      if((LL)s + w[u] <= m) dfs2(u + 1, s + w[u]);
  }
  
  int main()
  {
      scanf("%d%d", &m, &n);
      
      for(int i = 0; i < n; i ++) scanf("%d", &w[i]);
      sort(w, w + n, greater<int>());
      
      k = n / 2;
      
      dfs1(0, 0);
  
      sort(weight, weight + cnt);
      cnt = unique(weight, weight + cnt) - weight;
      
      dfs2(k, 0);
      
      printf("%d\n", ans);
      
      return 0;
  }
  ```

  ### eg.P3067 [USACO12OPEN] Balanced Cow Subsets G
  
  问题描述：将集合各子集划分成两组相同和的种类有多少种
  
  先枚举一半的集合
  
  ```cpp
  #include <iostream>
  #include <algorithm>
  #include <cstring>
  #include <vector>
  #include <unordered_map>
  
  using namespace std;
  
  const int N = 25;
  
  int n, a[N], res;
  bool s[1 << 20];
  unordered_map<int, vector<int>> mp;//记录达到sum的状态
  
  void dfs_1(int u, int sum, int state)
  {
  	if(u > n / 2)
  	{
  		mp[sum].push_back(state);
  		return;
  	}
  
  	dfs_1(u + 1, sum, state);//不选
  	dfs_1(u + 1, sum + a[u], state | 1 << (u - 1));//选
  	dfs_1(u + 1, sum - a[u], state | 1 << (u - 1));//选但是放在另外一侧
  	return;
  }
  
  void dfs_2(int u, int sum, int state)
  {
  	if(u > n)
  	{
  		if(mp.count(sum))
  			for(auto it: mp[sum])
  				s[it | state] = true;
  		
  		return;
  	}
  	dfs_2(u + 1, sum, state);//不选
  	dfs_2(u + 1, sum + a[u], state | 1 << (u - 1));//选
  	dfs_2(u + 1, sum - a[u], state | 1 << (u - 1));//选但是放在另外一侧
  	return;
  }
  
  
  int main()
  {
  	cin >> n;
  
  	for(int i = 1; i <= n; i ++) cin >> a[i];
  
  	dfs_1(1, 0, 0);//u, sum, state
  	dfs_2((n / 2) + 1, 0, 0);
  
  	for(int i = 1; i < 1 << 20; i ++)
  		if(s[i]) res ++;
  
  
  	cout << res << endl;
  
  	return 0;
  }
  ```
  
  

# IDA*

**==设计一个预估h，若当前深度 + h > max_depth则直接return==**

**正确性**保证：预估h <= 真实值

## eg. 排书

一次交换最多是三个tot 排好序，所以h = tot / 3向上取整

```cpp
#include <iostream>
#include <algorithm>
#include <cstring>

using namespace std;

const int N = 20;

int q[N];
int w[5][N];
int n;

int h()
{
    int tot = 0;
    for(int i = 0; i + 1 < n; i ++)
        if(q[i] != q[i + 1] - 1) tot ++;
    return (tot + 2) / 3;//tot / 3向上取整
}

bool dfs(int u, int depth)
{
    if(u + h() > depth) return false;
    if(!h()) return true;//已经排好序
    
    for(int len = 1; len < n; len ++)
        for(int l = 0; l + len - 1 < n; l ++)
        {
            int r = l + len - 1;
            for(int k = r + 1; k < n; k ++)//只放后面，要不然会枚举两次
            {
                memcpy(w[u], q, sizeof q);
                int y = l;
                for(int x = r + 1; x <= k; x ++, y ++) q[y] = w[u][x];
                for(int x = l; x <= r; x ++, y ++) q[y] = w[u][x];
                if(dfs(u + 1, depth)) return true;
                memcpy(q, w[u], sizeof q); // 恢复现场
            }
        }
    
    return false;
}

int main()
{
    int T;
    scanf("%d", &T);
    while(T --) 
    {
        scanf("%d", &n);
        for(int i = 0; i < n; i ++) scanf("%d", &q[i]);
        
        int max_depth = 0;
        while(max_depth < 5 && !dfs(0, max_depth)) max_depth ++;
        if(max_depth >= 5) puts("5 or more");
        else printf("%d\n", max_depth);
    }
    
    return 0;
}
```

双向BFS写法，见BFS里面章节。

## eg.回转游戏

做法：打表 + IDA*

问题描述（小模拟 + 爆搜）

如下图所示，有一个 `#` 形的棋盘，上面有 1,2,31,2,3 三种数字各 88 个。

给定 88 种操作，分别为图中的 A∼H。

这些操作会按照图中字母和箭头所指明的方向，把一条长为 77 的序列循环移动 11 个单位。

例如下图最左边的 `#` 形棋盘执行操作 A 后，会变为下图中间的 `#` 形棋盘，再执行操作 C 后会变成下图最右边的 `#` 形棋盘。

给定一个初始状态，请使用最少的操作次数，使 `#` 形棋盘最中间的 88 个格子里的数字相同

```cpp
/*
      0     1
      2     3
4  5  6  7  8  9  10
      11    12
13 14 15 16 17 18 19
      20    21
      22    23
*/

#include <cstdio>
#include <cstring>
#include <iostream>
#include <algorithm>

using namespace std;

const int N = 24;

int q[N];
int op[8][7] = {
    {0, 2, 6, 11, 15, 20, 22},
    {1, 3, 8, 12, 17, 21, 23},
    {10, 9, 8, 7, 6, 5, 4},
    {19, 18, 17, 16, 15, 14, 13},
    {23, 21, 17, 12, 8, 3, 1},
    {22, 20, 15, 11, 6, 2, 0},
    {13, 14, 15, 16, 17, 18, 19},
    {4, 5, 6, 7, 8, 9, 10}
};
int center[8] = {6, 7, 8, 11, 12, 15, 16, 17};
int opposite[8] = {5, 4, 7, 6, 1, 0, 3, 2};

int path[100];

int f()
{
    int sum[4];
    memset(sum, 0, sizeof sum);
    for(int i = 0; i < 8; i ++) sum[q[center[i]]] ++;
    int s = 0;
    for(int i = 1; i < 4; i ++) s = max(s, sum[i]);
    return 8 - s;
}

void calc(int x)
{
    int t = q[op[x][0]];
    for(int i = 0; i < 6; i ++) q[op[x][i]] = q[op[x][i + 1]];
    q[op[x][6]] = t;
}

bool dfs(int u, int depth, int last)
{
    if(u + f() > depth) return false;
    if(!f()) return true;
    
    for(int i = 0; i < 8; i ++)
        if(opposite[i] != last)
        {
            calc(i);
            path[u] = i;
            if(dfs(u + 1, depth, i)) return true;
            calc(opposite[i]);
        }
        
    return false;
}

int main()
{
    while(cin >> q[0], q[0])
    {
        for(int i = 1; i < N; i ++) cin >> q[i];
        
        int max_depth = 0;
        while(!dfs(0, max_depth, -1)) max_depth ++;
        if(max_depth == 0) puts("No moves needed");
        else
        {
            for(int i = 0; i < max_depth; i ++) cout << char(path[i] + 'A');
            cout << endl;
        }
        cout << q[6] << endl;
    }
    
    return 0;
}
```

