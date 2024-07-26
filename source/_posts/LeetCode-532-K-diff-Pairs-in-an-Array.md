---
title: LeetCode 532 - K-diff Pairs in an Array
urlname: leetcode_532
date: 2018-03-24 21:42:47
tags:
 - LeetCode
 - 算法
---

## 问题描述

Given an array of integers and an integer __k__, you need to find the number of __unique__ k-diff pairs in the array. Here a __k-diff__ pair is defined as an integer pair (i, j), where __i__ and __j__ are both numbers in the array and their absolute difference is __k__.

__Example 1__

>__Input:__ [3, 1, 4, 1, 5], k = 2
>
>__Output:__ 2
>
>__Explanation:__ There are two 2-diff pairs in the array, (1, 3) and (3, 5).
>Although we have two 1s in the input, we should only return the number of __unique__ pairs.

__Example 2__

> __Input:__[1, 2, 3, 4, 5], k = 1
>
> __Output:__ 4
>
> __Explanation:__ There are four 1-diff pairs in the array, (1, 2), (2, 3), (3, 4) and (4, 5).

__Example 3__

> __Input:__ [1, 3, 1, 5, 4], k = 0
>
> __Output:__ 1
>
> __Explanation:__ There is one 0-diff pair in the array, (1, 1).

__Note:__

1. The pairs (i, j) and (j, i) count as the same pair.
2. The length of the array won't exceed 10,000.
3. All the integers in the given input belong to the range: [-1e7, 1e7].

## 解决方案

这道题目主要意思是找出差值为 k 的数字对，而且数字对是不重复，例如数字对 (1, 3) 和数字对 (3, 1) 是重复的。首先判断数字 a 和数字 b 的差值是否为 k，对于数字 a 和数字 b 的差值，可以用 $\vert a - b \vert$表示，当差值为 k 时，即 $\vert a - b \vert = k$，去掉绝对值符号可以得到
$$
\begin{align}
& a = b - k \\
\text{or} \quad & a = b + k
\end{align}
$$
也就说知道数字 a，可以利用上面等式判断一个数字 b 与数字 a 的差值是否为 k。接下来解决重复数字对问题，对于带有重复数字对序列 `(1, 3), (3, 1), (3, 5), (5, 3)`，我们可以用一个无重复元素容器，例如STL中`set`、`map`，`unorder_set` 或 `unorder_map`，保存每一个数字对的最小元素，即 `1, 3, 5`，然后容器元素个数就是不重复数字对的个数。

综合上面的讨论，我们可以得到一个思路，首先创建两个容器，第一个容器保存访问过的元素，第二个容器为无重复元素容器，保存已找到的数字对的最小的元素，然后遍历数组的每一个元素，令当前访问的元素为 b，

* 如果 $a = b-k$ 在第一个容器中，保存 $\min(a, b) = b - k$ 到第二个容器中
* 如果 $a = b + k$ 在第一个容器中，保存 $\min(a, b) = b$ 到第二个容器中
* 如果上述条件都不满足，说明没有找到相应的数字对，将元素 $b$ 放入到第一个容器中

由于第一个容器要进行不断地查找，所以可以考虑使用哈希表或者二叉树之类的数据结构保存。基于上面思路，编写代码如下：

{% codeblock %}
#include <iostream>
#include <vector>
#include <unordered_set>
using namespace std;

class Solution {
public:
    int findPairs(vector<int>& nums, int k) {
        if (k < 0)
            return 0;

        int num;
        unordered_set<int> pair_smalls;
        unordered_set<int> unique_nums;
        for (int i=0; i<nums.size(); i++) {
            num = nums[i];
            if (unique_nums.find(num - k) != unique_nums.end())
                pair_smalls.insert(num - k);
            if (unique_nums.find(num + k) != unique_nums.end())
                pair_smalls.insert(num);

            unique_nums.insert(num);
        }

        return pair_smalls.size();
    }
};

int main()
{
    Solution solu;

    vector<int> nums1 = {3, 1, 4, 1, 5};
    int k1 = 2;
    cout << solu.findPairs(nums1, k1) << endl;

    vector<int> nums2 = {1, 2, 3, 4, 5};
    int k2 = -1;
    cout << solu.findPairs(nums2, k2) << endl;

    return 0;
}
{% endcodeblock %}
