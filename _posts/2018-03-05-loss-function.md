---
layout: post
title:  "常见损失函数"
categories: MachineLearning
tags:  PRML
author: sunlei
---

* content
{:toc}

损失函数基本定义了一个统计模型学习的目标和方法。一般分为回归损失函数和分类损失函数。比如，最常见的回归损失函数为最小二乘，而最常见的分类损失函数为交叉熵。




## 损失函数

## 分类损失函数

常见的分类损失函数有对数损失函数，指数损失函数，hinge loss等。对数损失函数有交叉熵（LR）， Gini指数（DT）， 指数损失函数最典型的用于AdaBoost，Hinge loss则是SVM中提出的损失函数。

如果限定是二分类，即$$t\in \lbrace -1, +1 \rbrace$$，cross entropy可以表示为$$\ln(1+exp(-yt))$$，指数损失函数为$$exp(-yt)$$，hinge loss则为$$max(0, 1-yt)$$。如下图所示，绿线表示指数损失函数，红线表示交叉熵，蓝线表示hinge loss，可以看出，指数损失函数对于大的$$-\mid yt\mid$$给出的损失值最大，呈指数增长，而交叉熵则是线性增长，对于Hinge loss来说，比较特殊的是，当$$yt>1$$的时候，loss为0，即这部分的样本不贡献损失，这个特点是其他的损失函数所没有的。

![分类损失函数](https://pic.superbed.cn/item/5e1315e476085c32891c4c56.png "分类损失函数")

正是由于指数损失函数对于分类错误的点或者outlier产生的损失很大，所以采用这类损失函数的模型对于样本的要求很高，对outlier等不鲁棒，这也是使用AdaBoost模型训练所需要注意的。

## 回归损失函数

常见的回归损失函数有squared error, absolute error, huber error。与分类损失函数类似，squared error针对outlier不鲁棒，而huber error是squared error和absolute error的综合。

![回归损失函数](https://pic.superbed.cn/item/5e1315cd76085c32891c486c.png "回归损失函数")
