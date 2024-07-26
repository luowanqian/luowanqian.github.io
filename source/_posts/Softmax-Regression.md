---
title: Softmax Regression
urlname: softmax_regression
date: 2018-03-30 19:18:32
tags:
 - 机器学习
---

## 模型介绍

Softmax回归 (或者称多项Logistic回归) 是Logistic回归的一般化形式，这是多分类模型，二项Logistic回归是其特殊情况，因为类别只有两类。类似二项Logistic回归模型 (关于二项Logistic回归模型，可以参考{% post_link Logistic-Regression 我的文章%}) ，Softmax回归模型也是由条件概率 $P(Y | X)$ 表示，$Y$ 的取值不再是 $\{0, 1\}$ 两类，而是 $\{1, 2, \ldots, K\}$，其中 $K$ 是类别数，这里类别不从 $0$ 开始是为了后面公式推导方便，实际编码中，类别映射通常都从 $0$ 开始。我们定义不同类别对应的条件概率为：
$$
\begin{align}
P(Y = 1 | x) &  = \frac{e^{\theta^T_1 x + b}}{\sum_{j=1}^K e^{\theta_j^T x + b}} \\
P(Y = 2 | x) & = \frac{e^{\theta^T_2 x + b}}{\sum_{j=1}^K e^{\theta_j^T x + b}} \\
\vdots \\
P(Y = K | x) & = \frac{e^{\theta^T_K x + b}}{\sum_{j=1}^K e^{\theta_j^T x + b}}
\end{align}
$$
这里 $x \in R^{d}$ 是输入，$Y \in \{1, 2, \ldots, K\}$ 输出，$\theta_k\in R^{d} \; (k=1,\ldots, K)$ 和 $b \in R$ 是未知参数。 同二项Logistic模型那样，对于给定的输入 $x$，分别计算 $x$ 属于不同类别的概率，然后 $x$ 的类别分到概率值最大的那一类。有时为了方便，将权值向量 $\theta_k$ 和输入向量 $x$ 加以扩充，仍记作 $\theta_k$ 和 $x$，即 $\theta_k = [\theta_{k1}, \ldots, \theta_{kd}, b]^T \in R^{d+1}$，$x = [x_1, x_2, \ldots, x_d, 1] \in R^{d+1}$，此时条件概率为：
$$
P(Y = k | x)  = \frac{e^{\theta^T_k x}}{\sum_{j=1}^K e^{\theta_j^T x}}  \quad (k=1, 2, \ldots, K)
$$

## 参数估计

模型参数估计方法同二项Logistic回归模型那样，使用极大似然估计法 (MLE) 进行估计。已知输入的训练数据集 $T = \{ (x_1, y_1), (x_2, y_2), \ldots, (x_N, y_N) \}$，其中 $x_i = [x_{i1}, \ldots, x_{id}, 1]^T \in R^{d+1}$，$y_i \in \{1, 2, \ldots, K\}$，待求的参数为 $\theta_k = [\theta_{k1}, \ldots, \theta_{kd}, b] \in R^{d+1} \; (k=1, 2, \ldots, K)$。前面不同类别的条件概率写成一条式子如下：
$$
P( Y = y | x) = \prod_{k=1}^K  \left(\frac{e^{\theta^T_k x}}{\sum_{j=1}^K e^{\theta_j^T x}}\right)^{1\{ y = k\}}
$$
这里 $1\{ \cdot \}$ 为指indicator函数，定义为
$$
1\{x \} = \begin{cases}
1 & \text{x is a true statement} \\
0 & \text{x is a false statement}
\end{cases}
$$
定义似然函数为：
$$
L(\theta_1, \ldots, \theta_K) = \prod_{i=1}^N \prod_{k=1}^K  \left(\frac{e^{\theta^T_k x_i}}{\sum_{j=1}^K e^{\theta_j^T x_i}}\right)^{1\{ y_i = k\}}
$$
对数似然函数为：
$$
\ln(L(\theta_1, \ldots, \theta_K)) = \sum_{i=1}^N \sum_{k=1}^K 1\{ y_i = k \} \ln\left( \frac{e^{\theta^T_k x_i}}{\sum_{j=1}^K e^{\theta_j^T x_i}} \right)
$$
对 $\ln(L(\theta_1, \ldots, \theta_K))$ 求最大值，得到 $\theta_k$ 的估计值，在实现时，通常转换为求最小值，即对 $\ln(L(\theta_1, \ldots, \theta_K))$ 求最大值转换成对 $-\ln(L(\theta_1, \ldots, \theta_K))$ 求最小值：
$$
\min_{\theta_1, \ldots, \theta_K}  -\sum_{i=1}^N \sum_{k=1}^K 1\{ y_i = k \} \ln \left( \frac{e^{\theta^T_k x_i}}{\sum_{j=1}^K e^{\theta_j^T x_i}} \right)
$$

