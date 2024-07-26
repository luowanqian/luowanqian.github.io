---
title: Coordinate Descent Algorithm for Lasso Problem
date: 2018-03-27 16:20:47
urlname: coordiante_descent_for_lasso
tags:
 - 机器学习
---

## Coordinate Descent

在机器学习算法中，我们经常需要解一些优化问题，优化算法有很多，其中坐标下降法 (Coordinate Descent) 是一个很经典的算法，坐标下降法主要思想是每次迭代，只对一个坐标轴方向进行优化，其他坐标轴的值固定不变，使得多变量优化问题变成单变量优化问题。例如需要解决以下优化问题

$$
\min_{x} f(x_1, x_2, \cdots,  x_d)
$$

其中 $x = (x_1, x_2, \cdots, x_d)^T \in R^d$。用坐标下降法解决该问题时，大概流程如下：

{% asset_img cd.png coordiante descent flow %}

固定其他变量 $x_i (i\ne j)$ 不变，选择一个变量 $x_j$ 进行优化，然后更新该变量的值，接着选择下一个坐标轴方向进行优化，直到算法收敛。在算法中，选择坐标轴 $j$ 的方法有很多，可以随机选取，也可以对所有坐标轴循环一遍。

## Lasso问题求解

Lasso优化问题通常如下表示：

$$
\min_{\beta} \frac{1}{2} \Vert y - X\beta \Vert^2_2 + \lambda \Vert \beta \Vert_1
$$

其中 $y \in R^{n}$，$X \in R^{n \times p}$，$\beta \in R^p$。令损失函数表示为 $f(\beta) = f(\beta_1, \cdots, \beta_p)$，即

$$
\begin{align}
f(\beta) &= \frac{1}{2} \Vert y - X\beta \Vert^2_2 + \lambda \Vert \beta \Vert_1 \\
& = \frac{1}{2} \sum_{i=1}^n (y_i - \sum_{j=1}^p x_{ij} \beta_j)^2 + \lambda \sum_{j=1}^p \vert \beta_j \vert
\end{align}
$$

