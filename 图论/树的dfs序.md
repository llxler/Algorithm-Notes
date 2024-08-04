- L存第一次出现
- Node存序列对应节点
- R存最后出现

```cpp
#include<bits/stdc++.h>

using namespace std;

const int N = 1e5 + 10;

int Node[N], L[N], R[N], totdfn, n;
vector<int> adj[N];

void dfs(int u, int fa) {
	L[u] = ++totdfn;
	Node[totdfn] = u;
	for (int j : adj[u])
		if (j != fa) {
			dfs(j, u);
		}
	R[u] = totdfn;
}

signed main() {
    cin.tie(nullptr)->ios::sync_with_stdio(false);
	cin >> n;
	for (int i = 0; i < n - 1; i++) {
		int u, v; cin >> u >> v;
		adj[u].emplace_back(v);
		adj[v].emplace_back(u);
	}
	dfs(1, -1);
	for (int i = 1; i <= n; i++) {
		cout << i << ":   ";
		for (int j = L[i]; j <= R[i]; j++) {
			cout << Node[j] << ' ';
		}
		cout << '\n';
	}
}   
```

