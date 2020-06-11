---
layout: post
title: "Wrap up for CMU Convex Optimization Course"
excerpt: ""
categories: articles
tags: [convex optimization]
comments: true
share: true
---

# Convex Set and Convex functions

Basic concepts of convex sets and functions, the definition and some common convex sets, such as polyhedron, convex cone, norm cone, 
halfspace, affine space.

# Canonical problems

Linear programming, conic programming, semidefinite programming,  

$$LP \subseteq QP \subseteq Second Order Conic Program \subseteq SDP \subseteq Conic Program$$


# Optimization methods

## Gradient descent

Intuition comes from a quardartic approximation, where replace the Hessain with a identity matrix, and find the next point $$x^{+}$$ to 
minimize the quadratic approximation

Apply backtracking line search when doing gradient descent

Convergence: linear convergence rate and it can be improved by accelartion(Nesterov) and even faster with strong convexity.


## Subgradient

We would have some loss that is not differentiable, we can always pick a subgradient and follow the direction of the subgradient. But 
the subgradient method is very slow and it's also not necessarily a descent method. 

It also need diminishing step sizes to ensure convergence. The subgradient has a very slow convergence rate.

## Proximal Gradient Descent

Instead of using subgradient method, we would notice that most loss function is actually a smooth function + another non-smooth function,
so we can just make quardratic approximation on the smooth part and leave the non-smooth part alone. By doing that, we can create a proxmial
operator based on the non-smooth part.

The convergence rate is the same as gradient descent.

## Stochastic Gradient Descent

Normally, we have large data input, which makes it costly to compute the overall gradient, therefore, rather than calculating the overall
gradient, we just calcualte the gradient on some random samples(mini-batch).

The convergence of SGD is rather slow, and cannot adapt to strong convexity. The convergence rate is $$O(1/\varepsilon^{2})$$ and $$O(1/\varepsilon)$$ top

There was a time when people believe that it's the best they can do, but it turns out that there's more that can be done by variance reduction and
acceleration (SAG, SAGA, ADAM)


# Optimality and Duality

More on these stuff later.



I followed this class for around 6 weeks and finished all the readings and homeworks. It's a very interesting experience. I guess one
thing I learned from this is that to completely follow a class like this, it's vital to do all the homeworks. The sense of accomplishment
just keeps you going and drive you to find out what's next. 

I am going to put my homework files here. The homework 3 and 5 are rather simple and I didn't write it down as a pdf.

[hw2](https://github.com/ryanyuan42/ryanyuan42.github.io/raw/master/assets/convex_opt_hw2.pdf)  

[hw4](https://github.com/ryanyuan42/ryanyuan42.github.io/raw/master/assets/convex_opt_hw4.pdf)


I am going to start following the standford CS224n. It's a not a theoretical class like this but also very intriguing as it talks about the architechture
of deep neural networks and why it's made that way. I've always wanted to find out but getting somewhat confused by those online blogs.

