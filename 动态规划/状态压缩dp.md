# eg.牛客竞赛 周赛32 F小红的矩阵（三进制类型）

![image-20240211224054158](C:\Users\24996\AppData\Roaming\Typora\typora-user-images\image-20240211224054158.png)

```cpp
#include<bits/stdc++.h>

using namespace std;

int p3[] = {1, 3, 9, 27, 81};
char g[5][1010];
int f[1010][100];
int n, m;
map<char, int> to;
int b[5], c[5]; //b是now, c是pre

bool check(int x) {
	for (int i = 1; i <= n; i++) {
		b[i] = x % 3;
		x /= 3;
	}
	for (int i = 2; i <= n; i++) {
		if (b[i] == b[i - 1]) return false;
	}
	return true;
}

bool check(int x, int y) {
	for (int i = 1; i <= n; i++) {
		c[i] = y % 3;
		y /= 3;
	}
	for (int i = 1; i <= n; i++) {
		if (b[i] == c[i]) return false;
	}
	return true;
}

signed main() {
    cin.tie(nullptr)->ios::sync_with_stdio(false);
    to['r'] = 0, to['e'] = 1, to['d'] = 2;
    cin >> n >> m;
    for (int i = 1; i <= n; i++)
    	for (int j = 1; j <= m; j++)
    		cin >> g[i][j];
    memset(f, 0x3f, sizeof f);
    for (int i = 1; i <= m; i++) {
    	if (i == 1) {
    		for (int state = 0; state < p3[n]; state++) {
    			int cnt = 0;
    			if (check(state)) {
					for (int j = 1; j <= n; j++) {
						if (to[g[j][i]] != b[j]) ++cnt;
					}
					f[i][state] = min(f[i][state], cnt);
    			}	
    		}
    	} else {
    		for (int state = 0; state < p3[n]; state++) {
    			if (check(state)) {
					for (int pre = 0; pre < p3[n]; pre++) {
						int cnt = 0;
						if (check(state, pre)) {
							for (int j = 1; j <= n; j++) {
								if (to[g[j][i]] != b[j]) ++cnt;
							}
							f[i][state] = min(f[i][state], f[i - 1][pre] + cnt);
						}
					}
    			}	
    		}
    	}
    }
	cout << *min_element(f[m], f[m] + 88) << '\n';
    return 0;
}   
```



# eg. 铺瓷砖（经典2*n方格）

问题描述：我们要在一个 2×n 大小的方格矩阵上铺瓷砖。

每个方格要么是空的（用 `0` 表示），要么是已经铺上瓷砖的（用 `1` 表示）。

现在，给定你无限多个占据三个方格的 L 形瓷砖，瓷砖在铺放时可以旋转 90,180,27090,180,270 度，即它可以有以下四种形态：

