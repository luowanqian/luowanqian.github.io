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

### 方案1

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
<summary>参考解题代码1</summary>

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

### 方案2

方案1是递归解法，这里介绍非递归解法。从二进制角度看整数 $n$，如果第 $k$ 位为1，说明 $x^n$ 可以表示为 $x^n = x^{2^k} \cdot x^{n-2^k}$，以此类推，将余下的 $x^{n-2^k}$ 根据非零位进行分解。例如 $n=5$，其二进制表示为 `101`，则根据非零位，我们可以得到以下分解:

$$
x^5 = x^{2^2} \cdot x^{2^0}
$$

根据分解可以得到一个迭代解法，就是计算结果初始值为 `ans = 1`，从第0位依次往高位对 $n$ 的二进制进行非零判断，如果 $n$ 的二进制第 $k$ 位非0，则 `ans` 乘上 $x^{2^k}$，即

$$
\text{ans} = \text{ans} \cdot x^{2^k}
$$

遍历完 $n$ 的所有二进制位，`ans` 就是我们求得的计算结果。

那么我们怎么快速得到 $x^{2^k}$ 呢？我们可以将 $n$ 不断进行`右移`操作，每移动1位，对 $x$ 就进行以下计算

```
x *= x
```

这样当我们右移 $k$ 次时，此时 $x$ 已经是原始 $x$ 的 $2^k$ 次方，此时我们用位与运算判断第1位是否非0，如果非0，按照前面迭代过程可得，最终计算结果 `ans` 需要乘上 $x$。

<details>
<summary>参考解题代码2</summary>

```cpp
#include <iostream>
using namespace std;


class Solution {
public:
    double myPow(double x, int n) {
        if (n == 0)
            return 1.0;

        long long num = n;
        double ans;

        if (n < 0) {
            // use long long type to avoid numerical overflow
            num = -(long long)n;
            x = 1 / x;
        }

        ans = 1.0;
        while (num > 0) {
            if ((num & 1) != 0)
                ans *= x;
            x *= x;
            num >>= 1;
        }

        return ans;
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