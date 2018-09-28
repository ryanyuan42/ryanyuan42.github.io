---
layout: post
title: "Feature reduction PCA and LDA"
excerpt: "KNN"
categories: articles
tags: [machine learning]
comments: true
share: true
---

# Curse of dimentionality

When the dimension of data increaes, it always need more samples to fill the space, which means that with the increasing 
dimensionality, the feature space is becoming more and more sparse. In practice, the classifier tends to degrade after a certain
number of dimensions instead of improve and therefore, it is quite important to reduce the dimension by mapping the dataset from
a higher dimension to a lower dimension to improve classifiers.  

There are two approaches that are avaialbe to reduce the dimensions, which are feature extraction and feature selection.

# Feature Extraction
The problem of feature extraction can be think as a problem to find a mapping function $$f: R^N \longrightarrow R^M $$ to map $$x$$
into a lower dimension space. A optimal mapping function should not increase $$P(error)$$ that is, a bayes rule should have the
same classification rate on the original space and the reduced dimension space.  

In general, the mapping function should be a non-linear function, but here we will just first introduce the linear mapping 
function. However, there is no systematic way to generate non-linear transforms. The selection of a particular subset of 
transforms is problem dependent. Therefore, a linear mapping function is commonly used. A linear function can be seen as 
$$y = Wx$$.  

Feature extraction can be classified into two groups: 
  * Signal representation: The goal of the feature extraction mapping is to represent the samples accurately in a 
  lower-dimensional space
  * Classification: The goal of the feature extraction mapping is to enhance the class-discriminatory information in the 
  lower-dimensional space

# PCA

For a vector $$x \in R^N$$, and let $$U = [\phi_1 \mid \phi_2 ... \mid \phi_N]$$ be an orthogonal matrix, the $$\phi_i$$ are
basis vectors in $$R^N$$. Therefore, we can represent $$x$$ as a linear combination of the basis vectors. $$x = \sum_{i=1}^N y_i \phi_i$$
What to noice here, is that, $$y_i = \phi_i^T x$$, which is easy to prove. Suppose we want to represent x with only M basis 
vectors, replace $$[y_{M+1}, ... y_N]^T$$ with some pre-selected constants $$b_i$$.  

Then we can represent the new x as $$\hat{x}(M) = \sum_{i=1}^M y_i \phi_i + \sum_{i=M+1}^N b_i \phi_i$$, therefore the error 
is $$\delta x(M) = x - \hat{x}(M) = \sum_{i=M+1}^N (y_i - b_i) \phi_i$$, here we try to minimize the mean square error,
$$error = E[( \delta x(m) ) ^ 2] = E[\sum_{i=M+1}^N \sum_{j=M+1}^N (y_i - b_i)(y_j - b_j) \phi_i^T \phi_j] = \sum_{i=M+1}^N 
E[(y_i - b_i)^2]$$  

To find the optimal $$b_i$$, we take derivative of $$b_i$$, we should get $$b_i = E[y_i]$$. Now,  


$$error = \sum_{i=M+1}^N E[(y_i - E[y_i])^2] = \sum_{i=M+1}^N E[(\phi_i^T x - E[\phi_i^T x])^T (\phi_i^T x - E[\phi_i^T x])]
= \sum_{i=M+1}^N \phi_i^T E[(x - E[x]) (x - E[x])^T] \phi_i = \sum_{i=M+1}^N \phi_i^T \Sigma_x \phi_i$$.  

Now, we want to minimize the error with the constraint $$\phi_i^T \phi = 1$$, by lanrangian multiplier, we can take 
derivative and find that $$\Sigma_x \phi_i = \lambda_i \phi_i$$.  

Therefore, $$\phi_i$$ and $$\lambda_i$$ are just eigenvectors and eigenvalues of $$\Sigma_x$$.
