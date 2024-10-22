---
title: LeetCode 1793. 好子数组的最大分数(单调栈 双指针)
tags:
  - leetcode
  - 算法
---
给你一个整数数组 `nums` **（下标从 0 开始）**和一个整数 `k` 。

一个子数组 `(i, j)` 的 **分数** 定义为 `min(nums[i], nums[i+1], ..., nums[j]) * (j - i + 1)` 。一个 **好** 子数组的两个端点下标需要满足 `i <= k <= j` 。

请你返回 **好** 子数组的最大可能 **分数** 。

---
## 1. 单调栈
用单调栈对每个 $nums[i]$ 求出对应的 $left[i]$ 和 $right[i]$，分别代表以 $nums[i]$ 为最小值的其左边和其右边最近且大于 $nums[i]$ 的值，那么对于开区间 $(left[i],right[i])$，其好数组的最大分数的就是
$$
nums[i] \times (right[i] - left[i] + 1)
$$
只需要枚举所有 $i$ 求出总体最大值即可。
```cpp
class Solution {
public:
    int maximumScore(vector<int>& nums, int k) {
        int n = nums.size();
        vector<int>pre(n, -1), sub(n, n);
        vector<int>st;
        for(int i = 0; i < n; i++){
            while(st.size() > 0 && nums[st.back()] >= nums[i]){
                st.pop_back();
            }
            if(st.size() > 0)pre[i] = st.back();
            st.push_back(i);
        }
        st.clear();
        for(int i = n - 1; i >= 0; i--){
            while(st.size() > 0 && nums[st.back()] >= nums[i]){
                st.pop_back();
            }
            if(st.size() > 0)sub[i] = st.back();
            st.push_back(i);
        }
        // for(int i = 0; i < n; i++)cout << pre[i] << " ";
        // cout << endl;
        // for(int i = 0; i < n; i++)cout << sub[i] << " ";
        int ans = 0;
        for(int i = 0; i < n; i++){
            int p = pre[i];
            int s = sub[i];
            if(p + 1 <= k && s - 1 >= k){
                ans = max(ans, nums[i] * (s - p - 1));
            }
        }
        return ans;
    }
}
```

## 2. 双指针
好子数组必须包括 $nums[k]$, 因此可以使用 $left$ 和 $right$ 选择区间 $(left,right)$，其初始值为 $k-1$ 和 $k+1$。
枚举分数中 $min$ 对应的值，最大值为 $nums[k]$, 最小值为 $nums$ 的最小值，从大到小进行枚举，当枚举到 $i$ 时，不断向左和向右移动，直到指向的元素小于 $i$。
```cpp
class Solution {
public:
    int maximumScore(vector<int>& nums, int k) {
        int n = nums.size();
        int left = k - 1, right = k + 1;
        int ans = 0;
        for (int i = nums[k];; --i) {
            while (left >= 0 && nums[left] >= i) {
                --left;
            }
            while (right < n && nums[right] >= i) {
                ++right;
            }
            ans = max(ans, (right - left - 1) * i);
            if (left == -1 && right == n) {
                break;
            }
        }
        return ans;
    }
};
```
