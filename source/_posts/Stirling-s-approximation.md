---
title: Stirling's approximation
date: 2018-07-14 21:36:10
urlname: stirling_approximation
tags:
 - 数学
---

## 介绍

斯特林公式 (Stirling's approximation) 是一条用来取n的阶乘的近似值的数学公式。常见的形式为

$$
n! \approx \sqrt{2 \pi n} (\frac{n}{e})^n
$$

对n的阶乘取自然对数，可以得到

$$
\ln{(n!)} \approx \frac{1}{2} \ln{(2 \pi)} + (n + \frac{1}{2}) \ln{(n)} - n
$$

一个会经常遇到的问题，那就是计算 $n! / a_n$，单独计算 $n!$ 需要很大的计算量，如果将问题转换为

$$
\frac{n!}{a_n} = e^{\ln{(n!)} - \ln{(a_n)}}
$$

$\ln{(n!)}$ 可以用前面介绍的近似公式进行求解，此时计算量就会小很多。

## 例子

这里举一个例子来感受下这个近似公式是如何缩减大量计算量。一个经典的概率问题－Birthday Problem

> __Birthday Prolem__
>
> Calculate the probability p that at least two people in a group of k people will have the same birthday, that is, will have been born on the same day of the same month but not necessarily in the same year.

可以很快地想到

> 至少两个人的生日相同的概率 = 1 - 所有人的生日不相同的概率

则计算公式为

$$
p = 1 - \frac{P_{365, k}}{365^k}
$$

其中 $P_{365, k}$ 为排列公式，等于 $365! / (365 - k)!$，则概率 $p$ 为

$$
p = 1 - \frac{365!}{(365 - k)!365^k}
$$

如果按照这个式子一个个计算里面的每一项时，需要很大的计算量，尤其是 $365!$，一般计算器估计算不出来。此时，我们将式子用指数，对数进行改写，可写为

$$
p = 1 - e^{\ln{(365)!} - \ln{(365-k)!} - k\ln{(365)}}
$$

其中 $\ln{(365)!}$ 和 $\ln{(365-k)!}$ 可以用Stirling公式进行近似计算，可以得到

$$
\begin{align}
& \ln{(365)!} - \ln{(365-k)!} - k \ln{(365)}  \\
\approx \; & \left[\frac{1}{2} \ln{(2 \pi)} + (365 + \frac{1}{2}) \ln{(365)} - 365 \right] \\
& - \left[ \frac{1}{2} \ln{(2 \pi)} + (365 - k + \frac{1}{2}) \ln{(365-k)} - 365 + k \right] \\
& - k \ln{(365)} \\
\approx \; & (365 + \frac{1}{2}) \ln{(365)} - (365 - k + \frac{1}{2}) \ln{(365-k)} - k - k \ln {(365)}
\end{align}
$$

可见，阶乘和指数部分变成一堆对数的乘积以及求和，这样计算量就小了很多。这里贴一下用Python实现的概率计算程序


```
import math
import sys


if __name__ == '__main__':
    k = int(sys.argv[1])
    sum = ((365 + 0.5) * math.log(365) -
           (365 - k + 0.5) * math.log(365 - k) -
           k - k * math.log(365))
    prob = 1.0 - math.exp(sum)
    print('Probability (k = {}) is {}'.format(k, prob))

```

用程序计算了一些值，得到下面列表

| k | prob | | k | prob |
| -- | -- | -- | -- | -- |
| 5 | 0.027 | | 30 | 0.706 |
| 10 | 0.117 | | 40 | 0.891 |
| 15 | 0.253 | | 50 | 0.970 |
| 20 | 0.411 | | 60 | 0.994 |
| 25 | 0.569 | | 70 | 0.999 |

从表格中可以看到，$k = 70$ 时，概率很接近 $1$ 了，原以为至少需要100多人才可能达到0.999这个概率值，可是现在看来是不需要达到100人就可以了。这让我想起了在班里，有人的生日和我是同一天，整个班的人数大概是50人，根据表格，可以得到概率为0.970，由此可见班里同学生日和我同一天的概率还是挺高的。

