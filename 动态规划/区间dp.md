# 区间dp

细节：初始往往要考虑ii和i+1,i



## [蓝桥杯 2023 省 A] 更小的数

## 题目描述

![image](https://cdn.luogu.com.cn/upload/image_hosting/y1rd2iun.png)

小蓝有一个长度均为 $n$ 且仅由数字字符 $0 \sim 9$ 组成的字符串，下标从 $0$ 到 $n-1$，你可以将其视作是一个具有 $n$ 位的十进制数字 $num$，小蓝可以从 $num$ 中选出一段连续的子串并将子串进行反转，最多反转一次。小蓝想要将选出的子串进行反转后再放入原位置处得到的新的数字 $num_{new}$ 满足条件 $num_{new}<num$，请你帮他计算下一共有多少种不同的子串选择方案，只要两个子串在 $num$ 中的位置不完全相同我们就视作是不同的方案。

注意，我们允许前导零的存在，即数字的最高位可以是 $0$，这是合法的。

```cpp
#include<bits/stdc++.h>

using namespace std;

using LL = long long;

int dp[5555][5555];

int main() {
	ios::sync_with_stdio(false);
    cin.tie(nullptr);
    
    string s;
    cin >> s;
    
	const int n = s.size();
	s = ' ' + s;
	for (int len = 2; len <= n; ++len) {
		for (int i = 1; i + len - 1 <= n; ++i) {
			int j = i + len - 1;
			if (s[i] > s[j]) {
				dp[i][j] = 1;
			} else if (s[i] < s[j]) {
				dp[i][j] = 0;
			} else {
				dp[i][j] = dp[i + 1][j - 1];
			}
		}
	}
	int res = 0;
	for (int i = 1; i <= n; ++i) {
		for (int j = i + 1; j <= n; ++j) {
			if (dp[i][j]) {
				++res;
			}
		}
	}
	cout << res << '\n';
    
    return 0;
}   
```



# Maximum Sum of Products

## 题面翻译

给定两个长为 $n$ 的序列 $a$ 和 $b$。你可以对 $a$ 的一段区间翻转，也可以不翻转，要求翻转后 $a$ 与 $b$ 对应位置之积的和最大。即求下式的值最大：
$$\sum_{i=1}^na_i\times b_i$$

其中 $n\le 5000$，$a_i,b_i\le10^7$。

translated by @zc_Ethan

## 题目描述

You are given two integer arrays $ a $ and $ b $ of length $ n $ .

You can reverse at most one subarray (continuous subsegment) of the array $ a $ .

Your task is to reverse such a subarray that the sum $ \sum\limits_{i=1}^n a_i \cdot b_i $ is maximized.

```cpp
#include <bits/stdc++.h>

using namespace std;

using LL = long long;

int main() {
    ios::sync_with_stdio(false);
    cin.tie(nullptr);

    int n;
    cin >> n;

    LL res = 0;
    vector<LL> a(n), b(n);
    for (int i = 0; i < n; ++i) {
        cin >> a[i];
    }
    for (int i = 0; i < n; ++i) {
        cin >> b[i];
        res += a[i] * b[i];
    }
    vector<vector<LL>> f(n + 10, vector<LL>(n + 10, 0));
    for (int len = 2; len <= n; ++len) {
        for (int i = 0; i + len - 1 < n; ++i) {
            int j = i + len - 1;
            f[i][j] = f[i + 1][j - 1] + a[i] * b[j] + a[j] * b[i] -
                      a[i] * b[i] - a[j] * b[j];
        }
    }
    LL mx = 0;
    for (int i = 0; i < n; ++i) {
        for (int j = 0; j < n; ++j) {
            mx = max(mx, f[i][j]);
        }
    }
    cout << mx + res << '\n';

    return 0;
}
```

# [yLCPC2024] D. 排卡

## 题目背景

经过千辛万苦，扶苏终于到达了机厅。但是她很快发现机厅排起了大 b 队 ~~（不是省选的 B 队）~~。

舞萌玩家们在人多的时候经常采取排卡的方式决定谁下一个上机。因为人实在是太多了，他们在排卡的时候，便注意到了卡上的编号。

他们向扶苏提了一个问题，你能解决吗？

## 题目描述

扶苏有一个双端队列 $a$。这个队列与计算机科学中队列的概念类似，不同的是，这个队列既可以从队列头读取和弹出元素，也可以在队列尾部读取和弹出元素，因此被称为『双端队列』。

这个队列中有 $n$ 个数。扶苏将通过 $n$ 次操作构造一个长度为 $n$ 的序列 $b$，第 $i$（$1 \leq i \leq n$）次操作会可以进行如下两个过程之一：

1. 令 $b_i$ 为 $a$ 的队列头，并在 $a$ 的头部弹出一个元素。
2. 令 $b_i$ 为 $a$ 的队列尾，并在 $a$ 的尾部弹出一个元素。

我们定义一个数对 $(i, j)$ 的得分为：

$$\mathrm{score}(i,j) = i^j \bmod 998244353$$

即 $i$ 的 $j$ 次幂对 $998244353$ 取余数的结果。特别的，在本题中我们规定 $0^0 = 0$。

现在，扶苏想用最优的策略构造 $b$ 序列，最大化如下式子的值：

$$\sum_{i = 1}^{n - 1} \mathrm{score}(b_i, b_{i + 1})$$

即 $b$ 所有相邻两项按原顺序计算的得分之和。

注意，我们仅在计算一个数对的时候将得分对 $998,244,353$ 取模，在计算求和时不再将这个和取余。

## 输入格式

**本题单个测试点内有多组测试数据**。第一行是一个正整数，表示数据组数 $T$。对每组数据：

第一行是一个整数 $n$（$2 \leq n \leq 10^3$），表示序列的长度。  
第二行有 $n$ 个整数 $a_1, a_2, \dots a_n$（$0 \leq a_i < 998,244,353$），按从头到尾的顺序表示队列 $a$ 里每个数字的值。

数据保证单个测试点内 $n$ 的和不超过 $10^3$。

## 输出格式

对每组测试数据，输出一行一个整数表示答案。

## 样例 #1

### 样例输入 #1

```
2
5
5 3 1 4 2
6
6 5 1 4 2 3
```

### 样例输出 #1

```
1168
15655
```

```cpp
#include <bits/stdc++.h>
#define rep(i, j, k) for (int i = (j); i <= (k); ++i)
#define per(i, j, k) for (int i = (j); i >= (k); --i)
#define SZ(v) int((v).size())
#define ALL(v) (v).begin(), (v).end()
#define V vector
#define pb push_back
#define fi first
#define se second
using LL = long long;
using pii = std::pair<int, int>;
using namespace std;

const int mod = 998244353;

LL power(LL a, LL b) {
	if (a == 0 && b == 0) return 0LL;
	LL res = 1;
	for (; b; a = a * a % mod, b >>= 1) {
		if (b & 1) {
			res = res * a % mod;
		}
	}
	return res;
}

void solve() {
    int n;
    cin >> n;
    V<V<LL>> dp1(n + 10, vector<LL>(n + 10)), dp2(n + 10, vector<LL>(n + 10));
    V<LL> a(n + 1);
    for (int i = 1; i <= n; ++i) {
        cin >> a[i];
    }
    for (int len = 2; len <= n; ++len) {
        for (int l = 1; l + len - 1 <= n; ++l) {
            int r = l + len - 1;
            // dp1 : left first
            dp1[l][r] = power(a[l], a[l + 1]) + dp1[l + 1][r];
            dp1[l][r] = max(dp1[l][r], power(a[l], a[r]) + dp2[l + 1][r]);
            // dp2 : right first
            dp2[l][r] = power(a[r], a[l]) + dp1[l][r - 1];
            dp2[l][r] = max(dp2[l][r], power(a[r], a[r - 1]) + dp2[l][r - 1]);
        }
    }
    cout << max(dp1[1][n], dp2[1][n]) << '\n';
}

int main() {
    ios::sync_with_stdio(false);
    cin.tie(nullptr);
    int T;
    cin >> T;
    while (T--) solve();
    return 0;
}
```

