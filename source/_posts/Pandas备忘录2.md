---
title: Pandas备忘录2
date: 2018-09-03 22:30:02
urlname: pandas_memo2
tags:
 - Python
 - Pandas
---

## Pandas备忘录系列文章

1. {% post_link Pandas备忘录 Pandas备忘录 %}

## 介绍

__Note:__ 如果没有特别说明，`pd`指的是`pandas`，`np`指的是`numpy`。

```
import pandas as pd
import numpy as np
```

## 查找

### 正则表达式查找

主要是用`Series.str.contains`这个函数

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

