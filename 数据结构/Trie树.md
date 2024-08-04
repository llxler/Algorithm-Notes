# Trie树

## 板子

```cpp
#include<bits/stdc++.h>

using namespace std;

const int N = 3e6 + 10;

int m, q;
char str[N];
char query[N];
int p[N][65], idx, cnt[N];

int getnum(char x) {
    if(x >= 'A' && x <= 'Z')
        return x - 'A';
    else if(x >= 'a' && x <= 'z')
        return x - 'a' + 26;
    else
        return x - '0' + 52;
} 

void insert(char s[]) {
    int son = 0;
    for (int i = 0; s[i]; i++) {
        int u = getnum(s[i]);
        if (!p[son][u]) p[son][u] = ++idx;
        son = p[son][u];
        cnt[son]++;
    }
}

int qry(char s[]) {
    int son = 0;
    for (int i = 0; s[i]; i++) {
        int u = getnum(s[i]);
        if (!p[son][u]) return 0;
        son = p[son][u];
    }
    return cnt[son];
}

void solve() {
    cin >> m >> q;
    for (int i = 0; i <= idx; i++)
        for (int j = 0; j < 65; j++)
            p[i][j] = 0;
    for (int i = 0; i <= idx; i++) cnt[i] = 0;
    idx = 0;
    while (m--) {
        cin >> str;
        insert(str);
    }
    while (q--) {
        cin >> query;
        cout << qry(query) << '\n';
    }
}

signed main() {
    cin.tie(nullptr)->ios::sync_with_stdio(false);
    int T = 1;
    cin >> T;
    while (T--) {
        solve();
    }
    return 0;
}   
```

## 应用：

01系列

## 最大异或和

问题描述：在给定的 N 个整数 A1，A2……A 中选出两个进行 xor（异或）运算，得到的结果最大是多少？

```cpp
#include<bits/stdc++.h>
#define int long long

using namespace std;

const int N = 1e5 + 10;

int a[N], n;
int p[N * 31][2], idx;

void insert(int x) {
    int son = 0;
    for (int i = 30; i >= 0; i--) {
        int val = (x >> i) & 1;
        if (!p[son][val]) p[son][val] = ++idx;
        son = p[son][val];
    }
}

int query(int x) {
    int son = 0, res = 0;
    for (int i = 30; i >= 0; i--) {
        int val = (x >> i) & 1;
        if (p[son][val ^ 1]) {
            son = p[son][val ^ 1];
            res += (1 << i);
        } else {
            son = p[son][val];
        }
    }
    return res;
}

signed main() {
    cin.tie(nullptr)->ios::sync_with_stdio(false);
    cin >> n;
    for (int i = 1; i <= n; i++) {
        cin >> a[i];
        insert(a[i]);
    }
    int ans = -1;
    for (int i = 1; i <= n; i++) {
        ans = max(ans, query(a[i]));
    }
    cout << ans << '\n';
    return 0;
} 
```



## eg. Tokitsukaze and Min-Max XOR(牛客集训营第2场)

带权值的Trie树（存2^j的逆元）

细节超多

```cpp
#include<bits/stdc++.h>

using namespace std;

using LL = long long;

const int N = 2e5 + 10, mod = 1e9 + 7;

LL p[N * 15][2], w[N * 15], idx, a[N];
int n, k;

LL qmi(LL a, LL b) {
    LL res = 1;
    for (; b; b >>= 1, a = a * a % mod) 
        if (b & 1) res = res * a % mod;
    return res;
}

void upd(int x, int id) {
    LL son = 0, inv = qmi(qmi(2, id), mod - 2);
    for (int i = 30; i >= 0; i--) {
        int val = (x >> i) & 1;
        if (!p[son][val]) p[son][val] = ++idx;
        son = p[son][val];
        w[son] = (w[son] + inv) % mod;
    }
}

LL query(int x) {
    LL son = 0, res = 0;
    for (int i = 30; i >= 0; i--) {
        int e = ((x >> i) & 1) ^ ((k >> i) & 1);
        if ((k >> i) & 1) res = (res + w[p[son][1 - e]]) % mod;
        if (!p[son][e]) return res;
        son = p[son][e];
    }
    res = (res + w[son]) % mod;
    return res;
}

void solve() {
    for (int i = 0; i <= idx; i++) w[i] = p[i][0] = p[i][1] = 0;
    idx = 0;
    cin >> n >> k;
    for (int i = 1; i <= n; i++) cin >> a[i];
    sort(a + 1, a + n + 1);
    LL res = 0;
    for (int i = 1; i <= n; i++) {
        res = (res + qmi(2, i - 1) * query(a[i]) % mod + 1) % mod;
        upd(a[i], i);
    }
    cout << res << '\n';
}

signed main() {
    cin.tie(nullptr)->ios::sync_with_stdio(false);
    int T = 1;
    cin >> T;
    while (T--) {
        solve();
    }
    return 0;
} 
```

