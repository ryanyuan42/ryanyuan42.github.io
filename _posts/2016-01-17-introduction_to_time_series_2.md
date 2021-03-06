---
layout: post
title: "Introduction To Time Series(2)"
excerpt: "ACF Function"
categories: articles
tags: [Time Series]
comments: true
share: true
---

终于要再开始写关于Time Series的东西了，寒假已经过去了，寒假里面主要学的就是一些关于quant的东西，一个模型，一次implementation，马上要开始在UCB的最后一个学期，希望能够好好度过吧。

上次讲到IID Noise，可实际上Time Series已经看了很多过去了，就当现在是在为自己复习吧。

###Random Walk

Random Walk是一个iid随机变量的累计的叠加过程。

$$S_{t} = X_{1}+...+X_{2}$$，其中S0=0 而Xt是iid noise, 

$$S_{t}$$被称作**simple symmetric random walk**.可以看作是一个人在time = 0的时候进行抛硬币走路行为，Heads就向右边走，Tails向左走。比较简单的一个时序。而需要注意的是，$$X_{t}$$的结果可以通过$$S_{t}$$来得到，也就是说$$S_{t} - S_{t-1} = X_{t}$$

###Models with Trend and Seasonality

讨论关于对具有**趋势**以及**季节性**的时序的建模过程，

$$X_{t} = m_{t} + Y_{t}$$

其中$$m_{t}$$是**趋势**（**trend component**），而$$Y_{t}$$具有mean = 0的性质，也就是说$$Y_{t}$$就是residual。估计$$m_{t}$$的一个比较有用的方法是最小二乘法。

比如说我们希望fit一个function，$$m_{t} = a_{0}+a_{1}t+a_{2}t^2$$，那么我希望通过$$a_{0}，a_{1}，a_{2}$$,最小化$$\sum_{t=1}^n(x_{t}-m_{t})^2$$，通过这样的方式得到$$m_{t}$$。

在实际建模的过程中，我们往往可以找到$$m_{t}$$，但是如果我们简单的估计residual就是它的mean=0的话，这样显然是不合理的，比如说如果$$Y_{t}$$和$$X_{t}$$具有高度相关性的话，我们或许可以得到更好的估计结果。

###Harmonic Regression

我们为了考虑到**季节性**，假设没有**趋势**的话，我们可以建模为，

$$X_{t} = s_{t} + Y_{t}$$，

其中$$s_{t} = a_{0} + \sum_{j=1}^k(a_{j}cos(\lambda_{j}t)+b_{j}sin(\lambda_{j}t))$$，这个模型的周期为d，也就是说，$$s_{t-d}=s_{t}$$，如果说观测数量n是d的整数倍数的话，那么$$\lambda_{j}, j = 1,...,k$$就也是$$2\pi/n$$的整数倍，也就是$$2\pi m_{1}/n,2\pi m_{2}/n,...$$，而m1,m2...这些被称作为**Fourier indices**，显而易见的是，一个正弦函数或者余弦函数的周期是，$$n/m$$，那么，如果我们要去fit一个model，它的周期是365，而我们有365个观测量（即n=365），m应该选取为1。

###A General Approach to Time Series Modeling

在介绍**dependence**和**stationary**之前，我们先把建模过程大概描述一下。

 * 画出Time Series，并且检验几个主要的特征，是否存在
  *  趋势，a trend
  *  季节性，a seasonal component  
  *  在行为上面任何明显的尖锐改变。
  *  任何无关的观测量
 * 移除trend和seasonal component来得到需要的stationary residuals，为了实现这个目标，往往需要对data做一些preliminary transformations。比如说，如果时序中的浮动程度，似乎与时序的Level有着线性增加的关系，那么最好把时序转换为自然对数$$lnX_{1}，lnX_{2}$$，这样浮动程度就会更加的稳定。（Note: 如果说有些data是负数，那最好先加一个正数确保没有负数后，再取自然对数）总之我们希望得到一个稳定的时序，把它refer为residuals
 * 选择一个model来捕捉residuals，运用许多sample statistics包括the sample autocorrelation function 样本自相关方程
 * 预测就是预测residuals，然后再做上面的transformation回去，以得到原始的时序$$X_{t}$$

