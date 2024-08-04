小林在玩一个抽卡游戏，其中有 n 种不同的卡牌，编号为 1 到 n。

每一次抽卡，她获得第 i 种卡牌的概率为 pi。

如果这张卡牌之前已经获得过了，就会转化为一枚硬币。

可以用 k 枚硬币交换一张没有获得过的卡。

小林会一直抽卡，直至集齐了所有种类的卡牌为止，求她的期望抽卡次数。

如果你给出的答案与标准答案的绝对误差不超过 10−4，则视为正确。

提示：聪明的小林会把硬币攒在手里，等到通过兑换就可以获得剩余所有卡牌时，一次性兑换并停止抽卡。

```cpp
#include <bits/stdc++.h>
#define pii pair<int, int> 
#define fi first
#define se second
#define LL long long

using namespace std;

int n, k;
double p[20], f[1 << 17][80];

double dp(int state, int cost, int remain) {
	double &v = f[state][cost];
	if (v >= 0) return v;
	if (remain * k <= cost) return v = 0;
	v = 0;
	for (int i = 0; i < n; ++i) {
		if (state >> i & 1) {
			v += (dp(state, cost + 1, remain) + 1) * p[i + 1];
		} else {
			v += (dp(state | (1 << i), cost, remain - 1) + 1) * p[i + 1];
		}
	}
	return v;
}

int main() {
	ios::sync_with_stdio(false);
	cin.tie(nullptr);
	cout << fixed << setprecision(10);
	memset(f, -1, sizeof f);
	cin >> n >> k;
	for (int i = 1; i <= n; ++i) {
		cin >> p[i];
	}
	cout << dp(0, 0, n) << '\n';
	return 0;
} 
```

