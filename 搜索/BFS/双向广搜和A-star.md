# 折半搜索 和 A-star

## 双向广搜

双向广搜的优化原理：将a^b -> 2 * a ^ (b / 2)，进一步优化：**每次选择两边最小的枝数进行拓展， 实现两边的拓展数量平衡**

应用场景：常常**用于最小步数模型**

## 双向广搜还可以用两个单向dfs + unordered_map来实现，后者好写得多

### eg.字串变换

正常搜索会爆炸，采用双向搜索来优化

```cpp
#include <iostream>
#include <cstring>
#include <algorithm>
#include <queue>
#include <unordered_map>

using namespace std;

const int N = 10;
unordered_map <string, int> da;
unordered_map <string, int> db;
string a[N], b[N];
queue <string> qa;
queue <string> qb;
int n;

int extend(queue <string> &q, unordered_map <string, int> &da, unordered_map <string, int> &db, string a[], string b[])
{
    auto t = q.front();
    q.pop();
    
    for(int i = 0; i < t.size(); i ++)
        for(int j = 0; j < n; j ++)
        if(t.substr(i, a[j].size()) == a[j])//可以替换
        {
            string tmp = t.substr(0, i) + b[j] + t.substr(i + a[j].size());
            if(db.count(tmp))return da[t] + db[tmp] + 1;
            if(da.count(tmp)) continue;
            da[tmp] = da[t] + 1;
            q.push(tmp);
        }
        
    return 11;
}

int bfs(string start, string end)
{
    
    if(start == end) return 0;
    
    qa.push(start), qb.push(end);
    da[start] = 0, db[end] = 0;
    
    while(qa.size() && qb.size())
    {
        int t;
        if(qa.size() <= qb.size())//则用qa来进行搜索
            t = extend(qa, da, db, a, b);
        else 
            t = extend(qb, db, da, b, a);
        if(t <= 10) return t;
    }
    
    return 11;
}

int main()
{
    string start, end;
    cin >> start >> end;
    
    while(cin >> a[n] >> b[n]) n ++;
    
    int step = bfs(start, end);
    if(step <= 10) cout << step << endl;
    else puts("NO ANSWER!");
    
    return 0;
}
```

## eg.买瓜

小蓝正在一个瓜摊上买瓜。瓜摊上共有 n 个瓜，每个瓜的重量为 Ai 。

小蓝刀功了得，他可以把任何瓜劈成完全等重的两份，不过每个瓜只能劈一刀。

小蓝希望买到的瓜的重量的和恰好为 m 。

请问小蓝至少要劈多少个瓜才能买到重量恰好为 m 的瓜。如果无论怎样小蓝都无法得到总重恰好为 m 的瓜，请输出 −1 。

```cpp
#include <bits/stdc++.h>
#define int long long
using namespace std;

unordered_map<int, int> key; // sum -> cnt

int n, m, ans = 2e9, N;
int a[66];

void dfs1(int u, int sum, int cnt) {
    if (sum > m || cnt > ans)
        return;
    if (sum == m) {
        ans = min(ans, cnt);
        return;
    }
    if (u == N + 1) {
    	if (sum < m) {
    		if (key.count(sum)) {
    			key[sum] = min(key[sum], cnt);
    		} else {
    			key[sum] = cnt;
    		}
    	}
    	return;
    }
    // 不选
    dfs1(u + 1, sum, cnt);
    // 全选
    dfs1(u + 1, sum + a[u], cnt);
    // 选一半
    dfs1(u + 1, sum + a[u] / 2, cnt + 1);
}

void dfs2(int u, int sum, int cnt) {
    if (sum > m || cnt > ans)
        return;
    if (sum == m) {
        ans = min(ans, cnt);
        return;
    }
    if (u == n + 1) {
        if (sum < m) {
        	if (key.count(m - sum)) {
        		ans = min(ans, key[m - sum] + cnt);
        	}
        }
        return;
    }
    // 不选
    dfs2(u + 1, sum, cnt);
    // 全选
    dfs2(u + 1, sum + a[u], cnt);
    // 选一半
    dfs2(u + 1, sum + a[u] / 2, cnt + 1);
}

signed main() {
    ios::sync_with_stdio(false);
    cin.tie(nullptr);
    cin >> n >> m;
    m *= 2;
    for (int i = 1; i <= n; ++i) {
        cin >> a[i];
        a[i] *= 2;
    }
    sort(a + 1, a + n + 1);
    N = n / 2;
    dfs1(1, 0, 0);
    dfs2(N + 1, 0, 0);
    if (ans == 2e9)
        ans = -1;
    cout << ans << '\n';
    return 0;
}
```

