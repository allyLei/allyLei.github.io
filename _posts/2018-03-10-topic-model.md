---
layout: post
title:  "Topic Model [pLSA, LDA, EM, Gibbs Sampling]"
categories: MachineLearning
tags:  PRML Sampling EM
author: sunlei
---

* content
{:toc}

主题模型是语言模型中一个重要的分支，理解Topic Model，也就理解了很多统计学习以及概率图模型的很多点，例如EM, Gibbs Sampling, MLE，MAP, Bayes等等。





## Topic Model

这里先简单介绍一下MLE，MAP和Bayes三种参数估计方法。MLE和MAP都是频率派，而Bayes是贝叶斯派。频率派认为一切参数都是固定的，MLE和MAP方法所需要求解的只是某个让似然函数最大的那个参数点，而Bayes则有根本性的不同，它认为参数是一个服从一定分布的随机变量，不是一个确定的值，所以一般情况下，我们所需要估计的是这个参数分布的hyper parameters。在inference上，频率派相对简单，因为参数已知并且固定，而Bayes模型则需要根据参数分布得到后验概率分布，相对复杂。

## PLSA

pLSA模型是Topic Model中频率派的代表，LDA相当于是pLSA的扩展。对pLSA模型的参数估计可以采用MLE和MAP方法。pLSA中文档的生成过程为：

1. 以 $$p(d_i)$$ 的概率选择文档$$d_i$$

2. 以$$p(z_k\mid d_i)$$的概率生成主题$$z_k$$, 主题服从多项式分布

3. 以$$p(w_j\mid z_k)$$的概率生成$$w_j$$，词语服从多项式分布

那么，在pLSA模型中，观测量为$$d, w$$，而主题$$z$$是隐变量。由上面的分析，可以得到

$$p(w_j\mid d_i)=\sum^K_{k=1}p(w_j \mid z_k)p(z_k\mid d_i)$$，

注意这里的$$p(w_j\mid z_k, d_i)=p(w_j\mid z_k)$$ (条件独立)，最终这个模型所需要学习的参数即为$$p(w_j\mid z_k)$$和$$p(z_k\mid d_i)$$，为了方便，分别使用$$\theta_{k,j}$$和$$\phi_{i,k}$$表示，那么

$$p(D, W) = \prod^N_{i=1}\prod^M_{j=1}p(d_i)p(w_j\mid d_i)^{n(w_j, d_i)}$$

取log，使用MLE估计参数，可以得到

$$L=\log p(D, W) = \sum^N_{i=1} \sum^M_{j=1} \lbrace \log(p(d_i))+n(w_j,d_i)\log p(w_j\mid d_i)\rbrace$$

最大化上述函数，考虑到$$p(d_i)$$为常数，故只需要最大化如下函数：

$$L=\sum^N_{i=1}\sum^M_{j=1}n(w_j, d_i) \log p(w_j\mid d_i)$$

带入$$p(w_j\mid d_i)$$可得，

$$L=\sum^N_{i=1}\sum^M_{j=1}n(w_j, d_i)\log \sum^K_{k=1} \theta_{k,j}\phi_{i,k}$$

可以发现，参数都在log下的求和中，不好求解，并且这里涉及到隐变量$$z_k$$，所以我们尝试用EM来求解。

### EM

EM主要是对包含有隐变量的模型进行参数估计的方法，比较典型的例子就是GMM和pLSA。假定隐变量为$$z$$，观测到的数据为$$y$$，这里一般$$y$$称为incomplete data，而$$(y,z)$$称为complete data。

那么，$$p(y,z)=\sum^K_{k=1}p(z_k)p(y\mid z_k)$$。目标函数为$$\log p(y,z)$$，

$$Q_{\theta, \theta^t}=E_{z\mid y;\theta}(\log p(y,z))=\sum^K_{k=1}p(z_k|y;\theta^t)\log p(y, z_k)$$

使用$$\theta = \arg \max_\theta Q_{\theta, \theta^t}$$来估计。

### 求解

使用EM算法来求解pLSA的参数$$\theta,\phi$$，这里的$$\theta,\phi$$需要满足概率累加和为1的限制条件：

$$\sum^M_{j=1}\theta_{k,j}=1, \sum^K_{k=1}\phi_{i,k}=1$$

这里的$$Q$$函数可以表示为

$$\sum^N_{i=1}\sum^M_{j=1}n(w_j, d_i)\sum^K_{k=1}p(z_k|d_i, w_j)\log \theta_{k,j}\phi_{i,k}$$

这里，$$p(z_k\mid d_i, w_j)$$可以用参数估计得到，再通过最大化Q函数，求解更新得到新的参数。这里的最大化Q函数，是一个有约束的函数优化问题，可以通过Lagrange乘子转化为无约束的问题来求解。

## LDA

LDA是pLSA加上Bayes框架的扩展。

