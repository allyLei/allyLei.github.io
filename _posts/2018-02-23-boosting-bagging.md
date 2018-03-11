---
layout: post
title:  "Boosting & Bagging, AdaBoost, GBDT"
categories: MachineLearning
tags:  PRML
author: sunlei
---

* content
{:toc}

Boosting和bagging是两种很常见的模型ensemble的方法，这其中最为成功并且广泛应用的有AdaBoost，GBDT，Random Forest，前两个是Boosting方法，Random Forest采用了Bagging的策略，同时也应该注意到这三个都是对DT的一种ensemble。AdaBoost在传统的特定物体检测上取得了很大的成功，GBDT则广泛应用在搜索排序等方向上，Random Forest在一般问题上都能取得不错baseline。




## Boosting & Bagging

在模型的Bias-Variance tradeoff上面考量的话，Boosting和Bagging的偏重点不同，Boosting更偏向于减小Bias，所以要求其基础模型不应该有太大的Variance，这也就是为什么Boosting算法中的基础模型较为简单，很多都是深度为1的树；而Bagging与此不同，更加偏重于减小Variance，所以其基础的每个模型的Bias不宜过大，采用的都是相对复杂的模型。

## Boosting

Boosting的想法很简单，即通过一系列弱分类器，如何组成强分类器，这其中主要分为了两种思想，一种是通过对样本的reweight来达到整体模型的boost，即AdaBoost；一种是通过迭代的模型集成来实现，如GBDT，MART等。

通常都是通过前向分布算法来实现Boosting的优化，即采用贪心的策略，保持前面的步骤不变，优化这一步。


### AdaBoost

AdaBoost通过对样本的加权来实现，这里我们只讨论二分类的情况。$$F(x)=\sum_{m=1}^M\alpha_mf_m(x)$$，$$y\in {-1, +1}$$。AdaBoost的损失函数为指数损失函数：$$\sum\exp(y_iF(x_i))$$。一个很简单的AdaBoost的训练过程如下：


1. Initialize $$ (x_i,y_i, w_i), w_i = 1/N $$

2. for m in [1, M]:

	2.1 train a weak classifier $$ f_m$$

	2.2 calculate $$ e_m = \Sigma w_{m-1}I(y_i\neq f_m(x_i)) $$，$$\alpha_m=\frac{1}{2}\log\frac{1-e_m}{e_m}$$

	2.3 re-weight sample, $$w_m = w_{m-1}\exp(-\alpha_mI(y_i=f_m(x_i)))/Z$$
	
	2.4 update classifier: $$F\_m(x) = F\_{m-1} + \alpha_mf_m(x)$$

AdaBoost的损失函数为指数损失函数，对于第m步，损失函数为

$$
\sum w^i_{m-1}\exp(y_i\alpha_mf_m(x_i)) = \sum \exp(\alpha_m)I(y_i=f_m(x_i)) w^i_{m-1} + \sum \exp(-\alpha_m)I(y_i\neq f_m(x_i))w^i_{m-1}
$$

对该式基于$$\alpha_m$$求导，可以得到最优的$$\alpha_m=\frac{1}{2}\log\frac{1-e_m}{e_m}$$ 。

AdaBoost的训练误差是指数级下降的。

AdaBoost对于样本的要求很高，这从损失函数的角度来考虑很简单，指数损失函数对于outlier是不鲁棒的。AdaBoost在实际使用过程中，其基础分类器一般采用tree，并且大多数为深度为1的单节点tree，这也使得AdaBoost虽然损失函数拟合的是经验损失，但是在实际中具体较好的泛化能力。

在检测中，通常会使用Cascade结构的AdaBoost，由于检测中正负样本的极度不均衡，所以使用Cascade结构可以实现负样本的fast reject。

### GBDT

Gradient Boosting Decision Tree（GBDT）在广告搜索排序中得到了广泛的应用，这也是实际表现不错的模型之一。在进行多个DT集成时，GBDT的主要思想和梯度下降法类似，唯一的不同就是GBDT是对函数的求导，而一般梯度下降法是对参数的求导。模型$$F(x)=\sum f_m(x)$$，损失函数$$\sum L(y_i, F(x_i))$$，那么在第$$t$$步，$$f_t(x) = - \rho_t\sum\frac{\partial{L(y_i, F(x))}}{\partial{F(x)}}$$，其中$$\rho_t$$即为迭代速率。

如果损失函数为Least Square，那么$$L(y, F(x)) = (y - F(x))^2$$，在第$$t$$步，$$f_t(x)$$需要拟合的就是$$y-F(x)$$即残差。

一个通用的GBDT的训练过程如下：

1. Initialize $$F(x)=h_0(x, \alpha_0)$$

