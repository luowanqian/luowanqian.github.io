---
title: LeetCode 1 - Two Sum
date: 2018-03-12 10:22:59
urlname: leetcode_1
tags:
 - 算法
 - LeetCode
---

> 问题描述：
>
> Given an array of integers, return __indices__ of the two numbers such that they add up to a specific target.
>
> You may assume that each input would have __exactly__ one solution, and you may not use the same element twice.
>
> __Example:__
>
> Given nums = [2, 7, 11, 15], target = 9. Because nums[0] + nums[1] = 2 + 7 = 9, return [0, 1].

## 时间复杂度为 O(n) 解法

 首先分析下问题，根据问题描述，可以得知数组是无序的。如果用暴力方法解决问题，也就是先在数组中固定一个数字，再判断数组中其余的 $n-1$ 个数字与它的和是不是等于目标值，这样时间复杂度是 $O(n^2)$。这里关键地方就是在于固定一个数字时，如何快速在数组中寻找与它的和等于目标值，而不是通过遍历方法一个个找，这样时间复杂度要 $O(n)$，想到快速查找，第一时间可以想到的是哈希表 (Hash Map)，因为这个查找元素的时间复杂度为 $O(1)$。

对于哈希表，我们可以用数组中的数字作为哈希表的key，用数字的下标作为哈希表的value。当我们从头遍历数组时，对于访问到的数字，我们首先判断哈希表中是否存在与它的和等于目标值的key，如果不存在，插入该数字到哈希表中，如果存在，算法返回两个数字对应的数组的下标。可以看出，这个算法只需要遍历一次数组，因此算法复杂度为 $O(n)$。

{% codeblock %}
#include <unordered_map>
#include <iostream>
#include <vector>
using namespace std;

class Solution {
public:
    vector<int> twoSum(vector<int>& nums, int target) {
        unordered_map<int, int> nums_map;
        unordered_map<int, int>::const_iterator iter;
        vector<int> indices;
        for (int i=0; i<nums.size(); i++) {
            if ((iter = nums_map.find(target - nums[i])) == nums_map.end()) {
                nums_map.insert(make_pair(nums[i], i));
            } else {
                indices.push_back(iter->second);
                indices.push_back(i);
                break;
            }
        }

        return indices;
    }
};

int main()
{
    int target = 9;
    vector<int> nums;
    nums.push_back(2);
    nums.push_back(7);
    nums.push_back(11);
    nums.push_back(15);

    Solution sol;
    vector<int> indices = sol.twoSum(nums, target);
    if (indices.size() == 0) {
        cout << "Not found" << endl;
    } else {
        cout << "Find: [" << indices[0] << ", " << indices[1] << "]" << endl;
    }
    return 0;
}
{% endcodeblock %}
