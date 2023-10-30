---
title: Manacher
date: 2023-10-30 19:40:31
---


### 一、算法实现

#### 1、Manacher模板

```c++
#include <algorithm>
#include <cstdio>
#include <cstring>
#include <iostream>


using namespace std;
#define N 12001000
// https://www.luogu.com.cn/problem/P3805
char S[N],M[N<<1];
int R[N<<1];
// 返回最长回文串长度, 并在R里存放回文半径
int manacher()
{
    int n = 0;
    for(int i = 0; S[i]; ++i){
        M[n++] = '#';
        M[n++] = S[i];
    }
    if(n == 0) return 0;
    M[n++] = '#';
    M[n] = 0;
    int r=1,c=1,ans = 1;R[0]=1;
    for(int i = 1; i < n; ++i){
        int p = i < r ? min(R[2*c - i], r - i + 1) : 1;
        while(i + p < n && i - p >= 0 && M[i+p] == M[i-p]) ++p;
        R[i] = p;
        ans = max(ans, p);
        if(r < p + i - 1) r = p + i - 1, c = i;
    }
    return ans - 1;
}
int main()
{
    scanf("%s", S);
    printf("%d\n", manacher());
    /*int n = strlen(M);
    for(int i = 0; i < n; ++i) printf("%c ", M[i]);
    puts("");
    for(int i = 0; i < n; ++i) printf("%d ", R[i]);
    puts("");*/
    return 0;
}
```

