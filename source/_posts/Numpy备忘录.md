---
title: Numpy备忘录
date: 2018-05-03 23:10:53
urlname: numpy_memo
tags:
 - Python
 - Numpy
---

## 介绍

本文主要是记录一些Numpy的使用方法以及注意事项。

__Note:__ 如果没有特别说明，`np` 指的是 `numpy`，代表导入的 numpy

```
import numpy as np
```

PS: 网上找到一份Numpy的CheatSheet，内容不错，感兴趣的可以去下载：[Numpy CheatSheet](https://www.dataquest.io/blog/numpy-cheat-sheet/)

## 转换ndarray数据类型

如果想转换ndarray的数据类型，可以使用ndarray的 `astype` 方法

```python
>>> arr = np.array([1, 2, 3, 4, 5])
>>> arr.dtype
dtype('int64')
>>> float_arr = arr.astype(np.float64)
>>> float_arr.dtype
dtype('float64')
```

__Note:__ 调用 `astype` 方法会生成新的数组，因此需要赋值到一个变量上。

## 切片不会生成新的数组

对数组进行切片后，返回的数组并不是原始数组的拷贝，只是一个对原始数组的引用，如果对切片后的数组进行数据修改，原始数组相应的位置数据会被修改。

```
>>> arr = np.arange(10)
>>> arr
array([0, 1, 2, 3, 4, 5, 6, 7, 8, 9])
>>> new_arr = arr[5:8]
>>> new_arr
array([5, 6, 7])

>>> new_arr[:] = 12
>>> new_arr
array([12, 12, 12])
>>> arr
array([ 0,  1,  2,  3,  4, 12, 12, 12,  8,  9])
```

如果想要切片后的数组是原始数组的拷贝，需要调用 `copy()` 方法。

```
>>> new_arr = arr[5:8].copy()
```

## Axis编号

调用一些Numpy函数时会遇到设置 `axis` 参数，该参数可以设为 `0` 或 `1` ，对于这两个值表示的意义，可以参考下图

{% asset_img 1.png %}

也就是说`axis 0` 代表行方向，`axis 1` 代表列方向。例如，我们使用 `mean()` 计算矩阵的平均值时，`A.mean(axis=0)` 代表每个平均值是沿着行方向计算可得，`A.mean(axis=1)` 代表每个平均值是沿着列方向计算可得。

```
>>> A = np.random.randn(2, 3)
>>> A
array([[-0.40797393,  0.24059956, -1.57582642],
       [ 0.31626161, -0.07033558,  0.58346107]])
>>> A.mean(axis=0)
array([-0.04585616,  0.08513199, -0.49618267])
>>> A.mean(axis=1)
array([-0.58106693,  0.27646237])
```

`A.mean(axis=0)` 大小为矩阵A列的个数，`A.mean(axis=1)`大小为矩阵A行的个数。

## 统计Boolean数组中True的个数

Boolean值通常可以转换成 0 (False) 或 1 (True)，因此可以使用 `sum()` 函数统计Boolean数组中 True 的个数

```
>>> bools = np.array([False, True, True, False, False])
>>> bools.sum()
2
>>> np.sum(bools)
2
```

既然可以知道数组中 True 的个数，自然也可以知道 True 所占的比例，此时可以用 `mean()` 函数进行计算

```
>>> bools.mean()
0.4
>>> np.mean(bools)
0.4
```