---
title: 101 NumPy Exercises
urlname: 101_numpy_exercises
date: 2020-01-28 10:08:11
list_number: false
tags:
  - Python
  - Numpy
---

# 1. 将 NumPy 导入为 np，并查看版本

<details>
<summary>English Version</summary>

Title: Import numpy as np and see the version

Difficulty Level: L1

Question: Import numpy as np and print the version number.

----

</details>

难度：L1

问题：将 NumPy 导入为 np，并输出版本号。

<details>
<summary>Solution</summary>

```python
>>> import numpy as np
>>> print(np.__version__)
1.15.4
```

</details>

# 2. 如何创建 1 维数组？

<details>
<summary>English Version</summary>

Title: How to create a 1D array?

Difficulty Level: L1

Question: Create a 1D array of numbers from 0 to 9.

----

</details>

难度：L1

问题：创建数字从 0 到 9 的 1 维数组。

期望输出：

```python
array([0, 1, 2, 3, 4, 5, 6, 7, 8, 9])
```

<details>
<summary>Solution</summary>

```python
>>> arr = np.arange(10)
>>> arr
array([0, 1, 2, 3, 4, 5, 6, 7, 8, 9])
```

</details>

# 3. 如何创建 boolean 数组？

<details>
<summary>English Version</summary>

Title: How to create a boolean array?

Difficulty Level: L1

Question: Create a 3×3 numpy array of all True’s.

----

</details>

难度：L1

问题：创建所有值为 True 的 3×3 NumPy 数组。

<details>
<summary>Solution 1</summary>

```python
>>> np.full((3, 3), True)
array([[ True,  True,  True],
       [ True,  True,  True],
       [ True,  True,  True]])
```

</details>

<details>

<summary>Solution 2</summary>

```python
>>> np.ones((3, 3), dtype=bool)
array([[ True,  True,  True],
       [ True,  True,  True],
       [ True,  True,  True]])
```

</details>

# 4. 如何从 1 维数组中提取满足给定条件的项？

<details>
<summary>English Version</summary>

Title: How to extract items that satisfy a given condition from 1D array?

Difficulty Level: L1

Question: Extract all odd numbers from `arr`.

----

</details>

难度：L1

问题：从 `arr` 中提取所有奇数。

输入：

```python
>>> arr = np.array([0, 1, 2, 3, 4, 5, 6, 7, 8, 9])
```

期望输出：

```python
array([1, 3, 5, 7, 9])
```

<details>
<summary>Solution</summary>

```python
>>> arr[arr % 2 == 1]
array([1, 3, 5, 7, 9])
```

</details>

# 5. 如何将 NumPy 数组中满足给定条件的项替换成另一个数值？

<details>
<summary>English Version</summary>

Title: How to replace items that satisfy a condition with another value in numpy array?

Difficulty Level: L1

Question: Replace all odd numbers in `arr` with -1.

----

</details>

难度：L1

问题：将 `arr` 中的所有奇数替换成 -1。

输入：

```python
>>> arr = np.array([0, 1, 2, 3, 4, 5, 6, 7, 8, 9])
```

期望输出：

```python
array([ 0, -1, 2, -1, 4, -1, 6, -1, 8, -1])
```

<details>
<summary>Solution</summary>

```python
>>> arr[arr % 2 == 1] = -1
>>> arr
array([ 0, -1,  2, -1,  4, -1,  6, -1,  8, -1])
```

</details>

# 6. 如何在不影响原始数组的前提下替换满足给定条件的项？

<details>
<summary>English Version</summary>

Title: How to replace items that satisfy a condition without affecting the original array?

Difficulty Level: L2

Question: Replace all odd numbers in `arr` with -1 without changing `arr`.

----

</details>

难度：L2

问题：将 `arr` 中所有奇数替换成 -1，且不改变 `arr`。


输入：

```python
>>> arr = np.array([0, 1, 2, 3, 4, 5, 6, 7, 8, 9])
```

期望输出：

```python
>>> out
array([ 0, -1, 2, -1, 4, -1, 6, -1, 8, -1])
>>> arr
array([0, 1, 2, 3, 4, 5, 6, 7, 8, 9])
```

<details>
<summary>Solution 1</summary>

```python
>>> out = np.copy(arr)
>>> out[out % 2 == 1] = -1
>>> out
array([ 0, -1,  2, -1,  4, -1,  6, -1,  8, -1])
>>> arr
array([0, 1, 2, 3, 4, 5, 6, 7, 8, 9])
out
```

</details>

<details>
<summary>Solution 2</summary>

```python
>>> out = np.where(arr % 2 == 1, -1, arr)
>>> out
array([ 0, -1,  2, -1,  4, -1,  6, -1,  8, -1])
>>> arr
array([0, 1, 2, 3, 4, 5, 6, 7, 8, 9])
```

</details>

# 7. 如何重塑（reshape）数组？

<details>
<summary>English Version</summary>

Title: How to reshape an array?

Difficulty Level: L1

Question: Convert a 1D array to a 2D array with 2 rows.

----

</details>

难度：L1

问题：将 1 维数组转换成 2 维数组（两行）。

输入：

```python
>>> arr = np.arange(10)
>>> arr
array([0, 1, 2, 3, 4, 5, 6, 7, 8, 9])
```

期望输出：

```python
array([[0, 1, 2, 3, 4],
       [5, 6, 7, 8, 9]])
```

<details>
<summary>Solution</summary>

```python
>>> arr.reshape((2, -1))
array([[0, 1, 2, 3, 4],
       [5, 6, 7, 8, 9]])
```

</details>

# 8. 如何垂直堆叠两个数组？

<details>
<summary>English Version</summary>

Title: How to stack two arrays vertically?

Difficulty Level: L2

Question: Stack arrays `a` and `b` vertically.

----

</details>

难度：L2

问题：垂直堆叠数组 `a` 和 `b`。

输入：

```python
>>> a = np.arange(10).reshape(2, -1)
>>> b = np.repeat(1, 10).reshape(2, -1)
>>> a
array([[0, 1, 2, 3, 4],
       [5, 6, 7, 8, 9]])
>>> b
array([[1, 1, 1, 1, 1],
       [1, 1, 1, 1, 1]])
```

期望输出：

```python
array([[0, 1, 2, 3, 4],
       [5, 6, 7, 8, 9],
       [1, 1, 1, 1, 1],
       [1, 1, 1, 1, 1]])
```

<details>
<summary>Solution 1</summary>

```python
>>> np.concatenate((a, b), axis=0)
array([[0, 1, 2, 3, 4],
       [5, 6, 7, 8, 9],
       [1, 1, 1, 1, 1],
       [1, 1, 1, 1, 1]])
```

</details>

<details>
<summary>Solution 2</summary>

```python
>>> np.vstack((a, b))
array([[0, 1, 2, 3, 4],
       [5, 6, 7, 8, 9],
       [1, 1, 1, 1, 1],
       [1, 1, 1, 1, 1]])
```

</details>

<details>
<summary>Solution 3</summary>

```python
>>> np.r_[a, b]
array([[0, 1, 2, 3, 4],
       [5, 6, 7, 8, 9],
       [1, 1, 1, 1, 1],
       [1, 1, 1, 1, 1]])
```

</details>

# 9. 如何水平堆叠两个数组？

<details>
<summary>English Version</summary>

Title: How to stack two arrays horizontally?

Difficulty Level: L2

Question: Stack the arrays `a` and `b` horizontally.

----

</details>

难度：L2

问题：水平堆叠数组 `a` 和 `b`。

输入：

```python
>>> a = np.arange(10).reshape(2, -1)
>>> b = np.repeat(1, 10).reshape(2, -1)
>>> a
array([[0, 1, 2, 3, 4],
       [5, 6, 7, 8, 9]])
>>> b
array([[1, 1, 1, 1, 1],
       [1, 1, 1, 1, 1]])
```

期望输出：

```python
array([[0, 1, 2, 3, 4, 1, 1, 1, 1, 1],
       [5, 6, 7, 8, 9, 1, 1, 1, 1, 1]])
```

<details>
<summary>Solution 1</summary>

```python
>>> np.concatenate((a, b), axis=1)
array([[0, 1, 2, 3, 4, 1, 1, 1, 1, 1],
       [5, 6, 7, 8, 9, 1, 1, 1, 1, 1]])
```

</details>

<details>
<summary>Solution 2</summary>

```python
>>> np.hstack((a, b))
array([[0, 1, 2, 3, 4, 1, 1, 1, 1, 1],
       [5, 6, 7, 8, 9, 1, 1, 1, 1, 1]])
```

</details>

<details>
<summary>Solution 3</summary>

```python
>>> np.c_[a, b]
array([[0, 1, 2, 3, 4, 1, 1, 1, 1, 1],
       [5, 6, 7, 8, 9, 1, 1, 1, 1, 1]])
```

</details>

# 10. 在不使用硬编码的前提下，如何在 NumPy 中生成自定义序列？

<details>
<summary>English Version</summary>

Title: How to generate custom sequences in numpy without hardcoding?

Difficulty Level: L2

Question: Create the following pattern without hardcoding. Use only numpy functions and the below input array `a`.

----

</details>

难度：L2

问题：在不使用硬编码的前提下创建以下模式。仅使用 NumPy 函数和以下输入数组 `a`。

输入

```python
>>> a = np.array([1, 2, 3])
```

期望输出：

```python
array([1, 1, 1, 2, 2, 2, 3, 3, 3, 1, 2, 3, 1, 2, 3, 1, 2, 3])
```

<details>
<summary>Solution 1</summary>

```python
>>> np.concatenate((np.repeat(a, 3), np.tile(a, 3)))
array([1, 1, 1, 2, 2, 2, 3, 3, 3, 1, 2, 3, 1, 2, 3, 1, 2, 3])
```

</details>

<details>
<summary>Solution 2</summary>

```python
>>> np.r_[np.repeat(a, 3), np.tile(a, 3)]
array([1, 1, 1, 2, 2, 2, 3, 3, 3, 1, 2, 3, 1, 2, 3, 1, 2, 3])
```

</details>

# 11. 如何获得两个 Python NumPy 数组中共同的项？

<details>
<summary>English Version</summary>

Title: How to get the common items between two python numpy arrays?

Difficulty Level: L2

Question: Get the common items between `a` and `b`.

----

</details>

难度：L2

问题：获取数组 `a` 和 `b` 中的共同项。

输入：

```python
>>> a = np.array([1, 2, 3, 2, 3, 4, 3, 4, 5, 6])
>>> b = np.array([7, 2, 10, 2, 7, 4, 9, 4, 9, 8])
```

期望输出：

```python
array([2, 4])
```

<details>
<summary>Solution</summary>

```python
>>> np.intersect1d(a, b)
array([2, 4])
```

</details>

