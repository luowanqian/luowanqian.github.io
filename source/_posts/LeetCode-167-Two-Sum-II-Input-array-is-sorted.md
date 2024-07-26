---
title: LeetCode 167 - Two Sum II - Input array is sorted
urlname: leetcode_167
date: 2018-03-30 23:30:48
tags:
 - LeetCode
 - 算法
---

## 问题描述

Given an array of integers that is already __sorted in ascending order__, find two numbers such that they add up to a specific target number.

The function twoSum should return indices of the two numbers such that they add up to the target, where index1 must be less than index2. Please note that your returned answers (both index1 and index2) are not zero-based.

You may assume that each input would have exactly one solution and you may not use the same element twice.

__Input:__ numbers={2, 7, 11, 15}, target=9

__Output:__ index1=1, index2=2

__Related Topics:__ Array, Two Pointers, Binary Search

原问题：[167. Two Sum II - Input array is sorted](https://leetcode.com/problems/two-sum-ii-input-array-is-sorted/description/)

## 解决方案

考虑我们在数组中选择两个数字，如果它们的和等于输入的 target，那么我们就找到了要找的这两个数字。如果和小于 target 呢？我们希望两个数字的和再大一点。由于数组已经排序好了，我们可以考虑选择较小的数字后面的数字。因为排在后面的数字要大一些，那么两个数字的和也要大一些，就有可能等于输入的数字 target 了。同样，当两个数字的和大于输入 target 的时候，我们可以选择较大数字前面的数字，因为排在数字前面的数字要小一些。

根据上面的思路，我们可以设定两个指针 point1 和 point2，point1 指向数组的第一个元素，point2 指向数组的最后一个元素，然后根据上面的判断准则，当两个指针指向的数字之和小于 target 时，指针 point1 加一，反之指针 point2 减一，通过不断地迭代，直到数组被遍历完。实现代码如下：

{% codeblock %}
#include <iostream>
#include <vector>
using namespace std;

class Solution
{
public:
    vector<int> twoSum(vector<int>& numbers, int target)
    {
        vector<int> idx;

        if (numbers.size() < 2)
            return idx;

        int left, right;
        left = 0;
        right = numbers.size()-1;
        while (left < right) {
            if (numbers[left] + numbers[right] < target) {
                left++;
            } else if (numbers[left] + numbers[right] > target) {
                right--;
            } else {
                idx.push_back(left+1);
                idx.push_back(right+1);
                break;
            }
        }

        return idx;
    }
};

int main()
{
    Solution solu;
    vector<int> nums {2, 7, 11, 15};
    int target = 9;
    vector<int> idx = solu.twoSum(nums, target);
    if (idx.size() < 2)
        cout << "Not found" << endl;
    else
        cout << "index1=" << idx[0]
             << ", index2=" << idx[1] << endl;

    return 0;
}
{% endcodeblock %}
