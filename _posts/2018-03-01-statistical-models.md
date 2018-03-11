---
layout: post
title:  "常见统计模型"
categories: MachineLearning
tags:  PRML
author: sunlei
---

* content
{:toc}

常见的统计模型大概有DT, NB, HMM, LR, CRF, SVM。统计模型一般分为判别式模型和产生式模型， 区别在于一个是条件概率分布，一个是联合概率分布。对模型的学习方法可以分为有监督学习和无监督学习以及半监督学习。

以上模型中，NB和HMM是典型的产生式模型，学习的是联合概率分布，其他的都是判别式模型。





## Decision Tree

在实际问题中应用中，DT类的方法表现较为优异，例如Random Forest, GBDT等，这主要是因为DT：

1. 能够应对多种类型的输入，数值输入，类别输入等，能够灵活的对实际问题进行建模

2. 能够方便的应对missing data, 并且具有可解释性，也比较适合作为有效特征组合和特征选择

3. 能够方便的Scale到很大的数据集上，结合XGBoost等工具，也能够快速的训练

对于单一DT，特征之间没有连续性，也就是说，针对输入数据，在分支时非左即右，所以在实际使用过程中，结合一定的Boosting或者Bagging的方法，来达到平滑的效果。

一个典型的DT可以由下式子表示：

$$y=\sum w_mI(i\in t)$$

DT将特征空间划分为很多块，每个叶子节点表示一个划分的特征空间，每个叶子节点统一输出一个值（回归一般是均值，分类则取类别占比最大的类别）。DT的inference相对简单，直接随着建成的树进行分支直至叶子节点即可。最重要的是学习的过程，即如何选择分支的feature，以及选择分支点。

通常学习都和损失函数息息相关。DT的损失函数一般为以下几种：

1. 回归损失函数：Squared error，absolute error， huber error

2. 分类损失函数：交叉熵plogp， Gini指数p(1-p)

![Decision Tree 损失函数](http://pic.puaschool.cn/pictures/2018/01/30/91d692dd56154e13a90834c67b2fded9.png?query=9fc0307a957a47769517d9695ebf39d5)

针对过拟合问题，一般DT的学习中的正则项选择为树的深度或者叶子节点的个数等，除了显式的在损失函数中加入正则项，还可以通过剪枝来减轻过拟合的问题。DT的学习过程一般都是从candidate feature中选择出一个一个最能够使得loss下降的feature进行split，正是由于DT的损失函数是加性的（对每个叶子节点损失的相加），所以针对特定的分支点，可以计算得到损失的增益值，选择损失值变的最小的那个feature，作为分支feature。在实际使用过程中，通常都是使用启发式搜索。

## Logistic Regression

LR是广义线性模型中的一种，它的链接函数是logit函数，即

$$logit(y) = \log \frac{y}{1-y} = wx$$

表达为概率的形式

$$p(y=1\mid x) = \frac {1}{1+\exp(wx)}$$

LR是假定服从Bernouli分布得到的广义线性模型，除了使用logit函数，也可以使用probit函数，但是由于使用logit函数，带来了很多的便捷性，例如求导($$\partial\log(p)/\partial x = 1 - p$$)等等，故大多数使用logit链接函数。

一般使用极大似然估计来学习LR模型，即$$\log p$$，考虑到模型的过拟合，一般会再加上正则项。

LR可以用于大规模的样本的学习，具有较好的Scalability，并且能够有效的支持在线学习，所以工业中很多会使用LR作为基准分类器（例如CTR预估模型）。LR与DT不同，它是一个数学模型，要求将输入转化为数值向量的形式。

## Maximum Entropy Model

最大熵模型在实际应用中不多，它的思想简洁明了，LR可以算是最大熵模型的一种特例。最大熵模型可以表示如下：

$$P(y\mid \mathrm{x}, \mathrm{\theta})=\frac{\exp (\mathrm{\theta} f(y,\mathrm{x}))}{\sum_y \exp(\mathrm{\theta}f(y,\mathrm{x}))}$$

可以明显看出，这里的$$f(y,x)$$与LR中简单的$$x$$不同，它可以根据$$y$$给出任何的特征条件，这使得它的表达能力比LR更强，但是这种将$$y$$引入特征的操作也会使得学习和inference都变得困难。

## Support Vector Machine

SVM一般都作为小样本集上的基准分类器。它采用Hinge loss作为损失函数，它的学习过程是典型的对于有约束的凸优化问题。利用Lagrange乘子，转化为极大极小值问题，最后利用SMO算法，顺序的求得模型的最优解。

SVM是一个在数学表达上很优美的模型，在逐渐演化下，一般会加入松弛变量，针对线性不可分的情况，会利用核函数。

## Naive Bayes & Hidden Markov Model

把NB和HMM放在一起，主要是因为后者是前者在sequential data上的扩展。它们都是生成模型。在有监督学习中，NB和HMM的学习都是相对简单的，只需要统计它们的联合概率即可，针对HMM，还可以通过无监督的方法，即EM算法来学习。在模型推理中，NB可以很简单的得到，HMM则可以利用Vertebi算法也就是DP的思想快速得到最优解。

NB和HMM都有一种很重要的特质，就是通过加入Markov条件独立性假设，使得问题的求解变得容易。在NB中，认为当前的状态只和前$$n$$个状态有关；在HMM中，则分为观测序列（$$x$$）和状态序列（隐变量$$z$$），加入了更强的独立性假设：当前的观测值只和当前的状态值有关，当前的状态值只和上一次的状态值有关，即

$$p(x_1, x_2, ..., x_n, z_1, z_2, ..., z_n)=p(z_1)\prod p(z_i\mid z_{i-1})\prod p(x_i\mid z_i)$$

在模型inference时，使用简单的一维DP就可以得到最优解。严格来说，HMM应该是包含有隐变量的模型，这意味着无法显式的得到观测序列，所以在训练时候，通常使用EM算法。HMM在实际应用中可以用于分词和语音识别。

## Conditional Random Field

CRF与HMM不同，它是判别模型，一维的线性链CRF一般用于NLP中的命名实体识别，词性标注等任务，二维CRF一般用于图像中的分割或者各种后处理任务。

CRF属于Markov Random Field的一种，它也是有Markov条件独立假设，这一类模型都是属于Probabilistic Graphic Model范畴，PGM一般都使用最大似然估计来学习。针对线性链CRF，一个简单的形式如下：

$$p(y_1, ..., y_n\mid x_1, ..., x_n)=\prod p(y_i,y_{i+1}\mid \mathrm{x})$$

其中，$$p(y_i,y_{i+1}\mid \mathrm{x})=\exp (\sum_k \lambda_kt_k(y_i, y_{i+1}, \mathrm{x})+\sum_{l} u_ls_l(y_i, \mathrm{x}))/Z$$

可以发现在CRF里，你可以定义多种特征，状态值可以和多个观测值相关，前后状态值之间也可以引入多种特征。而且利用最大似然估计，累乘可以转化为累加，这样的优化是典型的无约束优化问题，由梯度下降法及其各种变种可以解决。CRF的inference和HMM类似，也是使用DP来解决。

HMEM也可以解决词性标注等任务，它与CRF的一个最大的不同在于归一化的位置不同。CRF是全局归一化，而HMEM是局部归一化，局部归一化会导致的一个问题就是label bias。

