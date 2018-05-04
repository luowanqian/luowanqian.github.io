---
title: 剑指offer - 变态跳台阶
urlname: coding_interviews_crazy_steps
date: 2018-03-08 10:52:52
tags:
 - 算法
---

> 题目描述：一只青蛙一次可以跳上1级台阶，也可以跳上2级 …… 它也可以跳上n级。求该青蛙跳上一个n级的台阶总共有多少种跳法。

先从最简单的情况说起。如果只有1级台阶，跳法只有一种。如果有2级台阶，那就有两种跳法：一种是分两次跳，每次跳1级；另一种就是一次跳2级。如果有3级台阶，那就有四种跳法：一种是分三次跳，每次跳1级；一种是先跳2级，再跳1级；一种是先跳1级，再跳2级；还有一种是一次跳3级。

接下来讨论一般情况。我们把n级台阶时的跳法看成n的函数，记为$f(n)$。根据前面分析，可以得到
$$
\begin{aligned}
f(1) & = 1 = 2^0 \\
f(2) & = 2 = 2^{2-1} \\
f(3) & = 4 = 2^{3-1}
\end{aligned}
$$

可以看出一个规律，那就是$f(n) = 2^{n-1}$，那么怎么证明呢？先分析下n级台阶怎么跳，青蛙第一次跳有n中选择，可以第一次跳1级，此时后面台阶的跳法数目等于$f(n-1)$，如果第一次跳2级，此时后面台阶的跳法数目等于$f(n-2)$，一直到第一次直接跳$n$级，此时剩余台阶数为0，则跳法数目为$f(0)$，这里约定$f(0)=1$，为了后面分析方便。综上可以得到一个递推公式：
$$
f(n) = f(n-1) + f(n-2) + \cdots + f(2) + f(1) + f(0)
$$
接下来用数学归纳法证明$f(n)=2^{n-1}$。如果前$n$项满足公式$f(n)=2^{n-1}$，那么对于$f(n+1)$，可以得到
$$
\begin{align}
f(n+1) &= f(n) + f(n-1) + \cdots + f(1) + f(0) \\
& = 2^{n-1} + 2^{n-2} + \cdots + 2^0 + 1 \\
& = \frac{1 - 2^n}{1 - 2} + 1 \\
& = 2^n
\end{align}
$$
可看出$f(n+1)$仍然满足公式。知道通向公式后，剩下的实现就很简单了，贴上C++代码。

{% codeblock lang:cpp %}
#include <iostream>
using namespace std;

class Solution {
public:
    int jumpFloorII(int number) {
        if (number == 0)
            return 0;

        int f = 1;
        for (int i=1; i<number; i++)
            f *= 2;

        return f;
    }
};

int main()
{
    Solution solu;

    cout << "f(0)= " << solu.jumpFloorII(0) << endl;
    cout << "f(1)= " << solu.jumpFloorII(1) << endl;
    cout << "f(2)= " << solu.jumpFloorII(2) << endl;
    cout << "f(5)= " << solu.jumpFloorII(5) << endl;
    return 0;
}
{% endcodeblock %}