# [USACO09NOV] Lights G

## 题目背景

[English Edition](/paste/410oq6t5)

## 题目描述

给出一张 $n$ 个点 $m$ 条边的无向图，每个点的初始状态都为 $0$。  

你可以操作任意一个点，操作结束后该点以及所有与该点相邻的点的状态都会改变，由 $0$ 变成 $1$ 或由 $1$ 变成 $0$。  

你需要求出最少的操作次数，使得在所有操作完成之后所有 $n$ 个点的状态都是 $1$。

```cpp
#include <bits/stdc++.h>
#define rep(i, j, k) for (int i = (j); i <= (k); ++i)
#define per(i, j, k) for (int i = (j); i >= (k); --i)
#define ALL(v) (v).begin(), (v).end()
#define pb push_back
#define V vector
#define fi first
#define se second
#define int long long
using namespace std;
using pii = pair<int, int>;

unordered_map<int, int> key; // state -> cnt
int n, N, m, ans = 2e9;
V<int> e[66];

void dfs1(int u, int state, int cnt) {
	if (cnt >= ans) return;
	if (state == (1LL << n) - 1LL) {
		ans = min(ans, cnt);
		return;
	}
	if (u == N + 1) {
		if (key.count(state)) {
			key[state] = min(key[state], cnt);
		} else {
			key[state] = cnt;
		}
		return;
	}
	// 不选择
	dfs1(u + 1, state, cnt);
	// 选择
	int nxt = state;
	for (auto it : e[u]) {
		int now = (nxt >> (it - 1)) & 1;
		if (now) {
			nxt -= (1LL << (it - 1));
		} else {
			nxt += (1LL << (it - 1));
		}
	}
	dfs1(u + 1, nxt, cnt + 1);
}

void dfs2(int u, int state, int cnt) {
	if (cnt >= ans) return;
	if (state == (1LL << n) - 1) {
		ans = min(ans, cnt);
		return;
	}
	if (u == n + 1) {
		int need = (1LL << n) - 1 - state;
		if (key.count(need)) {
			ans = min(ans, key[need] + cnt);
		}
		return;
	}
	// 不选择
	dfs2(u + 1, state, cnt);
	// 选择
	int nxt = state;
	for (auto it : e[u]) {
		int now = (nxt >> (it - 1)) & 1;
		if (now) {
			nxt -= (1LL << (it - 1));
		} else {
			nxt += (1LL << (it - 1));
		}
	}
	dfs2(u + 1, nxt, cnt + 1);
}

signed main() {
	ios::sync_with_stdio(false);
	cin.tie(nullptr);
	cin >> n >> m;
	while (m--) {
		int a, b;
		cin >> a >> b;
		e[a].pb(b), e[b].pb(a);
	}
	for (int i = 1; i <= n; ++i) {
		e[i].pb(i);
	}
	N = n / 2;
	dfs1(1, 0, 0);
	dfs2(N + 1, 0, 0);
	cout << ans << '\n';
	return 0;
}
```





### eg.排书

问题描述见DFS—IDA*

