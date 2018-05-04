---
title: LeetCode 215 - Kth Largest Element in an Array
urlname: leetcode_215
date: 2018-03-09 23:31:35
tags:
 - 算法
 - LeetCode
---

> 问题描述：
>
> Find the kth largest element in an unsorted array. Note that it is the kth largest element in the sorted order, not the kth distinct element.
> For example,
> Given [3,2,1,5,6,4] and k = 2, return 5.
>
> Note:
> You may assume k is always valid, 1 ≤ k ≤ array's length.

这道题目最简单的思路莫过于把输入的 $n$ 个数进行排序，排序之后第 $k$ 个数字 (如果数是从大到小排序) 就所要求的第 $k$ 大的数字，这种思路的时间复杂度是 $O(nlog(n))$ 。但是这样还不是最快的做法，接下来讨论更快的算法。

## 时间复杂度为 O(nlog(k)) 的算法

我们可以先创建一个大小为 $k$ 的数据容器来存储最大的 $k$ 个数，接下来每次从输入的 $n$ 个数中读入一个数。如果容器中已有的数字少于 $k$ 个，则直接把这次读入的数放入容器之中；如果容器中已有 $k$ 个数字了，也就是容器已经满了，此时只能替换容器中的数字了。找出容器中 $k$ 个数的最小值，然后拿这次待插入的数与其比较，如果待插入的数字比容器中的最小值要大，则用这个数替换当前已有的最小值，如果待插入的数字比这个最小值要小，那么这个数不能是最大的 $k$ 个数之一，则抛弃这个数字。

 因此，当容器满了以后，我们要做三件事：一是在 $k$ 个数中找到最小数；二是有可能在这个容器中删除最小数； 三是有可能要插入一个新的数字。如果使用一棵二叉树来实现这个容器，那么我们能在 $O(log(k))$ 的时间内实现这三步操作。因此，对于 $n$ 个输入数字而言，总的时间效率是 $O(n log(k))$。

由于每次都需要找到 $k$ 个数中的最小值，很自然可以想到用最小堆。当然，我们还可以考虑是用红黑树来实现这个容器，红黑树可以保证查找、删除和插入这些操作都只需要 $O(log(k))$ 时间，在STL中，set 和 multiset 都是基于红黑树实现。

### 基于最小堆实现

{% codeblock %}

#include <queue>
#include <iostream>
using namespace std;

class Solution {
public:
    int findKthLargest(vector<int>& nums, int k) {
        priority_queue<int, vector<int>, greater<int>> k_numbers;
        int min_number;

        for (auto it=nums.begin(); it!=nums.end(); ++it) {
            if (k_numbers.size() < k) {
                k_numbers.push(*it);
            } else {
                min_number = k_numbers.top();
                if (*it > min_number) {
                    k_numbers.pop();
                    k_numbers.push(*it);
                }
            }
        }

        return k_numbers.top();
    }
};

int main()
{
    Solution solu;
    vector<int> nums = {3, 2, 1, 5, 6, 4};

    cout << "The 2th max number is " << solu.findKthLargest(nums, 2) << endl;

    return 0;
}

{% endcodeblock %}

### 基于红黑树实现

{% codeblock %}

#include <vector>
#include <set>
#include <iostream>
using namespace std;

class Solution {
public:
    int findKthLargest(vector<int>& nums, int k) {
        multiset<int> k_numbers;
        multiset<int>::iterator set_iterator;

        for (auto it=nums.begin(); it!=nums.end(); ++it) {
            if (k_numbers.size() < k) {
                k_numbers.insert(*it);
            } else {
                set_iterator = k_numbers.begin();
                if (*it > *set_iterator) {
                    k_numbers.erase(set_iterator);
                    k_numbers.insert(*it);
                }
            }
        }

        return *(k_numbers.begin());
    }
};

int main()
{
    Solution solu;
    vector<int> nums = {3, 2, 1, 5, 6, 4};

    cout << "The 2th max number is " << solu.findKthLargest(nums, 2) << endl;

    return 0;
}
{% endcodeblock %}
