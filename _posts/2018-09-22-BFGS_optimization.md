---
layout: post
title: "BFGS optimization"
excerpt: "BFGS and DFP"
categories: articles
tags: [numerical methods]
comments: true
share: true
---

# Optimization

Suppose we have a function $$f: {\rm I\!R^n} \longrightarrow {\rm I\!R}$$, we want to minimize/maxmizie the function, we can use the gradient descent method, follow current gradient and keeps going, the problem is that might not be fast enough. So, for such a function,
we can use taylor expansion to get (expand around x)  

$$f(x+ \Delta x) = f(x) + \Delta x^T g_n + \frac{1}{2} \Delta x^T H_n \Delta x + ...$$  

Let $$h_n(\Delta x) = f(x) + \Delta x^T g_n + \frac{1}{2} \Delta x^T H_n \Delta x $$  

where $$g_n$$ and $$H_n$$ is the gradient and Hessian at $$x_n$$. So we can take the derivative against $$\Delta x$$, we
would have $$\frac{\partial f}{\partial \Delta x}  = g_n + \frac{1}{2}(H_n^T + H_b) \Delta x = g_n + H_n \Delta x}$$.  

Then we would have $$\Delta x = - H_n^{-1} g_n $$, we would know that $$ - H_n^{-1} g_n$$ is a good direction to minimize the problem. We can check this out by $$\langle\ - H_n^{-1} g_n, g_n \rangle  = - g^T (H^{-1})^T g$$, so as long as we keep H as a positive definite matrix, we are going the direction that makes the function smaller.

In practice, we would set the update as $$x_{n+1} = x_n - \alpha (H_n^{-1} g_n) $$, where $$\alpha$$ can be seen as a step size and generally be solved by line search methods.

# Quasi-Newton Method

What can be expected is that, such method will cost a lot to compute the inverse of the Hessian matrix, therefore, we need a quasi-newton method to solve this. Broyden proposed to use rank 2 update method to approxiate the Hessian matrix, because the rank 1 update would not be able to keep the Hessian positive definite.  

# Secant condition
As usual, we need a secant condition for our approxiate, here the condition is $$H_{n+1}(x_{n+1} - x_{n}) = g_{n+1} - g_n$$, which lead us to $$H_{n}^{-1}y_n = s_n$$. DFP method is actually approximating the $$H_n$$ and update the inverse using sherman-morrison, BFGS method is directly updating the inverse of $$H_n$$. In later derivation, I'll just use $$H$$ to represent the inverse.  

To find the updating rule, we need to solve the following problem,  

$$argmin_H |H - H_k|_W \quad\text{subject to} H = H^T, Hy_k = s_k$$  

where the weighted forbenius norm is defined to be $$ \|A\|_W = \| W^{\frac{1}{2}} A W^{\frac{1}{2}}|_F$$, where the weight matrix $$W$$ is any positive definite matrix that satisfies $$W s_k = y_k$$ and we can assume that $$W = G_k^{-1}$$ where $$G_k$$ is the average hessian, $$G_k= \int_0^1 \nabla^2 f(x_k+\tau \alpha_k p_k)d\tau$$

# Derivation

Define $$\hat{H} = W^{\frac{1}{2}} H W^{\frac{1}{2}}$$, $$\hat{H_k} = W^{\frac{1}{2}} H_k  W^{\frac{1}{2}}$$, $$\hat{s_k} = W^{1}{2} s_k$$, $$\hat{y_k} = W^{-\frac{1}{2}} y_k$$, then the problem becomes,  

$$\min\|\hat H_k-\hat H\|_F\quad\text{subject to }\hat H\hat y_k=\hat y_k\ = \hat s_k $$ 

what else to notice is that, $$\hat{y_k}$$ is the eigenvector of $$\hat{H}$$, since $$\hat{H} \hat{y_k} = \hat{y_k} $$, then we can introduce a new orthogonal matrix, with new orthogonal basis, 

$$ U = [u |u_\bot]$$ where $$ u = \frac{\hat y_k}{\|\hat y_k\|} $$  

Since Frobenius norm is unitary invariant, (as it is sum of squares of the singular values), we can get  

$$ \|\hat H_k-\hat H\|_F=\|U^T\hat H_kU-U^T\hat HU\|_F = \left\|\begin{bmatrix}\color{blue}* & \color{blue}*\\\color{blue}* & \color{red}*\end{bmatrix}-\begin{bmatrix}\color{blue}1 & \color{blue}0\\\color{blue}0 & \color{red}*\end{bmatrix}\right\|_F. $$


$$\begin{align}
U^T\hat H_kU-U^T\hat HU&=\begin{bmatrix}u^T\\ u_\bot^T\end{bmatrix}\hat H_k\begin{bmatrix}u & u_\bot\end{bmatrix}-\begin{bmatrix}u^T\\ u_\bot^T\end{bmatrix}\hat H\begin{bmatrix}u & u_\bot\end{bmatrix}=\\
&=\begin{bmatrix}\color{blue}{u^T\hat H_ku} & \color{blue}{u^T\hat H_ku_\bot}\\\color{blue}{u_\bot^T\hat H_ku} & \color{red}{u_\bot^T\hat H_ku_\bot}\end{bmatrix}-\begin{bmatrix}\color{blue}{1} & \color{blue}{0}\\\color{blue}{0} & \color{red}{u_\bot^T\hat Hu_\bot}\end{bmatrix}.
\end{align}$$,  

we can find that, the blue part cannot be affected since it's fixed, we can only adjust the red part, which is to make them equal to cancel each other.

Therefore, $$\color{red}{u_\bot^T\hat Hu_\bot}=\color{red}{u_\bot^T\hat H_ku_\bot}.$$, which gives us the solution,  

$$\hat H=U\begin{bmatrix}\color{blue}1 & \color{blue}0\\\color{blue}0 & \color{red}{u_\bot^T\hat H_ku_\bot}\end{bmatrix}U^T.$$  

$$\hat H=\begin{bmatrix}u & u_\bot\end{bmatrix}\begin{bmatrix}1 & 0\\0 & u_\bot^T\hat H_ku_\bot\end{bmatrix}\begin{bmatrix}u^T \\ u_\bot^T\end{bmatrix}=uu^T+u_\bot u_\bot^T\hat H_ku_\bot u_\bot^T=
uu^T+(I-uu^T)\hat H_k(I-uu^T)$$





https://math.stackexchange.com/questions/2271887/how-to-solve-the-matrix-minimization-for-bfgs-update-in-quasi-newton-optimizatio
