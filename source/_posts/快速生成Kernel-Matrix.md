---
title: 快速生成Kernel Matrix
urlname: fast_kernel_matrix_generation
date: 2019-04-19 19:55:46
tags:
 - 数学
 - Python
 - 机器学习
---

## 介绍

有些机器学习算法，比如最小二乘支持向量机 (Least Squares Support Vector Machine, LSSVM)，需要计算核矩阵 (Kernel Matrix)。核矩阵的定义如下

$$
\Omega = K(X, Y) = \begin{bmatrix}
K(x_1, y_1) & K(x_1, y_2) & \cdots & K(x_1, y_n) \\
K(x_2, y_1) & K(x_2, y_2) & \cdots & K(x_2, y_n) \\
\vdots & \vdots & \ddots & \vdots \\
K(x_m, y_1) & K(x_n, y_2) & \cdots & K(x_m, y_n)
\end{bmatrix}
$$

其中$K(\cdot, \cdot)$为核函数，常见的有线性核以及RBF核，矩阵$X \in R^{m \times d}$和$Y \in R^{n \times d}$为数据矩阵，每一行代表一个样本，定义为

$$
\begin{aligned}
X & = [x_1 \, x_2 \, \cdots \, x_m]^T \\
Y & = [y_1 \, y_2 \, \cdots \, y_n]^T
\end{aligned}
$$

核矩阵的规模为$m \times n$，可以看出核矩阵的规模是由数据个数决定的，如果按照定义一个个生成核矩阵每一个元素，可想而知生成效率会极其低下，因此考虑用其他方式快速生成核矩阵。

## 快速生成核矩阵

本文只考虑核函数为RBF核的情况，其他情况有相类似的推导。RBF核又称高斯核，其定义为

$$
K(x, y) = e^{-\frac{\Vert x - y \Vert^2}{2\sigma^2}}
$$

其中$\sigma > 0$为高斯核带宽，$\Vert\cdot\Vert$为$L_2$范数。此时核矩阵$\Omega$为

$$
\Omega=K(X, Y) = \begin{bmatrix}
e^{-\frac{\Vert x_1 - y_1\Vert^2}{2\sigma^2}} & e^{-\frac{\Vert x_1 - y_2 \Vert^2}{2\sigma^2}} & \cdots & e^{-\frac{\Vert x_1 - y_n \Vert^2}{2\sigma^2}} \\
e^{-\frac{\Vert x_2 - y_1 \Vert^2}{2\sigma^2}} & e^{-\frac{\Vert x_2 - y_2 \Vert^2}{2\sigma^2}} & \cdots & e^{-\frac{\Vert x_2 - y_n \Vert^2}{2\sigma^2}} \\
\vdots & \vdots & \ddots & \vdots \\
e^{-\frac{\Vert x_m - y_1 \Vert^2}{2\sigma^2}} & e^{-\frac{\Vert x_m - y_2 \Vert^2}{2\sigma^2}} & \cdots & e^{-\frac{\Vert x_m - y_n \Vert^2}{2\sigma^2}}
\end{bmatrix}
$$

将自然指数运算提取到矩阵外面，则核矩阵$\Omega$表示为$\Omega=e^{-\frac{D}{2\sigma^2}}$，这里矩阵$D$为

$$
D = \begin{bmatrix}
\Vert x_1 - y_1 \Vert^2 & \Vert x_1 - y_2 \Vert^2 & \cdots & \Vert x_1 - y_n \Vert^2 \\
\Vert x_2 - y_1 \Vert^2 & \Vert x_2 - y_2 \Vert^2 & \cdots & \Vert x_2 - y_n \Vert^2 \\
\vdots & \vdots & \ddots & \vdots \\
\Vert x_m - y_1 \Vert^2 & \Vert x_m - y_2 \Vert^2 & \cdots & \Vert x_m - y_n \Vert^2
\end{bmatrix}
$$

将矩阵$D$中每一个元素展开，则可得

$$
\begin{aligned}
\Vert x_i - y_j \Vert^2 & = (x_i - y_j)^T(x_i - y_j) \\
& = x_i^T x_i - 2 x_i^T y_j + y_j^T y_j \\
& = \Vert x_i \Vert^2 - 2 x_i^T y_j + \Vert y_j \Vert^2
\end{aligned}
$$

从而矩阵$D$可以分解为三个矩阵之和

$$
D = A - 2B + C
$$

其中矩阵$A$定义为

$$
A = \begin{bmatrix}
\Vert x_1 \Vert^2 & \Vert x_1 \Vert^2 & \cdots & \Vert x_1 \Vert^2 \\
\Vert x_2 \Vert^2 & \Vert x_2 \Vert^2 & \cdots & \Vert x_2 \Vert^2 \\
\vdots & \vdots & \ddots & \vdots \\
\Vert x_m \Vert^2 & \Vert x_m \Vert^2 & \cdots & \Vert x_m \Vert^2
\end{bmatrix}_{m \times n}
$$

