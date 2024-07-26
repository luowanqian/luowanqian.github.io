---
title: LeetCode 191 - Number of 1 Bits
urlname: leetcode_191
date: 2018-04-03 12:27:25
tags:
 - LeetCode
 - 算法
---

## 问题描述

Write a function that takes an unsigned integer and returns the number of '1' bits it has (also known as the [Hamming weight](http://en.wikipedia.org/wiki/Hamming_weight)).

For example, the 32-bit integer '11' has binary representation `00000000000000000000000000001011`, so the function should return 3.

__Related Topics:__ Bit Manipulation

原问题：[191. Number of 1 Bits](https://leetcode.com/problems/number-of-1-bits/description/)

## 解决方案

这个问题需要要用到位运算中的与运算`&`，与运算规则如下所示

```
0 & 0 = 0
0 & 1 = 0
1 & 0 = 0
1 & 1 = 1
```

### 方案1

第一个解决方案是先判断数字二进制表示中最右边一位是不是1；接着把输入的数字右移一位，此时原来处于从右边数起的第二位被移到最右边了，再判断是不是1；这样每次移动一位，直到数字变成0为止。这个方案关键之处在于怎么判断一个数字的二进制表示的最右边一位是不是1。其实这个可以用与运算解决，只要把这个数字和1做与运算看结果是不是0就知道了，如果数字与1做与运算的结果是1，则表示数字的二进制表示的最右边一位是1，否则是0。

> 补充：这个解决方案针对无符号数字有效，对于有符号数字无效，因为有符号数字右移时，最高位会补1，此时如果使用数字变为0作为循环结束条件时，会陷入死循环，因此应该用for循环，循环次数为数字二进制的位数。

{% codeblock %}
class Solution
{
public:
    int hammingWeight(uint32_t n)
    {
        int num_bits;

        num_bits = 0;
        while (n) {
            if (n & 1)
                num_bits++;
            n = n >> 1;
        }

        return num_bits;
    }
};
{% endcodeblock %}

### 方案2

第二个解决方案是首先把数字与1做与运算，判断数字的二进制表示的最低位是不是1，接着把1左移1位，再和数字做与运算，就能判断数字的二进制表示的次低位是不是1，这样反复左移，直到所有位都被进行过与运算。

{% codeblock %}
class Solution
{
public:
    int hammingWeight(uint32_t n)
    {
        int num_bits;
        uint32_t flag;

        num_bits = 0;
        flag = 1;
        while (flag) {
            if (flag & n)
                num_bits++;
            flag = flag << 1;
        }

        return num_bits;
    }
};
{% endcodeblock %}

这个方案的循环次数等于数字的二进制的位数，方案一的循环次数由该数字的二进制表示中的最高位1的位置决定，在最坏情况下，方案一的循环次数等于数字的二进制的位数。
