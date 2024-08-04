# next_permutation使用法

```cpp
#include<bits/stdc++.h>

using namespace std;

void solve() {
    int a[6] = {0, 1, 2, 3, 4, 5};
    do {
        for (int i = 1; i <= 5; i++) cout << a[i] << ' ';
        cout << '\n';
    } while (next_permutation(a + 1, a + 5 + 1));
}

signed main() {
    cin.tie(nullptr)->ios::sync_with_stdio(false);
    int T = 1;
    //cin >> T;
    while (T--) {
        solve();
    }
    return 0;
}   
```

对应的结果就是：

```cpp
1 2 3 4 5 
1 2 3 5 4 
1 2 4 3 5 
1 2 4 5 3 
1 2 5 3 4 
1 2 5 4 3 
1 3 2 4 5 
1 3 2 5 4 
1 3 4 2 5 
1 3 4 5 2 
1 3 5 2 4 
1 3 5 4 2 
1 4 2 3 5 
1 4 2 5 3 
1 4 3 2 5 
1 4 3 5 2 
1 4 5 2 3 
1 4 5 3 2 
1 5 2 3 4 
1 5 2 4 3 
1 5 3 2 4 
1 5 3 4 2 
1 5 4 2 3 
1 5 4 3 2 
2 1 3 4 5 
2 1 3 5 4 
2 1 4 3 5 
2 1 4 5 3 
2 1 5 3 4 
2 1 5 4 3 
2 3 1 4 5 
2 3 1 5 4 
2 3 4 1 5 
2 3 4 5 1 
2 3 5 1 4 
2 3 5 4 1 
2 4 1 3 5 
2 4 1 5 3 
2 4 3 1 5 
2 4 3 5 1 
2 4 5 1 3 
2 4 5 3 1 
2 5 1 3 4 
2 5 1 4 3 
2 5 3 1 4 
2 5 3 4 1 
2 5 4 1 3 
2 5 4 3 1 
3 1 2 4 5 
3 1 2 5 4 
3 1 4 2 5 
3 1 4 5 2 
3 1 5 2 4 
3 1 5 4 2 
3 2 1 4 5 
3 2 1 5 4 
3 2 4 1 5 
3 2 4 5 1 
3 2 5 1 4 
3 2 5 4 1 
3 4 1 2 5 
3 4 1 5 2 
3 4 2 1 5 
3 4 2 5 1 
3 4 5 1 2 
3 4 5 2 1 
3 5 1 2 4 
3 5 1 4 2 
3 5 2 1 4 
3 5 2 4 1 
3 5 4 1 2 
3 5 4 2 1 
4 1 2 3 5 
4 1 2 5 3 
4 1 3 2 5 
4 1 3 5 2 
4 1 5 2 3 
4 1 5 3 2 
4 2 1 3 5 
4 2 1 5 3 
4 2 3 1 5 
4 2 3 5 1 
4 2 5 1 3 
4 2 5 3 1 
4 3 1 2 5 
4 3 1 5 2 
4 3 2 1 5 
4 3 2 5 1 
4 3 5 1 2 
4 3 5 2 1 
4 5 1 2 3 
4 5 1 3 2 
4 5 2 1 3 
4 5 2 3 1 
4 5 3 1 2 
4 5 3 2 1 
5 1 2 3 4 
5 1 2 4 3 
5 1 3 2 4 
5 1 3 4 2 
5 1 4 2 3 
5 1 4 3 2 
5 2 1 3 4 
5 2 1 4 3 
5 2 3 1 4 
5 2 3 4 1 
5 2 4 1 3 
5 2 4 3 1 
5 3 1 2 4 
5 3 1 4 2 
5 3 2 1 4 
5 3 2 4 1 
5 3 4 1 2 
5 3 4 2 1 
5 4 1 2 3 
5 4 1 3 2 
5 4 2 1 3 
5 4 2 3 1 
5 4 3 1 2 
5 4 3 2 1
```

## eg. abc 332_d Swapping Puzzle

https://atcoder.jp/contests/abc332/tasks/abc332_d

ACcode : https://atcoder.jp/contests/abc332/submissions/50085449

```cpp
#include<bits/stdc++.h>

using namespace std;

int n, m, res = 2e9;
int p[] = {0, 1, 2, 3, 4, 5}, q[] = {0, 1, 2, 3, 4, 5};// 用来枚举排列的辅助数组
int a[10][10], b[10][10];

bool check(int x[][10], int b[][10]) {
    for (int i = 1; i <= n; i++) 
        for (int j = 1; j <= m; j++) 
            if (x[p[i]][q[j]] != b[i][j]) return false;
    return true;
}

signed main() {
    cin.tie(nullptr)->ios::sync_with_stdio(false);
    cin >> n >> m;
    for (int i = 1; i <= n; i++)
        for (int j = 1; j <= m; j++)
            cin >> a[i][j];
    for (int i = 1; i <= n; i++)
        for (int j = 1; j <= m; j++)
            cin >> b[i][j];
    do {
        do {
            if (!check(a, b)) continue;// 失败则continue
			// 求出各自逆元 就是交换次数
            int cnt = 0;
            for (int i = 1; i <= n; i++) 
                for (int j = i + 1; j <= n; j++)
                    if (p[i] > p[j]) ++cnt;
            for (int i = 1; i <= m; i++) 
                for (int j = i + 1; j <= m; j++)
                    if (q[i] > q[j]) ++cnt;
            res = min(res, cnt);
        } while (next_permutation(q + 1, q + m + 1));
    } while (next_permutation(p + 1, p + n + 1));
    if (res == 2e9) res = -1;
    cout << res << '\n';
    return 0;
}
```

