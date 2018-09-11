---
layout: post
title: "Broyden's Method Derivation"
excerpt: "Numerical methods"
categories: articles
tags: [numerical methods]
comments: true
share: true
---

# Broyden's Method

## Newton's method
We know that in numerical methods, newton's method is very handy to use, and also fast to converge. Newton's method can be stated
as the following:  

$$ x_{n+1} = x_n - f'(x_n)^{-1} * f(x_n) $$  

However, the inverse of $$ f(x_n) $$ can be hard to calculate, such operation will take $$O(n^3)$$ time complexity. Therefore,
Broyden's method is introduced, to approxiate the inverse of the Jacobian matrix. 

## "Good" Broyden

Let's say we want to approximate the inverse of Jacobian matrix, we can first use rank-1 update to approximate the Jacobian matrix,
after that we can apply Sherman-Morrison to approximate the inverse of Jacobian. Broyden mentioned in his study that smaller rank matrix
should be used to update the Jacobian matrix, but here I am not sure why the smaller rank is needed.


### Rank 1 update
$$J_{k+1} = J_k  + uv^T$$  

### Secant condition
Secnat condition actually comes from the finite difference estimation of derivative in 1-dimesnion.  
$$J_{k+1}(x_{k+1} - x_k) = f(x_{k+1}) - f(x_k)$$ 
Let $$ x_{k+1} - x_k = d$$ and $$f(x_{k+1}) - f(x_k) = g$$

Combining the equations above, we should have $$u = \frac{g - J_k d}{v^T d}$$

Therefore, the material we need to start the update is only $$v$$. To find $$v$$, we can solve a optimization problem as follows:  
$$argmin ||uv^T||_F = ||\frac{hv^T}{v^Td}||_F$$, where $$h = g - Jd$$  

$$||\frac{hv^T}{v^Td}||_F = \sum_i\sum_j(\frac{h_iv_j}{\sum_k v_k d_k})^2$$  

After taking derivatives of $$v_j$$, we should have: 

$$2 \sum_i \frac{h_iv_j}{\sum_k v_k d_k} \frac{h_i\sum_k v_k d_k - h_i v_j d_j}{(\sum_k v_k d_k)^2} + \sum_i \sum_{l \neq j} 2 \frac{-h_i^2 v_l^2 d_j}{(\sum_k v_k d_k)^2}$$, to make it simpler, we have  
$$2 (\frac{\sum h_i^2}{(\sum_k v_k d_k})^2) (v_j - \frac{\sum_k v_k^2 d_j}{\sum_k v_k d_k}) = 0$$  

The equations satisfies when $$v = \alpha d$$, we can just let $$\alpha = 1$$, therefore, we can get the updating rules as follows:  

$$J_{k+1} = J_k + \frac{\Delta f_{k+1} - J_k \Delta x_{k+1}}{||\Delta x_{k+1}||^2} \Delta x_{k+1} ^ T$$  
Apply sherman-morrison on the it, we can get the update rule for inverse matrix of the Jacobian.

## "Bad" Broyden

"Bad" Broyden's idea is similar, given an slightly different secant condition, 

$$B_{k+1}g = d$$, where $$B$$ is an approximation to the inverse of Jacobian, then by the same logic as good broyden, write update rule as $$B_{k+1} = B_k + pq^T$$, and solve the optimization problem: 

$$|p q^T|_F$$

we would have $$q = \alpha g$$, which results in the final updating rule:  
$$B_{k+1} = B_k + \frac{\Delta x_{k+1} - B_k \Delta f(x_{k+1})}{||\Delta f(x_{k+1})||^2} \Delta f(x_{k+1})^T$$
