---
title: Pandas备忘录2
date: 2018-09-03 22:30:02
urlname: pandas_memo2
list_number: false
tags:
 - Python
 - Pandas
---

## 1. Pandas备忘录系列文章

1. {% post_link Pandas备忘录 Pandas备忘录 %}

## 2. 介绍

__Note:__ 如果没有特别说明，`pd`指的是`pandas`，`np`指的是`numpy`。

```python
import pandas as pd
import numpy as np
```

## 3. 查找

### 3.1. contains函数

主要是用`Series.str.contains`这个函数，如果参数`regex`设为True，则查找字符串解析为正则表达式，设为False则用常规查找方式

```
>>> s1 = pd.Series(['Mouse', 'dog', 'house and parrot', '23', 'frog'])
>>> s1
0               Mouse
1                 dog
2    house and parrot
3                  23
4                frog
dtype: object
>>> s1.str.contains('og', regex=False)
0    False
1     True
2    False
3    False
4     True
dtype: bool
>>> s1[s1.str.contains('og', regex=False)]
1     dog
4    frog
dtype: object
```

## 4. DataFrame增加行

### 4.1. append函数

可以使用`append`函数给DataFrame增加行

```
>>> df = pd.DataFrame([[1, 2], [3, 4]], columns=list('AB'))
>>> df
   A  B
0  1  2
1  3  4
>>> df2 = pd.DataFrame([[5, 6]], columns=list('AB'))
>>> df2
   A  B
0  5  6
>>> df.append(df2, ignore_index=True)
   A  B
0  1  2
1  3  4
2  5  6
```

### 4.2. 使用特定索引名称增加行

使用DataFrame的`loc`函数可以指定索引名称增加行，前提是得知道列的排列顺序，因此在创建DataFrame时要用`columns`参数指定列的排列顺序

```
>>> employees = pd.DataFrame(
...     data={'Name': ['John Doe', 'William Spark'],
...           'Age': [23, 24]},
...     index=['Emp001', 'Emp002'],
...     columns=['Name', 'Age'])
>>> employees
                 Name  Age
Emp001       John Doe   23
Emp002  William Spark   24
>>> employees.loc['Emp003'] = ['Sunny', 45]
>>> employees
                 Name  Age
Emp001       John Doe   23
Emp002  William Spark   24
Emp003          Sunny   45
```