# 12. 如何从一个数组中移除与另一个数组重复的项？

<details>
<summary>English Version</summary>

Title: How to remove from one array those items that exist in another?

Difficulty Level: L2

Question: From array `a` remove all items present in array `b`.

----

</details>

难度：L2

问题：从数组 `a` 中移除出现在数组 `b` 中的所有项。

输入：

```python
>>> a = np.array([1, 2, 3, 4, 5])
>>> b = np.array([5, 6, 7, 8, 9])
```

期望输出：

```python
array([1, 2, 3, 4])
```

<details>
<summary>Solution</summary>

```python
>>> np.setdiff1d(a, b)
array([1, 2, 3, 4])
```

</details>

# 13. 如何获取两个数组匹配元素的位置？

<details>
<summary>English Version</summary>

Title: How to get the positions where elements of two arrays match?

Difficulty Level: L2

Question: Get the positions where elements of `a` and `b` match.

----

</details>

难度：L2

问题：获取数组 a 和 b 中匹配元素的位置。

输入：

```python
>>> a = np.array([1, 2, 3, 2, 3, 4, 3, 4, 5, 6])
>>> b = np.array([7, 2, 10, 2, 7, 4, 9, 4, 9, 8])
```

期望输出：

```python
(array([1, 3, 5, 7]), )
```

<details>
<summary>Solution</summary>

```python
>>> np.where(a == b)
(array([1, 3, 5, 7]),)
```

</details>

# 14. 如何从 NumPy 数组中提取给定范围内的所有数字？

<details>
<summary>English Version</summary>

Title: How to extract all numbers between a given range from a numpy array?

Difficulty Level: L2

Question: Get all items between 5 and 10 from `a`.

----

</details>

难度：L2

问题：从数组 `a` 中提取 5 和 10 之间的所有项。

输入：

```python
>>> a = np.array([2, 6, 1, 9, 10, 3, 27])
```

期望输出：

```python
array([6, 9, 10])
```

<details>
<summary>Solution 1</summary>

```python
>>> a[(a >= 5) & (a <= 10)]
array([ 6,  9, 10])
```

</details>

<details>
<summary>Solution 2</summary>

```python
>>> index = np.where((a >= 5) & (a <= 10))
>>> a[index]
array([ 6,  9, 10])
```

</details>

<details>
<summary>Solution 3</summary>

```python
>>> index = np.where(np.logical_and(a>=5, a<=10))
>>> a[index]
array([ 6,  9, 10])
```

</details>

# 15. 如何创建一个 Python 函数以对 NumPy 数组执行元素级的操作？

<details>
<summary>English Version</summary>

Title: How to make a python function that handles scalars to work on numpy arrays?

Difficulty Level: L2

Question: Convert the function `maxx` that works on two scalars, to work on two arrays.

----

</details>

难度：L2

问题：转换函数 `maxx`，使其从只能对比标量而变为对比两个数组。

输入：

```python
>>> def maxx(x, y):
...     """Get the maximum of two items"""
...     if x >= y:
...        return x
...     else:
...        return y
...
>>> maxx(1, 5)
5
```

期望输出：

```python
>>> a = np.array([5, 7, 9, 8, 6, 4, 5])
>>> b = np.array([6, 3, 4, 8, 9, 7, 1])
>>> pair_max(a, b)
array([6., 7., 9., 8., 9., 7., 5.])
```

<details>
<summary>Solution</summary>

```python
>>> pair_max = np.vectorize(maxx, otypes=[float])
>>> a = np.array([5, 7, 9, 8, 6, 4, 5])
>>> b = np.array([6, 3, 4, 8, 9, 7, 1])
>>> pair_max(a, b)
array([6., 7., 9., 8., 9., 7., 5.])
```

</details>

# 16. 如何在 2d NumPy 数组中交换两个列？

<details>
<summary>English Version</summary>

Title: How to swap two columns in a 2d numpy array?

Difficulty Level: L2

Question: Swap columns 1 and 2 in the array `arr`.

----

</details>

难度：L2

问题：在数组 `arr` 中交换列 1 和列 2。

```python
>>> arr = np.arange(9).reshape(3, 3)
>>> arr
array([[0, 1, 2],
       [3, 4, 5],
       [6, 7, 8]])
```

<details>
<summary>Solution 1</summary>

```python
>>> arr[:, [1, 0, 2]]
array([[1, 0, 2],
       [4, 3, 5],
       [7, 6, 8]])
```

</details>

<details>
<summary>Solution 2</summary>

```python
# Swap in-place
>>> tmp = arr[:, 0].copy()
>>> arr[:, 0] = arr[:, 1]
>>> arr[:, 1] = tmp
>>> arr
array([[1, 0, 2],
       [4, 3, 5],
       [7, 6, 8]])
```

</details>

# 17. 如何在 2d NumPy 数组中交换两个行？

<details>
<summary>English Version</summary>

Title: How to swap two rows in a 2d numpy array?

Difficulty Level: L2

Question: Swap rows 1 and 2 in the array `arr`.

----

</details>

难度：L2

问题：在数组 `arr` 中交换行 1 和行 2。

```python
>>> arr = np.arange(9).reshape(3, 3)
>>> arr
array([[0, 1, 2],
       [3, 4, 5],
       [6, 7, 8]])
```

<details>
<summary>Solution 1</summary>

```python
>>> arr[[1, 0, 2], :]
array([[3, 4, 5],
       [0, 1, 2],
       [6, 7, 8]])
```

</details>

<details>
<summary>Solution 2</summary>

```python
# Swap in-place
>>> tmp = arr[0, :].copy()
>>> arr[0, :] = arr[1, :]
>>> arr[1, :] = tmp
>>> arr
array([[3, 4, 5],
       [0, 1, 2],
       [6, 7, 8]])
```

</details>

# 18. 如何反转 2D 数组的所有行？

<details>
<summary>English Version</summary>

Title: How to reverse the rows of a 2D array?

Difficulty Level: L2

Question: Reverse the rows of a 2D array `arr`.

----

</details>

难度：L2

问题：反转 2D 数组 `arr` 中的所有行。

```python
>>> arr = np.arange(9).reshape(3, 3)
>>> arr
array([[0, 1, 2],
       [3, 4, 5],
       [6, 7, 8]])
```

<details>
<summary>Solution</summary>

```python
>>> arr[::-1]
array([[6, 7, 8],
       [3, 4, 5],
       [0, 1, 2]])
```

</details>

# 19. 如何反转 2D 数组的所有列？

<details>
<summary>English Version</summary>

Title: How to reverse the columns of a 2D array?

Difficulty Level: L2

Question: Reverse the columns of a 2D array `arr`.

----

</details>

难度：L2

问题：反转 2D 数组 `arr` 中的所有列。

```python
>>> arr = np.arange(9).reshape(3, 3)
>>> arr
array([[0, 1, 2],
       [3, 4, 5],
       [6, 7, 8]])
```

<details>
<summary>Solution</summary>

```python
>>> arr[:, ::-1]
array([[2, 1, 0],
       [5, 4, 3],
       [8, 7, 6]])
```

</details>

# 20. 如何创建一个包含 5 和 10 之间浮点数的随机 2 维数组？

<details>
<summary>English Version</summary>

Title: How to create a 2D array containing random floats between 5 and 10?

Difficulty Level: L2

Question: Create a 2D array of shape 5x3 to contain random decimal numbers between 5 and 10.

----

</details>


难度：L2

问题：创建一个形态为 5×3 的 2 维数组，包含 5 和 10 之间的随机十进制小数。

<details>
<summary>Solution 1</summary>

```python
>>> np.random.seed(100)
>>> np.random.uniform(5, 10, size=(5, 3))
array([[7.71702471, 6.39184693, 7.12258795],
       [9.22388066, 5.02359428, 5.6078456 ],
       [8.35374542, 9.12926378, 5.68353295],
       [7.87546665, 9.45660977, 6.04601061],
       [5.9266411 , 5.54188445, 6.09848746]])
```

</details>

<details>
<summary>Solution 2</summary>

```python
>>> np.random.seed(100)
>>> arr = (10 - 5) * np.random.rand(5, 3) + 5
>>> arr
array([[7.71702471, 6.39184693, 7.12258795],
       [9.22388066, 5.02359428, 5.6078456 ],
       [8.35374542, 9.12926378, 5.68353295],
       [7.87546665, 9.45660977, 6.04601061],
       [5.9266411 , 5.54188445, 6.09848746]])
```

</details>

<details>
<summary>Solution 3</summary>

```python
# Maybe different from other solutions
>>> rand_arr = np.random.randint(low=5, high=10, size=(5, 3)) + np.random.random((5, 3))
>>> rand_arr
array([[6.41920093, 9.40003816, 7.78940871],
       [7.973373  , 6.51303275, 6.04690216],
       [5.26486281, 8.24187676, 9.69046437],
       [8.34740798, 7.26776599, 8.26254059],
       [8.46680771, 9.86023614, 6.52209887]])
```

</details>

# 21. 如何在 Python NumPy 数组中仅输出小数点后三位的数字？

<details>
<summary>English Version</summary>

Title: How to print only 3 decimal places in python numpy array?

Difficulty Level: L1

Question: Print or show only 3 decimal places of the numpy array `rand_arr`.

----

</details>

难度：L1

问题：输出或显示 NumPy 数组 `rand_arr` 中小数点后三位的数字。

输入：

```python
rand_arr = np.random.random((5, 3))
```

<details>
<summary>Solution</summary>

```python
>>> np.set_printoptions(precision=3)
>>> rand_arr
array([[0.152, 0.272, 0.846],
       [0.927, 0.521, 0.665],
       [0.465, 0.67 , 0.136],
       [0.829, 0.175, 0.343],
       [0.281, 0.177, 0.596]])
```

</details>

# 22. 如何通过禁用科学计数法（如 1e10）打印 NumPy 数组？

<details>
<summary>English Version</summary>

Title: How to pretty print a numpy array by suppressing the scientific notation (like 1e10)?

Difficulty Level: L1

Question: Pretty print `rand_arr` by suppressing the scientific notation (like 1e10).

----

</details>

难度：L1

问题：通过禁用科学计数法（如 1e10）打印 NumPy 数组 `rand_arr`。

输入：

```python
# Create the random array
>>> np.random.seed(100)
>>> rand_arr = np.random.random([3, 3]) / 1e3
>>> rand_arr
array([[5.43404942e-04, 2.78369385e-04, 4.24517591e-04],
       [8.44776132e-04, 4.71885619e-06, 1.21569121e-04],
       [6.70749085e-04, 8.25852755e-04, 1.36706590e-04]])
```

期望输出：

```python
array([[0.000543, 0.000278, 0.000425],
       [0.000845, 0.000005, 0.000122],
       [0.000671, 0.000826, 0.000137]])
```

