---
title: Matplotlib备忘录
urlname: matplotlib_memo
date: 2018-05-09 20:00:12
tags:
 - Matplotlib
 - Python
---

## 介绍

本文主要是记录一些 Matplotlib 的使用方法以及注意事项。

__Note:__ 如果没有特别说明，`plt` 代表 `matplotlib.pyplot`，`np` 代表 `numpy`

```python
import numpy as np
import matplotlib.pyplot as plt
```

本文代码主要在 Jupyter notebook 中执行，画图之前使用了一下 magic 方法

```
%matplotlib notebook
```

## 创建多个子图

一个常用的应用就是在一个 figure 里面画多个图，此时需要 `add_subplot` 方法或者 `subplots` 方法。

### 使用 add_subplot

```
fig = plt.figure()

ax1 = fig.add_subplot(1, 2, 1)
ax2 = fig.add_subplot(1, 2, 2)

ax1.plot(np.random.randn(50).cumsum(), 'k--')
ax2.hist(np.random.randn(100), bins=20, color='k', alpha=0.3)
```

{% asset_img 1.png %}

### 使用 subplots

```
fig, axes = plt.subplots(1, 2)

ax1 = axes[0]
ax2 = axes[1]

ax1.plot(np.random.randn(50).cumsum(), 'k--')
ax2.hist(np.random.randn(100), bins=20, color='k', alpha=0.3)
```

## 保存图像到文件中

可以使用 `plt.savefig` 将图像保存到文件中，也可以使用 figure 对象的属性方法 `savefig`。

```
data = np.arange(10)
fig, ax = plt.subplots(1, 1)
ax.plot(data)

plt.savefig('1.png')
fig.savefig('2.png')
```

## 特定图像

### Histogram

直方图 (Histogram) 使用的是 `hist` 函数进行绘制，`bins` 选项可以设置bin的个数。

```
mu, sigma = 100, 15
num_bins = 100
x = mu + sigma * np.random.randn(1000000)

n, bins, patches = plt.hist(x, num_bins, density=True)

plt.title('Histogram')
plt.grid(True)
```

{% asset_img histogram.png %}
