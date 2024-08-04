# FHQ-Treap(无旋Treap)

最简单好写的一种平衡树

基本操作有split（按值分裂/排名分裂）和merge, 其他建立在这两个基础上

**一旦split，一定要merge回去**

## 按权值分裂的模板

```cpp
#include<bits/stdc++.h>

using namespace std;

const int N = 1e6 + 5;

struct FHQ {
    int ls, rs;
    int key, pri;
    int sz;
} t[N];

int o, root;

void newnode(int x) { t[++o] = {0, 0, x, rand(), 1}; }

void pushup(int u) { t[u].sz = t[t[u].ls].sz + t[t[u].rs].sz + 1; }

void split(int u, int x, int &L, int &R) { // 权值分裂，返回以L和R为根的两棵树
    if (u == 0) { L = R = 0; return; }
    if (t[u].key <= x) { // 本节点比x小,那么到右子树上面找x
        L = u; // 记忆方法,这里是L,下面三个空里面都是R相关
        split(t[u].rs, x, t[u].rs, R); // R最右边(和语意一样)
    } else {
        R = u;
        split(t[u].ls, x, L, t[u].ls); // L在左边(和语意一样)
    }
    pushup(u);
}

int merge(int L, int R) { // 合并L,R为根的两颗树,并且返回这颗树的根
    if (!L || !R) return L + R;
    if (t[L].pri > t[R].pri) {
        t[L].rs = merge(t[L].rs, R); // 全是r,r右边(和语意一样)
        pushup(L);
        return L;
    } else {
        t[R].ls = merge(L, t[R].ls); // 全是l,l左边(和语意一样)
        pushup(R);
        return R;
    }
}

void insert(int x) {
    int L, R;
    split(root, x, L, R);
    newnode(x);
    int aa = merge(L, o);
    root = merge(aa, R);
}

void del(int x) {
    int L, R, p;
    split(root, x, L, R); // <= x, > x
    split(L, x - 1, L, p); // < x, == x
    p = merge(t[p].ls, t[p].rs); // 合并p左右子树,也就是删除p
    root = merge(merge(L, p), R);
}

int rnk(int x) {
    int L, R;
    split(root, x - 1, L, R); // <x, >= x
    int res = t[L].sz + 1;
    root = merge(L, R);
    return res;
}

int kth(int u, int k) { // 求排名第k的数
    if (k == t[t[u].ls].sz + 1) return u;
    else if (k < t[t[u].ls].sz + 1) return kth(t[u].ls, k); // 在左边
    else return kth(t[u].rs, k - t[t[u].ls].sz - 1); // 在右边
}

int precursor(int x) { // x的前驱
    int L, R;
    split(root, x - 1, L, R); // < x, >= x
    int res = t[kth(L, t[L].sz)].key;
    root = merge(L, R);
    return res;
}

int successor(int x) { // x的后继
    int L, R;
    split(root, x, L, R); // <= x, > x
    int res = t[kth(R, 1)].key;
    root = merge(L, R);
    return res;
}

int main() {
    ios::sync_with_stdio(false);
    cin.tie(nullptr);
    srand(time(NULL));

    int n;
    cin >> n;
    while (n--) {
        int opt, x;
        cin >> opt >> x;
        switch(opt) {
            case 1: insert(x); break;
            case 2: del(x); break;
            case 3: cout << rnk(x) << '\n'; break;
            case 4: cout << t[kth(root, x)].key << '\n'; break;
            case 5: cout << precursor(x) << '\n'; break;
            case 6: cout << successor(x) << '\n'; break;
        }
    }

    return 0;
}
```



## 按照排名分裂

区别在于split, merge相同

```c++
void split(int u, int x, int &L, int &R) {
    if (u == 0) { L = R = 0; return; }
    if (t[t[u].ls].sz + 1 <= x) { // x在右子树
        L = u;
        split(t[u].rs, x - t[t[u].ls].sz - 1, t[u].rs, R);
    } else { // x在左子树
        R = u;
        split(t[u].ls, x, L, t[u].ls);
    }
    pushup(u);
}
```















































































































