<details>
<summary>Solution</summary>

```python
# precision is optional
>>> np.set_printoptions(suppress=True, precision=6)
>>> rand_arr
array([[0.000543, 0.000278, 0.000425],
       [0.000845, 0.000005, 0.000122],
       [0.000671, 0.000826, 0.000137]])
```

</details>

# 23. 如何限制 NumPy 数组输出中项的数目？

<details>
<summary>English Version</summary>

Title: How to limit the number of items printed in output of numpy array?

Difficulty Level: L1

Question: Limit the number of items printed in python numpy array `a` to a maximum of 6 elements.

----

</details>

难度：L1

问题：将 Python NumPy 数组 `a` 输出的项的数目限制在最多 6 个元素。

输入：

```python
>>> a = np.arange(15)
>>> a
array([ 0,  1,  2,  3,  4,  5,  6,  7,  8,  9, 10, 11, 12, 13, 14])
```

期望输出：

```python
array([ 0,  1,  2, ..., 12, 13, 14])
```

<details>
<summary>Solution</summary>

```python
>>> np.set_printoptions(threshold=6)
>>> a
array([ 0,  1,  2, ..., 12, 13, 14])
```

</details>

# 24. 如何在不截断数组的前提下打印出完整的 NumPy 数组？

<details>
<summary>English Version</summary>

Title: How to print the full numpy array without truncating

Difficulty Level: L1

Question: Print the full numpy array `a` without truncating.

----

</details>

难度：L1

问题：在不截断数组的前提下打印出完整的 NumPy 数组 a。

输入：

```python
>>> np.set_printoptions(threshold=6)
>>> a = np.arange(15)
>>> a
array([ 0, 1, 2, ..., 12, 13, 14])
```

期望输出：

```python
>>> a
array([ 0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14])
```

<details>
<summary>Solution 1</summary>

```python
>>> np.set_printoptions(threshold=np.nan)
>>> a
array([ 0,  1,  2,  3,  4,  5,  6,  7,  8,  9, 10, 11, 12, 13, 14])
```

</details>

<details>
<summary>Solution 2</summary>

```python
>>> np.set_printoptions(threshold=1000)
>>> a
array([ 0,  1,  2,  3,  4,  5,  6,  7,  8,  9, 10, 11, 12, 13, 14])
```

</details>

# 25. 如何向 Python NumPy 导入包含数字和文本的数据集，同时保持文本不变？

<details>
<summary>English Version</summary>

Title: How to import a dataset with numbers and texts keeping the text intact in python numpy?

Difficulty Level: L2

Question: Import the iris dataset keeping the text intact.

----

</details>

难度：L2

问题：导入 iris 数据集，保持文本不变。

