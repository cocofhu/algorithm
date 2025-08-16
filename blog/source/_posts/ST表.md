---
title: ST表
date: 2025-08-16 17:02:31
---


### 一、算法实现

```c++
using ll = long long;
template<typename T, typename F>
struct disjoint_sparse_table {
    int n;
    vector<int> log2;
    vector<vector<T>> mat;
    F func;
    disjoint_sparse_table(const vector<T>& arr, const F& f) : n(int(arr.size())), func(f) {
        build_log2();
        mat.push_back(arr);
        int k = log2[n];
        for (int i = 1; i <= k; ++i) {
            mat.emplace_back(n);
            for (int j = 0; j < n - (1 << (i - 1)); ++j) {
                mat[i][j] = func(mat[i - 1][j], mat[i - 1][j + (1 << (i - 1))]);
            }
        }
    }
    void build_log2() {
        log2.resize(n + 5, 0);
        log2[1] = 0;
        log2[2] = 1;
        for (int i = 3; i <= n; ++i) log2[i] = log2[i >> 1] + 1;
    }
    T query(int l, int r) {
        int k = log2[r - l + 1];
        return func(mat[k][l], mat[k][r - (1 << k) + 1]);
    }
};
/*
        disjoint_sparse_table st(nums, [](int a, int b) {
            return gcd(a, b);
        });
*/
```