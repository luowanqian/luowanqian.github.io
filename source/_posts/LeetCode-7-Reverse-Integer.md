---
title: LeetCode 7 - Reverse Integer
urlname: leetcode_7
tags:
  - LeetCode
  - 算法
date: 2018-07-15 23:07:07
---

## 问题描述

Given a 32-bit signed integer, reverse digits of an integer.

__Example 1:__

```
Input: 123
Output: 321
```

__Example 2:__

```
Input: -123
Output: -321
```

__Example 3:__

```
Input: 120
Output: 21
```

__Note:__

Assume we are dealing with an environment which could only store integers within the 32-bit signed integer range: $[−2^{31},  2^{31} − 1]$. For the purpose of this problem, assume that your function returns 0 when the reversed integer overflows.

__Related Topics:__ Math

原问题: [7. Reverse Integer](https://leetcode.com/problems/reverse-integer/description/)

中文翻译版: [7. 反转整数](https://leetcode-cn.com/problems/reverse-integer/description/)

## 解决方案

解决思路就是：利用求模得到数字的每一位，求位的顺序要从低位到高位，然后根据获得的数位构造出逆数。这里有个困难就在求解过程中会发生数字溢出，一个解决方案就是用更多字节的整数类型来表示逆数，题目中使用的整数int是四个字节，此时可以用八字节的long long代替，然后判断数是否在32位整数表示的范围即可。代码如下：

```
#include <iostream>
using namespace std;

class Solution {
public:
    int reverse(int x) {
        int mod;
        long long num;
        bool postive;

        num = 0;
        postive = x >=0 ? true : false;
        while (x) {
            mod = x % 10;
            x = x / 10;
            num = num * 10 + mod;
            if ((postive && num > 0x7FFFFFFF)
                || (!postive && num < (signed int)0x80000000))
                return 0;
        }

        return num;
    }
};

int main()
{
    int x = 120;

    Solution solu;
    cout << "Revert: " << solu.reverse(x) << endl;

    return 0;
}
```