## 模型实现

根据前面描述，模型参数估计主要是要求解下面这个优化问题：
$$
\min_{\theta_1, \ldots, \theta_K}  -\frac{1}{N} \sum_{i=1}^N \sum_{k=1}^K 1\{ y_i = k \} \ln \left( \frac{e^{\theta^T_k x_i}}{\sum_{j=1}^K e^{\theta_j^T x_i}} \right)
$$
这里多乘一个系数 $\frac{1}{N}$ 并不会改变最优解的大小。上述问题可以用梯度下降法或者拟牛顿法进行求解，在求解过程中，需要计算目标函数的梯度，接下来推导梯度矩阵的表达式，这里首先令函数 $J(\theta_1, \ldots, \theta_K)$ 为损失函数 (目标函数)：
$$
J(\theta_1, \ldots, \theta_K) = -\frac{1}{N} \sum_{i=1}^N \sum_{k=1}^K 1\{ y_i = k \} \ln \left( \frac{e^{\theta^T_k x_i}}{\sum_{j=1}^K e^{\theta_j^T x_i}} \right)
$$

### 梯度推导

为了表示方便，这里定义参数矩阵 $\theta$ 为
$$
\theta = \begin{bmatrix}
\theta_1 & \theta_2 & \cdots & \theta_K
\end{bmatrix} = \begin{bmatrix}
\theta_{11} & \theta_{21} & \cdots & \theta_{K1} \\
\theta_{12} & \theta_{22} & \cdots & \theta_{K2} \\
\vdots & \vdots & & \vdots \\
\theta_{1d} & \theta_{2d} & \cdots & \theta_{Kd} \\
\end{bmatrix}
$$
矩阵 $\theta$ 的大小为 $R^{d \times K}$，每一列为参数向量 $\theta_k \, (k=1, \ldots, K)$，因此损失函数 $J(\theta_1, \ldots, \theta_K)$ 简写为 $J(\theta)$。为了后面推导方便，首先将损失函数分解为两部分：
$$
\begin{align}
J(\theta) & = -\frac{1}{N} \sum_{i=1}^N \sum_{k=1}^K 1\{ y_i = k \} \ln \left( \frac{e^{\theta^T_k x_i}}{\sum_{j=1}^K e^{\theta_j^T x_i}} \right) \\
& = -\frac{1}{N} \sum_{i=1}^N \sum_{k=1}^K 1 \{ y_i = k \} \ln ( e^{\theta^T_k x_i} ) +\frac{1}{N} \sum_{i=1}^N \sum_{k=1}^K 1 \{ y_i = k \} \ln (\sum_{j=1}^K e^{\theta_j^T x_i}) \\
& =  -\frac{1}{N} \sum_{i=1}^N \sum_{k=1}^K 1 \{ y_i = k \} (\theta^T_k x_i )+\frac{1}{N} \sum_{i=1}^N \sum_{k=1}^K 1 \{ y_i = k \} \ln (\sum_{j=1}^K e^{\theta_j^T x_i}) \\
\end{align}
$$
令这两部分分别为 $f(\theta)$ 和 $g(\theta)$
$$
\begin{align}
f(\theta) &= -\frac{1}{N} \sum_{i=1}^N \sum_{k=1}^K 1 \{ y_i = k \} (\theta^T_k x_i ) \\
g(\theta) & = \frac{1}{N} \sum_{i=1}^N \sum_{k=1}^K 1 \{ y_i = k \} \ln (\sum_{j=1}^K e^{\theta_j^T x_i})
\end{align}
$$
损失函数 $J(\theta)$ 对矩阵 $\theta$ 某一个元素 $\theta_{pq}$ ($\theta_{pq}$ 代表参数向量 $\theta_q$ 的第 $p$ 个元素) 的偏导为：
$$
\frac{\partial{J(\theta)}}{\partial{\theta_{pq}}} = \frac{\partial{f(\theta)}}{\partial{\theta_{pq}}} + \frac{\partial{g(\theta)}}{\partial{\theta_{pq}}}
$$