```cpp
#include <iostream>
#include <cstring>
#include <algorithm>
#include <unordered_map>
#include <queue>

using namespace std;

const int N = 20;

int n, q[N];

int extend(queue<string> &q, unordered_map<string, int> &da, unordered_map<string,int> &db)
{
    int cnt = q.size();

    for(int i = 0; i < cnt; i ++)
    {
        auto t = q.front();
        q.pop();

        for(int len = 1; len <= n; len ++)
            for(int l = 0; l + len - 1 < n; l ++)
            {
                int r = l + len - 1;
                for(int k = r + 1; k < n; k ++)//只放后面，要不然会枚举两次
                {
                    string j;
                    for(int i = 0; i < l; i ++) j += t[i];
                    for(int i = r + 1; i <= k; i ++) j += t[i];
                    for(int i = l; i <= r; i ++) j += t[i];
                    for(int i = k + 1; i < n; i ++) j += t[i];
                    if(da.count(j)) continue;
                    if(db.count(j)) return da[t] + 1 + db[j];
                    da[j] = da[t] + 1;
                    q.push(j);
                }
            }
    }

    return -1;
}

int bfs()
{
    string st, ed;
    for(int i = 1; i <= n; i ++) ed += char('0' + i);
    for(int i = 0; i < n; i ++) st += char('0' + q[i]);

    if(ed == st) return 0;
    
    queue<string>qa;
    queue<string>qb;
    unordered_map<string, int>disa;
    unordered_map<string, int>disb;

    disa[st] = 0, disb[ed] = 0;

    qa.push(st), qb.push(ed);

    int cnt = 2;

    while(cnt --)//各更新两次，即四次
    {
        int t;
        t = extend(qa, disa, disb);
        if(t != -1) return t;
        t = extend(qb, disb, disa);
        if(t != -1) return t;
    }

    return 5;
}

int main()
{
    int T;
    scanf("%d", &T);
    while(T --)
    {
        scanf("%d", &n);
        for(int i = 0; i < n; i ++) scanf("%d", &q[i]);
        
        int cnt = 0;
        for(int i = 0; i + 1 < n; i ++)
            if(q[i + 1] != q[i] + 1) cnt ++;
        
        if((cnt + 2) / 3 >= 5) puts("5 or more");
        else
        {
            int step = bfs();

            if(step >= 5) puts("5 or more");
            else printf("%d\n", step);
        }
    }

    return 0;
}
```

## A-star

A*要保证在有解的情况下用,不然还是会把整张图搜完

A*只能保证终点的结果是最优的，其他点出去不一定是最小

采用优先队列存取起点到当前点的真实距离和一个**估计距离**来排序

当==**终点第一次出队**==时return

正确性满足的前提是：==预估距离小于等于真实距离==

### eg.八数码问题

选取的估价函数：当前状态与目标状态所有数字的曼哈顿距离之和

1.首先通过逆序对来判断是否有解，只有偶数的时候才有解

2.其次选用曼哈顿距离来当错预估的h函数

```cpp
#include <queue>
#include <cstring>
#include <unordered_map>
#include <iostream>
#include <algorithm>

#define x first
#define y second

using namespace std;

typedef pair<int, string> PIS;

char op[] = "urdl";
int dx[4] = {-1, 0, 1, 0}, dy[4] = {0, 1, 0, -1};
string str;
string ed = "12345678x";

int h(string  state)//估价函数
{
    int res = 0;
    for(int i = 0; i < state.size(); i ++)
        if(state[i] != 'x')
        {
            int t = state[i] - '1';//它应该处于的位置
            res += (abs(i / 3 - t / 3) + abs(i % 3 - t % 3));
        }
    return res;
}

string bfs()
{
    priority_queue<PIS, vector<PIS>, greater<PIS>> heap;
    unordered_map<string, int>dis;//dis里面一直存储的真实距离，而预估距离只有在堆中才用的上
    unordered_map<string, pair<char, string>>pre;
    heap.push({h(str), str});
    dis[str] = 0;
    
    while(heap.size())
    {
        auto t = heap.top();
        heap.pop();
        
        string state = t.y;
        if(state == ed) break;//A*在出队时判断是最终答案
        string tmp = state;//用一个tmp去保存先前开始的state
        
        int x, y;//定位到要处理的位置
        for(int i = 0; i < state.size(); i ++)
            if(state[i] == 'x')
            {
                x = i / 3;
                y = i % 3;
                break;
            }
        
        for(int i = 0; i < 4; i ++)
        {
            state = tmp;
            int a = x + dx[i], b = y + dy[i];
            if(a < 0 || a >= 3 || b < 0 || b >= 3) continue;
            swap(state[x * 3 + y], state[a * 3 + b]);
            if(dis.count(state) == 0 || dis[state] > dis[tmp] + 1)//如果没有被更新过或者有更好的办法
            {
                dis[state] = dis[tmp] + 1;
                heap.push({dis[state] + h(state), state});
                pre[state] = {op[i], tmp};
            }
        }
    }
    
    string res;
    
    while(ed != str)
    {
        auto it = pre[ed];
        res += it.x;
        ed = it.y;
    }
    reverse(res.begin(), res.end());
    
    return res;
}

int main()
{
    string seq;
    
    for(int i = 0; i < 9; i ++)
    {
        char c;
        cin >> c;
        str += c;
        if(c != 'x') seq += c;
    }

    //求逆序对
    int cnt = 0;
    for(int i = 0; i < 8; i ++)
        for(int j = i; j < 8; j ++)
            if(seq[i] > seq[j]) cnt ++;
    
    if(cnt & 1) puts("unsolvable");
    else cout << bfs() << endl;
    
    return 0;
}
```

