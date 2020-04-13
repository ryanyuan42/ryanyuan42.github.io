---
layout: post
title: "SVM as optimization problem with Python"
excerpt: ""
categories: articles
tags: [convex optimization]
comments: true
share: true
---

## Problem

Consider a SVM problem: 

$$\min_{\beta,\beta_0,\xi_i} \frac{1}{2}\|\beta\|^2+\sum_{i=1}^{N}\xi_i \\
\xi_i\geq 0 \\
y_i(\beta^Tx_i +b) \geq 1-\xi_i$$  


We know we can convert this problem to dual, and this problem has strong duality(slater's condition)  

$$\max_{\alpha} -\frac{1}{2} \alpha^T Y G Y \alpha + 1^T\alpha \\
\alpha^T y = 0, \\
0 \leq \alpha \leq C1$$  

where $$G$$ is the kernel matrix


Therefore, we can solve the problem with quardratic programming. We will be able to get $$\alpha$$ after quardratic programming.

## Intercept

We will need get the intercept $$\beta_0$$ to be able to make predictions. To calculate the intercept, we can average all the support 
vectors. Methods can be found [here](https://stats.stackexchange.com/questions/211310/deriving-the-intercept-term-in-a-linearly-separable-and-soft-margin-svm): 

Summarized as follows:  

$$wx_{sp}+b=1$$, where sp is a support vector with $$y=1$$

$$wx_{sn}+b=-1$$, where sp is a support vector with $$y=-1$$

$$b=1 - wx_{sp}$$, and $$b=-1 - wx_{sp}$$, using this logic, we can average all support vectors to get a stable estimate of $$b$$
## Code

The following is a simple implementation of SVM with python.

{% highlight python %}
@jit(nopython=True)
def rbf(x, y, gamma):
    return np.exp(-gamma * np.linalg.norm(x - y, 2) ** 2)

@jit(nopython=True, nogil=True)
def construct_gram_matrix(X, gamma):
    n = X.shape[0]
    G = np.zeros((n, n))
    shown = set()
    for i in prange(n):
        for j in prange(n):
            if (j, i) in shown:
                G[i, j] = G[j, i]
            else:
                G[i, j] = rbf(X[i], X[j], gamma)
                shown.add((i, j))
    return G
    
    
def solve_svm(X, y, gamma, C):
    n = X.shape[0]
    gram = construct_gram_matrix(X, gamma)

    Y = np.diag(y)

    P = Y@gram@Y
    q = -np.ones(n)

    G = np.vstack((np.identity(n), -np.identity(n)))
    h = np.hstack((C * np.ones(n), np.zeros(n)))

    P = cp.matrix(P)
    q = cp.matrix(q)

    G = cp.matrix(G)
    h = cp.matrix(h)

    sol = cp.solvers.qp(P, q, G=G, h=h, A=cp.matrix(y.reshape(1, -1)), b=cp.matrix(0.0))
    alpha = np.array(sol['x']).reshape(-1)
    
    n = X.shape[0]
    ind = np.arange(n)
    sv = ind[alpha > 1e-5]

    b = y[sv].sum()
    for i in sv:
        b -= (alpha[sv] * y[sv] * gram[i, sv]).sum()
    b = b / len(sv)
    
    
    optimal_value = 0.5 * np.dot(np.dot(alpha, P), alpha)
    return alpha, b, sol, optimal_value


def predict(new_X, alpha, b, X, y, gamma):
    ind = np.arange(n)
    prediction = np.zeros(new_X.shape[0])
    sv = ind[alpha > 1e-5]

    for i in range(new_X.shape[0]):
        res = 0
        for a, sv_y, sv_x in zip(alpha[sv], y[sv], X[sv]):
            res += a * sv_y * rbf(new_X[i], sv_x, gamma)
        prediction[i] = res

    return prediction + b



def f(x, X_sv, y_sv, alpha_sv, gamma):
    # this function is used for screening rules, not essential for svm implementation
    res = 0
    for a, sv_y, sv_x in zip(alpha_sv, y_sv, X_sv):
        res += a * sv_y * rbf(x, sv_x, gamma)
    return res
    
    
result = dict()
for gamma in [10, 50, 100, 500]:
    for C in [0.01, 0.1, 0.5, 1]:
        result[(gamma, C)]  = solve_svm(X, y, gamma=gamma, C=C)
        

fig, axes = plt.subplots(nrows=4, ncols=4, figsize=(20, 20))
for i, gamma in enumerate([10, 50, 100, 500]):
    for j, C in enumerate([0.01, 0.1, 0.5, 1]):
        
        alpha, b, _, _ = result[(gamma, C)]
        axes[i, j].set_title("gamma=%d, C=%.2f" % (gamma, C))
        
        # create a mesh to plot in
        h = .02 # step size in the mesh grid
        x_min, x_max = X[:, 0].min() - 4*h, X[:, 0].max() + 4*h
        y_min, y_max = X[:, 1].min() - 4*h, X[:, 1].max() + 4*h
        xx, yy = np.meshgrid(np.arange(x_min, x_max, h),
                             np.arange(y_min, y_max, h))
        
        
        new_X = np.c_[xx.ravel(), yy.ravel()]
        Z = predict(new_X, alpha, b, X, y, gamma)

        Z[Z > 0] = 1
        Z[Z < 0] = -1
        Z = Z.reshape(xx.shape)
        axes[i, j].contourf(xx, yy, Z, cmap=plt.cm.coolwarm, alpha=0.2)
        axes[i, j].scatter(X[:, 0], X[:, 1], c=y, cmap=plt.cm.coolwarm)
        axes[i, j].set_xlim(xx.min(), xx.max())
        axes[i, j].set_ylim(yy.min(), yy.max())
        
{% endhighlight %}


![svm decision boundary with different gamma and C](https://raw.githubusercontent.com/ryanyuan42/ryanyuan42.github.io/master/assets/svm_decision_boundary.png)

# Screening Rules

Screening rules are usually found by manipulating the KKT conditions. Many different algorithms have different screening rules.  

We can do a screening on the data, i.e., screen out the points that $$\alpha_i = 0$$, because having them or not will not affect the final solution. Details can be found [here](http://proceedings.mlr.press/v28/ogawa13b.pdf)  

I chose not to put the code here because I found it not so useful: the points that can be discarded highly depend on the gamma and C the user pick, especially when the upper C and lower C is far away, no data points can be discarded.  


Maybe I get this conclusion only because I didn't test enough data sets especially on large ones. But the idea is simple: screen out all the data points that are not support vectors.

