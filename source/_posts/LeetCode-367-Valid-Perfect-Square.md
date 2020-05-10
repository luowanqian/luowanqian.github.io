---
title: LeetCode 367 - Valid Perfect Square
urlname: leetcode_367
tags:
  - LeetCode
  - 算法
  - Algorithm
date: 2020-05-10 09:19:53
---

## 问题描述

Given a positive integer `num`, write a function which returns True if `num` is a perfect square else False.

__Note:__ __Do not__ use any built-in library function such as `sqrt`.

__Example 1:__

```
Input: 16
Output: true
```

__Example 2:__

```
Input: 14
Output: false
```

__Related Topics:__ `Math`, `Binary Search`

原问题: [367. Valid Perfect Square](https://leetcode.com/problems/valid-perfect-square/)

中文翻译版: [367. 有效的完全平方数](https://leetcode-cn.com/problems/valid-perfect-square/)

## 解决方案

本题换种说法就是：求正整数 `num` 的平方根 `x`，且 `x` 的平方等于 `num`，如何求正整数的平方根，可以用二分查找进行求解，这个可以参考LeetCode原题 [69. Sqrt(x)](https://leetcode.com/problems/sqrtx/)（解题报告：{% post_link LeetCode-69-SqrtX LeetCode 69 - Sqrt(x) %}），二分查找后得到的平方根 `x`，只需判断 `x` 的平方是否等于 `num` 即可判断 `num` 是否为完全平方数


<details>
<summary>参考解题代码</summary>

```cpp
#include <iostream>
using namespace std;


class Solution {
public:
    bool isPerfectSquare(int num) {
        long long low, mid, high;

        low = 0;
        high = (long)num + 1;
        while (low < high) {
            mid = low + (high - low) / 2;
            if (mid * mid < num)
                low = mid + 1;
            else
                high = mid;
        }

        // if it doesn't find square of number
        if (low * low != num)
            return false;

        return true;
    }
};


int main()
{
    int num;
    Solution solu;

    // max of int
    // num = 2147483647;
    num = 14;
    cout << num << " is perfect square: "
         << solu.isPerfectSquare(num) << endl;
    return 0;
}
```

</details>