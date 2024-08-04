点点之间不相邻，同一层深度只能最多取一个，求取的最大权值



```cpp
#include <bits/stdc++.h>

using namespace std;

const int N = 2E5 + 5;

int n, dep[N], dp[N][2], w[N], mxd;
vector<int> ver[N];
map<int, int> cnt[N]; // 当前深度的dp值的个数
vector<int> e[N];

void dfs(int u, int depth) {
	mxd = max(mxd, depth);
	dep[u] = depth;
	ver[depth].push_back(u);
	for (int ver : e[u]) {
		dfs(ver, depth + 1);
	}
}

int main() {
	ios::sync_with_stdio(false);
	cin.tie(nullptr);
	cin >> n;
	for (int i = 2, x; i <= n; ++i) {
		cin >> x;
		e[x].push_back(i);
	}
	for (int i = 1; i <= n; ++i) {
		cin >> w[i];
	}
	dfs(1, 1);
	for (int d = mxd; d; --d) {
		for (int v : ver[d]) {
			// dp[v][0], 不选择该点
			if (cnt[d + 1].size()) {
				dp[v][0] = cnt[d + 1].rbegin() -> first; 
			}
			// dp[v][1]，选择该点
			dp[v][1] = w[v];
			if (cnt[d + 1].size()) {
				for (int son : e[v]) {
					cnt[d + 1][dp[son][1]]--;
					if (cnt[d + 1][dp[son][1]] == 0) cnt[d + 1].erase(dp[son][1]);
				}
				if (cnt[d + 1].size()) {
					dp[v][1] += cnt[d + 1].rbegin() -> first;
				}
				for (int son : e[v]) cnt[d + 1][dp[son][1]]++;
			}
			cnt[d][dp[v][1]]++, cnt[d][dp[v][0]]++;
		}
	}
	cout << max(dp[1][0], dp[1][1]) << '\n';
	return 0;
} 
```

