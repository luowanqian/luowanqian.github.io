---
title: Logistic Regression
urlname: logistic_regression
date: 2018-03-29 19:06:40
tags:
 - 机器学习
---

## 二项Logistic回归模型

二项Logistic回归模型是一种分类模型，由条件概率分布 $P(Y|X)$ 表示，这里的随机变量取值为实数，随机变量 $Y$ 取值为 0 或 1，这个条件概率分布是未知的，需要通过监督学习方法来进行估计。条件概率分布如下表示：
$$
\begin{align}
P(Y=1 | x) & = \frac{e^{x^T \beta + b}}{1 + e^{x^T\beta + b}} = \frac{1}{1 + e^{-x^T \beta + b}} \\
P(Y = 0 | x) & = \frac{1}{1 + e^{x^T \beta + b}}
\end{align}
$$
这里，$x\in R^d$ 是输入，$Y \in \{0, 1\}$ 是输出，$\beta \in R^d$ 和 $b$ 是参数。模型参数是未知的，需要进行模型训练获得，当获得模型参数时，对于一个新的输入实例 $x$，根据上面式子分别求出条件概率 $P(Y=1 | x)$ 和 $P(Y=0|x)$，比较两个条件概率值的大小，将实例 $x$ 分到较大的那一类。

## 模型参数估计

模型参数估计通常使用极大似然估计法 (MLE) 进行估计。已知输入的训练数据集 $T = \{(x_1, y_1), \ldots, (x_N, y_N)\}$，其中 $x_i = [x_{i1}, x_{i2}, \ldots, x_{id}]^T \in R^d$，$y_i \in \{0, 1\}$，待求的参数为 $\beta = [\beta_1, \beta_2, \ldots, \beta_d]^T \in R^d$ 和 $b \in R$。设：
$$
\begin{align}
P(Y=1|x) & = \phi(x) \\
P(Y =0 | x) & = 1 - \phi(x)
\end{align}
$$
两个结合成一个式子可以写成如下表示：
$$
P(Y = y | x) = \phi(x)^{y} (1 - \phi(x))^{1- y}
$$
定义似然函数为：
$$
L(\beta, b) = \prod_{i=1}^N \phi(x_i)^{y_i} (1 - \phi(x_i))^{1-y_i}
$$

则对数似然函数为：
$$
\begin{align}
\ln (L(\beta, b)) & = \ln \left( \prod_{i=1}^N \phi(x_i)^{y_i} (1 - \phi(x_i))^{1-y_i} \right) \\
& = \sum_{i=1}^N \left[  y_i \ln(\phi(x_i)) + (1- y_i) \ln(1-\phi(x_i))\right] \\
& = \sum_{i=1}^N \left[  y_i \ln\left(\frac{\phi(x_i)}{1 - \phi(x_i)} \right)  + \ln(1- \phi(x_i)) \right]
\end{align}
$$
根据前面模型描述，可以知道
$$
\phi(x) = \frac{e^{x^T \beta + b}}{1 + e^{x^T\beta + b}}
$$
则 $\frac{\phi(x)}{1-\phi(x)}$ 为
$$
\frac{\phi(x)}{1 - \phi(x)} = e^{x^T \beta + b}
$$
因此对数似然函数为：
$$
\begin{align}
\ln(L(\beta, b)) & = \sum_{i=1}^N \left[  y_i \ln(e^{x_i^T \beta}) + \ln\left( \frac{1}{1 + e^{x_i^T\beta + b}} \right) \right] \\
& = \sum_{i=1}^N \left[ y_i (x_i^T \beta) - \ln(1 + e^{x_i^T\beta + b}) \right]
\end{align}
$$
对 $\ln(L(\beta, b))$ 求最大值，得到 $\beta$ 和 $b$ 的估计值，不过在实现时，通常转化为求解最小值，即对 $\ln(L(\beta, b))$ 求最大值转换成对 $-\ln(L(\beta, b))$ 求最小值：
$$
\min_{\beta, b} \sum_{i=1}^N \left[ \ln(1 + e^{x_i^T\beta + b}) - y_i (x_i^T \beta) \right]
$$

