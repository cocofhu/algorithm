---
title: AC自动机
date: 2025-04-21 23:00:31
---


### 一、算法实现

```c++
#define AHO_CORASICK_ENABLE_WORDS_COUNT
// https://dl.acm.org/doi/pdf/10.1145/360825.360855
// https://oi.wiki/string/ac-automaton/
struct aho_corasick {

    static const int ALPHABET_SIZE = 27;

    // 字典树(在build的过程中优化为下一个匹配位置)
    vector<array<int, ALPHABET_SIZE>> trie_nodes;
    // fail的指针
    vector<int> fail;
    // 启动后缀链接 用于快速匹配所有出现的单词
    // https://leetcode.cn/problems/construct-string-with-minimum-cost/description/
#ifdef AHO_CORASICK_ENABLE_SUFFIX_LINK
    // 后缀链接(Suffix Link)，用于快速寻找fail指针指向的单词尾，0表示nullptr
    vector<int> last;
    // 字典树节点是否为单词结尾
    vector<bool> trie_exists;
#endif

    // 快速匹配某个单词是否出现
    // https://leetcode.cn/problems/stream-of-characters/description/
#ifdef AHO_CORASICK_FAST_MATCHED
    // 是否匹配到了任意一个单词(用于快速匹配)
    vector<bool> trie_matched;
#endif

    // 匹配每个单词在文章中出现的次数
    // https://www.luogu.com.cn/problem/P5357
#ifdef AHO_CORASICK_ENABLE_WORDS_COUNT
    // fail的指针构建反向树
    vector<vector<int>> fail_tree;
    // 每一个前缀被匹配的数量
    vector<int> trie_count;
    // dfs统计匹配节点 fail_tree时是否访问 用于优化递归
    vector<bool> trie_visited;
#endif

    aho_corasick() {
        clear();
    }
    void clear() {
        // 必须
        trie_nodes.clear();
        fail.clear();
#ifdef AHO_CORASICK_ENABLE_SUFFIX_LINK
        trie_exists.clear();
        last.clear();
#endif
#ifdef AHO_CORASICK_FAST_MATCHED
        trie_matched.clear();
#endif
#ifdef AHO_CORASICK_ENABLE_WORDS_COUNT
        fail_tree.clear();
        trie_count.clear();
        trie_visited.clear();
#endif
        new_node();
    }
    int new_node() {
        trie_nodes.push_back({});
        fail.push_back(0);
#ifdef AHO_CORASICK_ENABLE_SUFFIX_LINK
        trie_exists.push_back(false);
        last.push_back(0);
#endif
#ifdef AHO_CORASICK_FAST_MATCHED
        trie_matched.push_back(false);
#endif
#ifdef AHO_CORASICK_ENABLE_WORDS_COUNT
        fail_tree.emplace_back();
        trie_visited.push_back(false);
        trie_count.push_back(0);
#endif
        return int(trie_nodes.size() - 1);
    }
    int insert(const string& s) {
        int p = 0;
        for (char c : s) {
            if (trie_nodes[p][c & 31] == 0) {
                trie_nodes[p][c & 31] = new_node();
            }
            p = trie_nodes[p][c & 31];
        }
#ifdef AHO_CORASICK_FAST_MATCHED
        trie_matched[p] = true;
#endif
#ifdef AHO_CORASICK_ENABLE_SUFFIX_LINK
        trie_exists[p] = true;
#endif
        return p;
    }

    void build() {
        queue<int> q;
        for (int i = 1; i < ALPHABET_SIZE; ++i) {
            int u = trie_nodes[0][i];
            if (u != 0) {
                q.push(u);
            }
            fail[u] = 0;
        }
        while (!q.empty()) {
            int u = q.front();
            q.pop();
#ifdef AHO_CORASICK_FAST_MATCHED
            trie_matched[u] = trie_matched[fail[u]] || trie_matched[u];
#endif
            for (int i = 1; i < ALPHABET_SIZE; ++i) {
                int v = trie_nodes[u][i];
                if (v == 0) {
                    trie_nodes[u][i] = trie_nodes[fail[u]][i];
                } else {
                    fail[v] = trie_nodes[fail[u]][i];
#ifdef AHO_CORASICK_ENABLE_SUFFIX_LINK
                    last[v] = trie_exists[fail[v]] ? fail[v] : last[fail[v]];
#endif
                    q.push(v);
                }
            }
        }
#ifdef AHO_CORASICK_ENABLE_WORDS_COUNT
        int n = int(trie_nodes.size());
        for (int i = 1; i < n; ++i) {
            fail_tree[fail[i]].push_back(i);
        }
#endif
    }

#ifdef AHO_CORASICK_ENABLE_WORDS_COUNT
    void search_count(const string& text) {
        fill(trie_count.begin(), trie_count.end(), 0);
        fill(trie_visited.begin(), trie_visited.end(), false);
        ++trie_count[0];
        int p = 0;
        for (char c : text) {
            p = trie_nodes[p][c & 31];
            ++trie_count[p];
        }
    }
    // 先调用search_count 然后通过这个方法统计词频
    int calc_count(int u) {
        if (trie_visited[u]) {
            return trie_count[u];
        }
        trie_visited[u] = true;
        for (int v : fail_tree[u]) {
            trie_count[u] += calc_count(v);
        }
        return trie_count[u];
    }
#endif
};
```
