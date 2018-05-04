---
title: LeetCode 15 - 3Sum
date: 2018-03-30 23:29:51
urlname: leetcode_15
tags:
 - LeetCode
 - 算法
---

## 问题描述

Given an array S of n integers, are there elements a, b, c in S such that a + b + c = 0? Find all unique triplets in the array which gives the sum of zero.

__Note:__ The solution set must not contain duplicate triplets.

> For example, given array S = [-1, 0, 1, 2, -1, -4],
>
> A solution set is:
>
> [ [-1, 0, 1], [-1, -1, 2] ]

__Related Topics:__ Array, Two Pointers

原问题：[15. 3Sum](https://leetcode.com/problems/3sum/description/)

## 解决方案

这个问题是 [2Sum](https://leetcode.com/problems/two-sum/description/) 问题的一个扩展，题目不仅要求输出满足条件的数字对，而且要保证数字对不能重复。咋一看问题有点无从下手，但是 3Sum 问题可以转换成 2Sum 问题，如果我们固定数字 $a$，那么剩下的问题就变成 2Sum 问题了，也就是寻找数字 $b$ 和数字 $c$，使得 $b+c = -a$，这里有个问题就是如何选取数字 $a$，看题目给的例子的输出，我们发现输出的数字对里面的元素是有序的，这里启示我们可以作一个假设，那就是 $a \le b \le c$，因此关于如何选取数字 $a$ 可以先对输入的数组进行从小到大排序，然后从头开始遍历数组，这里设访问到的第 $i$ 个元素作为数字 $a$，然后数组 $i+1$ 的元素到最后一个元素作为数字 $b$ 和数字 $c$ 的搜索区间，此时问题已经变成一个有序数组的 2Sum 问题，这个问题解决思想类似LeetCode的一道题目—[Two Sum II - Input array is sorted](https://leetcode.com/problems/two-sum-ii-input-array-is-sorted/description/)，需要两个指针移动来进行搜索 (关于这个问题，可以参考{% post_link LeetCode-167-Two-Sum-II-Input-array-is-sorted 我的文章%})，区别在于这里需要找到多组数字 $b$ 和数字 $c$。

这道题目还有一个难点，就是解决重复的数字对，根据前面的描述，我们所遇到的重复有两个，一个是数字 $a$ 会重复，一个是数字对 $(b, c)$ 会重复，解决方案如下：

* 遇到数字 $a$ 重复，跳到下一个数字作为数字 $a$
* 在搜索满足条件的数字对 $(b, c)$ 时，如果搜索到满足条件的数字对 $(b, c)$ 后，两个指针同时移动，直到指针指向的元素不是之前搜索到的数字对 $(b, c)$

说了这么多，读者理解起来可能有点困难，还是看实现代码更容易理解前面所说的思路。

{% codeblock %}
#include <iostream>
#include <algorithm>
#include <vector>
using namespace std;

class Solution
{
public:
    vector<vector<int>> threeSum(vector<int>& nums)
    {
        vector<vector<int>> triplets;

        if (nums.size() < 3)
            return triplets;

        sort(nums.begin(), nums.end());
        int left, right, target;
        for (int i=0; i<nums.size(); i++) {
            if (i == 0 || nums[i] > nums[i-1]) {
                target = -nums[i];
                left = i+1;
                right = nums.size()-1;
                while (left < right) {
                    if (nums[left] + nums[right] < target) {
                        left++;
                    } else if (nums[left] + nums[right] > target) {
                        right--;
                    } else {
                        triplets.push_back(vector<int>{nums[i], nums[left], nums[right]});
                        left++;
                        right--;
                        while (left < right && nums[left] == nums[left-1])
                            left++;
                        while (left < right && nums[right] == nums[right+1])
                            right--;
                    }
                }
            }
        }

        return triplets;
    }
};

int main()
{
    Solution solu;
    //vector<int> nums {1,0,1,2,-1,-4};
    vector<int> nums {0,-5,3,-4,1,3,-4,-2,-2,-2,0,3,0,1,-4,-2,0};

    vector<vector<int>> triplets = solu.threeSum(nums);
    for (int i=0; i<triplets.size(); i++) {
        for (int j=0; j<triplets[i].size(); j++) {
            cout << triplets[i][j] << " ";
        }
        cout << endl;
    }
    return 0;
}
{% endcodeblock %}