#### 第一部分

函数 $f(\theta)$ 对 $\theta_{pq}$ 偏导可以得到：
$$
\begin{align}
\frac{\partial{f(\theta)}}{\partial{\theta_{pq}}} & = -\frac{1}{N} \sum_{i=1}^N 1\{ y_i = q \} \frac{\partial{\theta_q^T x_i}}{\partial{\theta_{pq}}} \\
& = -\frac{1}{N} \sum_{i=1}^N 1\{ y_i = q \} x_{ip}
\end{align}
$$

#### 第二部分

函数 $g(\theta)$ 对 $\theta_{pq}$  可以得到：
$$
\begin{align}
\frac{\partial{g(\theta)} }{\partial{\theta_{pq}}} & = \frac{1}{N} \sum_{i=1}^N \sum_{k=1}^K 1 \{ y_i = k \} \frac{ \partial\ln(\sum_{j=1}^K e^{\theta_j^Tx_i}) }{ \partial{\theta_{pq}} } \\
& =  \frac{1}{N} \sum_{i=1}^N \sum_{k=1}^K 1 \{ y_i = k \} \frac{1}{ \sum_{j=1}^K e^{\theta_j^Tx_i} } \frac{\partial{e^{\theta_q^T x_i}} }{\partial{\theta_{pq}}} \\
& = \frac{1}{N} \sum_{i=1}^N \sum_{k=1}^K 1 \{ y_i = k \} \frac{ e^{\theta_q^T x_i} }{ \sum_{j=1}^K e^{\theta_j^Tx_i} } \frac{ \partial{\theta^T_q x_i}  }{\partial{\theta_{pq}}} \\
& = \frac{1}{N} \sum_{i=1}^N \sum_{k=1}^K 1 \{ y_i = k \} \frac{ x_{ip} e^{\theta_q^T x_i} }{ \sum_{j=1}^K e^{\theta_j^Tx_i} } \\
& = \frac{1}{N} \sum_{i=1}^N \frac{ x_{ip} e^{\theta_q^T x_i} }{ \sum_{j=1}^K e^{\theta_j^Tx_i} } \sum_{k=1}^K 1 \{ y_i = k \}
\end{align}
$$
由于 $\sum_{k=1}^K 1 \{ y_i = k \} = 1$，则最终可以得到
$$
\frac{\partial{g(\theta)} }{\partial{\theta_{pq}}} = \frac{1}{N} \sum_{i=1}^N \frac{ x_{ip} e^{\theta_q^T x_i} }{ \sum_{j=1}^K e^{\theta_j^Tx_i} }
$$

#### 综合

综合第一部分和第二部分的推导，可以得到
$$
\begin{align}
\frac{\partial{J(\theta)}}{\partial{\theta_{pq}}} & = -\frac{1}{N} \sum_{i=1}^N 1\{ y_i = q \} x_{ip} + \frac{1}{N} \sum_{i=1}^N \frac{ x_{ip} e^{\theta_q^T x_i} }{ \sum_{j=1}^K e^{\theta_j^Tx_i} } \\
& = -\frac{1}{N} \sum_{i=1}^N \left[  1\{ y_i = q \} x_{ip} - \frac{ x_{ip} e^{\theta_q^T x_i} }{ \sum_{j=1}^K e^{\theta_j^Tx_i} } \right] \\
& = -\frac{1}{N} \sum_{i=1}^N \left[ x_{ip} \left(  1\{ y_i = q \}  - \frac{ e^{\theta_q^T x_i} }{ \sum_{j=1}^K e^{\theta_j^Tx_i} } \right) \right]
\end{align}
$$
如果要表示梯度向量 $\frac{\partial{J(\theta)}}{\partial{\theta_q}}$，可以写成如下形式：
$$
\frac{\partial{J(\theta)}}{\partial{\theta_q}} = -\frac{1}{N} \sum_{i=1}^N \left[ x_{i} \left(  1\{ y_i = q \}  - \frac{ e^{\theta_q^T x_i} }{ \sum_{j=1}^K e^{\theta_j^Tx_i} } \right) \right]
$$

## 参考

1. [UFLDL Softmax Regression](http://ufldl.stanford.edu/tutorial/supervised/SoftmaxRegression/)
