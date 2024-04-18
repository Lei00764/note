# Logistic Regression

> 分类模型和回归模型的区别：
> 主要看输出变量的类型，分类模型的输出结果是离散的，而回归模型的输出结果是连续的

Logistic Regression
虽然叫做 Logistic 回归？但其实是一种分类模型，常用于解决二分类问题

用一句话介绍 Logistic Regression：假设数据分布遵循**伯努利分布**，通过极大似然估计的方法来求解参数

本篇文章主要内容包括以下几点：
1. Logistic Regression 的数学模型怎么样的？
2. 如何求解 Logistic Regression 的参数？
3. Logistic Regression 和 Linear Regression 的区别在哪里？和贝叶斯的关系呢？
4. Logistic Regression 能否用于求解多分类问题？（面试也许会问）
5. Logistic Regression 能否用于求解非线性分类数据？
6. 对正则化的一些理解
7. Logistic Regression 为什么要用 Sigmoid 函数？（面试大概率会问）

现在假设有这么一个数据样本 $D$，$D = {(x_1, y_1), (x_2, y_2), ..., (x_i, y_i), ..., (x_n, y_n)}$，其中 $(x_i, y_i)$ 代表一个数据样本，$x_i$ 代表维度为 `m` 的输入向量，$y_i$ 表示真实结果，且 $y_i \in {0, 1}$， $y_i=1$ 表示正例，$y_i=0$ 表示反例
## 数学模型

Logistic Regression = Linear Regression + Sigmoid

其数学模型可表示为：

$$
P(y|x; \theta) = \sigma(\sum_i(w_ix_i+b_i)) = \sigma(\sum_i(w^Tx+b))
$$

其中，$\sigma(z) = \frac{1}{1+e^{-z}}$，即 Sigmoid 函数

在做二分类任务时，通过阈值来判断样本属于正例还是反例，例如，设置阈值为 0.5，即当 $P(y=1|x; \theta) > 0.5$ 时，判断为正例，否则归为反例

可以通过调整阈值大小来改善模型性能，例如，若提高该阈值，则模型的 Precision，即查准率会上升；弱降低该阈值，则模型的 Recall，即查全率会上升
## 参数求解

从概率学角度来看，使用极大似然估计来求解

极大似然估计说的是，求解一组参数，使得在这组参数下，数据的似然度（概率）越大。换句话就是，**利用已知的数据样本，找出最有可能生成该样本的参数**

在介绍似然函数之前，先来介绍一下伯努利分布，伯努利分布是一种离散概率分布，表示只有两种可能分布的随机事件，即 0 和 1，其中 1 表示成功， 0 表示失败。成功的概率记作 p，则失败的概率记作 1-p。

伯努利分布的概率密度函数可表示为：

$$
P(x) = p^x(1-p)^{1-x} = 
 \begin{cases}
 p,\,\,x=1\\
 1-p,\,\,x=0\\
 \end{cases}
$$

> 概率密度函数：对于离散型随机变量，概率密度函数描述该变量在每个可能取值处的概率

对于 Logistic Regression 来说，模型的似然函数（似然度）表示如下：

$$
L(\theta|D) = P(D|\theta) = \prod P(y|x; \theta) = \prod (\sigma(w^Tx+b))^y(1-\sigma(w^Tx+b))^{1-y}
$$

似然函数值（似然度）越大越好

值得注意的是，$L(\theta|D)$ 和 $P(D|\theta)$ 分别从两个角度来描述事件发生的可能性，前者指的是从观测结果出发，什么样的参数最有可能导致结果 $D$ 出现；后者则表示已知参数 $\theta$，发生观测结果 $D$ 的可能性大小

在机器学习中，通常用损失函数来衡量模型预测结果的好坏，其定义如下：

$$
J(\theta) = -\frac{1}{N}\ln(L(\theta|D)) = -\frac{1}{N}\sum y\ln(\sigma(w^Tx+b)) + (1-y)(\ln(1-\sigma(w^T+b)))
$$

下面使用梯度下降法来求解一组参数，使得损失函数值最小！
梯度下降，也称最速梯度下降，用一句话总结就是，**每一个都朝向梯度下降最快的方向走**

具体可分为三步：
1. 选择梯度下降方向，即 $-\nabla J(\theta)$
2. 确定走多远，即 $\theta^i = \theta^{i-1} - \alpha J(\theta)$
3. 重复上述两步，直至找到梯度为零的点或者到达最大迭代次数

下面基于随机梯度下降法，通过数学推导的方式求解参数

$$
\begin{align*}
\frac{\partial{J(\theta)}}{\partial{w}} 
&= -\frac{1}{N} \sum \frac{y}{\sigma(w^Tx+b)} \cdot \sigma(w^Tx+b)(1-\sigma(w^Tx+b)) \cdot x + \frac{1-y}{1-\sigma(w^Tx+b)} \cdot (-\sigma(w^Tx+b)) \cdot (1-\sigma(w^Tx+b)) \cdot x \\
&= -\frac{1}{N} \sum xy(1-\sigma(w^Tx+b)) - (1-y)x \sigma(w^Tx+b) \\
&= -\frac{1}{N} \sum xy - x \sigma(w^Tx+b)
\end{align*}
$$

