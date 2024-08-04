```cpp
#include <bits/stdc++.h>
using namespace std;

const int N = 15;

int f[N][N];

void init() {
    for (int i = 0; i <= 9; i++) f[1][i] = 1;
    for (int i = 2; i < N; i++)// 位数
        for (int j = 0; j <= 9; j++)// 填的数字
            for (int k = 0; k <= 9; k++)// 决策 
                if (abs(k - j) >= 2)
                    f[i][j] += f[i - 1][k];         
}

int dp(int x) {
    if (!x) return 0;
    int a[N], cnt = 0;
    while (x) a[++cnt] = x % 10, x /= 10;
    int res = 0, last = -2;
    for (int i = cnt; i; i--) {// 答案是cnt位的
        int now = a[i];
        for (int j = (i == cnt); j < now; j++) 
            if (abs(j - last) >= 2) res += f[i][j];
        if (abs(now - last) < 2) break;
        last = now;
        if (i == 1) res++;
    }
    for (int i = 1; i < cnt; i++) 
        for (int j = 1;j <= 9; j++) 
            res += f[i][j];
    return res;
}

int main() {
    cin.tie(nullptr)->ios::sync_with_stdio(false);
    init();
    int a, b;
    cin >> a >> b;
    cout << dp(b) - dp(a - 1) << '\n';
    return 0;
}
```



## 记忆化搜索写法

```cpp
#include <bits/stdc++.h>
using namespace std;

const int N = 15;

int f[N][N];
int num[N], cnt;

int dfs(int pos, int last, bool lead, bool limit) {
    int ans = 0;
    if (pos == 0) return 1;
    if (!lead && !limit && ~f[pos][last]) return f[pos][last];
    int up = (limit?num[pos]:9);
    for (int i = 0; i <= up; i++) {
        if (abs(i - last) < 2) continue;
        if (lead && i == 0) ans += dfs(pos - 1, -2, true, limit && i == up);
        else ans += dfs(pos - 1, i, false, limit && i == up);
    }
    if (!limit && !lead) f[pos][last] = ans;
    return ans;
}

int calc(int x) {
    memset(f, -1, sizeof f);
    cnt = 0;
    while (x) num[++cnt] = x % 10, x /= 10;
    return dfs(cnt, -2, true, true);
}

int main() {
    cin.tie(nullptr)->ios::sync_with_stdio(false);
    int l, r;
    cin >> l >> r;
    cout << calc(r) - calc(l - 1) << '\n';
    return 0;
}
```

