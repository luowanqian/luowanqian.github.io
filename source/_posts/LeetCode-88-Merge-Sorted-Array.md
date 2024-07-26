---
title: LeetCode 88 - Merge Sorted Array
date: 2018-03-10 15:54:23
urlname: leetcode_88
tags:
 - 算法
 - LeetCode
---

> 问题描述：
>
> Given two sorted integer arrays nums1 and nums2, merge nums2 into nums1 as one sorted array.
>
> __Note:__
> You may assume that nums1 has enough space (size that is greater or equal to m + n) to hold additional elements from nums2. The number of elements initialized in nums1 and nums2 are m and n respectively.

题目中说数组nums1大小为 m，数组nums2的大小为 n，要将数组nums2中的元素全部放到数组nums1中且保证数组nums1有序（这里有序假设是从小到大）。

## 时间复杂度为 O(nm) 的解法

初看到这个题目，一个最直观的想法就是设定两个指针分别指向两个数组开始的元素（令指针p1指向数组nums1，p2指向数组p2），然后从头开始遍历两个数组。

- 如果指针p1指向的元素小于等于指针p2指向的元素，指针p1加一。
- 如果指针p1指向的元素大于指针p2指向的元素，将指针p2指向的元素插入到指针p1指向的位置，指针p1指向的位置后面的元素都后移一位，指针p1和指针p2都加一。

这样操作直到数组nums1或数组nums2的元素遍历完，如果是数组nums1的元素先被遍历完，则将数组nums2剩下的元素都复制到数组nums1中，如果是数组nums2的元素先被遍历完，算法停止。在最坏的情况下，数组nums2的元素都小于数组nums1的元素，每次插入数组nums2的元素，都要移动数组nums1的 $O(m)$ 个字符，那么算法复杂度为 $O(nm)$。

## 时间复杂度为 O(n + m) 的解法

换个思路，我们不从头开始遍历数组，而是从数组后面开始遍历数组。这里我们准备三个指针：p、p1和p2，指针p指向数组nums1的第 n + m 个元素，指针p1指向数组nums1最后一个元素，指针p2指向数组nums2的最后一个元素。

* 如果指针p1指向的元素小于指针p2指向的元素，将指针p2指向的元素复制到指针p指向的位置，指针p和p2减一。
* 如果指针p1指向的元素大于等于指针p2指向的元素，将指针p1指向的元素复制到指针p指向的位置，指针p和p1减一。

这样操作直到数组nums1或数组nums2被遍历完，如果是数组nums1先被遍历完，将数组nums2剩余的元素复制到数组nums1中，如果是数组nums2先被遍历完，则算法停止。从上面分析我们可以看出，只需要遍历一次数组nums1和数组nums2就行，不需要移动元素，因此这个算法的时间复杂度为 $O(n + m)$。

{% codeblock %}
#include <iostream>
#include <vector>
using namespace std;

class Solution {
public:
    void merge(vector<int>& nums1, int m, vector<int>& nums2, int n) {

        if (m <= 0)
            nums1 = nums2;
        if (n <= 0)
            return;

        int p, p1, p2;
        p1 = m - 1;
        p2 = n - 1;
        p = m + n - 1;

        while (p >= 0 && p1 >= 0 && p2 >= 0) {
            if (nums1[p1] >= nums2[p2]) {
                nums1[p] = nums1[p1];
                p1--;
            } else if (nums1[p1] < nums2[p2]) {
                nums1[p] = nums2[p2];
                p2--;
            }
            p--;
        }

        if (p2 >= 0) {
            while (p >=0 && p2 >=0)
                nums1[p--] = nums2[p2--];
        }
    }
};

int main()
{
    Solution solu;

    vector<int> nums1 = {1, 3, 5, 7, 0, 0, 0};
    vector<int> nums2 = {2, 4, 6};
    solu.merge(nums1, 4, nums2, nums2.size());

    for (auto it=nums1.begin(); it!=nums1.end(); ++it)
        cout << *it << " ";
    cout << endl;

    vector<int> nums3 = {0, 0, 3, 0, 0, 0, 0, 0, 0};
    vector<int> nums4 = {-1, 1, 1, 1, 2, 3};
    solu.merge(nums3, 3, nums4, nums4.size());

    for (auto it=nums3.begin(); it!=nums3.end(); ++it)
        cout << *it << " ";
    cout << endl;

    return 0;
}
{% endcodeblock %}
