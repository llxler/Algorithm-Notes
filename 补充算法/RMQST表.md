# RMQ \ST

## 模板

```cpp
int st_q(int l, int r) {
	int j = log2(r - l + 1);
	return max(dp[l][j], dp[r - (1 << j) + 1][j]);
}

for (int i = 1; i <= n; ++i) {
		dp[i][0] = a[i];
	}
for (int i = 1; i <= log2(n); ++i) {
    for (int l = 1; l + (1 << i) - 1 <= n; ++l) {
        dp[l][i] = max(dp[l][i - 1], dp[l + (1 << (i - 1))][i - 1]);
    }
}
```

**细节**：

- 若当前为左端点为i，区间为len，那么区间右端点为i + len - 1
- 若当前右端点为i，区间为len，那么区间左端点为i - len + 1
- 都是往自己对应的那个方向缩个1！！！



解决区间最值问题（利用倍增思想）

```cpp
#include <bits/stdc++.h>

const int N = 1e5 + 10;

int n, m;
int a[N], f[N][20];

int query(int l, int r) {
    int j = (int) log2(r - l + 1);
    return std::max(f[l][j], f[r - (1 << j) + 1][j]);
}

int main() {
    std::cin.tie(nullptr)->std::ios::sync_with_stdio(false);
    std::cin >> n >> m;
    for (int i = 1; i <= n; i++) std::cin >> a[i];
    for (int i = 1; i <= n; i++) f[i][0] = a[i];
    for (int j = 1; j <= (int) log2(n); j++) 
        for (int i = 1; i + (1 << j) - 1 <= n; i++) 
            f[i][j] = std::max(f[i][j - 1], f[i + (1 << (j - 1))][j - 1]);
    while (m--) {
        int x, y;
        std::cin >> x >> y;
        std::cout << query(x, y) << '\n';
    }
    return 0;
}
```

# 例题

# [蓝桥杯 2022 省 A] 选数异或

## 题目描述

给定一个长度为 $n$ 的数列 $A_{1}, A_{2}, \cdots, A_{n}$ 和一个非负整数 $x$, 给定 $m$ 次查询, 每次询问能否从某个区间 $[l, r]$ 中选择两个数使得他们的异或等于 $x$ 。

## 输入格式

输入的第一行包含三个整数 $n, m, x$ 。

第二行包含 $n$ 个整数 $A_{1}, A_{2}, \cdots, A_{n}$。

接下来 $m$ 行，每行包含两个整数 $l_{i}, r_{i}$ 表示询问区间 $\left[l_{i}, r_{i}\right]$ 。

## 输出格式

对于每个询问, 如果该区间内存在两个数的异或为 $x$ 则输出 `yes`, 否则输出 `no`。

## 样例 #1

### 样例输入 #1

```
4 4 1
1 2 3 4
1 4
1 2
2 3
3 3
```

### 样例输出 #1

```
yes
no
yes
no
```

## 提示

**【样例说明】**

显然整个数列中只有 2,3 的异或为 1 。

**【评测用例规模与约定】**

对于 $20 \%$ 的评测用例, $1 \leq n, m \leq 100$;

对于 $40 \%$ 的评测用例, $1 \leq n, m \leq 1000$;

对于所有评测用例, $1 \leq n, m \leq 10^5,0 \leq x<2^{20}, 1 \leq l_{i} \leq r_{i} \leq n$ ， $0 \leq A_{i}<2^{20}$ 。

蓝桥杯 2022 省赛 A 组 D 题。

```cpp
#include <bits/stdc++.h>

using namespace std;

using LL = long long;

const int N = 1e5 + 10;

LL n, q, x;
LL a[N];
LL dp[N][30];
map<LL, LL> st;

LL query(int l, int r) {
	int len = log2(r - l + 1);
	return max(dp[l][len], dp[r - (1 << len) + 1][len]);
}

int main() {
    ios::sync_with_stdio(false);
    cin.tie(nullptr);
	cin >> n >> q >> x;
	for (int i = 1; i <= n; ++i) {
		cin >> a[i];
		LL goal = a[i] ^ x;
		if (st.count(goal)) dp[i][0] = st[goal];
		else dp[i][0] = -1;
		st[a[i]] = i;
	}
	for (int i = 1; i <= 25; ++i) {
		for (int j = 1; j + (1LL << i) - 1 <= n; ++j) {
			dp[j][i] = max(dp[j][i - 1], dp[j + (1 << (i - 1))][i - 1]);
		}
	}
	while (q--) {
		int l, r;
		cin >> l >> r;
		if (query(l, r) >= l) {
			cout << "yes\n";
		} else {
			cout << "no\n";
		}
	}
    return 0;
}
```

## 小笨的回文询问

小苯有一个长度为 nnn 的数组 aaa，他定义一个数组是好数组，当且仅当该数组是一个回文数组，且长度**严格大于** 222。

 他现在进行了 qqq 次询问，每次询问都给出一段区间 [l,r][l, r][l,r]，他想知道 aaa 在这一段区间中是否存在一个**子序列**是一个好数组，请你帮帮他吧。

**解法**：维护l，r中对应左端点的最大值，看这个最大值是否大于l

```cpp
#include <bits/stdc++.h>
#define ALL(v) (v).begin(), (v).end()
#define pb push_back
#define V vector
#define fi first
#define se second
#define ls u << 1
#define rs u << 1 | 1
using namespace std;
using pii = pair<int, int>;
using LL = long long;

const int N = 1e5 + 10;

int n, q, a[N], last[N];
int dp[N][20];

int st_q(int l, int r) {
	int j = log2(r - l + 1);
	return max(dp[l][j], dp[r - (1 << j) + 1][j]);
}

int main() {
	ios::sync_with_stdio(false);
	cin.tie(nullptr);
	cin >> n >> q;
	map<int, int> st;
	for (int i = 1; i <= n; ++i) {
		cin >> a[i];
		if (st.count(a[i])) {
			last[i] = st[a[i]];
		}
		st[a[i]] = i;
	}
	for (int i = n; i; --i) {
		if (last[i] == i - 1) {
			last[i] = last[last[i]];
		}
	}
	for (int i = 1; i <= n; ++i) {
		dp[i][0] = last[i];
	}
	for (int i = 1; i <= log2(n); ++i) {
		for (int l = 1; l + (1 << i) - 1 <= n; ++l) {
			dp[l][i] = max(dp[l][i - 1], dp[l + (1 << (i - 1))][i - 1]);
		}
	}
	while (q--) {
		int l, r;
		cin >> l >> r;
		int d = st_q(l, r);
		if (d >= l) cout << "YES\n";
		else cout << "NO\n";
	}
	return 0;
}
```

