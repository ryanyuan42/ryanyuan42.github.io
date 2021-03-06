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
would have $$\frac{\partial f}{\partial \Delta x}  = g_n + \frac{1}{2}(H_n^T + H_b) \Delta x = g_n + H_n \Delta x$$.  

Then we would have $$\Delta x = - H_n^{-1} g_n $$, we would know that $$ - H_n^{-1} g_n$$ is a good direction to minimize the problem. We can check this out by $$\langle\ - H_n^{-1} g_n, g_n \rangle  = - g^T (H^{-1})^T g$$, so as long as we keep H as a positive definite matrix, we are going the direction that makes the function smaller.

In practice, we would set the update as $$x_{n+1} = x_n - \alpha (H_n^{-1} g_n) $$, where $$\alpha$$ can be seen as a step size and generally be solved by line search methods.

# Quasi-Newton Method

What can be expected is that, such method will cost a lot to compute the inverse of the Hessian matrix, therefore, we need a quasi-newton method to solve this. Broyden proposed to use rank 2 update method to approxiate the Hessian matrix, because the rank 1 update would not be able to keep the Hessian positive definite.  

# Secant condition
As usual, we need a secant condition for our approxiate, here the condition is $$H_{n+1}(x_{n+1} - x_{n}) = g_{n+1} - g_n$$, which lead us to $$H_{n}^{-1}y_n = s_n$$. DFP method is actually approximating the $$H_n$$ and update the inverse using sherman-morrison, BFGS method is directly updating the inverse of $$H_n$$. In later derivation, I'll just use $$H$$ to represent the inverse.  

To find the updating rule, we need to solve the following problem,  

$$argmin_H |H - H_k|_W \quad\text{subject to} H = H^T, Hy_k = s_k$$  

where the weighted forbenius norm is defined to be  

$$ \| A \|_W = \| W^{\frac{1}{2}} A W^{\frac{1}{2}}\|_F$$,  

where the weight matrix $$W$$ is any positive definite matrix that satisfies $$W s_k = y_k$$ and we can assume that $$W = G_k^{-1}$$ where $$G_k$$ is the average hessian, $$G_k= \int_0^1 \nabla^2 f(x_k+\tau \alpha_k p_k)d\tau$$

What else to notice here, is that we know $$W$$ is a symmetric matrix, and any symmetric matrix can be diagonalized by an orthgonal matrix, $$ Q^T W Q = D$$, where $$D$$ is a diagonal matrix with all the eigenvalues as its diagnoal values. And since $$W$$ is a positive definite matrix, there exist a matrix $$B$$ such that $$B B = W$$. For example, we know that $$W = Q D Q^T$$ and we let $$B = Q D' Q^T$$ where $$D'_{ii} = \sqrt{D_{ii}}$$, and we can easily prove that $$B B = W$$.  

# Frobenius norm

Frobenius norm is the only norm that is unitary invariant, which means that the frobenius norm is not changed by unitary transformation. For any square matrix, the unitary(orthgonal) transformation can be seen as $$B = R^T A R, \quad\text{where} R \text{is a unitary(orthogonal matrix)} $$.  

It can also be easily proved:  

$$\|A\|_F = \sqrt{\sum_{i,j} a_{ij}^2} = \sqrt{tr(A^T A)} = \sqrt{\sum_i^R \lambda_i} = \sqrt{\sum_i^R \sigma_i^2}$$, therefore  

$$\| B \|_F^2 =  tr(B^T B) = tr(R^T A^T R R^T A R) = tr(R^T A^T A R) = tr(AR R^T A^T) = tr(AA^T) = \| A\|_F^2$$  


# Derivation

Define $$\hat{H} = W^{\frac{1}{2}} H W^{\frac{1}{2}}$$, $$\hat{H_k} = W^{\frac{1}{2}} H_k  W^{\frac{1}{2}}$$, $$\hat{s_k} = W^{1}{2} s_k$$, $$\hat{y_k} = W^{-\frac{1}{2}} y_k$$, then the problem becomes,  

$$\min\|\hat H_k-\hat H\|_F\quad\text{subject to }\hat H\hat y_k=\hat y_k\ = \hat s_k $$ 

what else to notice is that, $$\hat{y_k}$$ is the eigenvector of $$\hat{H}$$, since $$\hat{H} \hat{y_k} = \hat{y_k} $$, then we can introduce a new orthogonal matrix, with new orthogonal basis, 

$$ U = [u | u_\bot] \quad\text{where} u = \frac{\hat y_k}{\|\hat y_k\|} $$  

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

We use the fact of the orthgonal basis matrix  
$$I=UU^T=\begin{bmatrix}u & u_\bot\end{bmatrix}\begin{bmatrix}u^T \\ u_\bot^T\end{bmatrix}=uu^T+u_\bot u_\bot^T\quad\Leftrightarrow\quad u_\bot u_\bot^T=I-uu^T.$$  

Now, remember that we have the property $$W s_k = y_k$$, we expand the formula and get the BFGS update  
$$H_{k+1} = (I-\rho_k s_k y^\top_k)H_k (I-\rho_k y_k s^\top_k)+ \rho_k s_k s^\top_k \text{where}  \rho_k = (y^\top_k s_k)^{-1} $$

# Interpretation

From here, we can see that after the unitary transformation of the orthgonal matrix, we can easily solve the optimization problem. What else to notice is that, $$\hat{y_k}$$ is the eigenvector of $$\hat{H}$$, and the unitary transformation is basically representing the matrix in the eigenvector space.

And look at the update rule: $$H_{k+1} = (I-\rho_k s_k y^\top_k)H_k (I-\rho_k y_k s^\top_k)+ \rho_k s_k s^\top_k \text{where}  \rho_k = (y^\top_k s_k)^{-1} $$, we can know that as long as $$\rho_k \geq 0$$, and $$H_k$$ is psd, we can ensure that the approximated Hessian matrix is positive definite, $$\rho_k \geq 0$$ can be satisified if we have a convex function. Then as long as the first estimate of Hessian matrix is positive definite, we can make sure that we are always going in the right direction.

# Reference:
https://math.stackexchange.com/questions/2271887/how-to-solve-the-matrix-minimization-for-bfgs-update-in-quasi-newton-optimizatio
