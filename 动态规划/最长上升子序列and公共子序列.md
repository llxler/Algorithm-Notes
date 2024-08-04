# 最长上升子序列and公共子序列

## 基础算法dp

```cpp
#include <bits/stdc++.h>

using namespace std;

const int N = 5010;

int f[N], a[N];

int main()
{
    int n;
    cin >> n;
    for(int i = 1; i <= n; i ++) cin >> a[i];
    for(int i = 1; i <= n; i ++) f[i] = 1;
    for(int i = 1; i <= n; i ++)
        for(int j = 1; j < i; j ++)
            if(a[i] > a[j])
                f[i] = max(f[i], f[j] + 1);
    int len = -2e9;
    for(int i = 1; i <= n; i ++)
        len = max(len, f[i]);
    cout << len << endl;
    
    return 0;
}
```

## 贪心+ 二分 + 该算法与最长公共子序列的结合

```cpp
class Solution {
public:
    int lengthOfLIS(vector<int>& nums) {
        int n = nums.size(), len = 1;
        if (n == 0) return n;
        vector<int> d(n + 1, 0);
        d[len] = nums[0];
        for (int i = 1; i < n; ++i) {
            int v = nums[i];
            if (v > d[len]) {
                ++len;
                d[len] = v;
            } else {
                int lo = 1, hi = len;
                // d[k - 1] < v < d[k]
                while (lo < hi) {
                    int mid = (lo + hi) / 2;
                    if (d[mid] >= v) hi = mid;
                    else lo = mid + 1;
                }
                d[lo] = v;
            }
        }
        return len;
    }
};
```

