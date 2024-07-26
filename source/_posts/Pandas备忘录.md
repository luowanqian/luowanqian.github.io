---
title: Pandas备忘录
date: 2018-05-06 22:29:26
urlname: pandas_memo
list_number: false
tags:
 - Python
 - Pandas
---

## 1. Pandas备忘录系列文章

1. {% post_link Pandas备忘录2 Pandas备忘录2 %}

## 2. 介绍

本文主要是记录一些Pandas的使用方法以及注意事项

__Note:__ 如果没有特别说明，`pd` 指的是 `pandas`，`np` 指的是 `numpy`。

```python
import pandas as pd
import numpy as np
```

## 3. 构建DataFrame

构建一个DataFrame方法有很多，这里介绍一部分常用的方法，即

* From dict of ndarrays / lists
* From a list of dicts

### 3.1. From dict of ndarrays / lists

输入数据为一个字典，字典的key为列名，字典的value为一个Numpy的数组或者一个list，存储DataFrame一列的值

```
>>> data = {'one': [1, 2, 3],
...         'two': [3, 2, 1]}
>>> pd.DataFrame(data)
   one  two
0    1    3
1    2    2
2    3    1
```

### 3.2. From a list of dicts

输入数据为一个list，每个元素为一个字典，存储DataFrame一行的值，字典的key为列名，value为一个DataFrame中一个元素。

```
>>> data = [{'one': 1, 'two': 3},
...         {'one': 2, 'two': 2},
...         {'one': 3, 'two': 1}]
>>> pd.DataFrame(data)
   one  two
0    1    3
1    2    2
2    3    1
```

## 4. Series索引

Series索引类似Numpy的数组索引，除了可以使用 integer 作为索引值，还可以使用 index 作为索引值

```
>>> obj = pd.Series(np.arange(4.), index=['a', 'b', 'c', 'd'])
>>> obj
a    0.0
b    1.0
c    2.0
d    3.0
dtype: float64
>>> obj['b']
1.0
>>> obj[1]
1.0
>>> obj[:2]
a    0.0
b    1.0
dtype: float64
>>> obj[obj < 2]
a    0.0
b    1.0
dtype: float64

# 如果使用 label 索引，则索引区间为闭区间
>>> obj['b':'c']
b    1.0
c    2.0
dtype: float64
```

## 5. DataFrame索引

先贴张DataFrame索引方法的表格，摘录自《Python for Data Analysis》。

| Type               | Notes                                                        |
| ------------------ | ------------------------------------------------------------ |
| df[val]            | Select single column or sequence of  columns from the DataFrame; special case conveniences: boolean array (filter rows), slice (slice rows), or boolean DataFrame (set values bases on some criterion) |
| df.loc[val]        | Selects single row or subset of rows from the DataFrame by label |
| df.loc[:, val]     | Selects single column or subset of columns by label          |
| df.loc[val1, val2] | Select both rows and columns by label                        |
| df.iloc[where]     | Selects single row of subsets of rows from the DataFrame by integer position |
| df.iloc[:, where] | Selects single column or subset of columns by integer position |
| df.iloc[where_i,  where_j] | Select both rows and columns by integer position |
|df.at[label_i, label_j] | Select a single scalar value by row and column label |
| df.iat[i, j] | Select a single scalar value by row and column position (integers) |

先创建一个数据

```
>>> data = pd.DataFrame(np.arange(16).reshape((4, 4)),
...                     index=['Ohio', 'Colorado', 'Utah', 'New York'],
...                     columns=['one', 'two', 'three', 'four'])
>>> data
          one  two  three  four
Ohio        0    1      2     3
Colorado    4    5      6     7
Utah        8    9     10    11
New York   12   13     14    15
```

### 5.1. df[]语法

```
# 利用单个label选择单列
>>> data['two']
Ohio         1
Colorado     5
Utah         9
New York    13
Name: two, dtype: int64


# 利用多个label选择多列，可以改变列顺序
>>> data[['three', 'one']]
          three  one
Ohio          2    0
Colorado      6    4
Utah         10    8
New York     14   12


# 利用boolean数组选择多行
>>> bools = np.array([False, True, False, True])
>>> bools
array([False,  True, False,  True])
>>> data[bools]
          one  two  three  four
Colorado    4    5      6     7
New York   12   13     14    15


# 利用切片(slice)选择多行，类似Numpy的语法
>>> data[:2]
          one  two  three  four
Ohio        0    1      2     3
Colorado    4    5      6     7


# 利用boolean DataFrame选择数据
>>> data < 5
            one    two  three   four
Ohio       True   True   True   True
Colorado   True  False  False  False
Utah      False  False  False  False
New York  False  False  False  False
>>> data[data < 5] = 0
>>> data
          one  two  three  four
Ohio        0    0      0     0
Colorado    0    5      6     7
Utah        8    9     10    11
New York   12   13     14    15
```

