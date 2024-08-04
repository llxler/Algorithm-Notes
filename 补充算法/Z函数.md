# Z函数的概念

**定义Z[i]表示s以i开头与s最长公共前缀的长度**

特别的令Z[0] = 0;

O(n)复杂度求z函数

解析：

![image-20240503085423585](C:\Users\24996\AppData\Roaming\Typora\typora-user-images\image-20240503085423585.png)

```c++
vector<int> z_function(string s) {
    int n = (int)s.length();
    vector<int> z(n);
    for (int i = 1, l = 0, r = 0; i < n; ++i) {
        if (i <= r && z[i - l] < r - i + 1) {
            z[i] = z[i - l];
        } else {
            z[i] = max(0, r - i + 1);
            while (i + z[i] < n && s[z[i]] == s[i + z[i]])
                ++z[i];
        }
        if (i + z[i] - 1 > r)
            l = i, r = i + z[i] - 1;
    }
    return z;
}
```

## 例题

# Password

## 题面翻译

Asterix，Obelix 和他们的临时伙伴 Suffix、Prefix 已经最终找到了和谐寺。然而和谐寺大门紧闭，就连 Obelix 的运气也没好到能打开它。

不久他们发现了一个字符串 $S\ (1\leqslant\vert S\vert\leqslant1000000)$，刻在和谐寺大门下面的岩石上。Asterix 猜想那一定是打开寺庙大门的密码，于是就大声将字符串朗读了出来，然而并没有什么事发生。于是 Asterix 又猜想密码一定是字符串 $S$ 的子串 $T$。

Prefix 认为 $T$ 是 $S$ 的前缀，Suffix 认为 $T$ 是 $S$ 的后缀，Obelix 却认为 $T$ 应该是 $S$ 中的某一部分，也就是说，$T$ 既不是 $S$ 的前缀，也不是 $S$ 的后缀。

Asterix 选择子串 $T$ 来满足所有伙伴们的想法。同时，在所有可以被接受的子串变形中，Asterix 选择了最长的一个。当 Asterix 大声读出子串 $T$ 时，寺庙的大门开了。（也就是说，你需要找到既是 $S$ 的前缀又是 $S$ 的后缀同时又在 $S$ 中间出现过的最长子串）

现在给你字符串 $S$，你需要找到满足上述要求的子串 $T$。

### 输入格式

一行一个只包含小写字母的字符串 $S$。

### 输出格式

输出子串 $T$，如果 $T$ 不存在，输出 `Just a legend`。

## 样例 #1

### 样例输入 #1

```
fixprefixsuffix
```

### 样例输出 #1

```
fix
```

## 样例 #2

### 样例输入 #2

```
abcdabc
```

### 样例输出 #2

```
Just a legend
```

- 先找出前后缀匹配的 -> z[i] = n - i;
- 再看这个串是否在s中部出现

```cpp
#include<bits/stdc++.h>
#define ALL(v) (v).begin(), (v).end()
#define pb push_back
#define V vector
#define fi first
#define se second
using namespace std;
using pii = pair<int, int>;
using LL = long long;

vector<int> z_function(string s) {
    int n = (int)s.length();
    vector<int> z(n);
    for (int i = 1, l = 0, r = 0; i < n; ++i) {
        if (i <= r && z[i - l] < r - i + 1) {
            z[i] = z[i - l];
        } else {
            z[i] = max(0, r - i + 1);
            while (i + z[i] < n && s[z[i]] == s[i + z[i]])
                ++z[i];
        }
        if (i + z[i] - 1 > r)
            l = i, r = i + z[i] - 1;
    }
    return z;
}

bool check(string t, string s) {
    string str = t + '&' + s;
    auto z = z_function(str);
    int n = str.size(), szt = t.size(), szs = s.size();
    for (int i = szt + 2; i <= n - szt - 1; ++i) 
        if (z[i] == szt) return true;
    return false;
}

int main() {
    ios::sync_with_stdio(false);
    cin.tie(nullptr);

    int n;
    string s;
    V<string> solve;
    cin >> s;
    n = s.size();
    auto z = z_function(s);
    string res = "";
    for (int i = 1; i < n; ++i) {
        if (z[i] == n - i) { // 匹配成功
            string t = s.substr(i); // 模式串， s是文本串
            if (check(t, s)) {
                res = t;
                break;
            }
        }
    }
    if (res == "") res = "Just a legend";
    cout << res << '\n';

    return 0;
}
```

