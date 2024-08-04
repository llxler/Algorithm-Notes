soyorin有一个长度为 nnn 的数组，她每次操作可以选择一个不超过 nnn 的偶数 kkk ，使得 ai:=ai+i (1≤i≤k）

soyorin想知道她是否可以通过任意次操作将数组变成非降序。

```cpp
#include <bits/stdc++.h>

using namespace std;

using LL = long long;

void solve() {
    int n;
    cin >> n;
    vector<LL> a(n + 1);
    for (int i = 1; i <= n; i++) cin >> a[i];
    LL cnt = 0;
    if (n % 2 == 0) goto yes;
    for (int i = n - 1; i >= 2; i -= 2) {
    	a[i] += cnt * i, a[i - 1] += cnt * (i - 1);
    	if (a[i] > a[i + 1]) goto no;
    	LL can = (a[i + 1] - a[i]) / i;
    	a[i] += can * i, a[i - 1] += can * (i - 1);
    	cnt += can;
    }
    if (is_sorted(a.begin() + 1, a.end())) goto yes;
    goto no;
    if (0) yes: cout << "YES\n";
    if (0) no: cout << "NO\n";
}

signed main() {
    cin.tie(nullptr)->ios::sync_with_stdio(false);
    int t = 1;
    cin >> t;
    while (t--) solve();
    return 0;
}
```

