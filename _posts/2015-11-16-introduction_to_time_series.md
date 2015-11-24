---
layout: post
title: "Introduction To Time Series"
excerpt: ""
categories: articles
tags: [Time Series]
comments: true
share: true
---
Some Simple Time Series Models
-------
最完整的Time Series Model应该是随机向量序列{$$X_{1}+X_{2}+...$$}的联合分布。也就是
$$P(X_{1}\leq x_{1}...X_{n}\leq x_{n}), -\infty <x_{1},x_{2}...x_{n}<\infty, n=1,2...$$
 
 但是这样太过于复杂，所以相反的我们使用这个联合分布函数的first- and second-order moments，即$$EX_{t}$$与$$E(X_{t+h}X_{t}), t=1,2..., h =0,1,2...$$
,$${X_{t}}$$的性质完全决定于它们。这样的$${X_{t}}$$被称作是Second-order properties。

当一些特殊的情况下，比如说所有的联合分布都是多元正态分布时候，那$${X_{t}}$$的second-order properties就完全决定了联合分布，也因此决定了这个序列的概率特征

IID noise
----
IID noise是一个最简单的时间序列模型，它的前提是没有趋势，或者seasonal component（no trend, or seasonal component），所有的随机变量都独立同分布，即independent and identically distributed(iid)，mean = 0. 

这样一来，联合分布就可以化简为：

>$$P(X_{1}\leq x_{1}...X_{n}\leq x_{n})=P[X_{1}\leq x_{1}]...P[X_{1}\leq x_{n}]=F(x_{1})...F(x_{n})$$

对于$$h\geq1$$以及所有的$$x,x_{1}...,x_{n}$$,有
>$$P[X_{n+h}\leq x|X_{1}=x_{1},...,X_{n}=x_{n}]=P[X_{n+h}]$$

很好理解，因为是独立同分布，那么在n之后的观测值完全不由n之前的观测值来决定。

>复习一些东西:
>Mean Squared Error, 均方误差
>MSE 是用来测量平均误差的
>Standard Error of mean, $$SE_{\bar{x}} = \frac{s}{\sqrt{n}}$$, 这里s是样本的标准差，所以这个公式也是个估计值，完全体应该是$$SE_{\bar{x}}= \frac{\sigma}{\sqrt{n}}$$, sigma就是标准差
>$$MSE = \frac{1}{n}\sum_{t=1}^n(observed_{t} - predicted_{t})^2$$
>$$Var(x)=E((x-E(x))^2)$$
>$$MSE = E[(\widehat{\theta}-\theta)^2]=Var(\theta-\theta)+(E[\theta-\theta)])^2=var(\widehat{\theta})+(bias)^2$$ 
A binary process
------
作为一个IID noise的例子，考虑以下的独立同分布的随机变量，
$$P[X_{t}=1]=p, P[X_{t}=-1]=1-p, 其中p = 1/2$$


----------
*To be continued, 16 Nov, 2015*

