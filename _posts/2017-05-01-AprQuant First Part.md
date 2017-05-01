---
layout: post
title: "AprQuant First Part"
excerpt: ""
categories: articles
tags: [AprQuant, ARMA, Fisher Information]
comments: true
share: true
---

# AprQuant真的开始写了  
话说也挺讽刺的，我的AprQuant first part在May才出现....我的这个执行力
这几天忙完了论文，打算真的开始做AprQuant，于是想先挑一个最简单的，就先从时序里面的ARMA开始做好了。  
结果不做不知道，做了以后才发现各种问题，从简单的AR(1)开始做起，

$$X_{t} = \phi X_{t-1} + c + w_{t}$$, $$\w_{t}$$是一个normal iid的white noise。

为了估计出$\phi, c, \w_t$，就必须要用maximum likelihood estimation，但是具体如何用又是另外一回事。  
MLE在一些情况下就是OLS，因为当得到了likelihood function之后，其实可以把$X_1$看作是一个已知的数字，这样X_1的likelihood就是1，剩下要做的就是去
maximize conditional likelihood, which is actually minimize this:

$$\sum_{t=2}^T(y_t - c - \phi y_{t-1})^2 = (y - X\beta)^T(y - X\beta)$$

This becomes OLS.  

$$y = (y_2, y_3, ..., y_T), X = \begin{pmatrix} 1 & y_1 \\ 1 & y_2\\. & .\\ 1 & y_{T-1} \end{pmatrix}$$ 

$$\beta =  \begin{matrix} c \\ \phi \end{matrix} $$


## MLE

除此以外，在MLE之中，还可以看到一个有趣的定理，即Cramer-Rao下界，它的证明可以查看statistical inference, 证明起来主要就是用了柯西不等式和chain rule，
总之告诉了我们，在满足regularity conditions的时候，unbiased estimator的方差有一个下界，即inverse of fisher information，fisher information和
Hessein matrix很像，即-1 * condition on theta, likelihood对theta的二阶导。通过Slutsky thereom, 可以得到unbiased estimator - true estimator服从
normal的分布（均值为0，方差为inverse fisher information matrix）

但是实际应用之中，肯定是需要optimize likelihood，我发现，至少用一些涉及到二阶导的方法中（L-BFGS），很难得到global minimum,能不能得到全局最小值很大程度
取决于initial point，但是梯度方法本质上就无法解决这样的问题，只能用一些启发式的算法来解决这样的问题，比如说退火，和遗传算法来解决它。

所以，我猜这也是为什么大多ARMA的参数估计中都是使用Conditional Likelihood的原因，因为Exact likelihood不好解，而conditional likelihood可以得到解析
解，通过简单OLS方法就可以获得最后的估计值，并且可以统计SSR,SSE得到t值和p值。

下次试试看用SA来解决Exact Likelihood。
