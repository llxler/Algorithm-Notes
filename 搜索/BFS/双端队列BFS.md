# **双端队列BFS**(处理0，1边权)

典型问题：**边权为0，1的模型**

解法：将边**权为0插入到队头，边权为1插入到队尾**，因为同样满足两段性和单调性，可以转化为DIJ算法，因此这样的做法是正确的。

**和DIJ的写法相像**

eg.电路维修

每个格点都是电线的接点，每个格子都包含一个电子元件。

电子元件的主要部分是一个可旋转的、连接一条对角线上的两个接点的短电缆。

在旋转之后，它就可以连接另一条对角线的两个接点。

电路板左上角的接点接入直流电源，右下角的接点接入飞行车的发动装置。

达达发现因为某些元件的方向不小心发生了改变，电路板可能处于断路的状态。

她准备通过计算，旋转最少数量的元件，使电源与发动装置通过若干条短缆相连。

不过，电路的规模实在是太大了，达达并不擅长编程，希望你能够帮她解决这个问题。

**注意**：只能走斜向的线段，水平和竖直线段不能走。

解法：==将通路看成0加入队头，闭路看成1加入队尾==，这个题目有个特性：偶数起点，则所有奇数点到不了

```cpp
#include <cstring>
#include <iostream>
#include <deque>

#define x first
#define y second

using namespace std;

typedef pair <int, int> PII;

const int N = 520;

char g[N][N];//一定要注意这里是int还是char
bool st[N][N];//这个题需要除了dis之外的st来判断是否还要计算
int dis[N][N];
int n, m;

int bfs()//一个点可能被更新多次，但是入队只能入一次
{
    int dx[4] = {-1, -1, 1, 1}, dy[4] = {-1, 1, 1, -1};
    int ix[4] = {-1, -1, 0, 0}, iy[4] = {-1, 0, 0, -1};
    char cs[] = "\\/\\/";//要有数组下标的话，就开五个和以上
    
    memset(st, false, sizeof st);
    memset(dis, 0x3f, sizeof dis);
    
    dis[0][0] = 0;
    deque <PII> q;
    q.push_back({0, 0});
    
    while(!q.empty())
    {
        auto t = q.front();
        q.pop_front();
        int x = t.x, y = t.y;

        if(x == n && y == m) return dis[x][y];//这道题只能在出队时候判断，这个时候完全更新了最小值
        if(st[x][y]) continue;
        st[x][y] = true;
        
        for(int i = 0; i < 4; i ++)
        {
            int a = x + dx[i], b = y + dy[i];
            int ca = x + ix[i], cb = y + iy[i];
            if(a < 0 || a > n || b < 0 || b > m) continue;//这里可以等于n是因为点相对于格多一个
            int w = (g[ca][cb] != cs[i]);
            int d = w + dis[x][y];
            if(d <= dis[a][b])
            {
                dis[a][b] = d;
                if(!w) q.push_front({a, b});
                else q.push_back({a, b});
            }
        }
    }
    return -1;    
}

int main()
{
    int T;
    cin >> T;
    while(T --)
    {
        cin >> n >> m;
        for(int i = 0; i < n; i ++) cin >> g[i];
        
        if((n + m) & 1) cout << "NO SOLUTION" << endl;
        else cout << bfs() << endl;
    }
    
    return 0;
}
```

细节注意：初始g数组一定要注意g的类型，多组数据初始化，要把队列初始化在bfs中，避免上组数据的影响