注：上面式子中的 $x$ 和 $y$ 表示一个数据样本；$\sigma(w^Tx+b)$ 表示的是输入样本 $x$，得到的预测值

实际上，Logistic Regression 的函数函数是凸函数，因此可以保证我们找到的局部最优值同时也是全局最优值；还可以使用牛顿法来求解该问题
## 多分类问题

Logistic Regression 通常是用来解决二分类问题，即使用 Sigmoid 将结果映射到一个 0~1 之间的数据，然后通过预先设定一个阈值来判断属于正例还是反例

解决多分类问题有两个思路，一种思路是训练多个二分类器，这种方法适合于类别与类别之间不是互斥的，另一种思路是使用 Softmax 函数，即 Multinomial Logistic Regression，这种方法适合于类别与类别之间是互斥的

> 多元 logistics 回归实际就是多个二元 logistics 回归模型描述各类与参考分类相比各因素的作用。如， 对于一个三分类的因变量（口味：酸、甜、辣），可建立两个二元logistics回归模型，分别描述酸味与甜味相比及辣味与酸味相比，各口味的作用。但在估计这些模型参数时，所有对象是一起估计的，其他参数的意义及模型的筛选等与二元logistics类似。

对应的数学建模形式如下：

$$
P(y=i|x, \theta) = \frac{e^{w_i^Tx+b_i}}{\sum_j^Ke^{w_j^Tx+b_j}}
$$

## 非线性分类问题

Logistic Regression 本质上是一个线性模型，但是可以用来求解非线性可分的数据

方法：通过特征变换的方法，对数据升维，因为在低维空间不可分的数据，到高维空间中线性可分的几率会高一些
e.g. 原始特征有两个维度，即  $[x_1,x_2]$，可以通过特征变换的方式，增加新的数据维度，例如， $[x_1,x_2,x_1^2,x_2^2,x_1x_2]$ 
## 正则化

为什么会提出正则化？或者说正则化能解决什么问题？
当模型参数过多时，很容易出现过拟合，正则化的提出主要是为了解决过拟合问题，并提高模型在未见数据上的泛化能力

正则化通过在损失函数中引入一个惩罚项，惩罚项主要跟模型的权重相关，添加正则项之后，模型倾向于学习更加学习更加简单的模型，降低权重的影响！

常见的正则项： $L_1$ 正则化和 $L_2$ 正则化

添加正则项后的损失函数：

$$
J(\theta) = -\frac{1}{N}\ln(L(\theta|D)) = -\frac{1}{N}\sum y\ln(\sigma(w^Tx+b)) + (1-y)(\ln(1-\sigma(w^T+b))) + \lambda||w_i||_p
$$

$L_1$ 正则化：
- 使权重尽可能为 0
- 适用于模型剪枝，模型压缩等问题
$L_2$ 正则化：
- 使权重值分布更平滑，但通常不会使权重变为 0
- 适用于解决普通的过拟合问题
## 为什么要用 Sigmoid 函数？

首先来说一下为什么可以用，$\sum(wx_i+b)$ 的输出值是实数，而样本的类标签为 {0, 1}，因为可以用过 Sigmoid 函数在这两者之间建立一个映射关系，而且平滑，方便求导，当然，类似的函数还有很多

下面来说一下为什么要用 Sigmoid 函数，而不用别的函数？

![image.png](https://lei-1306809548.cos.ap-shanghai.myqcloud.com/Obsidian20240418201306.png)

> 最大熵原理：学习概率模型时，在所有可能的概率模型（即概率分布）中，熵最大的模型是最好的模型
## 代码实现
```python
"""
@File    :   main.py
@Time    :   2024/04/18 11:56:58
@Author  :   Xiang Lei
@Version :   1.0
@Desc    :   logistic Regression 算法复现

"""

# Ref: https://realpython.com/logistic-regression-python/

import numpy as np
import matplotlib.pyplot as plt

from sklearn.linear_model import LogisticRegression
from sklearn.metrics import classification_report, confusion_matrix

# x: independent variable
# y: dependent variable

# x = np.arange(10).reshape(-1, 1)
# 多维数据
x = np.random.randn(10, 100)
y = np.array([0, 0, 0, 0, 1, 1, 1, 1, 1, 1])

model = LogisticRegression(solver="liblinear", random_state=0)  # 初始化一个 LR 模型

model.fit(x, y)

print(model.classes_)  # 有哪些类别/
print(model.intercept_)  # b 向量
print(model.coef_)  # w 矩阵

print(model.predict_proba(x))  # 预测结果 [[为0的概率, 为1的概率]]
print(model.predict(x))
print(model.score(x, y))
```

## 参考：
https://zhuanlan.zhihu.com/p/74874291
https://zhuanlan.zhihu.com/p/58434325
https://zhuanlan.zhihu.com/p/341387537
https://realpython.com/logistic-regression-python/
https://tech.meituan.com/2015/05/08/intro-to-logistic-regression.html

