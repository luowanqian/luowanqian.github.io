---
title: LeetCode 349 - Intersection of Two Arrays
urlname: leetcode_349
tags:
  - LeetCode
  - 算法
date: 2018-04-21 22:57:41
---

## 问题描述

Given two arrays, write a function to compute their intersection.

__Example:__

Given nums1 = `[1, 2, 2, 1]`, nums2 = `[2, 2]`, return `[2]`.

__Note:__

* Each element in the result must be unique.


* The result can be in any order.

__Related Topics:__ Hash Table, Two Pointers, Binary Search, Sort

原问题: [349. Intersection of Two Arrays](https://leetcode.com/problems/intersection-of-two-arrays/description/)

中文翻译版: [349. 两个数组的交集](https://leetcode-cn.com/problems/intersection-of-two-arrays/description/)

## 解决方案

### 方案1

可以创建两个哈希表，首先遍历数组 nums1，将数组元素添加到第一个哈希表中，然后遍历数组 nums2，如果数组元素在第一个哈希表中不存在，则将该数组元素添加到第二个哈希表中，遍历完数组 nums2 后，将第二个哈希表的中元素作为结果输出。实现代码如下：

```
#include <iostream>
#include <vector>
#include <unordered_set>
using namespace std;

class Solution {
public:
    vector<int> intersection(vector<int>& nums1, vector<int>& nums2)
    {
        unordered_set<int> unique_nums1;
        for (auto it=nums1.begin(); it!=nums1.end(); it++)
            unique_nums1.insert(*it);

        unordered_set<int> unique_nums;
        for (auto it=nums2.begin(); it!=nums2.end(); it++) {
            if (unique_nums1.find(*it) != unique_nums1.end())
                unique_nums.insert(*it);
        }

        vector<int> ret;
        for (auto it=unique_nums.begin(); it!=unique_nums.end(); it++)
            ret.push_back(*it);

        return ret;
    }
};

int main()
{
    Solution solu;
    vector<int> nums1 = {1, 2, 2, 1};
    vector<int> nums2 = {2, 2};
    vector<int> ret = solu.intersection(nums1, nums2);

    for (auto it=ret.begin(); it!=ret.end(); it++)
        cout << *it << " ";
    cout << endl;

    return 0;
}
```

### 方案2

将数组 nums1 和数组 nums2 进行从小到大排序，设置两个指针 p1 和 p2 分别指向数组 nums1 和数组 nums2 的第一个元素。开始遍历数组时，如果指针 p1 和指针 p2 指向的元素不相等，则移动指向元素中较小值的指针，如果指针 p1 和指针 p2 指向的元素相等，则同时移动指针 p1 和 p2，并记录该相等的元素 (找到相等元素时先判断该元素是否和这个记录值相等，如果相等则说明交集已经存在该元素，不需要添加到交集中，否则添加到交集中)，依次操作，直到其中一个集合没有元素可比较为止。实现代码如下：

```
#include <algorithm>
#include <vector>
#include <iostream>
using namespace std;

class Solution {
public:
    vector<int> intersection(vector<int>& nums1, vector<int>& nums2)
    {
        vector<int> ret;
        if (nums1.size() == 0)
            return ret;
        else if (nums2.size() == 0)
            return ret;

        sort(nums1.begin(), nums1.end());
        sort(nums2.begin(), nums2.end());

        int p1, p2, repeat;
        p1 = p2 = 0;
        repeat = nums1[0] < nums2[0] ? nums1[0] - 1 : nums2[0] - 1;
        while (p1 < nums1.size() && p2 < nums2.size()) {
            if (nums1[p1] < nums2[p2]) {
                p1++;
            } else if (nums1[p1] > nums2[p2]) {
                p2++;
            } else {
                if (repeat != nums1[p1])
                    ret.push_back(nums1[p1]);
                repeat = nums1[p1];
                p1++;
                p2++;
            }
        }

        return ret;
    }
};

int main()
{
    Solution solu;
    vector<int> nums1 = {1, 2, 2, 1};
    vector<int> nums2 = {2, 2};
    vector<int> ret = solu.intersection(nums1, nums2);

    for (auto it=ret.begin(); it!=ret.end(); it++)
        cout << *it << " ";
    cout << endl;

    return 0;
}
```
