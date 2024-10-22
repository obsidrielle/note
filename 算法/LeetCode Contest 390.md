---
title: LeetCode Contest 390
tags:
  - 算法
  - leetcode
---
# [100268. 最长公共后缀查询](https://leetcode.cn/problems/longest-common-suffix-queries/)
给你两个字符串数组 `wordsContainer` 和 `wordsQuery` 。

对于每个 `wordsQuery[i]` ，你需要从 `wordsContainer` 中找到一个与 `wordsQuery[i]` 有 **最长公共后缀** 的字符串。如果 `wordsContainer` 中有两个或者更多字符串有最长公共后缀，那么答案为长度 **最短** 的。如果有超过两个字符串有 **相同** 最短长度，那么答案为它们在 `wordsContainer` 中出现 **更早** 的一个。

请你返回一个整数数组 `ans` ，其中 `ans[i]` 是 `wordsContainer` 中与 `wordsQuery[i]` 有 **最长公共后缀** 字符串的下标。

---
设 $s=wordsContainer[i]$，倒着遍历 $s$，插入字典树，插入时对于每个经过的节点，更新节点对应的最小字符串长度及其下标。查询时倒着遍历找到最后一个匹配的节点就是答案。
```cpp
struct Node {
    Node *son[26]{};
    int min_l = INT_MAX, i;
};

class Solution {
public:
    vector<int> stringIndices(vector<string> &wordsContainer, vector<string> &wordsQuery) {
        Node *root = new Node();
        for (int idx = 0; idx < wordsContainer.size(); ++idx) {
            auto &s = wordsContainer[idx];
            int l = s.length();
            auto cur = root;
            if (l < cur->min_l) {
                cur->min_l = l;
                cur->i = idx;
            }
            for (int i = s.length() - 1; i >= 0; i--) {
                int b = s[i] - 'a';
                if (cur->son[b] == nullptr) {
                    cur->son[b] = new Node();
                }
                cur = cur->son[b];
                if (l < cur->min_l) {
                    cur->min_l = l;
                    cur->i = idx;
                }
            }
        }

        vector<int> ans;
        ans.reserve(wordsQuery.size());
        for (auto &s: wordsQuery) {
            auto cur = root;
            for (int i = s.length() - 1; i >= 0 && cur->son[s[i] - 'a']; i--) {
                cur = cur->son[s[i] - 'a'];
            }
            ans.push_back(cur->i);
        }
        return ans;
    }
};
```