###Stationary Model与自相关函数

定义：

让$${X_{t}}$$为一个时序，它的$$EX_{t}^2<\infty$$，它的mean function是$$\mu_{x}(t)=E(X_{t})$$，它的协方差方程是$$\nu_x(r,s)=Cov(X_{r}, X_{s})= E[(X_{r} - \mu_{x}(r))(X_{s} - \mu_{x}(s))] $$ for all integers r,s and t.

那我们说Xt是(weakly) stationary的，如果有：
 1. $$\mu_x(t)$$对于t是独立的
 2. $$\nu_x(t+h, t)$$对于每个h都是independent of t的

严格的stationar需要$$(X_1, .., X_n) 和 (X_{1+h},...,X_{n+h})$$有相同的联合分布函数（对于所有的整数n，以及h>0）。如果时序是strictly stationary并且$$EX_{t}^2<\infty对于所有t$$，那么$$X_t$$也是weakly stationary的。

对于条件2，我们指的$$\nu_x$$函数都是只有一个变量的，定义为：

$$\nu_x:=\nu_x(h,0)=\nu_x(t+h, t)$$，其中$$\nu_x()$$refer 为autocovariance function，而$$\nu_x(h)$$是它在lag = h时的值。

定义：

如果$$X_t$$是一个stationary的时序，那么The Autocovariance Function(ACVF)就是：

$$\nu_x(h)=Cov(X_{t+h}, X_t) = E[(X_{t+h} - \nu_x(t+h))(X_t - \nu_x(t))]$$

The Autocorrelation Function(ACF)是，

$$\rho_x(h) = \frac{\nu_x(h)}{\nu_x(0)} = Cor(X_{t+h}, X_t)$$

IID Noise是一个stationary的时序

而WHITE NOISE，白噪声，是指，$$X_t$$是uncorrelated random variables，每个随机变量的mean都是0，variance都是$$\sigma^2$$，那么白噪声也是稳定的（stationary）。它与IID NOISE拥有相同的协方差方程。

IID一定是WN，然而反过来却不对。

Random Walk，可以简单证明它的协方差方程与t相关，所以它不是稳定的。

有MA(1)和AR(1)两个模型可以介绍一下：

####MA(1)，First-Order Moving Average

考虑以下的Series，$$X_t =  Z_t + \theta Z_{t-1}，t = 0, +-1....$$，其中$$Z_t$$是WN(0, $$\sigma^2$$)然后theta是个常数。

那么也就是说$$E(X_t) = 0, E(X_t^2) = \sigma^2(1+\theta^2)<\infty$$，那么这样的话我们可以的到协方差方程为：$$\nu_X(t+h, t) = \sigma^2(1+\theta^2), h = 0;\theta\sigma^2, h = 1, -1; 0, h <-1, h > 1$$ 这样看来，期望是Independent of t，而协方差函数也是independent of t。

结论，$$X_t$$是稳定的（stationary），而自相关函数（ACF）是，

$$\rho_X(h) = 1 , h = 0$$

$$\rho_X(h) =  \frac \theta{1+\theta^2} , h=-1, 1$$

$$\rho_X(h) = 0, h < -1, h > 1$$

####AR(1), First-Order Autoregression 一阶自回归

假设$$X_t$$是一个稳定的序列，$$X_t =  Z_t + \phi X_{t-1}，t = 0, +-1....$$

其中$$Z_t$$依旧是白噪声，WN(0, $$\sigma^2$$)， 并且$$Z_t$$与$$X_s$$是uncorrelated，对于s < t来说。

既然如此，那么$$E(X_t) = \phi E(X_{t-1})$$，又因为X是稳定的，那么期望一定要与t无关，可见$$E(X_t) = 0$$.

所以ACF function为，$$\rho_X(h)  = \phi^ {\mid h\mid}, h =0, 1,-1,...$$

接下去复习Sample Autocorrelation function，意思其实很简单，就是说上面提到的自相关方程中，都是预设好的模型，但是在实际问题中并不会这样，所以我们需要通过观测值，observations得到样本自相关方程。

*Jan 17, 2016*
