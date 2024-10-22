---
title: LeetCode Contest 389
tags:
  - leetcode
  - 算法
---
#  [100255. 成为 K 特殊字符串需要删除的最少字符数](https://leetcode.cn/problems/minimum-deletions-to-make-string-k-special/)
给你一个字符串 `word` 和一个整数 `k`。

如果 `|freq(word[i]) - freq(word[j])| <= k` 对于字符串中所有下标 `i` 和 `j`  都成立，则认为 `word` 是 **k 特殊字符串**。

此处，`freq(x)` 表示字符 `x` 在 `word` 中的出现频率，而 `|y|` 表示 `y` 的绝对值。

返回使 `word` 成为 **k 特殊字符串** 需要删除的字符的最小数量。

---
## 1. 
必然有一种字母不需要删除，因为如果每种字母都至少删除一个，也可以都增加一个，而不影响字母数量的之差。因此只需要枚举出现次数最少的字母 $c$，字母 $c$ 肯定无需删除。
- 出现次数小于 $cnt[i]$ 的字母，全部删除。
- 出现次数大于等于 $cnt[i]$ 的字母，保留 $min(cnt[j],cnt[i]+k)$ 个。累积保留的字母个数。
最后用整长度减去最多保留的长度就是答案。
```C++
class Solution {
public:
    int minimumDeletions(string word, int k) {
        int cnt[26]{};
        for (char c: word) {
            cnt[c - 'a']++;
        }
        ranges::sort(cnt);

        int max_save = 0;
        for (int i = 0; i < 26; i++) {
            int sum = 0;
            for (int j = i; j < 26; j++) {
                sum += min(cnt[j], cnt[i] + k); // 至多保留 cnt[i]+k 个
            }
            max_save = max(max_save, sum);
        }
        return word.length() - max_save;
    }
};
```
