---
title: LeetCode 136 - Single Number
urlname: leetcode_136
date: 2018-03-12 08:33:13
tags:
 - 算法
 - LeetCode
---

> 问题描述：
>
> Given an array of integers, every element appears twice except for one. Find that single one.
>
> __Note:__
> Your algorithm should have a linear runtime complexity. Could you implement it without using extra memory?

## 时间复杂度为 O(n) 算法

算法要用到异或运算，异或运算 的规律如下所示：

> 0 xor 0 = 0
>
> 0 xor 1 = 1
>
> 1 xor 0 = 1
>
> 1 xor 1 = 0

很容易验证一个异或运算的性质：任何一个数字异或它自己都等于0，试想有个数组 $[A, B, A, C, B]$，数字 $A$ 和数字 $B$ 重复出现两次，数字 $C$ 只出现一次，如果我们将数组所有数字进行异或运算，会出现什么情况？首先，可以写出一个表达式
$$
A \text{ xor } B \text{ xor } A \text{ xor } C \text{ xor B}
$$

由于异或运算满足交换律和结合律，因此可以得到下面这个表达式
$$
(A \text{ xor } A ) \text{ xor } ( B \text{ xor } B)  \text{ xor C}
$$
由于 $A \text{ xor } A = 0$ 以及 $B \text{ xor } B = 0$，并且0与任何数异或都为原来的数，因此上面的表达式最终化简为
$$
(A \text{ xor } A ) \text{ xor } ( B \text{ xor } B)  \text{ xor C} = C
$$
可见，对数组所有数进行异或运算，最终结果就是那个只出现一次的数。知道算法原理，我们可以很容易写出实现代码

{% codeblock %}
#include <iostream>
#include <vector>
using namespace std;

class Solution {
public:
    int singleNumber(vector<int>& nums) {
        int ret = 0;
        for (auto it=nums.begin(); it!=nums.end(); ++it)
            ret ^= *it;

        return ret;
    }
};

int main()
{
    Solution solu;

    vector<int> v1 = {2, 3, 2, -1, 3};
    cout << "Single Number is: " << solu.singleNumber(v1) << endl;

    return 0;
}
{% endcodeblock %}
