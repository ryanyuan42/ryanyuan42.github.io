---
layout: post
title: "Black Litterman"
excerpt: "bayesian view of BL"
categories: articles
tags: [portfolio management]
comments: true
share: true
---

# Portofolio Optimization

We have so many different optimization methods, and what we are trying to beat the simple average of a given portfolio. Nowadays, when
we are doing risk management, we are curious about the VaR and CVaR optimization, and of course the classical mean-variance optimization.
But before the optimization methods can be applied, we need to have an estimation of the joint distribution which is different than the 
margin distribution, since we have the copula information in the joint distribution, where the copula should represent our risk.  

# Estimating distribution

Intuitively, we can estimate the joint distribution from the historical data, fit it into some parameteric model or just use kernel density
distribution. However, with such a diffcult market, it will be very impractical and unrealible since our distribution may be chaning all
the time and sometimes we have our personal views or predictions towards current market. What we need is the Black-Litterman framework to
incorporate our views into the distribution to make the distribution more reliable.

# Black-Litterman

Surprisingly, many people didn't really have a good documentation of Black-Litterman. Here we assume that we have a random vector $$r$$, 
representing the return vector on the market. I guess that is because many important authors developed this model, but using very different
interpretaion or aspects, which results so many confusions in Black-Litterman model.


$$r \sim N(\mu, \Sigma)$$  

We view the parameter $$\mu$$ as a random variable, and we think the prior distribution for the parameter as $$\mu \sim N(\pi, \Sigma_{\pi})$$,
and we will have some views to reflect what we think of the market, for example, first, a relative view in which the investor believes that 
asset 1 will outperform asset 3 by 2% with confidence, $$\omega_1$$. Second, an absolute view in which the investor believes that asset 2 
will return 3% with confidence $$\omega_2$$. Then we can represent a pick matrix $$P$$, vector of return for each view $$q$$ and uncertainty
matrix $$q$$ as  

$$P = \begin{bmatrix}  1 & 0 & -1 & 0 \\ 0 & 1 & 0 & 0 \end{bmatrix}$$  
$$q = \begin{bmatrix} 2 \\ 3 \end{bmatrix}$$  
$$\Omega = \begin{bmatrix} \omega_11 & 0 \\ 0 & \omega_22 \end{bmatrix}$$  

Then, by saying those views, we are saying, $$E[P r] = q$$, then we are saying a model that $$P \mu = q + \epsilon, \text{where} \epsilon \sim
N(0, \Omega)$$, then we will have $$q \mid \mu \propto exp(-\frac{1}{2} (q - P\mu)^T \Omega^{-1} (q - P\mu)) $$ and $$\mu \propto 
exp(-\frac{1}{2} (\mu - \pi)^T  \Sigma_{\pi}^{-1} (\mu - \pi))$$.  

Then we know the posterior's negative log likelihod is (remove the terms that don't have $$\mu$$, and completing the square):

$$\mu \mid q \propto (q - P\mu)^T \Omega^{-1} (q - P\mu)) + (\mu - \pi)^T  \Sigma_{\pi}^{-1} (\mu - \pi)) \propto
\mu^T (P^T \Omega^{-1} P + C^{-1}) \mu - 2 (q^T \Omega^{-1} P + \pi^T C^{-1}) \mu$$,

now we let $$A = P^T \Omega^{-1} P + C^{-1} \text{and} b^T = (q^T \Omega^{-1} P + \pi^T C^{-1})$$ then we have $$\mu \mid q \propto 
\mu^T A \mu - 2 \mu^T b = \mu^T A \mu - 2 \mu^T A A^{-1}b \propto (\mu - A^{-1}b)^T A (\mu - A^{-1}b)$$  

Therefore, we know that the posterior mean is $$A^{-1} b$$ and the posterior varaince is $$A^{-1}$$, which is that the posterior 
distribution is $$N( (P^T \Omega^{-1} P + \Sigma_{\pi}^{-1})^{-1}) * (P^T \Omega^{-1} q + \Sigma_{\pi}^{-1} \pi ),  (P^T \Omega^{-1} P + \Sigma_{\pi}^{-1})^{-1})$$  


Traditional black-litterman has $$\pi$$ be the equilibrium return and the $$\Sigma_{\pi}^{-1} = \tau \Sigma$$.



