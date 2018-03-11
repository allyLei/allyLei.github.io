---
layout: post
title:  "EM算法"
categories: MachineLearning
tags:  PRML Graph
author: sunlei
---

* content
{:toc}

EM算法主要是用来解决具有隐变量的模型的最大似然参数的估计。常见的例子有混合高斯模型，隐马尔科夫模型，主题模型中的pLSA。Gibbs Sampling可以看成是EM算法的一种推广。







## EM

假设能够观察到的数据为$$y$$，隐变量为$$z$$，需要估计的参数为 $$ \theta $$。那么最大似然估计为$$ \ln p(y\mid\theta)$$。假设隐变量服从一定的分布$$q(z)$$，则

$$\ln p(y|\theta) = \sum q(z)\ln \frac{p(y, z | \theta)}{q(z)} - \sum q(z)\ln \frac{p(z|y,\theta)}{q(z)}$$

其中，第二项为$$q(z)$$与$$p(z\mid y,\theta)$$的KL散度，注意KL散度只有在两个分布一模一样的时候才为0，其余情况都大于0。我们可以将上式简写为:

$$\ln p(y|\theta)=L(q, \theta) + KL(q, p(z|y,\theta))$$

EM算法包括两个步骤：

1. E-step: $$Q(\theta, \theta^t)=\sum p(z\mid y,\theta^t)\ln p(y, z\mid \theta)$$

2. M-step: $$\theta^{t+1}=\arg \max\_{\theta} Q(\theta, \theta^t)$$

EM其实是一种类似于Gibbs Sampling的maximazation/maximation方法, 由上面的公式，我们还可以得到，

$$L(q,\theta)=\ln p(y|\theta)-KL(q, p(z|y,\theta))$$

那么，EM可以看成两个maxmization过程：

1. 保持$$\theta$$不变，最大化$$L(q, \theta)$$，这里由于$$\ln p(y\mid\theta)$$与$$q$$没有关系，所以当$$q(z)=p(z\mid y,\theta)$$时，上式取得最大

2. 保持$$q(z)$$不变，最大化$$L(q,\theta)$$，即等同于最大化$$\sum p(z\mid y,\theta^t)\ln p(y, z\mid\theta)$$


EM算法可以保证每次都朝着增大似然概率的方向走，但是不保证可以走到全局最优点。
