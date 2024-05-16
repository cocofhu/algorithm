---
title: LCA最近公共祖先
date: 2024-05-14 23:49:34
---


### 一、常用模板

#### 1、树上倍增
```c++
// https://www.luogu.com.cn/problem/P3379
const int maxn = 500010;
using ll = long long int;
struct Edge {
    int next, to;
} edges[maxn * 2];
int heads[maxn], pos = 0;

void addEdge(int a, int b) {
    edges[++pos].next = heads[a];
    edges[pos].to = b;
    heads[a] = pos;
}
int st[maxn][20];
int depth[maxn];
void dfs(int u, int fa){
    depth[u] = fa == -1 ? 0 : depth[fa] + 1;
    st[u][0] = fa;
    for(int i = 1; ~st[u][i-1] && i < 20; ++i){
        st[u][i] = st[st[u][i-1]][i-1];
    }
    for(int i = heads[u]; ~i; i = edges[i].next){
        int v = edges[i].to;
        if(fa == v) continue;
        dfs(v, u);
    }
}
int lca(int u, int v){
    if(depth[u] < depth[v]) return lca(v, u);
    for(int i = 19; ~i; --i){
        if(~st[u][i] && depth[st[u][i]] >= depth[v]) u = st[u][i];
    }
    if(u == v) return u;
    for(int i = 19; ~i; --i){
        if(st[u][i] != st[v][i]) u = st[u][i], v = st[v][i];
    }
    return st[u][0];
}
int main(){
    int n, m, s, a, b;
    memset(heads, -1, sizeof(heads));
    memset(st, -1, sizeof(st));
    scanf("%d%d%d", &n, &m, &s);
    for(int i = 1; i < n; ++i){
        scanf("%d%d", &b, &a);
        if(a == b) continue;
        addEdge(a, b);
        addEdge(b, a);
    }
    dfs(s, -1);
    for(int i = 0; i < m; ++i){
        scanf("%d%d", &b, &a);
        printf("%d\n", lca(a, b));
    }
    return 0;
}

```

#### 2、Tarjan
```c++
const int maxn = 500010;
using ll = long long int;

struct Edge {
    int next, to;
} edges[maxn * 2];
int heads[maxn], pos = 0;
using pi = pair<int, int>;
vector<pi> qst[maxn];
int ans[maxn];
void addEdge(int a, int b) {
    edges[++pos].next = heads[a];
    edges[pos].to = b;
    heads[a] = pos;
}
int F[maxn];
bool vis[maxn];
int Find(int x){
    return x == F[x] ? x : F[x] = Find(F[x]);
}
void dfs(int u, int fa){
    F[u] = u;
    vis[u] = true;
    for(int i = heads[u]; ~i; i = edges[i].next){
        int v = edges[i].to;
        if(fa == v) continue;
        dfs(v, u);
    }
    for(auto &p : qst[u]){
        if(vis[p.first]) ans[p.second] = Find(p.first);
    }
    F[u] = fa;
}

int main(){
    int n, m, s, a, b;
    memset(heads, -1, sizeof(heads));
    memset(vis, 0, sizeof(vis));
    scanf("%d%d%d", &n, &m, &s);
    for(int i = 0; i < n; ++i) qst[i].clear();
    for(int i = 1; i < n; ++i){
        scanf("%d%d", &b, &a);
        if(a == b) continue;
        addEdge(a, b);
        addEdge(b, a);
    }

    for(int i = 0; i < m; ++i){
        scanf("%d%d", &b, &a);
        qst[a].emplace_back(b, i);
        qst[b].emplace_back(a, i);
    }
    dfs(s, s);
    for(int i = 0; i < m; ++i) printf("%d\n", ans[i]);
    return 0;
}

```

#### 3、RMQ
```c++
using namespace std;
const int maxn = 500010;
using ll = long long int;

struct Edge {
    int next, to;
} edges[maxn * 2];
int heads[maxn], pos = 0;
void addEdge(int a, int b) {
    edges[++pos].next = heads[a];
    edges[pos].to = b;
    heads[a] = pos;
}
int st[maxn * 2][20], idx = -1;
int loc[maxn], depth[maxn];
int Log2[maxn * 2];
void dfs(int u, int fa){
    depth[u] = fa == u ? 0 : depth[fa] + 1;
    st[++idx][0] = u;
    loc[u] = idx;
    for(int i = heads[u]; ~i; i = edges[i].next){
        int v = edges[i].to;
        if(fa == v) continue;
        dfs(v, u);
        st[++idx][0] = u;
    }
}
int minNode(int u, int v){
    return depth[u] < depth[v] ? u : v;
}
int query(int u, int v){
    u = loc[u], v = loc[v];
    if(u > v) swap(u, v);
    int k = Log2[v - u + 1];
    return minNode(st[u][k], st[v - (1 << k) + 1][k]);
}
int main(){
    int n, m, s, a, b;
    memset(heads, -1, sizeof(heads));
    Log2[1] = 0, Log2[2] = 1;
    for(int i = 3, len = maxn * 2; i < len; ++i) Log2[i] = Log2[i >> 1] + 1;
    scanf("%d%d%d", &n, &m, &s);
    for(int i = 1; i < n; ++i){
        scanf("%d%d", &b, &a);
        if(a == b) continue;
        addEdge(a, b);
        addEdge(b, a);
    }
    dfs(s, s);
    // dfn 欧拉序列长度
    int len = n * 2 - 1;
    for(int i = 1; i < 20; ++i){
        for(int j = 0; j < len; ++j){
            int next = j + (1 << (i - 1));
            if(next >= len) break;
            st[j][i] = minNode(st[j][i - 1], st[next][i - 1]);
        }
    }
        for(int i = 0; i < m; ++i){
        scanf("%d%d", &b, &a);
        printf("%d\n", query(a, b));
    }
    return 0;
}
```