## 模型实现

根据前面描述，模型参数估计主要是要求解

$$
\min_{\beta, b} \frac{1}{N} \sum_{i=1}^N \left[ \ln(1 + e^{x_i^T\beta + b}) - y_i (x_i^T \beta) \right]
$$

这里多乘一个系数 $\frac{1}{N}$ 并不会改变最优解的大小。上述问题可以采用梯度下降法或者拟牛顿法进行求解，在求解过程中，需要计算目标函数的梯度。令函数 $f(\beta, b)$ 为：
$$
f(\beta, b) = \sum_{i=1}^N \left[ \ln(1 + e^{x_i^T\beta + b}) - y_i (x_i^T \beta) \right]
$$
则函数 $f(\beta, b)$ 对 $\beta_k \; (k=1, \ldots, d)$ 的偏导为
$$
\begin{align}
\frac{\partial{f(\beta, b)}}{\partial \beta_k} & = \frac{1}{N} \sum_{i=1}^N \left( \frac{x_{ik}e^{\beta^T x_i + b}}{1 + e^{\beta^T x_i + b}} -x_{ik} y_i \right) \\
& = \frac{1}{N} \sum_{i=1}^N \left( \frac{x_{ik}}{1 + e^{-(\beta^T x_i + b)}} - x_{ik}y_i \right)
\end{align}
$$
令矩阵 $X \in R^{N \times d}$ 为
$$
X = \begin{bmatrix}
x_{11} & \ldots & x_{1d} \\
x_{21} & \ldots  & x_{2d} \\
\vdots &  & \vdots \\
x_{N1} & \ldots & x_Nd
\end{bmatrix} = \begin{bmatrix}
x_1^T \\
x_2^T \\
\vdots \\
x_N^T
\end{bmatrix}
$$
则 $X\beta + b$ 为
$$
X \beta + b = [\beta^T x_1 + b , \ldots, \beta^T x_N + b]^T
$$
令函数 $g(x)$ 为 Sigmoid 函数，也就是
$$
g(x) = \frac{1}{1 + e^{-x}}
$$
则 $g(X \beta + b)$ 为
$$
g(X \beta + b) = \begin{bmatrix}
\frac{1}{1 + e^{-(\beta^T x_1 +b)}} \\
\frac{1}{1 + e^{-(\beta^T x_2 +b)}}  \\
\vdots \\
\frac{1}{1 + e^{-(\beta^T x_N +b)}}
\end{bmatrix}
$$
令 $Xt =X^T$，矩阵 $Xt$ 第 $k$ 行为 $Xt[k, :]$，则可以得到
$$
\sum_{i=1}^N \frac{x_{ik}}{1 + e^{-(\beta^T x_i + b)}} = Xt[k, :]  g(X\beta + b)
$$
以及
$$
\sum_{i=1}^N x_{ik} y_i = Xt[k, :] y
$$
因此
$$
\begin{align}
\frac{\partial{f(\beta, b)}}{\partial \beta_k} & = \frac{1}{N} \sum_{i=1}^N \left( \frac{x_{ik}e^{\beta^T x_i + b}}{1 + e^{\beta^T x_i + b}} -x_{ik} y_i \right) \\
& = \frac{1}{N} \sum_{i=1}^N \left( \frac{x_{ik}}{1 + e^{-(\beta^T x_i + b)}} - x_{ik}y_i \right) \\
& = \frac{1}{N} Xt[k, :]  \left(g(X\beta + b) - y \right)
\end{align}
$$
则可得
$$
\begin{bmatrix}
\frac{\partial f(\beta, b)}{\partial \beta_1} \\
\frac{\partial f(\beta, b)}{\partial \beta_2} \\
\vdots \\
\frac{\partial f(\beta, b)}{\partial \beta_d}
\end{bmatrix} = \frac{1}{N} \begin{bmatrix}
Xt[1, :]  \left(g(X\beta + b) - y\right) \\
Xt[2, :]  \left(g(X\beta + b) - y\right) \\
\vdots \\
Xt[N, :]  \left(g(X\beta + b) - y\right)
\end{bmatrix} = \frac{1}{N} X^T (g(X\beta+b)- y)
$$
前面是求函数 $f(\beta, b)$ 对 $\beta_k$ 的偏导，对于偏置项 $b$，函数 $f(\beta, b)$ 对 $b$ 的偏导为
$$
\begin{align}
\frac{\partial f(\beta, b)}{\partial b} &  =  \frac{1}{N} \sum_{i=1}^N \left( \frac{e^{\beta^T x_i + b}}{1 + e^{\beta^T x_i + b}} - y_i \right)  \\
& =  \frac{1}{N} \sum_{i=1}^N \left( \frac{1}{1 + e^{-(\beta^T x_i + b)}} - y_i \right) \\
& = \frac{1}{N} 1^T (g(X\beta + b) - y)
\end{align}
$$