## 例题：

# Division + LCP (easy version)

## 题目描述

This is the easy version of the problem. In this version $ l=r $ .

You are given a string $ s $ . For a fixed $ k $ , consider a division of $ s $ into exactly $ k $ continuous substrings $ w_1,\dots,w_k $ . Let $ f_k $ be the maximal possible $ LCP(w_1,\dots,w_k) $ among all divisions.

 $ LCP(w_1,\dots,w_m) $ is the length of the Longest Common Prefix of the strings $ w_1,\dots,w_m $ .

For example, if $ s=abababcab $ and $ k=4 $ , a possible division is $ \color{red}{ab}\color{blue}{ab}\color{orange}{abc}\color{green}{ab} $ . The $ LCP(\color{red}{ab},\color{blue}{ab},\color{orange}{abc},\color{green}{ab}) $ is $ 2 $ , since $ ab $ is the Longest Common Prefix of those four strings. Note that each substring consists of a continuous segment of characters and each character belongs to exactly one substring.

Your task is to find $ f_l,f_{l+1},\dots,f_r $ . In this version $ l=r $ .

## 输入格式

The first line contains a single integer $ t $ ( $ 1 \le t \le 10^4 $ ) — the number of test cases.

The first line of each test case contains two integers $ n $ , $ l $ , $ r $ ( $ 1 \le l = r \le n \le 2 \cdot 10^5 $ ) — the length of the string and the given range.

The second line of each test case contains string $ s $ of length $ n $ , all characters are lowercase English letters.

It is guaranteed that the sum of $ n $ over all test cases does not exceed $ 2\cdot 10^5 $ .

## 输出格式

For each test case, output $ r-l+1 $ values: $ f_l,\dots,f_r $ .

## 样例 #1

### 样例输入 #1

```
7
3 3 3
aba
3 3 3
aaa
7 2 2
abacaba
9 4 4
abababcab
10 1 1
codeforces
9 3 3
abafababa
5 3 3
zpozp
```

### 样例输出 #1

```
0
1
3
2
10
2
0
```

## 提示

In the first sample $ n=k $ , so the only division of $ aba $ is $ \color{red}a\color{blue}b\color{orange}a $ . The answer is zero, because those strings do not have a common prefix.

In the second sample, the only division is $ \color{red}a\color{blue}a\color{orange}a $ . Their longest common prefix is one.

**这是一道二分+字符串匹配好题**

```cpp
#include <bits/stdc++.h>
#define ALL(v) (v).begin(), (v).end()
#define pb push_back
#define V vector
#define fi first
#define se second
using namespace std;
using LL = long long;
using pii = pair<int, int>;

V<int> z_function(string s) {
    int n = s.size();
    V<int> z(n);
    for (int i = 1, l = 0, r = 0; i < n; ++i) {
        if (i <= r && z[i - l] < r - i + 1) {
            z[i] = z[i - l];
        } else {
            z[i] = max(0, r - i + 1);
            while (i + z[i] < n && s[z[i]] == s[i + z[i]]) ++z[i];
        }
        if (i + z[i] - 1 > r) {
            r = i + z[i] - 1;
            l = i;
        }
    }
    return z;
}

void solve() {
	int n, k, _;
    string s;
    cin >> n >> k >> _ >> s;
    // 讲s分割成k段使得lcp最大
    int l = 0, r = n / k;
    auto check = [&](int len) {
        string t = s.substr(0, len);
        string str = t + "&" + s;
        // 看s里面有多少t
        V<int> z = z_function(str);
        int n = str.size(), szt = t.size(), szs = s.size();
        int res = 0;
        for (int i = szt + 1; i <= n - szt; ++i) 
            if (z[i] == szt) ++res, i = i + szt - 1;
        if (res >= k) return true;
        return false;
    };
    while (l < r) {
        int mid = (l + r + 1) / 2;
        if (check(mid)) l = mid;
        else r = mid - 1;
    }
    cout << r << '\n';
}

int main() {
    ios::sync_with_stdio(false);
    cin.tie(nullptr);
    
    int t;
    cin >> t;
    while (t--) {
    	solve();
	}
	
    return 0;
}
```

