---
title: ConfigSpace备忘录
urlname: configspace_memo
date: 2019-05-03 10:10:09
tags:
 - Python
---

## 介绍

ConfigSpace是一个用于管理算法参数空间的Python包，主要用于算法参数选择任务。一些AutoML库，例如[SMAC3](https://github.com/automl/SMAC3)、[BOHB](https://github.com/automl/HpBandSter)以及[auto-sklearn](https://github.com/automl/auto-sklearn)，会用到该包。项目主页为：[https://github.com/automl/ConfigSpace](https://github.com/automl/ConfigSpace)。

注明：本文章相关代码在[Gist](https://gist.github.com/luowanqian/5fd452f204b60a56e5297c96489ed6dc)

## 初始化

使用ConfigSpace包时通常要创建一个参数空间实例

```
import ConfigSpace as CS
import ConfigSpace.hyperparameters as CSH

cs = CS.ConfigurationSpace()
```

这个参数空间集合实例`cs`包含所有参数的设置

## 整数参数和浮点参数

本节开始将介绍如何配置算法的参数空间，这里举例的算法为SVM分类算法，算法具体实现为[sklearn.svm.SVC](https://scikit-learn.org/stable/modules/generated/sklearn.svm.SVC.html)。由SVC类介绍可以知道两个参数：

1. `C`为惩罚参数，数据类型为浮点数，且$C \ge 0$
2. `max_iter`为最大迭代次数，数据类型为整数

假设要限定`C`的取值范围为$[0, 1]$以及`max_iter`的取值范围为$[10, 100]$，可以用`UniformFloatHyperparameter`和`UniformIntegerHyperparameter`设定参数范围

```
param_c = CSH.UniformFloatHyperparameter(name='C', lower=0, upper=1)
param_max_iter = CSH.UniformIntegerHyperparameter(name='max_iter', lower=10, upper=100)
```

设定完参数空间后，需要添加到参数空间集合实例`cs`中

```
cs.add_hyperparameter(param_c)
cs.add_hyperparameter(param_max_iter)
```

此时可以使用`cs`的`sample_configuration`方法进行采样获得一组随机的参数

```
cs.sample_configuration()
```

此时输出类似下面这种情况

```
Configuration:
  C, Value: 0.7114185317566737
  max_iter, Value: 84
```

## Categorical参数和参数之间的联系

由`sklearn.svm.SVC`类介绍可知，算法核类型由参数`kernel`控制 

* `kernel`限定算法的核类型，取值主要有`'linear'`，`'poly'`，`'rbf'`，`'sigmoid'`

此时可以用`CategoricalHyperparameter`来代表参数`kernel`

```
param_kernel = CSH.CategoricalHyperparameter(name='kernel', choices=['linear', 'poly', 'rbf', 'sigmoid'])

cs.add_hyperparameter(param_kernel)
```

每一种核还有相应的参数设置（设定SVC类对应的参数），即

* Linear核$K(x, y)=x^Ty$，无参数
* Poly核$K(x, y)=(\gamma x^Ty + r)^d$，其中参数$\gamma$对应`gamma`，参数$r$对应`coef0`，参数$d$对应`degree`
* RBF核$K(x, y)=\exp(-\gamma \Vert x - y\Vert^2)$，其中参数$\gamma$对应`gamma`
* Sigmoid核$K(x, y)=\tanh(\gamma x^T y + r)$，其中参数$\gamma$对应`gamma`，参数$r$对应`coef0`

首先创建参数`degree`、`coef0`以及`gamma`的参数空间

```
param_degree = CSH.UniformIntegerHyperparameter(name='degree', lower=2, upper=4)
param_coef0 = CSH.UniformFloatHyperparameter(name='coef0', lower=0, upper=1)
param_gamma = CSH.UniformFloatHyperparameter(name='gamma', lower=1e-5, upper=1e2)

cs.add_hyperparameters([param_degree, param_coef0, param_gamma])
```

有前面的描述可以知道不同的核对应不同的参数，也就是说核参数和核类型参数之间是由关联的

* 参数`degree`关联Poly核
* 参数`coef0`关联Poly核和Sigmoid核
* 参数`gamma`关联Poly核、RBF核和Sigmoid核

要想表示这种参数之间的关系，可以使用`EqualsCondition`以及`OrConjunction`，即

```
cond1 = CS.EqualsCondition(param_degree, param_kernel, 'poly')
cond2 = CS.OrConjunction(CS.EqualsCondition(param_coef0, param_kernel, 'poly'),
                         CS.EqualsCondition(param_coef0, param_kernel, 'sigmoid'))
cond3 = CS.OrConjunction(CS.EqualsCondition(param_gamma, param_kernel, 'rbf'),
                         CS.EqualsCondition(param_gamma, param_kernel, 'poly'),
                         CS.EqualsCondition(param_gamma, param_kernel, 'sigmoid'))

cs.add_conditions([cond1, cond2, cond3])
```

其中

```
CS.EqualsCondition(param_degree, param_kernel, 'poly')
```

意思为参数`kernel`值为`'poly'`时，设定参数`degree`的值。如果有多个条件，需要用`OrConjunction`来OR这些条件

```
cond2 = CS.OrConjunction(CS.EqualsCondition(param_coef0, param_kernel, 'poly'),
                         CS.EqualsCondition(param_coef0, param_kernel, 'sigmoid'))
```

意思为当参数`kernel`值为`'poly'`时，设定参数`coef0`值，或者当参数`kernel`值为`'sigmoid'`时，设定参数`coef0`值。

## 禁止参数取值组合出现

前面我们设定了`sklearn.svm.SVC`类某些参数的参数空间，假如SVC的核选择的是Linear核，即参数`kernel`取值为`'Linear'`，此时SVM变成了LinearSVM。如果SVC类的LinearSVM实现为[sklearn.svm.LinearSVC](https://scikit-learn.org/stable/modules/generated/sklearn.svm.LinearSVC.html)，这时可以用LinearSVC类参数进一步控制算法的运行过程。
`注：这里只是假设一种情况，即SVC类有LinearSVC类的全部参数，真实情况是SVC类并没有LinearSVC类的全部参数。`

LinearSVC类部分参数如下

* `penalty`设置正则项类型，数据类型为字符串，取值为`'l1'`或者`'l2'`
* `loss`设置损失函数类型，数据类型为字符串，取值为`'hinge'`或者`'squared_hinge'`
* `dual`设置算法是否求解对偶问题，数据类型为布尔值，实际可以替换成字符串类型

首先根据这三个参数设置参数空间

```
param_penalty = CSH.CategoricalHyperparameter(name='penalty', choices=['l1', 'l2'], default_value='l2')
param_loss = CSH.CategoricalHyperparameter(name='loss', choices=['hinge', 'squared_hinge'], default_value='squared_hinge')
param_dual = CSH.CategoricalHyperparameter(name='dual', choices=['True','False'], default_value='False')

cs.add_hyperparameters([param_penalty, param_loss, param_dual])
```

当核类型为Linear核时，这三个参数才会被设置，因此要进行参数关联

```
cond1 = CS.EqualsCondition(param_penalty, param_kernel, 'linear')
cond2 = CS.EqualsCondition(param_loss, param_kernel, 'linear')
cond3 = CS.EqualsCondition(param_dual, param_kernel, 'linear')

cs.add_conditions([cond1, cond2, cond3])
```

这里限定一些参数组合不能出现

* 参数`penalty`取值`'l1'`，参数`loss`取值`'hinge'`
* 参数`dual`取值`'False'`，参数`penalty`取值`'l2'`，参数`loss`取值`'hinge'`
* 参数`dual`取值`'False'`，参数`'penalty'`取值`'l1'`

要禁止出现某些参数组合，可以使用`ForbiddenEqualsClause`，如果有多个组合，需要使用`ForbiddenAndConjunction`进行OR

```
penalty_loss = CS.ForbiddenAndConjunction(
        CS.ForbiddenEqualsClause(param_penalty, 'l2'),
        CS.ForbiddenEqualsClause(param_loss, 'hinge')
)
dual_penalty_loss = CS.ForbiddenAndConjunction(
        CS.ForbiddenEqualsClause(param_dual, 'False'),
        CS.ForbiddenEqualsClause(param_penalty, 'l2'),
        CS.ForbiddenEqualsClause(param_loss, 'hinge')
)

penalty_dual = CS.ForbiddenAndConjunction(
        CS.ForbiddenEqualsClause(param_dual, 'False'),
        CS.ForbiddenEqualsClause(param_penalty, 'l1')
)

cs.add_forbidden_clause(penalty_loss)
cs.add_forbidden_clause(dual_penalty_loss)
cs.add_forbidden_clause(penalty_dual)
```