根据前面描述的坐标下降算法，要求第 $k$ 坐标轴方向的最优值，即求
$$
\min_{\beta_k} f(\beta_1, \cdots, \beta_{k-1}, \beta_k, \beta_{k+1}, \cdots, \beta_p)
$$
其中 $\beta_{j} {(j\ne k)}$ 已知且值固定不变。要求解该问题，可以通过梯度等于0来求解。对 $f(\beta)$ 求偏导可以得到：
$$
\begin{align}
\frac{\partial{f(\beta)}}{\partial{\beta_k}} & = -\sum_{i=1}^n (y_i - \sum_{j=1}^p x_{ij} \beta_j) x_{ik} + \frac{\partial{(\lambda \vert \beta_k \vert)}}{\partial{\beta_k}} \\
& =- \sum_{i=1}^n (y_i - \sum_{j \ne k} x_{ij} \beta_j)x_{ik} + \sum_{i=1}^n x_{ik}^2 \beta_k + \frac{\partial{(\lambda \vert \beta_k \vert)}}{\partial{\beta_k}} \\
& = - r_k + z_k \beta_k + \frac{\partial{(\lambda \vert \beta_k \vert)}}{\partial{\beta_k}}
\end{align}
$$
其中 $r_k =\sum_{i=1}^n (y_i - \sum_{j \ne k} x_{ij} \beta_j)x_{ik}$，$z_k = \sum_{i=1}^n x_{ik}^2$，对于 $\frac{\partial{(\lambda \vert \beta_k \vert)}}{\partial{\beta_k}}$，这里需要引入 subgradient 概念，具体内容可以参考[这篇文章](http://jocelynchi.com/soft-thresholding-operator-and-the-lasso-solution)。

$$
\frac{\partial{(\lambda \vert \beta_k \vert)}}{\partial{\beta_k}} = \begin{cases}
\lambda & \beta_k \gt 0 \\
[-\lambda, \lambda] & \beta_k = 0 \\
-\lambda & \beta_k \lt 0
\end{cases}
$$

则可以得到 $f(\beta)$ 的偏导为：

$$
\frac{\partial{f(\beta)}}{\partial{\beta_k}} = \begin{cases}
-r_k + z_k \beta_k + \lambda & \beta_k \gt 0 \\
[-r_k - \lambda, -r_k + \lambda] & \beta_k = 0 \\
-r_k + z_k \beta_k - \lambda & \beta_k \lt 0
\end{cases}
$$

令 $\frac{\partial{f(\beta)}}{\partial{\beta_k}} = 0$，可以得到极小值点 $\beta_k^*$ ，由于 $\frac{\partial{f(\beta)}}{\partial{\beta_k}}$ 是个分段函数，需要分类讨论：

* 当 $\beta_k \gt 0$ 时
  $$
  \begin{align}
  & -r_k + z_k \beta_k^* + \lambda = 0 \\
  \Rightarrow \quad & \beta_k^* = \frac{r_k - \lambda}{z_k}
  \end{align}
  $$
  由于 $\beta_k \gt 0$，则 $\beta_k^* = \frac{r_k - \lambda}{z_k} \gt 0$，即 $r_k \gt \lambda$

* 当 $\beta_k \lt 0$ 时
  $$
  \begin{align}
  & -r_k + z_k \beta_k^* - \lambda = 0 \\
  \Rightarrow \quad & \beta_k^* =\frac{ r_k + \lambda}{z_k}
  \end{align}
  $$
  由于 $\beta_k \lt 0$，则 $\beta_k^* = r_k + \lambda \lt 0$，即 $r_k \lt -\lambda$

* 当 $\beta_k = 0$ 时，可以得到 $0 \in [-r_k - \lambda, -r_k + \lambda]$，则可以得到
  $$
  \begin{cases}
  -r_k - \lambda \le 0 \\
  -r_k + \lambda \ge 0
  \end{cases} \Rightarrow -\lambda \le r_k \le \lambda
  $$







综上所有内容，我们可以得到第 $k$ 个坐标轴方向的最优值为：
$$
\beta_k^* = \begin{cases}
\frac{r_k - \lambda}{z_k} & r_k \gt \lambda \\
0 & -\lambda \le r_k \le \lambda \\
\frac{r_k + \lambda}{z_k} & r_k \lt -\lambda
\end{cases}
$$

定义一个函数 $S(a, \lambda)$，这个函数也称为 __soft thresholding function__
$$
S(a, \lambda) = \begin{cases}
a - \lambda & a \gt \lambda \\
0 & -\lambda \le a \le \lambda \\
a + \lambda & a \lt -\lambda
\end{cases}
$$
所以 $\beta_k^* = \frac{1}{z_k}S(r_k, \lambda)$，$k = 1, 2, \ldots, n$。这个函数也可以写成另一种形式
$$
S(a, \lambda) = (a-\lambda)_{+} - (-a - \lambda)_{+}
$$
这里 $(x)_{+} = \max(x, 0)$。

## 变种问题

由于Lasso优化问题的表示有很好几种，不同表示的优化问题，对应解的表示也会有所不同，虽然没什么大变化，但是还是罗列一下。

### 变种一

Lasso优化问题表示如下：
$$
\min_{\beta}  \Vert y - X\beta \Vert^2_2 + \lambda \Vert \beta \Vert_1
$$
令 $f(\beta) = \Vert y - X \beta \Vert_2^2 + \lambda \Vert \beta \Vert_1$，则 $\frac{\partial{f(\beta)}}{\partial{\beta_k}}$ 为：
$$
\begin{align}
\frac{\partial{f(\beta)}}{\partial{\beta_k}} & = -2 \sum_{i=1}^n (y_i - \sum_{j=1}^p x_{ij} \beta_j) x_{ik} + \frac{\partial{(\lambda \vert \beta_k \vert)}}{\partial{\beta_k}} \\
& =- 2 \sum_{i=1}^n (y_i - \sum_{j \ne k} x_{ij} \beta_j)x_{ik} + 2 \sum_{i=1}^n x_{ik}^2 \beta_k + \frac{\partial{(\lambda \vert \beta_k \vert)}}{\partial{\beta_k}} \\
& = - 2 r_k + 2z_k \beta_k + \frac{\partial{(\lambda \vert \beta_k \vert)}}{\partial{\beta_k}}
\end{align}
$$
其中 $r_k =  \sum_{i=1}^n (y_i - \sum_{j \ne k} x_{ij} \beta_j)x_{ik}$。使用 subgradient 可以进一步得到
$$
\frac{\partial{f(\beta)}}{\partial{\beta_k}} = \begin{cases}
- 2r_k + 2 z_k \beta_k + \lambda & \beta_k \gt 0 \\
[-2r_k - \lambda, - 2 r_k + \lambda] & \beta_k = 0 \\
- 2r_k + 2 z_k \beta_k - \lambda & \beta_k \lt 0
\end{cases}
$$

根据前面讨论，令偏导为0，求得极值点 $\beta_k^*$ 为

$$
\beta_k^* = \begin{cases}
\frac{2r_k - \lambda}{2z_k} & 2r_k \gt \lambda \\
0 & -\lambda \le 2r_k \le \lambda \\
\frac{2r_k + \lambda}{2z_k} & 2r_k \lt -\lambda
\end{cases}
$$

用 soft thresholding function 表示为 $\beta_k^* = \frac{1}{2z_k} S(2r_k, \lambda)$

### 变种二

Lasso优化问题表示如下：
$$
\min_{\beta} \Vert X\beta - y\Vert^2_2 + \lambda \Vert \beta \Vert_1
$$

令 $f(\beta) = \Vert X \beta - y \Vert^2_2 + \lambda \Vert \beta \Vert_1$，则 $\frac{\partial{f(\beta)}}{\partial{\beta_k}}$ 为：
$$
\begin{align}
\frac{\partial{f(\beta)}}{\partial{\beta_k}} & = 2 \sum_{i=1}^n (\sum_{j=1}^p x_{ij} \beta_j - y_i) x_{ik} + \frac{\partial{(\lambda \vert \beta_k \vert)}}{\partial{\beta_k}} \\
& = 2 \sum_{i=1}^n (\sum_{j \ne k} x_{ij} \beta_j  - y_i)x_{ik} + 2 \sum_{i=1}^n x_{ik}^2 \beta_k + \frac{\partial{(\lambda \vert \beta_k \vert)}}{\partial{\beta_k}} \\
& = 2 r_k + 2z_k \beta_k + \frac{\partial{(\lambda \vert \beta_k \vert)}}{\partial{\beta_k}}
\end{align}
$$
其中 $r_k = \sum_{i=1}^n (\sum_{j \ne k} x_{ij} \beta_j  - y_i)x_{ik}$。使用 subgradient 可以进一步得到
$$
\frac{\partial{f(\beta)}}{\partial{\beta_k}} = \begin{cases}
2r_k + 2 z_k \beta_k + \lambda & \beta_k \gt 0 \\
[2r_k - \lambda, 2 r_k + \lambda] & \beta_k = 0 \\
2r_k + 2 z_k \beta_k - \lambda & \beta_k \lt 0
\end{cases}
$$
根据前面讨论，令偏导为0，求得极值点 $\beta_k^*$ 为
$$
\beta_k^* = \begin{cases}
\frac{-2r_k - \lambda}{2z_k} & -2r_k \gt \lambda \\
0 & -\lambda \le -2r_k \le \lambda \\
\frac{-2r_k + \lambda}{2z_k} & -2r_k \lt -\lambda
\end{cases}
$$

用 soft thresholding function 表示为 $\beta_k^* = \frac{1}{2z_k} S(-2r_k, \lambda)$

### 变种三

Lasso优化问题表示如下：
$$
\min_{\beta} \frac{1}{2 n} \Vert y - X\beta \Vert^2_2 + \lambda \Vert \beta \Vert_1
$$
同前面的推导，这里直接给出结果
$$
\beta_k^* = \begin{cases}
\frac{r_k - \lambda}{z_k} & r_k \gt \lambda \\
0 & -\lambda \le r_k \le \lambda \\
\frac{r_k + \lambda}{z_k} & r_k \lt -\lambda
\end{cases}
$$
其中 $r_k =\frac{1}{n} \sum_{i=1}^n (y_i - \sum_{j \ne k} x_{ij} \beta_j)x_{ik}$，$z_k = \frac{1}{n} \sum_{i=1}^n x_{ik}^2$

## 偏置 bias 求解

前面讨论的模型不包括偏置，如果模型包括偏置 $b$，则优化问题写为
$$
\min_{\beta, b} \frac{1}{2} \Vert y - X\beta - 1b \Vert^2_2 + \lambda \Vert \beta \Vert_1
$$
这里的 $1$ 代表元素全为 1 的向量，大小为 $R^n$，损失函数 $f(\beta, b)$ 为
$$
\begin{align}
f(\beta, b) &= \frac{1}{2} \Vert y - X\beta - 1 b\Vert^2_2 + \lambda \Vert \beta \Vert_1 \\
& = \frac{1}{2} \sum_{i=1}^n (y_i - \sum_{j=1}^p x_{ij} \beta_j - b)^2 + \lambda \sum_{j=1}^p \vert \beta_j \vert
\end{align}
$$
则对 $f(\beta, b)$ 求偏导 $\beta_k$ 可以得到
$$
\begin{align}
\frac{\partial{f(\beta, b)}}{\partial{\beta_k}} & = -\sum_{i=1}^n (y_i - \sum_{j=1}^p x_{ij} \beta_j - b) x_{ik} + \frac{\partial{(\lambda \vert \beta_k \vert)}}{\partial{\beta_k}} \\
& =- \sum_{i=1}^n (y_i - \sum_{j \ne k} x_{ij} \beta_j - b)x_{ik} + \sum_{i=1}^n x_{ik}^2 \beta_k + \frac{\partial{(\lambda \vert \beta_k \vert)}}{\partial{\beta_k}} \\
& = - r_k + z_k \beta_k + \frac{\partial{(\lambda \vert \beta_k \vert)}}{\partial{\beta_k}}
\end{align}
$$
其中 $r_k =\sum_{i=1}^n (y_i - \sum_{j \ne k} x_{ij} \beta_j -b)x_{ik}$，$z_k = \sum_{i=1}^n x_{ik}^2$，令 $\frac{\partial{f(\beta, b)}}{\partial{\beta_k}} = 0$，可以得到
$$
\beta_k^* = \begin{cases}
\frac{r_k - \lambda}{z_k} & r_k \gt \lambda \\
0 & -\lambda \le r_k \le \lambda \\
\frac{r_k + \lambda}{z_k} & r_k \lt -\lambda
\end{cases}
$$
对 $f(\beta, b)$ 求偏导 $b$ 可以得到
$$
\begin{align}
\frac{\partial{f(\beta, b)}}{\partial{b}} & = -\sum_{i=1}^n (y_i - \sum_{j=1}^p x_{ij} \beta_j - b) \\
& = n b - \sum_{i=1}^n (y_i - \sum_{j=1}^p x_{ij} \beta_j)
\end{align}
$$
令 $\frac{\partial{f(\beta, b)}}{\partial{b}} = 0$，可以得到
$$
b^* = \frac{1}{n} \sum_{i=1}^n (y_i - \sum_{j=1}^p x_{ij} \beta_j)
$$

## 算法实现

在本节，要解决的优化问题如下表示

$$
\min_{\beta, b} \frac{1}{2 n} \Vert y - X\beta - 1b\Vert^2_2 + \lambda \Vert \beta \Vert_1
$$
根据前面的讨论，在选定第 $k$ 坐标轴方向，解 $\beta^*_k$的表达式为
$$
\beta_k^* = \begin{cases}
\frac{r_k - \lambda}{z_k} & r_k \gt \lambda \\
0 & -\lambda \le r_k \le \lambda \\
\frac{r_k + \lambda}{z_k} & r_k \lt -\lambda
\end{cases}
$$

其中 $r_k =\frac{1}{n} \sum_{i=1}^n (y_i - \sum_{j \ne k} x_{ij} \beta_j - b)x_{ik}$，$z_k = \frac{1}{n} \sum_{i=1}^n x_{ik}^2$，解 $b^*$ 的表达式为
$$
b^* = \frac{1}{n} \sum_{i=1}^n (y_i - \sum_{j=1}^p x_{ij} \beta_j)
$$

这里可以将 $r_k$ 写成向量化形式，由于算法每次只更新一个坐标轴方向的值，即

$$
\begin{align}
& [\beta_1, \ldots, \beta_{k-1}, \beta_{k}, \beta_{k+1}, \ldots, \beta_p] \\
\Rightarrow \; & [\beta_1, \ldots, \beta_{k-1}, \beta_{k}^*, \beta_{k+1}, \ldots, \beta_p]
\end{align}
$$
因此 $r_k$ 可以写为
$$
\begin{align}
r_k & =\frac{1}{n} \sum_{i=1}^n (y_i - \sum_{j =1}^p x_{ij} \beta_j - b + x_{ik} \beta_k)x_{ik}
\end{align}
$$
这里的 $\beta_k$ 是未更新之前的值，更新后的值为 $\beta_k^*$，注意区别。用向量表示 $r_k$ 为
$$
r_k = \frac{1}{n} (X[:, k])^T (y - X\beta - 1 b + \beta_k X[:, k])
$$

其中 $X[:, k]$ 代表矩阵 $X$ 第 $k$ 列。$b^*$ 的表达式写成向量形式可以写为
$$
b^* = \frac{1}{n} 1^T (y - X\beta)
$$
算法整体流程如下表示

{% asset_img flow.png coordiante descent flow %}

核心代码如下

{% codeblock %}
class Lasso:
    """
    The optimization objective of Lasso is
        1/(2 * n) * ||y - Xw||^2_2 + alpha * ||w||_1
    """

    def __init__(self, alpha=1.0, fit_intercept=True, normalize=False,
                 copy_X=True, max_iter=1000, tol=0.0001, selection='cyclic'):
        self._alpha = alpha
        self._fit_intercept = fit_intercept
        self._normalize = normalize
        self._copy_X = copy_X
        self._max_iter = max_iter
        self._tol = tol
        self._selection = selection

        if normalize:
            self._scaler = preprocessing.StandardScaler()

    def compute_step(self, k, X, y, coef, intercept, alpha):
        n, p = X.shape
        y_predict = np.dot(X, coef) + intercept
        rk = np.dot(X[:, k], y - y_predict + X[:, k] * coef[k])
        rk = rk / (1.0 * n)
        zk = np.linalg.norm(X[:, k], ord=2) ** 2
        zk = zk / (1.0 * n)
        coef_k = (np.amax([rk-alpha, 0]) - np.amax([-rk-alpha, 0]))
        coef_k = coef_k / (1.0 * zk)

        return coef_k

    def objective(self, X, y, coef, intercept, alpha):
        n, p = X.shape
        total = 0

        y_predict = np.dot(X, coef) + intercept
        total += \
            1/(2.0*n) * np.linalg.norm(y-y_predict, ord=2) ** 2
        total += alpha * np.linalg.norm(coef, ord=1)

        return total

    def fit(self, X, y):
        if self._copy_X:
            X = X.copy()
        if self._normalize:
            X = self._scaler.fit_transform(X)
        self._objectives = []

        # initialize data
        num_samples, num_features = X.shape
        coef = np.zeros(num_features)
        old_coef = np.zeros(num_features)
        intercept = 0
        if self._fit_intercept:
            tmp = y - np.dot(X, coef)
            intercept = np.sum(tmp) / (1.0 * num_samples)
        num_iters = 0
        for iter in range(self._max_iter):
            num_iters = num_iters + 1
            if (self._selection == 'cyclic'):
                for k in range(num_features):
                    old_coef[k] = coef[k]
                    coef[k] = self.compute_step(k, X, y, coef,
                                                intercept, self._alpha)
                if self._fit_intercept:
                    tmp = y - np.dot(X, coef)
                    intercept = np.sum(tmp) / (1.0 * num_samples)

                # check condition of convergence
                coef_updates = np.abs(coef - old_coef)
                if np.amax(coef_updates) < self._tol:
                    break
            self._objectives.append(self.objective(X, y, coef,
                                                   intercept, self._alpha))

        self._coef = coef
        self._intercept = intercept
        self._num_iters = num_iters

        return self

    def predict(self, X):
        if self._copy_X:
            X = X.copy()
        if self._normalize:
            X = self._scaler.transform(X)

        y_predict = np.dot(X, self._coef) + self._intercept

        return y_predict

    def score(self, X, y):
        y_predict = self.predict(X)

        return r2_score(y, y_predict)

    @property
    def coef_(self):
        return self._coef

    @property
    def intercept_(self):
        return self._intercept

    @property
    def n_iter_(self):
        return self._num_iters

    @property
    def objectives_(self):
        return self._objectives

    def __str__(self):
        return ('Lasso(alpha={}, copy_X={}, '
                'fit_intercept={}, max_iter={}, '
                'normalize={}, selection=\'{}\', '
                'tol={})').format(self._alpha, self._copy_X,
                                  self._fit_intercept, self._max_iter,
                                  self._normalize, self._selection,
                                  self._tol)
{% endcodeblock %}

相关代码在：[GitHub](https://github.com/luowanqian/MachineLearning/tree/master/CoordinateDescent/Lasso)

## 参考

1. [A COORDINATE DESCENT ALGORITHM FOR THE LASSO PROBLEM](http://jocelynchi.com/a-coordinate-descent-algorithm-for-the-lasso-problem)
2. [SOFT-THRESHOLDING OPERATOR AND THE LASSO SOLUTION](http://jocelynchi.com/soft-thresholding-operator-and-the-lasso-solution)