### 5.2. df.loc语法

`df.loc[]` 索引值为 axis labels

```
# 使用 df.loc[val] 选择行
>>> data.loc['Utah']
one       8
two       9
three    10
four     11
Name: Utah, dtype: int64
>>> data.loc[['Utah', 'Ohio']]
      one  two  three  four
Utah    8    9     10    11
Ohio    0    1      2     3
# 如果使用 label 索引，则索引区间为闭区间
>>> data.loc[:'Utah']
          one  two  three  four
Ohio        0    1      2     3
Colorado    4    5      6     7
Utah        8    9     10    11


# 使用 df.loc[:, val] 选择列
>>> data.loc[:, 'one']
Ohio         0
Colorado     4
Utah         8
New York    12
Name: one, dtype: int64
>>> data.loc[:, ['one', 'two']]
          one  two
Ohio        0    1
Colorado    4    5
Utah        8    9
New York   12   13
# 如果使用 label 索引，则索引区间为闭区间
>>> data.loc[:, :'two']
          one  two
Ohio        0    1
Colorado    4    5
Utah        8    9
New York   12   13


# 使用 df.loc[val1, val2] 选择多行多列
>>> data.loc[['Colorado', 'Ohio'], ['two', 'three']]
          two  three
Colorado    5      6
Ohio        1      2
```

### 5.3. df.iloc语法

`df.iloc[]` 索引值为 integers

```
# 使用 df.iloc[where] 选择行
>>> data.iloc[2]
one       8
two       9
three    10
four     11
Name: Utah, dtype: int64
>>> data.iloc[[2,1]]
          one  two  three  four
Utah        8    9     10    11
Colorado    4    5      6     7
>>> data.iloc[:2]
          one  two  three  four
Ohio        0    1      2     3
Colorado    4    5      6     7


# 使用 df.iloc[:, where] 选择列
>>> data.iloc[:, 1]
Ohio         1
Colorado     5
Utah         9
New York    13
Name: two, dtype: int64
>>> data.iloc[:, [2, 0]]
          three  one
Ohio          2    0
Colorado      6    4
Utah         10    8
New York     14   12
>>> data.iloc[:, :2]
          one  two
Ohio        0    1
Colorado    4    5
Utah        8    9
New York   12   13


# 使用 df.iloc[where_i, where_j] 选择多行多列
>>> data.iloc[2, :2]
one    8
two    9
Name: Utah, dtype: int64
>>> data.iloc[:2, :2]
          one  two
Ohio        0    1
Colorado    4    5
```

### 5.4. Reset Index

可以使用DataFrame的`reset_index()`函数将index变为DataFrame的一列，原index替换为递增的整数索引

```
>>> data.reset_index()
      index  one  two  three  four
0      Ohio    0    1      2     3
1  Colorado    4    5      6     7
2      Utah    8    9     10    11
3  New York   12   13     14    15
```

这个函数还有一个用处就是可以给每一行编一个号，如果index已为递增的整数索引，再调用`reset_index()`函数时，就会多出一列，内容为递增整数，相当于给每一行编个号。

```
>>> data2 = data.reset_index()
>>> data2
      index  one  two  three  four
0      Ohio    0    1      2     3
1  Colorado    4    5      6     7
2      Utah    8    9     10    11
3  New York   12   13     14    15
>>> data2.reset_index()
   level_0     index  one  two  three  four
0        0      Ohio    0    1      2     3
1        1  Colorado    4    5      6     7
2        2      Utah    8    9     10    11
3        3  New York   12   13     14    15
```

列`level_0`可以用于数据编号。

## 6. Series数据映射

已知数据

```
>>> data = pd.DataFrame({'Age': [22, 38, 26, 35, 35],
...                      'Sex': ['male', 'female', 'female', 'female', 'male']})
>>> data
   Age     Sex
0   22    male
1   38  female
2   26  female
3   35  female
4   35    male
```

`Sex` 那一列的数据取值有两种，分别为 `female` 和 `male`，此时想要作一个数据映射

```
female -> 0
male -> 1
```

可以使用 `map()` 函数，输入映射参数 (映射用一个字典表示)

```
>>> sex_to_num = {'female': 0, 'male': 1}
>>> data['Sex'] = data['Sex'].map(sex_to_num)
>>> data
   Age  Sex
0   22    1
1   38    0
2   26    0
3   35    0
4   35    1
```

## 7. 缺失值

__Note:__ 本节所述的 `NA` 代表

```
from numpy import nan as NA
```

### 7.1. 缺失值分析

首先分析数据缺失值情况

1. Series

对于 Series，可以使用 `isnull()` 函数判断 Series 中的数据是否为缺失值