![QQ截图20240124095904.png](https://cdn.acwing.com/media/article/image/2024/01/24/19_29bc7279ba-QQ%E6%88%AA%E5%9B%BE20240124095904.png) ![2.png](https://cdn.acwing.com/media/article/image/2024/01/24/19_4a92f282ba-2.png) ![3.png](https://cdn.acwing.com/media/article/image/2024/01/24/19_4cb8a944ba-3.png) ![4.png](https://cdn.acwing.com/media/article/image/2024/01/24/19_4ed0ac43ba-4.png)

瓷砖之间不能有任何重叠部分。

请你计算，最多可以在给定方格矩阵上同时铺放多少块这种 L 形瓷砖。

fi, j -> j为0表示没有方格，为12表示上/下凸出一块，然后左边是完整的，为3表示向左边指的类型

```cpp
// 状态压缩写法
#include <bits/stdc++.h> 

using namespace std;

int n;
int f[111][11], state[111];
string a, b;

int main() {
    cin >> a >> b;
    n = a.size();
    a = ' ' + a, b = ' ' + b;
    for (int i = 1; i <= n; i++) {
        if (a[i] - '0' == 1) state[i] |= 1; 
        if (b[i] - '0' == 1) state[i] |= 2;
    }
    state[0] = state[n + 1] = 3;
    for (int i = 1; i <= n; i++)
        for (int j = 0; j < 4; j++) 
            for (int k = 0; k < 4; k++) {
                if (j & state[i]) continue;
                if (j == 0) f[i][j] = max(f[i][j], f[i - 1][k]);
                else if (j == 3) {
                    if ((state[i - 1] & 1) == 0 && (k & 1) == 0) {
                        f[i][j] = max(f[i][j], f[i - 1][k] + 1);
                    }
                    if ((state[i - 1] & 2) == 0 && (k & 2) == 0) {
                        f[i][j] = max(f[i][j], f[i - 1][k] + 1);
                    }
                } else {
                    if ((state[i - 1] & 3) == 0 && (k & 3) == 0) {
                        f[i][j] = max(f[i][j], f[i - 1][k] + 1);
                    }
                }
            }
    cout << *max_element(f[n], f[n] + 11) << '\n';
    return 0;
}

// 贪心写法
#include<bits/stdc++.h>

using namespace std;

int n = 2, m;
string a, b;
int g[3][110];

signed main() {
    cin.tie(nullptr)->ios::sync_with_stdio(false);
    cin >> a >> b;
    m = a.size();
    for (int i = 1; i <= m; i++) g[1][i] = a[i - 1] - '0';
    for (int i = 1; i <= m; i++) g[2][i] = b[i - 1] - '0';
    int res = 0;
    g[2][0] = g[1][0] = g[1][m + 1] = g[2][m + 1] = 1;
    for (int j = 1; j <= m; j++) 
        if (g[1][j] == g[2][j] && g[1][j] == 0) {
            if (g[1][j - 1] == 0) {
                res++;
                g[1][j - 1] = 1, g[1][j] = 1, g[2][j] = 1;
            } else if (g[2][j - 1] == 0) {
                res++;
                g[2][j - 1] = 1, g[1][j] = 1, g[2][j] = 1;
            } else if (g[2][j + 1] == 0) {
                res++;
                g[2][j + 1] = 1, g[1][j] = 1, g[2][j] = 1;
            } else if (g[1][j + 1] == 0) {
                res++;
                g[1][j + 1] = 1, g[1][j] = 1, g[2][j] = 1;
            }
        }
    cout << res << '\n';
    return 0;
}   
```

# TSP问题 

**先枚举状态再枚举点**

而在棋盘问题中先枚举行再枚举状态

```cpp
// TSP
#include<bits/stdc++.h>
#define ALL(v) (v).begin(), (v).end()
#define pb push_back
#define V vector
#define fi first
#define se second
using namespace std;
using pii = pair<int, int>;
using LL = long long;

int n;
int g[22][22];
int dp[1 << 20][22];

int main() {
    ios::sync_with_stdio(false);
    cin.tie(nullptr);
    // freopen("in.in", "r", stdin);
    // freopen("out.out", "w", stdout);
    cin >> n;
    int m = (1 << n) - 1;
    for (int i = 1; i <= n; ++i) {
        for (int j = 1; j <= n; ++j) {
            cin >> g[i][j];
        }
    }
    memset(dp, 0x3f, sizeof dp);
    dp[1][1] = 0;
    for (int i = 1; i <= m; ++i) {
        for (int j = 1; j <= n; ++j) {
            if (dp[i][j] == 0x3f3f3f3f) continue;
            for (int k = 1; k <= n; ++k) {
                if (k == j || (i >> (k - 1)) & 1) continue;
                dp[i | (1 << (k - 1))][k] = min(dp[i | (1 << (k - 1))][k], dp[i][j] + g[j][k]);
            }
        }
    }
    cout << dp[m][n] << '\n';
    return 0;
}
```

例题2

```cpp
#include<bits/stdc++.h>
#define ALL(v) (v).begin(), (v).end()
#define pb push_back
#define V vector
#define fi first
#define se second
using namespace std;
using pii = pair<int, int>;
using LL = long long;

const int N = 20, M = 1 << N;

struct Ver {
    int x, y, z, w;
} ver[N];

double f[M][N];
int n, w[N];

double dist(Ver a, Ver b) {
    return sqrt(
        1. * (a.x - b.x) * (a.x - b.x) +
        1. * (a.y - b.y) * (a.y - b.y) +
        1. * (a.z - b.z) * (a.z - b.z)
    );
}


int main() {
    ios::sync_with_stdio(false);
    cin.tie(nullptr);
    cout << fixed << setprecision(2);

    cin >> n;
    int m = (1 << n) - 1;
    for (int i = 0; i < n; ++i) {
        cin >> ver[i].x >> ver[i].y >> ver[i].z >> w[i];
    }
    
    for (int i = 0; i < M; ++i) 
        for (int j = 0; j < N; ++j) 
            f[i][j] = 20000000.0;
    for (int i = 0; i < n; ++i)
        f[1 << i][i] = 0;
	
    for (int msk = 1; msk <= m; ++msk)
        for (int i = 0; i < n; ++i)
            if (msk >> i & 1)
                for (int j = 0; j < n; ++j) 
                    if (msk >> j & 1 && (i != j)) {
                        f[msk][i] = min(f[msk][i],
                        f[msk - (1 << i)][j] + w[i] * dist(ver[i], ver[j]));
                    }

	double mn = 2e9;
    for (int i = 0; i < n; ++i) {
        mn = min(f[m][i], mn);
    }
    cout << mn;

    return 0;
}
```