这里定义一个向量$\alpha$，其第$i$个元素为数据矩阵$X$第$i$行所有元素平方和，即

$$
\alpha = \begin{bmatrix}
\Vert x_1 \Vert^2 & \Vert x_2 \Vert^2 & \cdots & \Vert x_m \Vert^2
\end{bmatrix}^T
$$

则矩阵$A$可以表示为

$$
\begin{aligned}
A & = \alpha 1^T \\
& = \begin{bmatrix}
\Vert x_1 \Vert^2 \\
\Vert x_2 \Vert^2 \\
\vdots \\
\Vert x_m \Vert^2
\end{bmatrix} \begin{bmatrix}
1 & 1 & \cdots & 1
\end{bmatrix}
\end{aligned}
$$

矩阵$B$定义为

$$
B = \begin{bmatrix}
x_1^T y_1 & x_1^T y_2 & \cdots & x_1^T y_n \\
x_2^T y_1 & x_2^T y_2 & \cdots & x_2^T y_n \\
\vdots & \vdots & \ddots & \vdots \\
x_m^T y_1 & x_m^T y_2 & \cdots & x_m^T y_n
\end{bmatrix}_{m \times n}
$$

很容易可以知道矩阵$B$由数据矩阵$X$和$Y$相乘而得

$$
B = XY^T
$$

矩阵$C$定义为

$$
C = \begin{bmatrix}
\Vert y_1 \Vert^2 & \Vert y_2 \Vert^2 & \cdots & \Vert y_n \Vert^2 \\
\Vert y_1 \Vert^2 & \Vert y_2 \Vert^2 & \cdots & \Vert y_n \Vert^2 \\
\vdots & \vdots & \ddots & \vdots \\
\Vert y_1 \Vert^2 & \Vert y_2 \Vert^2 & \cdots & \Vert y_n \Vert^2
\end{bmatrix}_{m \times n}
$$

类似地，这里定义一个向量$\beta$，其第$i$个元素为数据矩阵$Y$第$i$行所有元素平方和，即

$$
\beta = \begin{bmatrix}
\Vert y_1 \Vert^2 & \Vert y_2 \Vert & \cdots & \Vert y_n \Vert^2
\end{bmatrix}^T
$$

则矩阵$C$可以表示为

$$
\begin{aligned}
C & = 1 \beta^T \\
& = \begin{bmatrix}
1 \\
1 \\
\vdots \\
1
\end{bmatrix} \begin{bmatrix}
\Vert y_1 \Vert^2 & \Vert y_2 \Vert & \cdots & \Vert y_n \Vert^2
\end{bmatrix}
\end{aligned}
$$

综上可以得到

$$
D = \alpha 1^T - 2 XY^T + 1 \beta^T
$$

## 实现

### 原始生成方法

按照核矩阵定义，一个个生成矩阵每一个元素，实现代码如下

```
def method1(X1, X2, sigma=1.0):
    num_samples1, dims = X1.shape
    num_samples2, dims = X2.shape
    mat = np.empty((num_samples1, num_samples2))
    
    for i in range(num_samples1):
        for j in range(num_samples2):
            vec = X1[i, :] - X2[j, :]
            mat[i, j] = np.exp(-0.5 / sigma**2 * np.dot(vec, vec))
    
    return mat
```

### 快速生成方法1

根据前面讨论，将核矩阵分解为一系列矩阵向量乘积，实现代码如下

```
def method2(X1, X2, sigma=1.0):
    X1_sum = np.sum(X1**2, axis=1)
    X2_sum = np.sum(X2**2, axis=1)
    mat1 = np.dot(np.reshape(X1_sum, (X1_sum.shape[0], 1)),
                  np.ones((1, X2_sum.shape[0])))
    mat2 = np.dot(np.ones((X1_sum.shape[0], 1)),
                  np.reshape(X2_sum, (1, X2_sum.shape[0])))
    mat = mat1 + mat2 - 2 * np.dot(X1, X2.T)
    mat = np.exp(-0.5 / sigma**2 * mat)
    
    return mat
```

### 快速生成方法2

利用Numpy的广播机制，进一步提升核矩阵生成速度，实现代码如下

```
def method3(X1, X2, sigma=1.0):
    mat = np.sum(X1**2, 1).reshape(-1, 1) + np.sum(X2**2, 1) - 2 * np.dot(X1, X2.T)
    mat = np.exp(-0.5 / sigma**2 * mat)
    
    return mat
```

## 实验

实验代码在：[https://gist.github.com/luowanqian/d2f655a95f77dfe088fd626f72d78966](https://gist.github.com/luowanqian/d2f655a95f77dfe088fd626f72d78966)

这里做一个简单的实验，比对各种方法的效率。实验的数据矩阵$X$大小为$1000 \times 100$，数据矩阵$Y$大小为$5000 \times 100$，实验结果如下

| 方法 | 生成时间 |
| ---- | ---- |
| 原始生成 | 25.1 s |
| 快速生成1 | 132 ms |
| 快速生成2 | 106 ms |

由结果可以看出，快速生成方法速度提升是极大的。