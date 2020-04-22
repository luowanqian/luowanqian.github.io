---
title: LeetCode 50 - Pow(x, n)
urlname: leetcode_50
tags:
  - LeetCode
  - 算法
  - Algorithm
date: 2020-04-22 23:06:28
---

## 问题描述

Implement [pow(x, n)](http://www.cplusplus.com/reference/valarray/pow/), which calculates $x$ raised to the power $n$ ($x^n$).

__Example 1:__

```
Input: 2.00000, 10
Output: 1024.00000
```

__Example 2:__

```
Input: 2.10000, 3
Output: 9.26100
```

__Example 3:__

```
Input: 2.00000, -2
Output: 0.25000
Explanation: 2^(-2) = (1/2)^2 = 1/4 = 0.25
```

__Note:__

* $-100.0 \lt x \lt 100.0$
* $n$ is a 32-bit signed integer, within the range $[-2^{31}, 2^{31}-1]$

__Related Topics:__ `Math`, `Binary Search`

原问题: [50. Pow(x, n)](https://leetcode.com/problems/powx-n/)

中文翻译版: [50. Pow(x, n)](https://leetcode-cn.com/problems/powx-n/)

## 解决方案

题目中 $n$ 是整数，此时求 $x$ 的 $n$ 次方可以分解为两个 $x$ 的 $n/2$ 次方相乘，即：

$$
x^n = \begin{cases}
x^{n/2} \cdot x^{n/2}  & n \text{ is even} \\
x^{n/2} \cdot x^{n/2} \cdot x & n \text{ is odd}
\end{cases}
$$

则此题可以用递归进行求解，需要注意的是如果 $n$ 是负数，不能在代码里将 $n$ 转为正数，$x$ 转为 $1/x$，因为该题的测试用例中会有 $n = -2^{31}$ 这种取值，如果取正会导致数值溢出，解决办法是当 $n$ 为奇数时，此时

$$
x^{n} = x^{n/2} \cdot x^{n/2} \cdot \frac{1}{x} 
$$

<details>
<summary>参考解题代码</summary>

```cpp
#include <iostream>
using namespace std;

class Solution {
public:
    double myPow(double x, int n) {
        // do not transfer n to -n if n < 0
        // because of numerical overflow (n = -2^31)
        if (n == 0)
            return 1.0;
        double half = myPow(x, n/2);
        if (n % 2 == 0) {
            return half * half;
        } else {
            if (n < 0)
                x = 1 / x;
            return half * half * x;
        }
    }
};

int main()
{
    double x;
    int n;
    Solution solu;

    x = 1.00000;
    n = -2147483648;    // n = -2^31
    cout << "Pow(" << x << ", " << n << ") = "
         << solu.myPow(x, n) << endl;
    return 0;
}
```

</details>
