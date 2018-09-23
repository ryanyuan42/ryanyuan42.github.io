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

$$f(x+ \Delta x) = f(x) + \textbf{\Delta x^T} \textbf{g_n} + \frac{1}{2} \Delta x^T H_n \Delta x + ...$$  

Let $$h_n(\textbf{\Delta x}) = f(x) + \textbf{\Delta x^T} \textbf{g_n} + \frac{1}{2} \Delta x^T H_n \Delta x $$  

where $$\textbf{g_n}$$ and $$\textbf{H_n}$$ is the gradient and Hessian at $$x_n$$. So we can take the derivative against $$\Delta x$$, we
would have $$frac{\partial f}{\partial \textbf{\Delta x}}  = \textbf{g_n} + \frac{1}{2}(H_n^T + H_b) \textbf{\Delta x} = \textbf{g_n} + \textbf{H_n \Delta x}$$.  

Then we would have $$\textbf{\Delta x} = - \textbf{H_n^{-1} g_n} $$, we would know that $$ - \textbf{H_n^{-1} g_n}$$ is a good direction to minimize the problem. We can check this out by $$< - \textbf{H_n^{-1} g_n}, \textbf{g_n}> = - g^T (H^{-1})^T g$$, so as long as 

https://math.stackexchange.com/questions/2271887/how-to-solve-the-matrix-minimization-for-bfgs-update-in-quasi-newton-optimizatio
