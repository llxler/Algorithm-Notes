能想到枚举当前的数的总和就已经非常困难

运用记忆化搜索来完成

```cpp
#include <bits/stdc++.h>
#define int long long
using namespace std;

int f[20][150][150], mod;
int num[20];

int dfs(int pos, int s, int sum, bool limit) {
    int ans = 0;
    if (s < 0) return 0;// 没有贡献
    if (pos == 0) return !s && !sum;
    if (!limit && ~f[pos][s][sum]) return f[pos][s][sum];
    int up = (limit?num[pos]:9);
    for (int i = 0; i <= up; i++) 
        ans += dfs(pos - 1, s - i, (sum * 10 + i) % mod, limit && i == up);
    if (!limit) f[pos][s][sum] = ans;
    return ans;
}

int calc(int x) {
    int len = 0, res = 0;
    while (x) num[++len] = x % 10, x /= 10;
    for (int i = 1; i <= 140; i++) {
        memset(f, -1, sizeof f);
        res += dfs(len, mod = i, 0, true);
    }
    return res;
}

signed main() {
    cin.tie(nullptr)->ios::sync_with_stdio(false);
    int n;
    cin >> n;
    cout << calc(n) << '\n';
    return 0;
}
```