### eg.第k短路径

给定一张 N 个点（编号 1,2…N），M 条边的有向图，求从起点 S 到终点 T 的第 K 短路的长度，路径允许重复经过点或边。

**第k次弹出的值就是第k短的路**

```cpp
#include <iostream>
#include <queue>
#include <cstring>
#include <algorithm>

#define x first
#define y second

using namespace std;

typedef pair<int, int> PII;
typedef pair<int, PII> PIII;

const int N = 1010, M = 20010;
const int INF = 0x3f3f3f3f;

int h[N], rh[N], e[M], w[M], ne[M], idx;
int n, m, S, T, K;
int dist[N];
bool st[N];

void add(int h[], int a, int b, int c)
{
    e[idx] = b, w[idx] = c, ne[idx] = h[a], h[a] = idx ++;
}

void dijkstra(int u)
{
    memset(dist, 0x3f, sizeof dist);
    dist[u] = 0;
    
    priority_queue<PII, vector<PII>, greater<PII>>heap;
    heap.push({0, u});
    
    while(heap.size())
    {
        auto t = heap.top();
        heap.pop();
        
        int ver = t.y;
        
        if(st[ver]) continue;
        st[ver] = true;
        
        for(int i = rh[ver]; ~i; i = ne[i])
        {
            int j = e[i];
        
            if(dist[j] > dist[ver] + w[i])
            {
                dist[j] = dist[ver] + w[i];
                heap.push({dist[j], j});
            }
        }
    }
}

int astar(int u)
{
    priority_queue<PIII, vector<PIII>, greater<PIII>>heap;//预估距离，{真实距离，标号}
    heap.push({dist[u], {0, u}});
    
    int cnt = 0;
    
    while(heap.size())
    {
        auto t = heap.top();
        heap.pop();
        
        int distance = t.y.x, ver = t.y.y;
        
        if(ver == T) cnt ++;
        if(cnt == K) return distance;
        
        for(int i = h[ver]; ~i; i = ne[i])
        {
            int j = e[i];
            //不管大还是小得全部加进去'
            if(dist[j] != INF)//有效跳跃点才加进去
                heap.push({dist[j] + distance + w[i], {distance + w[i], j}});
        }
    }
    
    return -1;
}

int main()
{
    scanf("%d%d", &n, &m);
    
    memset(h, -1, sizeof h);
    memset(rh, -1, sizeof rh);
    
    for(int i = 0; i < m; i ++)
    {
        int a, b, w;
        scanf("%d%d%d", &a, &b, &w);
        add(h, a, b, w);
        add(rh, b, a, w);
    }
    
    scanf("%d%d%d", &S, &T, &K);
    
    if(S == T) K ++;
    
    dijkstra(T); //求预估函数H
    
    printf("%d\n", astar(S));
    
    return 0;
}
```

