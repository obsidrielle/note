---
title: LeetCode Biweekly 126
tags:
  - 算法
  - leetcode
---
# [100241. 求出所有子序列的能量和](https://leetcode.cn/problems/find-the-sum-of-the-power-of-all-subsequences/)
给你一个字符串 `s` 。`s[i]` 要么是小写英文字母，要么是问号 `'?'` 。

对于长度为 `m` 且 **只** 含有小写英文字母的字符串 `t` ，我们定义函数 `cost(i)` 为下标 `i` 之前（也就是范围 `[0, i - 1]` 中）出现过与 `t[i]` **相同** 字符出现的次数。

字符串 `t` 的 **分数** 为所有下标 `i` 的 `cost(i)` 之 **和** 。

比方说，字符串 `t = "aab"` ：

- `cost(0) = 0`
- `cost(1) = 1`
- `cost(2) = 0`
- 所以，字符串 `"aab"` 的分数为 `0 + 1 + 0 = 1` 。

你的任务是用小写英文字母 **替换** `s` 中 **所有** 问号，使 `s` 的 **分数****最小 *。

请你返回替换所有问号 `'?'` 之后且分数最小的字符串。如果有多个字符串的 **分数最小** ，那么返回字典序最小的一个。

---
可以发现任意字符对于 $Cost$ 的加和和字符的位置无关，而只跟字符出现的数量有关，比如说 `...a...a` 和 `a...a...` 对 $Cost$ 的贡献相同。所以说对于每个问号，只需要寻找当前出现次数最少的字符。同时由于加和和字符的位置无关，只需要排序后顺序插入。
```C++
class Solution {
public:
    string minimizeStringValue(string s) {
        unordered_map<char, int> cnt;
        vector<char> res;
        
        for (auto & e : s) {
            cnt[e]++;
        }
        
        for (auto & e : s) {
            if (e == '?') {
                char min_cost_char = 'a';
                int min_cost = 0x3f3f3f3f;
                
                for (char i = 'a'; i <= 'z'; i++) {
                    if (cnt[i] < min_cost) {
                        min_cost_char = i;
                        min_cost = cnt[i];
                    }    
                }
                
                res.push_back(min_cost_char);
                cnt[min_cost_char]++;
            }
        }
        
        sort(res.begin(), res.end());
        
        int idx = 0;
        
        string ans = "";
        
        for (auto & e : s) {
            if (e == '?') {
                ans += res[idx++];
            } else {
                ans += e;
            }
        }
        
        return ans;
    }
};
```

# [100241. 求出所有子序列的能量和](https://leetcode.cn/problems/find-the-sum-of-the-power-of-all-subsequences/)
给你一个长度为 `n` 的整数数组 `nums` 和一个 **正** 整数 `k` 。

一个整数数组的 **能量** 定义为和 **等于** `k` 的子序列的数目。

请你返回 `nums` 中所有子序列的 **能量和** 。

由于答案可能很大，请你将它对 `1e9 + 7` **取余** 后返回。

---

01 背包问题。$f[i][sum]$ 表示数组第 $i$ 位，和为 $sum$ 的所有**子序列**的能量和。
不管是选择还是不选择，前 $i-1$ 位的子序列都会是前 $i$ 位的子序列，因此选择和不选择共计两次 $f[i-1][sum]$，如果选择加入，则还会有一次 $f[i-1][sum-nums[i]]$ 的贡献。
```C++
class Solution {
public:
    int sumOfPower(vector<int>& nums, int k) {
        const int MOD = int(1e9 + 7);

        long long f[120][120] = {0};

        f[0][0] = 1;
        
        for (int i = 1; i <= nums.size(); i++) {
            for (int j = 0; j <= k; j++) {
                f[i][j] += 1LL * f[i - 1][j] * 2 % MOD;
                
                if (j - nums[i - 1] >= 0) {
                    f[i][j] += 1LL * f[i - 1][j - nums[i - 1]] % MOD;
                }

                f[i][j] %= MOD;
            }
        }

        return f[nums.size()][k];
    }
};
```
