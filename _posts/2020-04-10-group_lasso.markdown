---
layout: post
title: "Group Lasso"
excerpt: ""
categories: articles
tags: [convex optimization]
comments: true
share: true
---

Consider a group lasso problem:

$$min \frac{1}{2N} \|X\beta - y\|_2^2 + \lambda \Sigma_j w_j \|\beta_(j)\|_2$$, A common choice for weights on groups $$w_j$$ is $$\sqrt{p_j}$$, 
where $$p_j$$ is number of predictors that belong to the $$j$$th group, to adjust for the group sizes.


Derivation:


For group j, we know that

$$0 \in \frac{1}{t}(z_{(j)} - \beta_{(j)} + \lambda w_j \partial\|z_{(j)}\|_2$$

$$0 = z_{(j)} - \beta_{(j)} + \lambda t w_j v_{(j)}$$


If $$z_{(j)} \neq 0$$
$$v_{(j)} = \frac{z_{(j)}}{\|z_{(j)}\|_2}$$

else, any $$\|v_{(j)}\|_2 \leq 1 $$ is in a subgradient


If $$z_{(j)} = 0$$,  $$0 = z_{(j)} - \beta_{(j)} + \lambda t w_j \frac{z_{(j)}}{\|z_{(j)}\|_2}$$

$$z_{(j)} = (I + diag(\lambda t \frac{w_j}{\|z_{(j)}\|_2}))^{-1} \beta_{(j)} = (1 + \lambda t \frac{w_j}{\|z_{(j)}\|_2})^{-1} \beta_{(j)}$$, we can do this becase the element on the diagnoal matrix is all the same

Take norm on both sides, we can get

$$\|z_{(j)}\|_2 = \|\beta_{(j)}\|_2 - \lambda t w_j$$

therefore, 

$$z_{(j)} = (1 - \frac{\lambda t w_j}{\|\beta_{(j)}\|_2}) \beta_{(j)}$$


If $$z_{(j)} = 0$$, $$z_{(j)}=0$$, at this time, $$\|v_{(j)}\|_2 \leq 1 $$, i.e, $$\|\beta_{(j)}\|_2 \leq \lambda t w_j$$

therefore, $$z_{j} = (1 - \frac{\lambda t w_j}{\|\beta_{(j)}\|_2})_{+} \beta_{(j)}$$


$$prox_{th}(\beta_{(j)}) = (1 - \frac{\lambda t w_j}{\|\beta_{(j)}\|_2})_{+} \beta_{(j)}$$


$$\beta_{(j)}^k = prox_{t_kh}(\beta_{(j)}^{k-1} - t_k \nabla g(\beta_{(j)}^{k-1})) = prox_{t_kh}(\beta_{(j)}^{k-1}  - \frac{t_k}{N} [(X^{(j)})^T (X\beta^{k-1} - y)])$$