2. for m in [1, M]:

	2.1 calculate $$\tilde{y} = - \frac{\partial L(y, F(x))}{\partial F(x)}$$
	
	2.2 optimize $$\alpha_{m}=\arg\min_{\alpha}(\tilde{y}-h_m(x, \alpha_m))$$

	2.3 optimize $$\rho_m=\arg\min L(y, F(x)+\rho_m h_m(x, \alpha_m)$$

	2.4 update $$F(x) = F(x) + \rho_m h_m(x, \alpha_m)$$

这里的$$\rho$$即是梯度下降法中的学习率，为了防止过拟合，GBDT中会采用shrinkage的方法，即限定每一步走的步长，模型更为鲁棒，但是整体树的个数会增加。

值得注意的是，GBDT本质上做的是回归，如果使用GBDT做分类的话，和DT不一样的是，DT采用gini系统来做split，GBDT使用一个将叶子节点经过softmax转化作为概率的交叉熵作为损失函数。

### XGBoost

XGBoost是对GBDT，CART等树的一个很好的实现。在XGBoost中，GBDT的实现与上面介绍的稍有不同：

1. GBDT是对导数的一阶估计，而XGBoost还包含了对导数的二阶估计

2. XGBoost中加入了正则项，用以控制树的复杂度，防止过拟合

3. XGBoost中每个叶子节点都有其权重，相当于GBDT中的$$\rho$$，通过对这个权重的正则，可以防止过拟合

4. XGBoost可以random的选择数据集，类似于Random Forest中的策略

5. XGBoost优化了很多的特征并行加速等等工程细节

值得注意的是，GBDT采用对损失函数求函数的导数的方法，而XGBoost则对损失函数在$$F_{t-1}(x)$$点进行Taylor级数展开，得到：

$$\sum L(y_i, F_t(x)) = \sum L(y_i, F_{t-1}(x_i))+h_if_t(x)+\frac{1}{2}g_if_t^2(x)+\Omega(f_t)$$

其中，$$h_i = \frac{\partial L(y_i, F_{t-1}(x))}{\partial F_{t-1}(x)}$$， $$g_i=\frac{\partial^2 L(y_i, F_{t-1}(x))}{\partial^2 F_{t-1}(x)}$$

这样，针对每一个节点，都能找到一个optimal的权重作为该叶子的权重。去除掉常数项，并且对每个叶子节点进行求和，可以得到

$$L_t=\sum^{T}_{t=1}[(\sum_{i\in t}h_i)w_t+\frac{1}{2}(\sum_{i\in t}g_i+\lambda)w^2_t]+\gamma T$$

对$$w_t$$求导，可以得到最佳的 $$w^*_t=-\frac{\sum_{i\in t}h_i}{\sum_{i\in t}g_i + \lambda} $$，当$$w_t=w^*_t$$时，

$$L=-\frac{1}{2}\sum^T_{t=1}\frac{(\sum_{i\in t}h_i)^2}{\sum_{i \in t}g_i+\lambda}+\gamma T$$

这样在贪心+启发式搜索得到切分feature和切分点时候，能够很方便的得到增益，例如在一个点切分为$$h_l, h_r$$时候，增益为：

$$L_{split}=\frac{1}{2}(\frac{H^2_l}{G_l+\lambda}+\frac{H^2_r}{G_r+\lambda}-\frac{(H_l+H_r)^2}{G_l+G_r+\lambda})-\gamma$$

## Bagging

Bagging中最成功的代表即Random Forest，采用多棵树的结果的Average来作为最终的结果。Bagging采用多个模型的Average或者投票结果来作为最终的结果，最佳的情况，可以将均方误差降低$$M$$倍（$$M$$为Bagging模型的个数）。

假定$$f_m(x) = h_m(x)+e_m$$, 那么$$M$$个模型的average均方误差为$$\frac{1}{M}\sum^{M}_{m=1}E(e^2_m)$$，通过Bagging的方法得到的模型的均方误差为$$E(\lbrace\frac{1}{M}\sum^M_{m=1}e_m\rbrace^2)$$，当$$E(e_m)=0$$，$$E(e_me_l)=0$$，即误差的均值为0，并且每两个模型之间不相关时，Bagging得到的模型的均方误差降低了$$M$$倍。但是通常模型之间都有很大的相关性，所以Bagging的策略能够降低误差，但是不会有$$M$$倍的差距。

### Random Forest

Random Forest的基本思想如下：随机选择数据集的一部分，随机选择特征中的一部分，生成一颗树，如此迭代生成一个森林，最后的决策由这多棵树共同投票来决策。Random Forest能够实现比一般DT更好的结果。