```
>>> data = pd.Series([1, NA, 3.5, NA, 7])
>>> data
0    1.0
1    NaN
2    3.5
3    NaN
4    7.0
dtype: float64
>>> data.isnull()
0    False
1     True
2    False
3     True
4    False
dtype: bool
```

要判断有多少个缺失值，可以使用 `sum()` 判断 `isnull()` 返回的 Boolean 数组中有多少个 True。

```
>>> data.isnull().sum()
2
```

2. DataFrame

对于 DataFrame，同样可以使用 `isnull()` 函数判断数据是否为缺失值

```
>>> data = pd.DataFrame([[1., 6.5, 3.], [1, NA, NA],
...                      [NA, NA, NA], [NA, 6.5, 3.]])
>>> data
     0    1    2
0  1.0  6.5  3.0
1  1.0  NaN  NaN
2  NaN  NaN  NaN
3  NaN  6.5  3.0
>>> data.isnull()
       0      1      2
0  False  False  False
1  False   True   True
2   True   True   True
3   True  False  False
```

如果要知道每一列的缺失值是多少，可以使用 `info()` 函数，或者将 `isnull()` 与 `sum()` 配合起来

```
>>> data.info()
<class 'pandas.core.frame.DataFrame'>
RangeIndex: 4 entries, 0 to 3
Data columns (total 3 columns):
0    2 non-null float64
1    2 non-null float64
2    2 non-null float64
dtypes: float64(3)
memory usage: 176.0 bytes
>>> data.isnull().sum()
0    2
1    2
2    2
dtype: int64
```

如果要知道缺失值总数，可以作两次 `sum()`

```
>>> data.isnull().sum().sum()
6
```

### 7.2. 缺失值补全

处理缺失值的一个方法就是使用插值或者自设的值补全缺失值。首先构造一个有缺失值的 DataFrame

```
>>> df = pd.DataFrame(np.random.randn(7, 3))
>>> df.iloc[:4, 1] = NA
>>> df.iloc[:2, 2] = NA
>>> df
          0         1         2
0  1.309480       NaN       NaN
1 -0.542253       NaN       NaN
2 -1.129503       NaN -0.396921
3  1.172466       NaN -1.114420
4  0.893893  0.572520  0.648532
5  0.293066  1.875362 -0.426759
6 -0.389516  0.379184  0.759198
```

1. 使用自设的值补全缺失值

这里主要使用函数 `fillna()`，传入一个值，则缺失值就被设为该值

```
# 缺失值使用0补全
>>> df.fillna(0)
          0         1         2
0  1.309480  0.000000  0.000000
1 -0.542253  0.000000  0.000000
2 -1.129503  0.000000 -0.396921
3  1.172466  0.000000 -1.114420
4  0.893893  0.572520  0.648532
5  0.293066  1.875362 -0.426759
6 -0.389516  0.379184  0.759198
```

如果传入的是一个字典，则可以对每一列的缺失值设定不同的补全值

```
# 第二列缺失值用0.5补全，第三列缺失值用0补全
>>> df.fillna({1: 0.5, 2: 0})
          0         1         2
0  1.309480  0.500000  0.000000
1 -0.542253  0.500000  0.000000
2 -1.129503  0.500000 -0.396921
3  1.172466  0.500000 -1.114420
4  0.893893  0.572520  0.648532
5  0.293066  1.875362 -0.426759
6 -0.389516  0.379184  0.759198
```

2. 插值补全缺失值

这里主要使用函数 `fillna()` 的 `method` 参数，例子中数据由于某列第一个元素有缺失，所以方法选择 backfill。

```
>>> df.fillna(method='backfill')
          0         1         2
0  1.309480  0.572520 -0.396921
1 -0.542253  0.572520 -0.396921
2 -1.129503  0.572520 -0.396921
3  1.172466  0.572520 -1.114420
4  0.893893  0.572520  0.648532
5  0.293066  1.875362 -0.426759
6 -0.389516  0.379184  0.759198
```

### 7.3. 缺失值删除

1. 删除有缺失值的行

DataFrame的`dropna`函数默认是删除有缺失值的行

```
>>> df = pd.DataFrame({"name": ['Alfred', 'Batman', 'Catwoman'],
...                    "toy": [np.nan, 'Batmobile', 'Bullwhip'],
...                    "born": [pd.NaT, pd.Timestamp("1940-04-25"), pd.NaT]})
>>> df
       name        toy       born
0    Alfred        NaN        NaT
1    Batman  Batmobile 1940-04-25
2  Catwoman   Bullwhip        NaT
>>> df.dropna()
     name        toy       born
1  Batman  Batmobile 1940-04-25
```

2. 删除有缺失值的列

设定`dropna`函数的参数axis为1可以删除有缺失值的列