这里的 $1$ 代表元素全是1的列向量。综上可以得到，函数 $f(\beta, b)$ 的梯度为：
$$
\nabla f(\beta, b) = \begin{bmatrix}
\frac{1}{N} 1^T (g(X\beta + b) - y) \\
\frac{1}{N} X^T (g(X\beta+b)- y)
\end{bmatrix}
$$
这个梯度向量第一个元素代表 $\frac{\partial f(\beta, b)}{\partial b}$。知道梯度向量的表示，我们可以用梯度下降法或拟牛顿法 (例如BFGS等) 来求解优化问题，我这里采用BFGS算法来进行求解，核心代码如下：

{% codeblock %}
import numpy as np
from scipy.optimize import minimize


class LogisticRegression:
    def __init__(self, alpha=1.0, max_iter=1000, tol=1e-4):
        self.alpha = alpha
        self.max_iter = max_iter
        self.tol = tol

    def sigmoid(self, x):
        return 1 / (1.0 + np.exp(-x))

    def objective(self, beta, X, y):
        n, _ = X.shape
        tmp = np.dot(X, beta[1:]) + beta[0]
        object = np.sum(np.log(1+np.exp(tmp))) - np.dot(y, tmp)

        return object / (1.0 * n)

    def gradient(self, beta, X, y):
        n, _ = X.shape
        tmp = self.sigmoid(np.dot(X, beta[1:]) + beta[0]) - y
        grads = []
        grads.append(np.sum(tmp)/(1.0*n))
        grads.extend(np.dot(X.T, tmp)/(1.0*n))

        return np.array(grads)

    def callback(self, beta):
        self.history.append(self.objective(beta, self.X, self.y))

    def fit(self, X, y):
        n, p = X.shape
        self.X = X
        self.y = y
        self.history = []

        # initialize
        beta0 = np.random.randn(p+1)

        res = minimize(self.objective, beta0, args=(X, y), method='BFGS',
                       jac=self.gradient, callback=self.callback)

        self.coef = res.x[1:]
        self.intercept = res.x[0]

        return self

    def predict(self, X):
        activation = self.sigmoid(np.dot(X, self.coef) + self.intercept)
        y = np.zeros(X.shape[0], dtype=np.int)
        y[activation >= 0.5] = 1

        return y

    def score(self, X, y):
        y_predict = self.predict(X)

        return np.mean(y == y_predict) * 100

    @property
    def coef_(self):
        return self.coef

    @property
    def intercept_(self):
        return self.intercept

    @property
    def history_(self):
        return self.history
{% endcodeblock %}

全部相关的代码在：[GitHub](https://github.com/luowanqian/MachineLearning/tree/master/LogisticRegression)