从 [Iris Data Set](https://archive.ics.uci.edu/ml/datasets/Iris) 网页下载数据集 `iris.data`。

<details>
<summary>Solution</summary>

```python
>>> url = "https://archive.ics.uci.edu/ml/machine-learning-databases/iris/iris.data"
>>> iris = np.genfromtxt(url, delimiter=",", dtype=object)
>>> iris[:3]
array([[b'5.1', b'3.5', b'1.4', b'0.2', b'Iris-setosa'],
       [b'4.9', b'3.0', b'1.4', b'0.2', b'Iris-setosa'],
       [b'4.7', b'3.2', b'1.3', b'0.2', b'Iris-setosa']], dtype=object)
```

Since we want to retain the species, a text field, I have set the `dtype` to `object`. Had I set `dtype=None`, a 1d array of tuples would have been returned.

</details>

# 26. 如何从 1 维元组数组中提取特定的列？

<details>
<summary>English Version</summary>

Title: How to extract a particular column from 1D array of tuples?

Difficulty Level: L2

Question: Extract the text column species from the 1D `iris_1d`.

----

</details>

难度：L2

问题：从导入的 1 维 `iris_1d` 中提取文本列 species。

输入：

```python
>>> url = "https://archive.ics.uci.edu/ml/machine-learning-databases/iris/iris.data"
>>> iris_1d = np.genfromtxt(url, delimiter=",", dtype=None)
```

<details>
<summary>Solution 1</summary>

```python
>>> species = np.array([row[4] for row in iris_1d])
>>> species[:7]
array([b'Iris-setosa', b'Iris-setosa', b'Iris-setosa', b'Iris-setosa',
       b'Iris-setosa', b'Iris-setosa', b'Iris-setosa'], dtype='|S18')
```

</details>

<details>
<summary>Solution 2</summary>

```python
>>> vfunc = np.vectorize(lambda x: x[4])
>>> species = vfunc(iris_1d)
>>> species[:7]
array([b'Iris-setosa', b'Iris-setosa', b'Iris-setosa', b'Iris-setosa',
       b'Iris-setosa', b'Iris-setosa', b'Iris-setosa'], dtype='|S15')
```

</details>

# 27. 如何将 1 维元组数组转换成 2 维 NumPy 数组？

<details>
<summary>English Version</summary>

Title: How to convert a 1d array of tuples to a 2d numpy array?

Difficulty Level: L2

Question: Convert the 1D `iris_1d` to 2D array `iris_2d` by omitting the species text field.

----

</details>

难度：L2

问题：忽略 species 文本字段，将 1 维 `iris_1d` 转换成 2 维数组 `iris_2d`。

输入：

```python
>>> url = "https://archive.ics.uci.edu/ml/machine-learning-databases/iris/iris.data"
>>> iris_1d = np.genfromtxt(url, delimiter=",", dtype=None)
```

<details>
<summary>Solution</summary>

```python
>>> iris_2d = np.array([row.tolist()[:4] for row in iris_1d])
>>> iris_2d[:3]
array([[5.1, 3.5, 1.4, 0.2],
       [4.9, 3. , 1.4, 0.2],
       [4.7, 3.2, 1.3, 0.2]])
```

</details>

# 28. 如何计算 NumPy 数组的平均值、中位数和标准差？

<details>
<summary>English Version</summary>

Title: How to compute the mean, median, standard deviation of a numpy array?

Difficulty: L1

Question: Find the mean, median, standard deviation of iris's `sepal length` (1st column).

----

</details>

难度：L1

问题：找出 iris `sepal length`（第一列）的平均值、中位数和标准差。

```python
>>> url = "https://archive.ics.uci.edu/ml/machine-learning-databases/iris/iris.data"
>>> iris_1d = np.genfromtxt(url, delimiter=",", dtype=None)
```

<details>
<summary>Solution</summary>

```python
>>> sepal_length = np.array([row[0] for row in iris_1d])
>>> mean, median, std = np.mean(sepal_length), np.median(sepal_length), np.std(sepal_length)
>>> mean, median, std
(5.843333333333334, 5.8, 0.8253012917851409)
```

</details>

# 29. 如何归一化数组，使值的范围在 0 和 1 之间？

<details>
<summary>English Version</summary>

Title: How to normalize an array so the values range exactly between 0 and 1?

Difficulty: L2

Question: Create a normalized form of iris's `sepal length` whose values range exactly between 0 and 1 so that the minimum has value 0 and maximum has value 1.

----

</details>

难度：L2

问题：创建 iris `sepal length` 的归一化格式，使其值在 0 到 1 之间。

输入：

```python
url = "https://archive.ics.uci.edu/ml/machine-learning-databases/iris/iris.data"
sepal_length = np.genfromtxt(url, delimiter=",", dtype=float, usecols=[0])
```

<details>
<summary>Solution</summary>

```python
>>> max_value = np.max(sepal_length)
>>> min_value = np.min(sepal_length)
>>> sepal_length_nm = (sepal_length - min_value) / (max_value - min_value)
>>> sepal_length_nm[:3]
array([0.22222222, 0.16666667, 0.11111111])
```

</details>

# 30. 如何计算 softmax 分数？

<details>
<summary>English Version</summary>

Title: How to compute the softmax score?

Difficulty Level: L3

Question: Compute the softmax score of `sepal length`.

----

</details>

难度：L3

问题：计算 `sepal length` 的 softmax 分数。

```python
url = "https://archive.ics.uci.edu/ml/machine-learning-databases/iris/iris.data"
sepal_length = np.genfromtxt(url, delimiter=",", dtype=float, usecols=[0])
```

<details>
<summary>Solution</summary>

According formula:

$$
S(x_i) = \frac{e^{x_i}}{\sum_j e^{x_j}}
$$

```python
>>> sepal_length_exp = np.exp(sepal_length)
>>> exp_sum = np.sum(sepal_length_exp)
>>> sepal_length_sm = sepal_length_exp / exp_sum
>>> sepal_length_sm[:5]
array([0.00221959, 0.00181724, 0.00148783, 0.00134625, 0.00200836])
```

For numerical stability, the formula changes to:

$$
S(x_i) = \frac{e^{(x_i - x_{max})}}{\sum_j e^{(x_j - x_{max})}}
$$

where $x_{max} = max(x)$.

```python
>>> sepal_length_exp = np.exp(sepal_length - np.max(sepal_length))
>>> exp_sum = np.sum(sepal_length_exp)
>>> sepal_length_sm = sepal_length_exp / exp_sum
>>> sepal_length_sm[:5]
array([0.00221959, 0.00181724, 0.00148783, 0.00134625, 0.00200836])
```

</details>

# 31. 如何找到 NumPy 数组的百分数？

<details>
<summary>English Version</summary>

Title: How to find the percentile scores of a numpy array?

Difficulty Level: L1

Question: Find the 5th and 95th percentile of iris's `sepal length`.

----

</details>

难度：L1

问题：找出 iris `sepal length`（第一列）的第 5 个和第 95 个百分数。

```python
url =  "https://archive.ics.uci.edu/ml/machine-learning-databases/iris/iris.data"
sepallength = np.genfromtxt(url, delimiter=",", dtype=float, usecols=[0])
```

<details>
<summary>Solution</summary>

```python
>>> np.percentile(sepallength, [5, 95])
array([4.6  , 7.255])
```

</details>

# 32. 如何在数组的随机位置插入值？

<details>
<summary>English Version</summary>

Title: How to insert values at random positions in an array?

Difficulty Level: L2

Question: Insert `np.nan` values at 20 random positions in `iris_2d` dataset.

----

</details>

难度：L2

问题：在 `iris_2d` 数据集中的 20 个随机位置插入 `np.nan` 值。

输入：

```python
>>> url = "https://archive.ics.uci.edu/ml/machine-learning-databases/iris/iris.data"
>>> iris_2d = np.genfromtxt(url, delimiter=",", dtype=object)
```

<details>
<summary>Solution 1</summary>

```python
>>> rand_row = np.random.randint(iris_2d.shape[0], size=20)
>>> rand_col = np.random.randint(iris_2d.shape[1], size=20)
>>> iris_2d[rand_row, rand_col] = np.nan
>>> iris_2d[:10]
array([[b'5.1', b'3.5', b'1.4', b'0.2', b'Iris-setosa'],
       [b'4.9', b'3.0', b'1.4', b'0.2', b'Iris-setosa'],
       [b'4.7', b'3.2', b'1.3', b'0.2', b'Iris-setosa'],
       [b'4.6', b'3.1', b'1.5', b'0.2', b'Iris-setosa'],
       [b'5.0', b'3.6', b'1.4', b'0.2', b'Iris-setosa'],
       [b'5.4', b'3.9', b'1.7', b'0.4', b'Iris-setosa'],
       [b'4.6', b'3.4', b'1.4', b'0.3', b'Iris-setosa'],
       [b'5.0', b'3.4', b'1.5', b'0.2', b'Iris-setosa'],
       [b'4.4', b'2.9', nan, b'0.2', b'Iris-setosa'],
       [b'4.9', b'3.1', b'1.5', b'0.1', b'Iris-setosa']], dtype=object)
```

</details>

<details>
<summary>Solution 2</summary>

```python
>>> i, j = np.where(iris_2d)
>>> iris_2d[np.random.choice(i, 20), np.random.choice(j, 20)] = np.nan
>>> iris_2d[:10]
array([[b'5.1', b'3.5', b'1.4', b'0.2', b'Iris-setosa'],
       [b'4.9', b'3.0', b'1.4', b'0.2', b'Iris-setosa'],
       [b'4.7', b'3.2', b'1.3', b'0.2', b'Iris-setosa'],
       [b'4.6', b'3.1', b'1.5', b'0.2', b'Iris-setosa'],
       [b'5.0', b'3.6', b'1.4', b'0.2', b'Iris-setosa'],
       [b'5.4', b'3.9', b'1.7', b'0.4', b'Iris-setosa'],
       [b'4.6', b'3.4', b'1.4', b'0.3', b'Iris-setosa'],
       [b'5.0', b'3.4', b'1.5', b'0.2', nan],
       [b'4.4', b'2.9', b'1.4', b'0.2', b'Iris-setosa'],
       [b'4.9', b'3.1', b'1.5', b'0.1', b'Iris-setosa']], dtype=object)
```

</details>

# 33. 如何在 NumPy 数组中找出缺失值的位置？

<details>
<summary>English Version</summary>

Title: How to find the position of missing values in numpy array?

Difficulty Level: L2

Question: Find the number and position of missing values in `iris_2d`'s `sepal length` (1st column).

----

</details>

难度：L2

问题：在 `iris_2d` 的 `sepal length`（第一列）中找出缺失值的数目和位置。

输入：

```python
>>> url = "https://archive.ics.uci.edu/ml/machine-learning-databases/iris/iris.data"
>>> iris_2d = np.genfromtxt(url, delimiter=",", dtype=float, usecols=[0, 1, 2, 3])
>>> iris_2d[np.random.randint(150, size=20), np.random.randint(4, size=20)] = np.nan
```

<details>
<summary>Solution 1</summary>

```python
# number of nan
>>> np.isnan(iris_2d[:, 0]).sum()
5

# index of nan
>>> np.where(np.isnan(iris_2d[:, 0]))
(array([ 12,  13,  47,  53, 143]),)
```

</details>

<details>
<summary>Solution 2</summary>

```python
>>> nan_bools = np.isnan(iris_2d[:, 0])

# number of nan
>>> num_nans = np.sum(nan_bools)
>>> num_nans
5

# index of nan
>>> index = np.arange(len(nan_bools))
>>> nan_index = index[nan_bools]
>>> nan_index
array([ 12,  13,  47,  53, 143])
```

</details>

# 34. 如何基于两个或以上条件过滤 NumPy 数组？

<details>
<summary>English Version</summary>

Title: How to filter a numpy array based on two or more conditions?

Difficulty Level: L3

Question: Filter the rows of `iris_2d` that has `petal length (3rd column) > 1.5` and `sepal length (1st column) < 5.0`.

----

</details>

难度：L3

问题：过滤 `iris_2d` 中满足 `petal length（第三列）> 1.5` 和 `sepal length（第一列）< 5.0` 的行。

输入：

```python
>>> url = "https://archive.ics.uci.edu/ml/machine-learning-databases/iris/iris.data"
>>> iris_2d = np.genfromtxt(url, delimiter=",", dtype=float, usecols=[0, 1, 2, 3])
```

<details>
<summary>Solution</summary>

```python
>>> condition = (iris_2d[:, 2] > 1.5) & (iris_2d[:, 0] < 5.0)
>>> iris_2d[condition]
array([[4.8, 3.4, 1.6, 0.2],
       [4.8, 3.4, 1.9, 0.2],
       [4.7, 3.2, 1.6, 0.2],
       [4.8, 3.1, 1.6, 0.2],
       [4.9, 2.4, 3.3, 1. ],
       [4.9, 2.5, 4.5, 1.7]])
```

</details>

# 35. 如何在 NumPy 数组中删除包含缺失值的行？

<details>
<summary>English Version</summary>

Title: How to drop rows that contain a missing value from a numpy array?

Difficulty Level: L3:

Question: Select the rows of `iris_2d` that does not have any `nan` value.

----

</details>

难度：L3

问题：选择 `iris_2d` 中不包含 `nan` 值的行。

输入：

```python
>>> url = "https://archive.ics.uci.edu/ml/machine-learning-databases/iris/iris.data"
>>> iris_2d = np.genfromtxt(url, delimiter=",", dtype=float, usecols=[0, 1, 2, 3])
>>> iris_2d[np.random.randint(150, size=20), np.random.randint(4, size=20)] = np.nan
```

<details>
<summary>Solution 1</summary>

```python
>>> iris_2d[np.sum(np.isnan(iris_2d), axis=1) == 0][:5]
array([[5.1, 3.5, 1.4, 0.2],
       [4.7, 3.2, 1.3, 0.2],
       [4.6, 3.1, 1.5, 0.2],
       [5. , 3.6, 1.4, 0.2],
       [5.4, 3.9, 1.7, 0.4]])
```

</details>

<details>
<summary>Solution 2</summary>

```python
>>> any_nan_in_row = np.array([~np.any(np.isnan(row)) for row in iris_2d])
>>> iris_2d[any_nan_in_row][:5]
array([[5.1, 3.5, 1.4, 0.2],
       [4.7, 3.2, 1.3, 0.2],
       [4.6, 3.1, 1.5, 0.2],
       [5. , 3.6, 1.4, 0.2],
       [5.4, 3.9, 1.7, 0.4]])
```

</details>

# 36. 如何找出 NumPy 数组中两列之间的关联性？

<details>
<summary>English Version</summary>

Title: How to find the correlation between two columns of a numpy array?

Difficulty Level: L2

Question: Find the correlation between `sepal length`(1st column) and `petal length`(3rd column) in `iris_2d`.

----

</details>

难度：L2

问题：找出 `iris_2d` 中 `sepal length`（第一列）和 `petal length`（第三列）之间的关联性。

输入：

```python
>>> url = "https://archive.ics.uci.edu/ml/machine-learning-databases/iris/iris.data"
>>> iris_2d = np.genfromtxt(url, delimiter=",", dtype=float, usecols=[0, 1, 2, 3])
```

<details>
<summary>Solution 1</summary>

```python
>>> np.corrcoef(iris_2d[:, 0], iris_2d[:, 2])[0, 1]
0.8717541573048718
```

</details>

<details>
<summary>Solution 2</summary>

```python
>>> from scipy.stats.stats import pearsonr
>>> corr, p_value = pearsonr(iris_2d[:, 0], iris_2d[:, 2])
>>> corr
0.8717541573048712
```

</details>

# 37. 如何确定给定数组是否有空值？

<details>
<summary>English Version</summary>

Title: How to find if a given array has any null values?

Difficulty Level: L2

Question: Find out if `iris_2d` has any missing values.

----

</details>

难度：L2

问题：确定 `iris_2d` 是否有缺失值。

输入：

```python
>>> url = "https://archive.ics.uci.edu/ml/machine-learning-databases/iris/iris.data"
>>> iris_2d = np.genfromtxt(url, delimiter=",", dtype=float, usecols=[0, 1, 2, 3])
```

<details>
<summary>Solution 1</summary>

```python
>>> np.sum(np.isnan(iris_2d)) > 0
False
```

</details>

<details>
<summary>Solution 2</summary>

```python
>>> np.isnan(iris_2d).any()
False
```

</details>

# 38. 如何在 NumPy 数组中将所有缺失值替换成0？

<details>
<summary>English Version</summary>

Title: How to replace all missing values with 0 in a numpy array?

Difficulty Level: L2

Question: Replace all ccurrences of `nan` with 0 in numpy array.

----

</details>

难度：L2

问题：在 NumPy 数组中将所有 `nan` 替换成 0。

输入：

```python
>>> url = "https://archive.ics.uci.edu/ml/machine-learning-databases/iris/iris.data"
>>> iris_2d = np.genfromtxt(url, delimiter=",", dtype=float, usecols=[0, 1, 2, 3])
>>> iris_2d[np.random.randint(150, size=20), np.random.randint(4, size=20)] = np.nan
```

<details>
<summary>Solution</summary>

```python
>>> iris_2d[np.isnan(iris_2d)] = 0
```

</details>

# 39. 如何在 NumPy 数组中找出唯一值的数量？

<details>
<summary>English Version</summary>

Title: How to find the count of unique values in a numpy array?

Difficulty Level: L2

Question: Find the unique values and the count of unique values in iris's `species`.

----

</details>

难度：L2

问题：在 iris 的 `species` 列中找出唯一值及其数量。

输入：

```python
>>> url = "https://archive.ics.uci.edu/ml/machine-learning-databases/iris/iris.data"
>>> iris = np.genfromtxt(url, delimiter=",", dtype=object)
>>> names = ("sepallength", "sepalwidth", "petallength", "petalwidth", "species")
```

<details>
<summary>Solution</summary>

```python
>>> unique, counts = np.unique(iris[:, 4], return_counts=True)
>>> unique
array([b'Iris-setosa', b'Iris-versicolor', b'Iris-virginica'],
      dtype=object)
>>> counts
array([50, 50, 50])
```

</details>

# 40. 如何将一个数值转换为一个类别（文本）数组？

<details>
<summary>English Version</summary>

Title: How to convert a numeric to a categorical (text) array?

Difficulty Level: L2

Question: Bin the petal length (3rd) column of `iris_2d` to form a text array, such that if petal length is:

```
Less than 3 --> 'small'
3-5 --> 'medium'
>=5 --> 'large'
```

----

</details>

难度：L2

问题：将 `iris_2d` 的 petal length（第三列）转换以构建一个文本数组，按如下规则进行转换：

```
Less than 3 –> 'small'
3-5 –> 'medium'
>=5 –> 'large'
```

输入：

```python
>>> url = "https://archive.ics.uci.edu/ml/machine-learning-databases/iris/iris.data"
>>> iris = np.genfromtxt(url, delimiter=",", dtype=object)
>>> names = ("sepallength", "sepalwidth", "petallength", "petalwidth", "species")
```

<details>
<summary>Solution 1</summary>

```python
# Bin petallength 
>>> petal_length_bin = np.digitize(iris[:, 2].astype(float), [0, 3, 5, 10])

# Map it to respective category
>>> label_map = {1: "small", 2: "medium", 3: "large", 4: np.nan}
>>> petal_length_cat = [label_map[x] for x in petal_length_bin]

# View
>>> petal_length_cat[:4]
['small', 'small', 'small', 'small']
```

</details>

<details>
<summary>Solution 2</summary>

```python
>>> petal_length = iris[:, 2].astype(float)
>>> petal_length_cat = np.full(len(petal_length), None,dtype=object)

>>> petal_length_cat[petal_length < 3] = "small"
>>> petal_length_cat[(petal_length >= 3) & (petal_length < 5)] = "medium"
>>> petal_length_cat[petal_length >= 5] = "large"

>>> petal_length_cat[:4]
array(['small', 'small', 'small', 'small'], dtype=object)
```

</details>

# 41. 如何基于 NumPy 数组现有列创建一个新的列？

<details>
<summary>English Version</summary>

Title: How to create a new column from existing columns of a numpy array?

Difficulty Level: L2

Question: Create a new column for volume in `iris_2d`, where volume is `(pi x petallength x sepal_length^2)/3`.

----

</details>

难度：L2

问题：为 `iris_2d` 中的 volume 列创建一个新的列，volume 指 `(pi x petal_length x sepal_length^2)/3`。

输入：

```python
>>> url = "https://archive.ics.uci.edu/ml/machine-learning-databases/iris/iris.data"
>>> iris_2d = np.genfromtxt(url, delimiter=",", dtype=object)
>>> names = ("sepallength", "sepalwidth", "petallength", "petalwidth", "species")
```

<details>
<summary>Solution 1</summary>

```python
>>> volume = (np.pi * iris_2d[:, 2].astype(float) * (iris_2d[:, 0].astype(float))**2) / 3
>>> out = np.c_[iris_2d, volume]
>>> out[:4]
array([[b'5.1', b'3.5', b'1.4', b'0.2', b'Iris-setosa',
        38.13265162927291],
       [b'4.9', b'3.0', b'1.4', b'0.2', b'Iris-setosa',
        35.200498485922445],
       [b'4.7', b'3.2', b'1.3', b'0.2', b'Iris-setosa', 30.0723720777127],
       [b'4.6', b'3.1', b'1.5', b'0.2', b'Iris-setosa',
        33.238050274980004]], dtype=object)
```

</details>

<details>
<summary>Solution 2</summary>

```python
# Compute volume
>>> sepal_length = iris_2d[:, 0].astype('float')
>>> petal_length = iris_2d[:, 2].astype('float')
>>> volume = (np.pi * petal_length * (sepal_length**2))/3

# Introduce new dimension to match iris_2d's
>>> volume = volume[:, np.newaxis]

# Add the new column
>>> out = np.hstack([iris_2d, volume])

# View
>>> out[:4]
array([[b'5.1', b'3.5', b'1.4', b'0.2', b'Iris-setosa',
        38.13265162927291],
       [b'4.9', b'3.0', b'1.4', b'0.2', b'Iris-setosa',
        35.200498485922445],
       [b'4.7', b'3.2', b'1.3', b'0.2', b'Iris-setosa', 30.0723720777127],
       [b'4.6', b'3.1', b'1.5', b'0.2', b'Iris-setosa',
        33.238050274980004]], dtype=object)
```

</details>

# 42. 如何在 NumPy 中执行概率采样？

<details>
<summary>English Version</summary>

Title: How to do probabilistic sampling in numpy?

Difficulty Level: L3

Question: Randomly sample iris's `species` such that `setosa` is twice the number of `versicolor` and `virginica`.

----

</details>

难度：L3

问题：随机采样 iris 数据集中的 `species` 列，使得 `setosa` 的数量是 `versicolor` 和 `virginica` 数量的两倍。

```python
# Import iris keeping the text column intact
url = "https://archive.ics.uci.edu/ml/machine-learning-databases/iris/iris.data"
iris = np.genfromtxt(url, delimiter=",", dtype=object)
```

<details>
<summary>Solution</summary>

```python
# Get the species column
>>> species = iris[:, 4]

# Probablistic Sampling
>>> np.random.seed(100)
>>> probs = np.r_[np.linspace(0, 0.500, num=50), np.linspace(0.501, 0.750, num=50), np.linspace(0.751, 1.0, num=50)]
>>> index = np.searchsorted(probs, np.random.random(150))
>>> species_out = species[index]
>>> np.unique(species_out, return_counts=True)
(array([b'Iris-setosa', b'Iris-versicolor', b'Iris-virginica'],
      dtype=object), array([77, 37, 36]))
```

</details>

# 43. 如何在多维数组中找到一维的第二最大值？

<details>
<summary>English Version</summary>

Title: How to get the second largest value of an array when grouped by another array?

Difficulty Level: L2

Question: What is the value of second longest `petal length` of species `setosa`

----

</details>

难度：L2

问题：在 species `setosa` 的 `petal length` 列中找到第二最大值。

输入：

```python
>>> url = "https://archive.ics.uci.edu/ml/machine-learning-databases/iris/iris.data"
>>> iris = np.genfromtxt(url, delimiter=",", dtype=object)
>>> names = ("sepallength", "sepalwidth", "petallength", "petalwidth", "species")
```

<details>
<summary>Solution</summary>

```python
>>> iris_setosa = iris[iris[:, 4] == b"Iris-setosa", :]
>>> petal_len_setosa = iris_setosa[:, 2].astype(float)
>>> second_large = np.sort(np.unique(petal_len_setosa))[-2]
>>> second_large
1.7
```

</details>

# 44. 如何用给定列将 2 维数组排序？

<details>
<summary>English Version</summary>

Title: How to sort a 2D array by a column

Difficulty Level: L2

Question: Sort the iris dataset based on `sepal length` column.

----

</details>

难度：L2

问题：基于 `sepal length` 列将 iris 数据集排序。

输入：

```python
>>> url = "https://archive.ics.uci.edu/ml/machine-learning-databases/iris/iris.data"
>>> iris = np.genfromtxt(url, delimiter=",", dtype=object)
>>> names = ("sepallength", "sepalwidth", "petallength", "petalwidth", "species")
```

<details>
<summary>Solution</summary>

```python
>>> index = np.argsort(iris[:, 0])
>>> iris_sort = iris[index]
>>> iris_sort[:10]
array([[b'4.3', b'3.0', b'1.1', b'0.1', b'Iris-setosa'],
       [b'4.4', b'3.2', b'1.3', b'0.2', b'Iris-setosa'],
       [b'4.4', b'3.0', b'1.3', b'0.2', b'Iris-setosa'],
       [b'4.4', b'2.9', b'1.4', b'0.2', b'Iris-setosa'],
       [b'4.5', b'2.3', b'1.3', b'0.3', b'Iris-setosa'],
       [b'4.6', b'3.6', b'1.0', b'0.2', b'Iris-setosa'],
       [b'4.6', b'3.1', b'1.5', b'0.2', b'Iris-setosa'],
       [b'4.6', b'3.4', b'1.4', b'0.3', b'Iris-setosa'],
       [b'4.6', b'3.2', b'1.4', b'0.2', b'Iris-setosa'],
       [b'4.7', b'3.2', b'1.3', b'0.2', b'Iris-setosa']], dtype=object)
```

</details>

# 45. 如何在 NumPy 数组中找到最频繁出现的值？

<details>
<summary>English Version</summary>

Title: How to find the most frequent value in a numpy array?

Difficulty Level: L1

Question: Find the most frequent value of `petal length` (3rd column) in iris dataset.

----

</details>

难度：L1

问题：在 iris 数据集中找到 `petal length`（第三列）中最频繁出现的值。

输入：

```python
>>> url = "https://archive.ics.uci.edu/ml/machine-learning-databases/iris/iris.data"
>>> iris = np.genfromtxt(url, delimiter=",", dtype= object)
>>> names = ("sepallength", "sepalwidth", "petallength", "petalwidth", "species")
```

<details>
<summary>Solution</summary>

```python
>>> uniques, counts = np.unique(iris[:, 2], return_counts=True)
>>> uniques[np.argmax(counts)]
b'1.5'
```

</details>

# 46. 如何找到第一个大于给定值的数的位置？

<details>
<summary>English Version</summary>

Title: How to find the position of the first occurrence of a value greater than a given value?

Difficulty Level: L2

Question: Find the position of the first occurrence of a value greater than 1.0 in `petal width` 4th column of iris dataset.

----

</details>

难度：L2

问题：在 iris 数据集的 `petal width`（第四列）中找到第一个值大于 1.0 的数的位置。

输入：

```python
>>> url = "https://archive.ics.uci.edu/ml/machine-learning-databases/iris/iris.data"
>>> iris = np.genfromtxt(url, delimiter=",", dtype=object)
```

<details>
<summary>Solution 1</summary>

```python
>>> np.argwhere(iris[:, 3].astype(float) > 1.0)[0][0]
50
>>> np.where(iris[:, 3].astype(float) > 1.0)[0][0]
50
```

</details>

<details>
<summary>Solution 2</summary>

```python
>>> index = np.arange(len(iris))
>>> index = index[iris[:, 3].astype(float) > 1.0]
>>> index[0]
50
```

</details>

# 47. 如何将数组中所有大于给定值的数替换为给定的 cutoff 值？

<details>
<summary>English Version</summary>

Title: How to replace all values greater than a given value to a given cutoff?

Difficulty Level: L2

Question: From the array `a`, replace all values greater than 30 to 30 and less than 10 to 10.

----

</details>

难度：L2

问题：对于数组 `a`，将所有大于 30 的值替换为 30，将所有小于 10 的值替换为 10。

输入：

```python
>>> np.random.seed(100)
>>> a = np.random.uniform(1, 50, 20)
```

<details>
<summary>Solution 1</summary>

```python
# Cutoff in-place
>>> a[a > 30] = 30
>>> a[a < 10] = 10
>>> a[:5]
array([27.62684215, 14.64009987, 21.80136195, 30.        , 10.        ])
```

</details>

<details>
<summary>Solution 2</summary>

```python
>>> a_cutoff = np.clip(a, a_min=10, a_max=30)
>>> a_cutoff[:5]
array([27.62684215, 14.64009987, 21.80136195, 30.        , 10.        ])
```

</details>

<details>
<summary>Solution 3</summary>

```python
>>> a_cutoff = np.where(a < 10, 10, np.where(a > 30, 30, a))
>>> a_cutoff[:5]
array([27.62684215, 14.64009987, 21.80136195, 30.        , 10.        ])
```

</details>

# 48. 如何在 NumPy 数组中找到 top-n 数值的位置？

<details>
<summary>English Version</summary>

Title: How to get the positions of top n values from a numpy array?

Difficulty Level: L2

Question: Get the positions of top 5 maximum values in a given array `a`.

----

</details>

难度：L2

问题：在给定数组 `a` 中找到 top-5 最大值的位置。

输入：

```python
>>> np.random.seed(100)
>>> a = np.random.uniform(1, 50, 20)
>>> a
array([27.62684215, 14.64009987, 21.80136195, 42.39403048,  1.23122395,
        6.95688692, 33.86670515, 41.466785  ,  7.69862289, 29.17957314,
       44.67477576, 11.25090398, 10.08108276,  6.31046763, 11.76517714,
       48.95256545, 40.77247431,  9.42510962, 40.99501269, 14.42961361])
```

<details>
<summary>Solution 1</summary>

```python
>>> index = np.argsort(a)[::-1]
>>> index[:5]
array([15, 10,  3,  7, 18])
```

</details>

<details>
<summary>Solution 2</summary>

```python
# Assume each element in array `a` is nonnegative
>>> index = np.argpartition(-a, 5)
>>> index[:5]
array([15, 10,  3,  7, 18])
```

</details>

# 49. 如何逐行计算数组中所有值的数量？

<details>
<summary>English Version</summary>

Title: How to compute the row wise counts of all possible values in an array?

Difficulty Level: L4

Question: Compute the counts of unique values row-wise.

----

</details>

难度：L4

问题：逐行计算唯一值的数量。

输入：

```python
>>> np.random.seed(100)
>>> arr = np.random.randint(1, 11, size=(6, 10))
>>> arr
array([[ 9,  9,  4,  8,  8,  1,  5,  3,  6,  3],
       [ 3,  3,  2,  1,  9,  5,  1, 10,  7,  3],
       [ 5,  2,  6,  4,  5,  5,  4,  8,  2,  2],
       [ 8,  8,  1,  3, 10, 10,  4,  3,  6,  9],
       [ 2,  1,  8,  7,  3,  1,  9,  3,  6,  2],
       [ 9,  2,  6,  5,  3,  9,  4,  6,  1, 10]])
```

期望输出：

```python
[[1, 0, 2, 1, 1, 1, 0, 2, 2, 0],
 [2, 1, 3, 0, 1, 0, 1, 0, 1, 1],
 [0, 3, 0, 2, 3, 1, 0, 1, 0, 0],
 [1, 0, 2, 1, 0, 1, 0, 2, 1, 2],
 [2, 2, 2, 0, 0, 1, 1, 1, 1, 0],
 [1, 1, 1, 1, 1, 2, 0, 0, 2, 1]]
```

输出包含 10 个列，表示从 1 到 10 的数字。这些数值分别代表每一行的计数数量。例如，Cell(0, 2) 中有值 2，这意味着，数字 3 在第一行出现了两次。

<details>
<summary>Solution 1</summary>

```python
# Assume each number is in [1, 10]
>>> results = []
>>> for row in arr:
...     uniques, counts = np.unique(row, return_counts=True)
...     zeros = np.zeros(10, dtype=int)
...     zeros[uniques-1] = counts
...     results.append(zeros.tolist())
...
>>> np.array(results)
array([[1, 0, 2, 1, 1, 1, 0, 2, 2, 0],
       [2, 1, 3, 0, 1, 0, 1, 0, 1, 1],
       [0, 3, 0, 2, 3, 1, 0, 1, 0, 0],
       [1, 0, 2, 1, 0, 1, 0, 2, 1, 2],
       [2, 2, 2, 0, 0, 1, 1, 1, 1, 0],
       [1, 1, 1, 1, 1, 2, 0, 0, 2, 1]])
```

</details>

<details>
<summary>Solution 2</summary>

```python
# More general
>>> def counts_of_all_values_rowwise(arr2d):
...     # Unique values and its counts row wise
...     return([[int(b[a==i]) if i in a else 0 for i in np.unique(arr2d)] for a, b in num_counts_array])
...
>>> np.array(counts_of_all_values_rowwise(arr))
array([[1, 0, 2, 1, 1, 1, 0, 2, 2, 0],
       [2, 1, 3, 0, 1, 0, 1, 0, 1, 1],
       [0, 3, 0, 2, 3, 1, 0, 1, 0, 0],
       [1, 0, 2, 1, 0, 1, 0, 2, 1, 2],
       [2, 2, 2, 0, 0, 1, 1, 1, 1, 0],
       [1, 1, 1, 1, 1, 2, 0, 0, 2, 1]])
```

</details>

# 50. 如何将 array_of_arrays 转换为平面 1 维数组？

<details>
<summary>English Version</summary>

Title: How to convert an array of arrays into a flat 1d array?

Difficulty Level: 2

Question: Convert `array_of_arrays` into a flat linear 1d array.

----

</details>

难度：L2

问题：将 `array_of_arrays` 转换为平面线性 1 维数组。

输入：

```python
>>> arr1 = np.arange(3)
>>> arr2 = np.arange(3, 7)
>>> arr3 = np.arange(7, 10)
>>> array_of_arrays = np.array([arr1, arr2, arr3])
>>> array_of_arrays
array([array([0, 1, 2]), array([3, 4, 5, 6]), array([7, 8, 9])],
      dtype=object)
```

期望输出：

```python
array([0, 1, 2, 3, 4, 5, 6, 7, 8, 9])
```

<details>
<summary>Solution 1</summary>

```python
>>> arr2d = np.concatenate([arr for arr in array_of_arrays])
>>> arr2d
array([0, 1, 2, 3, 4, 5, 6, 7, 8, 9])
```

</details>

<details>
<summary>Solution 2</summary>

```python
>>> arr2d = np.array([a for arr in array_of_arrays for a in arr])
>>> arr2d
array([0, 1, 2, 3, 4, 5, 6, 7, 8, 9])
```

</details>

# 51. 如何为 NumPy 数组生成 one-hot 编码？

<details>
<summary>English Version</summary>

Title: How to generate one-hot encodings for an array in numpy?

Difficulty Level L4

Question: Compute the one-hot encodings (dummy binary variables for each unique value in the array).

----

</details>

难度：L4

问题：计算 one-hot 编码。

输入：

```python
>>> np.random.seed(101)
>>> arr = np.random.randint(1, 4, size=6)
>>> arr
array([2, 3, 2, 2, 2, 1])
```

期望输出：

```python
array([[0., 1., 0.],
       [0., 0., 1.],
       [0., 1., 0.],
       [0., 1., 0.],
       [0., 1., 0.],
       [1., 0., 0.]])
```

<details>
<summary>Solution 1</summary>

```python
>>> arr_shift = arr - 1
>>> one_hot = np.eye(3)[arr_shift]
>>> one_hot
array([[0., 1., 0.],
       [0., 0., 1.],
       [0., 1., 0.],
       [0., 1., 0.],
       [0., 1., 0.],
       [1., 0., 0.]])
```

</details>

<details>
<summary>Solution 2</summary>

```python
>>> def one_hot_encodings(arr):
...     uniqs = np.unique(arr)
...     out = np.zeros((arr.shape[0], uniqs.shape[0]))
...     for i, k in enumerate(arr):
...         out[i, k-1] = 1
...     return out
...
>>> one_hot_encodings(arr)
array([[0., 1., 0.],
       [0., 0., 1.],
       [0., 1., 0.],
       [0., 1., 0.],
       [0., 1., 0.],
       [1., 0., 0.]])
```

</details>

# 52. 如何创建由类别变量分组确定的一维数值？

<details>
<summary>English Version</summary>

Title: How to create row numbers grouped by a categorical variable?

Difficulty Level: L3

Question: Create row numbers grouped by a categorical variable. Use the following sample from iris `species` as input.

----

</details>

难度：L3

问题：创建由类别变量分组的行数。使用以下来自 iris `species` 的样本作为输入。

输入：

```python
>>> url = "https://archive.ics.uci.edu/ml/machine-learning-databases/iris/iris.data"
>>> species = np.genfromtxt(url, delimiter=",", dtype=str, usecols=4)
>>> np.random.seed(100)
>>> species_small = np.sort(np.random.choice(species, size=20))
>>> species_small
array(['Iris-setosa', 'Iris-setosa', 'Iris-setosa', 'Iris-setosa',
       'Iris-setosa', 'Iris-versicolor', 'Iris-versicolor',
       'Iris-versicolor', 'Iris-versicolor', 'Iris-versicolor',
       'Iris-versicolor', 'Iris-versicolor', 'Iris-versicolor',
       'Iris-versicolor', 'Iris-virginica', 'Iris-virginica',
       'Iris-virginica', 'Iris-virginica', 'Iris-virginica',
       'Iris-virginica'], dtype='<U15')
```

期望输出：

```python
[0, 1, 2, 3, 4, 0, 1, 2, 3, 4, 5, 6, 7, 8, 0, 1, 2, 3, 4, 5]
```

<details>
<summary>Solution 1</summary>

```python
>>> groups = []
>>> for val in np.unique(species_small):
...     groups.append(np.arange(len(species_small[species_small == val])))
...
>>> np.concatenate(groups).tolist()
[0, 1, 2, 3, 4, 0, 1, 2, 3, 4, 5, 6, 7, 8, 0, 1, 2, 3, 4, 5]
```

</details>

<details>
<summary>Solution 2</summary>

```python
>>> [i for val in np.unique(species_small) for i, grp in enumerate(species_small[species_small==val])]
[0, 1, 2, 3, 4, 0, 1, 2, 3, 4, 5, 6, 7, 8, 0, 1, 2, 3, 4, 5]
```

</details>

# 53. 如何基于给定的类别变量创建分组 id？

<details>
<summary>English Version</summary>

Title: How to create groud ids based on a given categorical variable?

Difficulty Level: L4

Question: Create group ids based on a given categorical variable. Use the following sample from iris `species` as input.

----

</details>

难度：L4

问题：基于给定的类别变量创建分组 id。使用以下来自 iris `species` 的样本作为输入。

输入：

```python
>>> url = "https://archive.ics.uci.edu/ml/machine-learning-databases/iris/iris.data"
>>> species = np.genfromtxt(url, delimiter=",", dtype=str, usecols=4)
>>> np.random.seed(100)
>>> species_small = np.sort(np.random.choice(species, size=20))
>>> species_small
array(['Iris-setosa', 'Iris-setosa', 'Iris-setosa', 'Iris-setosa',
       'Iris-setosa', 'Iris-versicolor', 'Iris-versicolor',
       'Iris-versicolor', 'Iris-versicolor', 'Iris-versicolor',
       'Iris-versicolor', 'Iris-versicolor', 'Iris-versicolor',
       'Iris-versicolor', 'Iris-virginica', 'Iris-virginica',
       'Iris-virginica', 'Iris-virginica', 'Iris-virginica',
       'Iris-virginica'], dtype='<U15')
```

期望输出：

```python
[0, 0, 0, 0, 0, 1, 1, 1, 1, 1, 1, 1, 1, 1, 2, 2, 2, 2, 2, 2]
```

<details>
<summary>Solution</summary>

```python
>>> output = np.full(len(species_small), 0)
>>> uniques = np.unique(species_small)
>>> for val in uniques:
...     group_id = np.where(uniques == val)[0][0]
...     output[species_small == val] = group_id
...
>>> output.tolist()
[0, 0, 0, 0, 0, 1, 1, 1, 1, 1, 1, 1, 1, 1, 2, 2, 2, 2, 2, 2]
```

</details>

# 54. 如何使用 NumPy 对数组中的项进行排序？

<details>
<summary>English Version</summary>

Title: How to rank items in an array using numpy?

Difficulty Level: L2

Question: Create the ranks for the given numeric array `a`.

----

</details>

难度：L2

问题：为给定的数值数组 `a` 创建排序。

输入：

```python
>>> np.random.seed(10)
>>> a = np.random.randint(20, size=10)
>>> a
array([ 9,  4, 15,  0, 17, 16, 17,  8,  9,  0])
```

期望输出：

```python
array([4, 2, 6, 0, 8, 7, 9, 3, 5, 1])
```

<details>
<summary>Solution</summary>

```python
>>> np.argsort(np.argsort(a))
array([4, 2, 6, 0, 8, 7, 9, 3, 5, 1])
```

</details>

# 55. 如何使用 NumPy 对多维数组中的项进行排序？

<details>
<summary>English Version</summary>

Title: How to rank items in a multidimensional array using numpy?

Difficulty Level: L3

Question: Create a rank array of the same shape as a given numeric array `a`.

----

</details>

难度：L3

问题：给出一个数值数组 `a`，创建一个形态相同的排序数组。

输入：

```python
>>> np.random.seed(10)
>>> a = np.random.randint(20, size=[2, 5])
>>> a
array([[ 9,  4, 15,  0, 17],
       [16, 17,  8,  9,  0]])
```

期望输出：

```python
array([[4, 2, 6, 0, 8],
       [7, 9, 3, 5, 1]])
```

<details>
<summary>Solution 1</summary>

```python
>>> a_flat = a.flatten()
>>> sort_idx = np.argsort(np.argsort(a_flat))
>>> sort_idx.reshape((2, -1))
array([[4, 2, 6, 0, 8],
       [7, 9, 3, 5, 1]])
```

</details>

<details>
<summary>Solution 2</summary>

```python
>>> a.ravel().argsort().argsort().reshape(a.shape)
array([[4, 2, 6, 0, 8],
       [7, 9, 3, 5, 1]])
```

</details>

# 56. 如何在 2 维 NumPy 数组中找到每一行的最大值？

<details>
<summary>English Version</summary>

Title: How to find the maximum value in each row of a numpy array 2d?

Difficulty Level: L2

Question: Compute the maximum for each row in the given array.

----

</details>

难度：L2

问题：在给定数组中找到每一行的最大值。

```python
>>> np.random.seed(100)
>>> a = np.random.randint(1, 10, [5, 3])
>>> a
array([[9, 9, 4],
       [8, 8, 1],
       [5, 3, 6],
       [3, 3, 3],
       [2, 1, 9]])
```

<details>
<summary>Solution 1</summary>

```python
>>> np.amax(a, axis=1)
array([9, 8, 6, 3, 9])
```

</details>

<details>
<summary>Solution 2</summary>

```python
>>> np.apply_along_axis(np.max, arr=a, axis=1)
array([9, 8, 6, 3, 9])
```

</details>

# 57. 如何计算 2 维 NumPy 数组每一行的 min-by-max？

<details>
<summary>English Version</summary>

Title: How to compute the min-by-max for each row for a numpy array 2d?

Difficulty Level: L3

Question: Compute the min-by-max for each row for given 2d numpy array.

----

</details>

难度：L3

问题：给定一个 2 维 NumPy 数组，计算每一行的 min-by-max。

```python
>>> np.random.seed(100)
>>> a = np.random.randint(1, 10, [5, 3])
>>> a
array([[9, 9, 4],
       [8, 8, 1],
       [5, 3, 6],
       [3, 3, 3],
       [2, 1, 9]])
```

<details>
<summary>Solution</summary>

```python
>>> np.apply_along_axis(lambda x: np.min(x)/np.max(x), axis=1, arr=a)
array([0.44444444, 0.125     , 0.5       , 1.        , 0.11111111])
```

</details>

# 58. 如何在 NumPy 数组中找到重复条目？

<details>
<summary>English Version</summary>

Title: How to find the duplicate records in a numpy array?

Difficulty Level: L3

Question: Find the duplicate entries (2nd occurrence onwards) in the given numpy array and mark them as `True`. First time occurrences should be `False`.

----

</details>

难度：L3

问题：在给定的 NumPy 数组中找到重复条目（从第二次出现开始），并将其标记为 `True`。第一次出现的条目需要标记为 `False`。

输入：

```python
>>> np.random.seed(100)
>>> a = np.random.randint(0, 5, 10)
>>> a
array([0, 0, 3, 0, 2, 4, 2, 2, 2, 2])
```

期望输出：

```python
array([False,  True, False,  True, False, False,  True,  True,  True,
        True])
```

<details>
<summary>Solution</summary>

```python
>>> out = np.full(a.shape[0], True)
>>> unique_positions = np.unique(a, return_index=True)[1]
>>> out[unique_positions] = False
>>> out
array([False,  True, False,  True, False, False,  True,  True,  True,
        True])
```

</details>

# 59. 如何找到 NumPy 的分组平均值？

<details>
<summary>English Version</summary>

Title: How to find the grouped mean in numpy?

Difficulty Level L3

Question: Find the mean of a numeric column grouped by a categorical column in a 2D numpy array.

----

</details>

难度：L3

问题：在 2 维 NumPy 数组的类别列中找到数值 `sepal length` 的平均值。

输入：

```python
url = "https://archive.ics.uci.edu/ml/machine-learning-databases/iris/iris.data"
iris = np.genfromtxt(url, delimiter=",", dtype=object)
names = ("sepallength", "sepalwidth", "petallength", "petalwidth", "species")
```

期望输出：

```python
[[b'Iris-setosa', 3.418],
 [b'Iris-versicolor', 2.770],
 [b'Iris-virginica', 2.974]]
```

<details>
<summary>Solution</summary>

```python
>>> uniques = np.unique(iris[:, 4])
>>> output = []
>>> for v in uniques:
...     group = iris[iris[:, 4] == v]
...     output.append([v, np.mean(group[:, 1].astype(float))])
...
>>> output
[[b'Iris-setosa', 3.418], [b'Iris-versicolor', 2.7700000000000005], [b'Iris-virginica', 2.974]]
```

</details>

# 60. 如何将 PIL 图像转换成 NumPy 数组？

<details>
<summary>English Version</summary>

Title: How to convert a PIL image to numpy array?

Difficulty Level: L3

Question: Import the image from the following url and convert it to a numpy array.

----

</details>

难度：L3

问题：从以下 url 中导入图像，并将其转换成 NumPy 数组。

```python
>>> url = "https://upload.wikimedia.org/wikipedia/commons/8/8b/Denali_Mt_McKinley.jpg"
```

<details>
<summary>Solution</summary>

```python
>>> import requests
>>> from io import BytesIO
>>> from PIL import Image
>>> response = requests.get(url)
>>> img = Image.open(BytesIO(response.content))
>>> img_arr = np.asarray(img)
>>> img_arr[:5, :5]
array([[[  9,  72, 125],
        [  9,  72, 125],
        [  9,  72, 125],
        [ 10,  73, 126],
        [ 10,  73, 126]],

       [[  9,  72, 125],
        [  9,  72, 125],
        [ 10,  73, 126],
        [ 10,  73, 126],
        [ 10,  73, 126]],

       [[  9,  72, 125],
        [ 10,  73, 126],
        [ 10,  73, 126],
        [ 10,  73, 126],
        [ 11,  74, 127]],

       [[ 10,  73, 126],
        [ 10,  73, 126],
        [ 10,  73, 126],
        [ 11,  74, 127],
        [ 11,  74, 127]],

       [[ 10,  73, 126],
        [ 10,  73, 126],
        [ 11,  74, 127],
        [ 11,  74, 127],
        [ 11,  74, 127]]], dtype=uint8)
```

</details>

# 61. 如何删除 NumPy 数组中所有的缺失值？

<details>
<summary>English Version</summary>

Title: How to drop all missing values from a numpy array?

Difficulty Level: L2

Question: Drop all `nan` values from a 1D numpy array.

----

</details>

难度：L2

问题：从 1 维 NumPy 数组中删除所有的 `nan` 值。

输入：

```python
>>> arr = np.array([1, 2, 3, np.nan, 5, 6, 7, np.nan])
>>> arr
array([ 1.,  2.,  3., nan,  5.,  6.,  7., nan])
```

期望输出：

```python
array([1., 2., 3., 5., 6., 7.])
```

<details>
<summary>Solution</summary>

```python
>>> arr[~np.isnan(arr)]
array([1., 2., 3., 5., 6., 7.])
```

</details>

# 62. 如何计算两个数组之间的欧几里得距离？

<details>
<summary>English Version</summary>

Title: How to compute the euclidean distance between two arrays?

Difficulty Level: L1

Question: Compute the euclidean distance between two arrays `a` and `b`.

----

</details>

难度：L1

问题：计算两个数组 `a` 和 `b` 之间的欧几里得距离。

输入：

```python
>>> a = np.array([1, 2, 3, 4, 5])
>>> b = np.array([4, 5, 6, 7, 8])
>>> a
array([1, 2, 3, 4, 5])
>>> b
array([4, 5, 6, 7, 8])
```

<details>
<summary>Solution 1</summary>

```python
>>> np.sqrt(np.sum((a-b)**2))
6.708203932499369
```

</details>

<details>
<summary>Solution 2</summary>

```python
>>> np.linalg.norm(a-b)
6.708203932499369
```

</details>

# 63. 如何在一个 1 维数组中找到所有的局部极大值（peak）？

<details>
<summary>English Version</summary>

Title: How to find all the local maxima (or peaks) in a 1d array?

Difficulty Level: L4

Question: Find all the peaks in a 1D numpy array `a`. Peaks are points surrounded by smaller values on both sides.

----

</details>

难度：L4

问题：在 1 维数组 `a` 中找到所有的 peak。peak 是指一个数字比两侧的数字都大。

输入：

```python
>>> a = np.array([1, 3, 7, 1, 2, 6, 0, 1])
>>> a
array([1, 3, 7, 1, 2, 6, 0, 1])
```

期望输出：

```python
array([2, 5])
```

其中 2 和 5 是局部最大值 7 和 6 的下标。

<details>
<summary>Solution</summary>

```python
>>> double_diff = np.diff(np.sign(np.diff(a)))
>>> peak_locations = np.where(double_diff == -2)[0] + 1
>>> peak_locations
array([2, 5])
```

</details>

# 64. 如何从 2 维数组中减去 1 维数组，从 2 维数组的每一行分别减去 1 维数组的每一项？

<details>
<summary>English Version</summary>

Title: How to subtract a 1d array from a 2d array, where each item of 1d array subtracts from respective row?

Difficulty Level: L2

Question: Subtract the 1d array `b_1d` from the 2d array `a_2d`, such that each item of `b_1d` subtracts from respective row of `a_2d`.

----

</details>

难度：L2

问题：从 2 维数组 `a_2d` 中减去 1 维数组 `b_1d`，即从 `a_2d` 的每一行分别减去 `b_1d` 的每一项。

输入：

```python
>>> a_2d = np.array([[3, 3, 3],[4, 4, 4],[5, 5, 5]])
>>> b_1d = np.array([1, 2, 3])
>>> a_2d
array([[3, 3, 3],
       [4, 4, 4],
       [5, 5, 5]])
>>> b_1d
array([1, 2, 3])
```

期望输出：

```python
array([[2, 2, 2],
       [2, 2, 2],
       [2, 2, 2]])
```

<details>
<summary>Solution</summary>

```python
>>> a_2d - b_1d[:, np.newaxis]
array([[2, 2, 2],
       [2, 2, 2],
       [2, 2, 2]])
```

</details>

# 65. 如何在数组中找出某个项的第 n 个重复索引？

<details>
<summary>English Version</summary>

Title: How to find the index of n'th repetition of an item in an array

Difficulty Level L2

Question: Find the index of 5th repetition of number 1 in `x`.

</details>

难度：L2

问题：找到数组 `x` 中数字 1 的第 5 个重复索引。

输入：

```python
>>> x = np.array([1, 2, 1, 1, 3, 4, 3, 1, 1, 2, 1, 1, 2])
```

<details>
<summary>Solution 1</summary>

```python
>>> n = 5
>>> [i for i, v in enumerate(x) if v == 1][n-1]
8
```

</details>

<details>
<summary>Solution 2</summary>

```python
>>> n = 5
>>> index = np.arange(len(x))
>>> index[x == 1][n-1]
8
```

</details>

<details>
<summary>Solution 3</summary>

```python
>>> n = 5
>>> np.where(x == 1)[0][n-1]
8
```

</details>

# 66. 如何将 NumPy 的 datetime64 对象（object）转换为 datetime 的 datetime 对象？

<details>
<summary>English Version</summary>

Title: How to convert numpy's datetime64 object to datetime's datetime object?

Difficulty Level: L2

Question: Convert numpy's `datetime64` object to datetime's `datetime` object.

----

</details>

难度：L2

问题：将 NumPy 的 `datetime64` 对象转换为 datetime 的 `datetime` 对象。

```python
# Input: a numpy datetime64 object
>>> dt64 = np.datetime64("2018-02-25 22:10:10")
```

<details>
<summary>Solution 1</summary>

```python
>>> dt64.tolist()
datetime.datetime(2018, 2, 25, 22, 10, 10)
```

</details>

<details>
<summary>Solution 2</summary>

```python
>>> from datetime import datetime
>>> dt64.astype(datetime)
datetime.datetime(2018, 2, 25, 22, 10, 10)
```

</details>

# 67. 如何计算 NumPy 数组的移动平均数？

<details>
<summary>English Version</summary>

Title: How to compute the moving average of a numpy array?

Difficulty Level: L3

Question: Compute the moving average of window size 3, for the given 1D array.

----

</details>

难度：L3

问题：给定 1 维数组，计算 window size 为 3 的移动平均数。

输入：

```python
>>> np.random.seed(100)
>>> Z = np.random.randint(10, size=10)
>>> Z
array([8, 8, 3, 7, 7, 0, 4, 2, 5, 2])
```

<details>
<summary>Solution 1</summary>

Source: [How to calculate moving average using NumPy?](https://stackoverflow.com/questions/14313510/how-to-calculate-moving-average-using-numpy)

```python
>>> def moving_average(a, n=3):
...     ret = np.cumsum(a, dtype=float)
...     ret[n:] = ret[n:] - ret[:-n]
...     return ret[n-1:] / n
...
>>> moving_average(Z, n=3).round(2)
array([6.33, 6.  , 5.67, 4.67, 3.67, 2.  , 3.67, 3.  ])
```

</details>

<details>
<summary>Solution 2</summary>

```python
>>> np.convolve(Z, np.ones(3)/3, mode="valid").round(2)
array([6.33, 6.  , 5.67, 4.67, 3.67, 2.  , 3.67, 3.  ])
```

</details>

# 68. 给定起始数字、length 和步长，如何创建一个 NumPy 数组序列？

<details>
<summary>English Version</summary>

Title: How to create a numpy array sequence given only the starting point, length and the step?

Difficulty Level: L2

Question: Create a numpy array of length 10, starting from 5 and has a step of 3 between consecutive numbers.

----

</details>

难度：L2

问题：从 5 开始，创建一个 length 为 10 的 NumPy 数组，相邻数字的差是 3。

<details>
<summary>Solution 1</summary>

```python
>>> def seq(start, length, step):
...     end = start + (step*length)
...     return np.arange(start, end, step)
...
>>> seq(5, 10, 3)
array([ 5,  8, 11, 14, 17, 20, 23, 26, 29, 32])
```

</details>

<details>
<summary>Solution 2</summary>

```python
>>> np.arange(5, 5+3*10, 3)
array([ 5,  8, 11, 14, 17, 20, 23, 26, 29, 32])
```

</details>

# 69. 如何在不规则 NumPy 日期序列中填充缺失日期？

<details>
<summary>English Version</summary>

Title: How to fill in missing dates in an irregular series of numpy dates?

Difficulty Level: L3

Question: Given an array of a non-continuous sequence of dates. Make it a continuous sequence of dates, by filling in the missing dates.

</details>

难度：L3

问题：给定一个非连续日期序列的数组，通过填充缺失的日期，使其变成连续的日期序列。

输入：

```python
>>> dates = np.arange(np.datetime64("2018-02-01"), np.datetime64("2018-02-25"), 2)
>>> dates
array(['2018-02-01', '2018-02-03', '2018-02-05', '2018-02-07',
       '2018-02-09', '2018-02-11', '2018-02-13', '2018-02-15',
       '2018-02-17', '2018-02-19', '2018-02-21', '2018-02-23'],
      dtype='datetime64[D]')
```

<details>
<summary>Solution 1</summary>

```python
>>> out = []
>>> for date, d in zip(dates, np.diff(dates)):
...     out.append(np.arange(date, (date+d)))
...
>>> filled_in = np.array(out).reshape(-1)
>>> output = np.hstack([filled_in, dates[-1]])
>>> output
array(['2018-02-01', '2018-02-02', '2018-02-03', '2018-02-04',
       '2018-02-05', '2018-02-06', '2018-02-07', '2018-02-08',
       '2018-02-09', '2018-02-10', '2018-02-11', '2018-02-12',
       '2018-02-13', '2018-02-14', '2018-02-15', '2018-02-16',
       '2018-02-17', '2018-02-18', '2018-02-19', '2018-02-20',
       '2018-02-21', '2018-02-22', '2018-02-23'], dtype='datetime64[D]')
```

</details>

<details>
<summary>Solution 2</summary>

```python
>>> filled_in = np.array([np.arange(date, (date+d)) for date, d in zip(dates, np.diff(dates))]).reshape(-1)
>>> output = np.hstack([filled_in, dates[-1]])
>>> output
array(['2018-02-01', '2018-02-02', '2018-02-03', '2018-02-04',
       '2018-02-05', '2018-02-06', '2018-02-07', '2018-02-08',
       '2018-02-09', '2018-02-10', '2018-02-11', '2018-02-12',
       '2018-02-13', '2018-02-14', '2018-02-15', '2018-02-16',
       '2018-02-17', '2018-02-18', '2018-02-19', '2018-02-20',
       '2018-02-21', '2018-02-22', '2018-02-23'], dtype='datetime64[D]')
```

</details>

# 70. 如何基于给定的 1 维数组创建 strides？

<details>
<summary>English Version</summary>

Title: How to create strides from a given 1D array?

Difficulty Level: L4

Question: From the given 1d array `arr`, generate a 2d matrix using strides, with a window length of 4 and strides of 2, like [[0,1,2,3], [2,3,4,5], [4,5,6,7]..]

----

</details>

难度：L4

问题：给定 1 维数组 `arr`，使用 strides 生成一个 2 维矩阵，其中 window length 等于 4，strides 等于 2，例如 [[0,1,2,3], [2,3,4,5], [4,5,6,7]..]。

输入：

```python
>>> arr = np.arange(15)
>>> arr
array([ 0,  1,  2,  3,  4,  5,  6,  7,  8,  9, 10, 11, 12, 13, 14])
```

期望输出：

```
array([[ 0,  1,  2,  3],
       [ 2,  3,  4,  5],
       [ 4,  5,  6,  7],
       [ 6,  7,  8,  9],
       [ 8,  9, 10, 11],
       [10, 11, 12, 13]])
```

<details>
<summary>Solution</summary>

```python
>>> def gen_strides(a, stride_len=5, window_len=5):
...     n_strides = ((a.size - window_len) // stride_len) + 1
...     return np.array([a[s:(s+window_len)] for s in np.arange(0, n_strides*stride_len, stride_len)])
...
>>> gen_strides(np.arange(15), stride_len=2, window_len=4)
array([[ 0,  1,  2,  3],
       [ 2,  3,  4,  5],
       [ 4,  5,  6,  7],
       [ 6,  7,  8,  9],
       [ 8,  9, 10, 11],
       [10, 11, 12, 13]])
```

</details>

# References

1. [101 NumPy Exercises for Data Analysis (Python)](https://www.machinelearningplus.com/python/101-numpy-exercises-python/)
2. [70 道 NumPy 测试题](https://mp.weixin.qq.com/s/lyga06w4SSGgB-G7eHJwew)