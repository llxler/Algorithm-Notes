```cpp
accumulate(a.begin(), a.end(), (初始+多少))
```

```cpp
string s = "-122134";
cout << stoll(s) << '\n'; // 转化为longlong
cout << stoi(s) << '\n'; // 转化为int
int num = -12323213;
string ss = to_string(num);
cout << ss << '\n';
```

```cpp
cout << __gcd(18, 12) << '\n';
cout << __lg(18) << '\n';// 求18的二进制位数
```

set的stl应用

```cpp
#include<bits/stdc++.h>

using namespace std;

using pii = pair<int, int>;

void solve() {
    int n, m;
    cin >> n >> m;
    vector<int> a(n + 1, 0);
	multiset<pii> st;
	for (int i = 1; i <= n; i++) st.insert({0, i});
	int cnt = 0;
	while (m--) {
		int id; cin >> id;
		int val = a[id]++;
		auto it = *st.lower_bound({val, id});
		st.erase(it);
		st.insert({it.first + 1, it.second});
		int value_max = (*prev(st.end())).first;
		auto shit = *st.lower_bound({value_max, -1});
		cout << shit.second << '\n';
	}
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

Multiset里面的针对性删除（用迭代器），整体删除用值

```cpp
#include<bits/stdc++.h>

using namespace std;

void solve() {
	int n, res = 0; cin >> n;
    multiset<int> st;
    for (int i = 1, x; i <= n; i++) {
    	cin >> x;
    	st.insert(x);
    }
    while (st.size()) {
    	++res;
    	auto t = *st.begin();
    	while (st.lower_bound(t + 1) != st.end()) {
    		auto now = st.lower_bound(t + 1);
    		int val = *now;
    		if (val != t + 1) {
    			break;
    		} else {
    			st.erase(now);
    			t++;
    		}
    	}
    	st.erase(st.begin());
    }
    cout << res << '\n';
}

signed main() {
    cin.tie(nullptr)->ios::sync_with_stdio(false);
    int t = 1; 
    // cin >> t; 
    while (t--) solve();
    return 0;
}   
```



自定义unordered_map哈希

```cpp
struct custom_hash {
    static uint64_t splitmix64(uint64_t x) {
        // http://xorshift.di.unimi.it/splitmix64.c
        x += 0x9e3779b97f4a7c15;
        x = (x ^ (x >> 30)) * 0xbf58476d1ce4e5b9;
        x = (x ^ (x >> 27)) * 0x94d049bb133111eb;
        return x ^ (x >> 31);
    }

    size_t operator()(uint64_t x) const {
        static const uint64_t FIXED_RANDOM = chrono::steady_clock::now().time_since_epoch().count();
        return splitmix64(x + FIXED_RANDOM);
    }
};

unordered_map<int, int, custom_hash> mp;
```

在不得不使用unorder但是有可能发生哈希碰撞的时候使用

```cpp
#include<bits/stdc++.h>

using namespace std;

using LL = long long;

struct custom_hash {
    static uint64_t splitmix64(uint64_t x) {
        x += 0x9e3779b97f4a7c15;
        x = (x ^ (x >> 30)) * 0xbf58476d1ce4e5b9;
        x = (x ^ (x >> 27)) * 0x94d049bb133111eb;
        return x ^ (x >> 31);
    }

    size_t operator()(uint64_t x) const {
        static const uint64_t FIXED_RANDOM = chrono::steady_clock::now().time_since_epoch().count();
        return splitmix64(x + FIXED_RANDOM);
    }
};

void solve() {
    int n;
    LL sum = 0;
    cin >> n;
    vector<int> a(n);
    for (auto &it : a) cin >> it, sum += it;
	if (2LL * sum % n != 0) {
		cout << 0 << '\n';
		return;
	}
	int goal = (LL) 2LL * sum / n;
	LL res = 0;
	unordered_map<int, int, custom_hash> st;
	for (int i = 0; i < n; i++) {
		if (st.count(goal - a[i])) {
			res += st[goal - a[i]];
		}
		st[a[i]]++;
	}
	cout << res << '\n';
}

signed main() {
    cin.tie(nullptr)->ios::sync_with_stdio(false);
    int t = 1;
    cin >> t;
    while (t--) solve();
    return 0;
}
```



## iota

```cpp
V<int> p(n - 1);
iota(p.begin(), p.end(), 0);
sort(p.begin(), p.end(), [&](int x, int y) {
	int dx = a[x + 1] - a[x], dy = a[y + 1] - a[y];
	return dx > dy;
});
```

## Vector的删除实现简易版约瑟夫环

```cpp
#include <bits/stdc++.h>
#define rep(i, j, k) for (int i = (j); i <= (k); ++i)
#define per(i, j, k) for (int i = (j); i >= (k); --i)
#define ALL(v) (v).begin(), (v).end()
#define V vector
#define pb push_back
#define fi first
#define se second
using LL = long long;
using pii = std::pair<int, int>;
using namespace std;

int main() {
    ios::sync_with_stdio(false);
    cin.tie(nullptr);
    int n, k;
    cin >> n >> k;
    V<int> pos;
    rep(i, 1, n) pos.pb(i);
    int cur = 0;
    while (k--) {
        int ai;
        cin >> ai;
        V<int> now(pos);
        int sz = now.size();
		ai %= sz;
		cur = (cur + ai) % sz;
		now.erase(find(now.begin(), now.end(), pos[cur]));
		cout << pos[cur] << ' ';
		cur = find(now.begin(), now.end(), pos[(cur + 1) % sz]) - now.begin();
		pos = now;
    }
    return 0;
}
```

