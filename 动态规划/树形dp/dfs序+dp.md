# [ABC348E] Minimize Sum of Distances

## 题目描述

[problemUrl]: https://atcoder.jp/contests/abc348/tasks/abc348_e

$ N $ 頂点からなる木が与えられます。頂点は $ 1 $ から $ N $ までの番号がついており、 $ i $ 番目の辺は頂点 $ A_i,\ B_i $ を結んでいます。

長さ $ N $ の正整数列 $ C\ =\ (C_1,\ C_2,\ \ldots\ ,C_N) $ が与えられます。$ d(a,\ b) $ を頂点 $ a,\ b $ の間にある辺の数とし、 $ x\ =\ 1,\ 2,\ \ldots\ ,N $ に対して $ \displaystyle\ f(x)\ =\ \sum_{i=1}^{N}\ (C_i\ \times\ d(x,\ i)) $ とします。$ \displaystyle\ \min_{1\ \leq\ v\ \leq\ N}\ f(v) $ を求めてください。

## 输入格式

入力は以下の形式で標準入力から与えられる。

> $ N $ $ A_1 $ $ B_1 $ $ A_2 $ $ B_2 $ $ \vdots $ $ A_{N\ -\ 1} $ $ B_{N\ -\ 1} $ $ C_1 $ $ C_2 $ $ \cdots $ $ C_N $

## 输出格式

答えを一行に出力せよ。

## 样例 #1

### 样例输入 #1

```
4
1 2
1 3
2 4
1 1 1 2
```

### 样例输出 #1

```
5
```

## 样例 #2

### 样例输入 #2

```
2
2 1
1 1000000000
```

### 样例输出 #2

```
1
```

## 样例 #3

### 样例输入 #3

```
7
7 3
2 5
2 4
3 1
3 6
2 1
2 7 6 9 3 4 6
```

### 样例输出 #3

```
56
```

## 提示

### 制約

- $ 1\ \leq\ N\ \leq\ 10^5 $
- $ 1\ \leq\ A_i,\ B_i\ \leq\ N $
- 与えられるグラフは木である。
- $ 1\ \leq\ C_i\ \leq\ 10^9 $

### Sample Explanation 1

例として、 $ f(1) $ を求めることを考えます。$ d(1,\ 1)\ =\ 0,\ d(1,\ 2)\ =\ 1,\ d(1,\ 3)\ =\ 1,\ d(1,\ 4)\ =\ 2 $ です。 よって、 $ f(1)\ =\ 0\ \times\ 1\ +\ 1\ \times\ 1\ +\ 1\ \times\ 1\ +\ 2\ \times\ 2\ =\ 6 $ となります。 同様に、 $ f(2)\ =\ 5,\ f(3)\ =\ 9,\ f(4)\ =\ 6 $ です。$ f(2) $ が最小なので `5` を出力します。

### Sample Explanation 2

$ f(2)\ =\ 1 $ で、これが最小です。

```cpp
#include <bits/stdc++.h>

using namespace std;

using LL = long long;

const int N = 1e5 + 5;

int n, o;
LL a[N], s[N], dp[N], sum;
vector<int> e[N];
int L[N], R[N], dep[N];

void dfs(int u, int fa, int d) {
	L[u] = ++o;
	s[o] = a[u];
	dep[u] = d;
	for (auto ver : e[u]) 
		if (ver != fa) {
			dfs(ver, u, d + 1);
		}
	R[u] = o;
}

void _dfs(int u, int fa) {
	for (auto ver : e[u])
		if (ver != fa) {
			int l = L[ver], r = R[ver];
			LL sub = s[r] - s[l - 1];
			dp[ver] = dp[u] - sub + (sum - sub);
			_dfs(ver, u);
		}
}

int main() {
	ios::sync_with_stdio(false);
	cin.tie(nullptr);
	cin >> n;
	for (int i = 0; i < n - 1; ++i) {
		int a, b;
		cin >> a >> b;
		e[a].push_back(b);
		e[b].push_back(a);
	}
	for (int i = 1; i <= n; ++i) {
		cin >> a[i];
		sum += a[i];
	}
	dfs(1, -1, 0);
	for (int i = 1; i <= n; ++i) {
		s[i] += s[i - 1];
	}
	// 算出dp[1]
	for (int i = 2; i <= n; ++i) {
		dp[1] += dep[i] * a[i];
	}
	// 求dp 2 - n
	_dfs(1, -1);
	cout << *min_element(dp + 1, dp + 1 + n) << '\n';
	return 0;
} 
```

