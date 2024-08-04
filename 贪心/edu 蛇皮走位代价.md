# Minimum Grid Path

## 题面翻译

n✖n的矩阵
让你从（0，0）走到（n,n)
每次只能向上走或者向右走且步长至少为1，而且走完之后必须换方向
最多换n-1次方向，也就是走n段路
每段都都有一个权值，这段路的计算代价为这次走的长度×这段路的权值（c[i])
，求最小的代价

## 题目描述

Let's say you are standing on the $ XY $ -plane at point $ (0, 0) $ and you want to reach point $ (n, n) $ .

You can move only in two directions:

- to the right, i. e. horizontally and in the direction that increase your $ x $ coordinate,
- or up, i. e. vertically and in the direction that increase your $ y $ coordinate.

In other words, your path will have the following structure:

- initially, you choose to go to the right or up;
- then you go some positive integer distance in the chosen direction (distances can be chosen independently);
- after that you change your direction (from right to up, or from up to right) and repeat the process.

You don't like to change your direction too much, so you will make no more than $ n - 1 $ direction changes.

As a result, your path will be a polygonal chain from $ (0, 0) $ to $ (n, n) $ , consisting of at most $ n $ line segments where each segment has positive integer length and vertical and horizontal segments alternate.

Not all paths are equal. You have $ n $ integers $ c_1, c_2, \dots, c_n $ where $ c_i $ is the cost of the $ i $ -th segment.

Using these costs we can define the cost of the path as the sum of lengths of the segments of this path multiplied by their cost, i. e. if the path consists of $ k $ segments ( $ k \le n $ ), then the cost of the path is equal to $ \sum\limits_{i=1}^{k}{c_i \cdot length_i} $ (segments are numbered from $ 1 $ to $ k $ in the order they are in the path).

Find the path of the minimum cost and print its cost.

## 输入格式

The first line contains the single integer $ t $ ( $ 1 \le t \le 1000 $ ) — the number of test cases.

The first line of each test case contains the single integer $ n $ ( $ 2 \le n \le 10^5 $ ).

The second line of each test case contains $ n $ integers $ c_1, c_2, \dots, c_n $ ( $ 1 \le c_i \le 10^9 $ ) — the costs of each segment.

It's guaranteed that the total sum of $ n $ doesn't exceed $ 10^5 $ .

## 输出格式

For each test case, print the minimum possible cost of the path from $ (0, 0) $ to $ (n, n) $ consisting of at most $ n $ alternating segments.

## 样例 #1

### 样例输入 #1

```
3
2
13 88
3
2 3 1
5
4 3 2 1 4
```

### 样例输出 #1

```
202
13
19
```

## 提示

In the first test case, to reach $ (2, 2) $ you need to make at least one turn, so your path will consist of exactly $ 2 $ segments: one horizontal of length $ 2 $ and one vertical of length $ 2 $ . The cost of the path will be equal to $ 2 \cdot c_1 + 2 \cdot c_2 = 26 + 176 = 202 $ .

In the second test case, one of the optimal paths consists of $ 3 $ segments: the first segment of length $ 1 $ , the second segment of length $ 3 $ and the third segment of length $ 2 $ .

The cost of the path is $ 1 \cdot 2 + 3 \cdot 3 + 2 \cdot 1 = 13 $ .

In the third test case, one of the optimal paths consists of $ 4 $ segments: the first segment of length $ 1 $ , the second one — $ 1 $ , the third one — $ 4 $ , the fourth one — $ 4 $ . The cost of the path is $ 1 \cdot 4 + 1 \cdot 3 + 4 \cdot 2 + 4 \cdot 1 = 19 $ .

```cpp
#include<bits/stdc++.h>

using namespace std;

using i64 = long long;

void solve() {
    int n; cin >> n;
    vector<i64> c(n + 1);
    for (int i = 1; i <= n; i++) cin >> c[i];
	i64 r_min = c[1], r_sum = c[1], u_min = c[2], u_sum = c[2];
	i64 res = n * r_min + n * u_min;
	for (int i = 2; i <= n - 1; i++) {// 枚举方向变化
		if (i % 2 == 0) {// r
			r_min = min(r_min, c[i + 1]);
			r_sum += c[i + 1];
			res = min(res, r_min*(n-(i+1)/2)+r_sum-r_min+u_min*(n-i/2+1)+u_sum-u_min);
		} else {// u
			u_min = min(u_min, c[i + 1]);
			u_sum += c[i + 1];
			res = min(res, r_min*(n-i/2)+r_sum-r_min+u_min*(n-(i+1)/2+1)+u_sum-u_min);
		}
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

