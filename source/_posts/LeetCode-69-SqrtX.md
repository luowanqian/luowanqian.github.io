---
title: LeetCode 69 - Sqrt(x)
urlname: leetcode_69
tags:
  - LeetCode
  - 算法
  - Algorithm
date: 2020-04-23 22:17:53
---

## 问题描述

Implement `int sqrt(int x)`.

Compute and return the square root of $x$, where $x$ is guaranteed to be a non-negative integer.

Since the return type is an integer, the decimal digits are truncated and only the integer part of the result is returned.

__Example 1:__

```
Input: 4
Output: 2
```

__Example 2:__

```
Input: 8
Output: 2
Explanation: The square root of 8 is 2.82842..., and since 
             the decimal part is truncated, 2 is returned.
```

__Related Topics:__ `Math`, `Binary Search`

原问题: [69. Sqrt(x)](https://leetcode.com/problems/sqrtx/)

中文翻译版: [69. x 的平方根](https://leetcode-cn.com/problems/sqrtx/)

## 解决方案

首先 $x$ 的平方根取值范围为 $[0, x]$，该区间中可能是 $x$ 的平方根的数要求满足其平方小于等于 $x$。题目中要求输出的是整数，此时问题变为在区间 $[0, x]$ 中寻找满足 $s^2 <= x$ 条件的最大的整数 $s$。

很自然地，我们可以从 $0$ 开始遍历，直到某个值的平方大于 $x$，此时前一个值就是所求的平方根，但是这种解法一般会超时。既然是在一个离散的区间内进行查找，并且区间的元素是有序的，此时我们可以用二分查找 (`Binary Search`) 快速找到我们想要的值。

<details>
<summary>参考解题代码</summary>

```cpp
/*
 * Use binary search to find the square root of x
 */

#include <iostream>
using namespace std;


class Solution {
public:
    int mySqrt(int x) {
        long long low, high, mid, square;

        // search in [low, high), so high = x + 1
        // use casting to avoid numerical overflow
        low = 0, high = (long long)x + 1;
        while (low < high) {
            mid = low + (high - low) / 2;
            square = mid * mid;
            if (square <= x)
                low = mid + 1;
            else
                high = mid;
        }
        return low - 1;
    }
};

int main()
{
    int x = 2147483647;
    Solution solu;

    cout << "Sqrt(" << x << ") = " << solu.mySqrt(x) << endl;
    return 0;
}
```

这里参考了知乎一个关于二分查找问题的回答 [二分查找有几种写法？它们的区别是什么？](https://www.zhihu.com/question/36132386/answer/530313852)，因为我们要找的是 $s^2 <= x$ (等价于 $s <= \sqrt{x}$) 的上界，所以参考了 `upper_bound(value) - 1` 的写法。

</details>