```
>>> df = pd.DataFrame({"name": ['Alfred', 'Batman', 'Catwoman'],
...                    "toy": [np.nan, 'Batmobile', 'Bullwhip'],
...                    "born": [pd.NaT, pd.Timestamp("1940-04-25"), pd.NaT]})
>>> df
       name        toy       born
0    Alfred        NaN        NaT
1    Batman  Batmobile 1940-04-25
2  Catwoman   Bullwhip        NaT
>>> df.dropna(axis=1)
       name
0    Alfred
1    Batman
2  Catwoman
```

## 8. 重复值

已知DataFrame

```
>>> data = pd.DataFrame({'k1': ['one', 'two'] * 3 + ['two'],
...                      'k2': [1, 1, 2, 3, 3, 4, 4]})
>>> data
    k1  k2
0  one   1
1  two   1
2  one   2
3  two   3
4  one   3
5  two   4
6  two   4
```

### 8.1. 删除重复值

可以使用函数`drop_duplicates()`删除重复值

```
>>> data.drop_duplicates()
    k1  k2
0  one   1
1  two   1
2  one   2
3  two   3
4  one   3
5  two   4
```

函数`drop_duplicates()`默认考虑的是全部列，也可以设定某些列来判断是否重复

```
>>> data.drop_duplicates(['k1'])
    k1  k2
0  one   1
1  two   1
```

## 9. 排序

已知DataFrame

```
>>> df = pd.DataFrame({'b': [4, 7, -3, 2], 'a': [0, 1, 0, 1]})
>>> df
   a  b
0  0  4
1  1  7
2  0 -3
3  1  2
```

### 9.1. 根据某些列进行排序

排序使用函数`sort_values()`，如果要根据某些列进行排序，可以设定`by=`参数

```
>>> df.sort_values(by='a')
   a  b
0  0  4
2  0 -3
1  1  7
3  1  2
>>> df.sort_values(by=['a', 'b'])
   a  b
2  0 -3
0  0  4
3  1  2
1  1  7
```

## 10. GroupBy

已知DataFrame

```
>>> df = pd.DataFrame({'key1':['a','a','b','b','a'],
...                    'key2':['one','two','one','two','one'],
...                    'data1':np.random.randn(5),
...                    'data2':np.random.randn(5)})
>>> df
      data1     data2 key1 key2
0  2.462027  0.054159    a  one
1  0.283423 -0.658160    a  two
2 -0.969307 -0.407126    b  one
3 -0.636756  1.925338    b  two
4 -0.408266  1.833710    a  one
```

### 10.1. 查看分组名

DataFrame分了组后，想知道每个分组的名字，可以写为

```
>>> df.groupby('key1').groups
{'a': Int64Index([0, 1, 4], dtype='int64'), 'b': Int64Index([2, 3], dtype='int64')}
>>> df.groupby('key1').groups.keys()
dict_keys(['a', 'b'])
```

### 10.2. 分组计算和以及平均值

如果想要根据列`key1`的值分组计算`data1`的和，可以写为

```
>>> df['data1'].groupby(df['key1']).sum().reset_index()
  key1     data1
0    a  2.337185
1    b -1.606063
```

或者

```
>>> df.filter(['data1', 'key1']).groupby('key1', as_index=False).sum()
  key1     data1
0    a  2.337185
1    b -1.606063
```

这个`as_index=False`使得`key1`的值不作为index。计算平均值只需要将`sum()`换成`mean()`即可。

## 11. Merge

### 11.1. 笛卡尔乘积

两个集合$X$和$Y$的笛卡尔乘积(Cartesian product)，表示为$X \times Y$，是指第一个对象是$X$的成员而第二个对象是$Y$的所有可能有序对的其中一个成员。举个例子，假设集合$A = \{ a,b  \}$，集合$B = \{ 0, 1, 2 \}$，则两个集合的笛卡尔积为 $\{ (a, 0), (a, 1), (a, 2), (b, 0),  (b, 1), (b, 2) \}$。使用Pandas的`merge`函数可以实现两个DataFrame的笛卡尔积。

```
>>> df1 = pd.DataFrame({'A': ['a', 'b', 'c']})
>>> df1
   A
0  a
1  b
2  c
>>> df2 = pd.DataFrame({'B': [0, 1, 2]})
>>> df2
   B
0  0
1  1
2  2
>>> pd.merge(df1.assign(foo=0), df2.assign(foo=0), on=['foo']).drop(columns=['foo'])
   A  B
0  a  0
1  a  1
2  a  2
3  b  0
4  b  1
5  b  2
6  c  0
7  c  1
8  c  2
```

主要是实现思想是增加一列`foo`，值设为0，然后使用`merge`函数进行合并。

## 12. 读取数据

### 12.1. 读取csv文件

读取csv数据文件需要用到 `read_csv()` 函数