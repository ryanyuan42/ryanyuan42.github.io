---
layout: post
title: "building machine learning system in python(2)"
excerpt: "knn"
categories: articles
tags: [python, machine learning]
comments: true
share: true
---
## Building more complex classifiers

之前我们使用的是一个非常简单的模型，一个在单独维度上的简单的threshold，我们可以看到很多其它的模型。

那么什么组成一个classification model呢？我们可以把它分为以下三个部分:
 * **the structure of the model**：在之前的例子中，我们使用的是，单独特征的一个threshold
 * **the search procedure**：在之前的例子中，我们用了每种特征和threshold的组合
 * **The loss function**：使用loss function，我们要决定那种可能错误更少一点，我们可以用training error，通常我们希望loss function能够实现最小化

我们可以用这些来得到不同的结果，比如说，我们可以尝试得到一个使得training error最小的threshold，但我们只对每个特征检验三个值：mean value, mean + one standard deviation, 和 mena - one standard deviation。这样比较靠谱因为如果要test所有的值的话就太耗时了（假设我们有很多很多个data）

或者说，我们也可以用一个不同的loss function。有可能出现一种类型误差比另外一种类型的误差是更加耗时的。False negatives和False positives是不一样的。**False negatives**（当测试的结果显示是negative的时候，但这是错误的，比如说以为没有这类疾病，但实际上是有的，也就是漏报率），**False positives**（当测试结果显示Positive的时候，但这是错误的，比如以为有这类疾病，但实际上没有，也就是误报率）。

You want to minimize the false negatives as much as you can 因为这样更加危险。

## A more complex dataset and a more complex classifier

接下来看点稍微复杂一点的dataset，以此作为分类算法的介绍。

### Seeds Dataset

接下来看一个有关农业的数据，它仍旧很小，但是已经大到难以让我们像对待Iris Flower那样简单的plot这些数据了。这是一个用来衡量小麦种子的数据集。一共有七个特征：
 * Area(A)
 * Perimeter (P)
 * Compactness (C = 4piA/P^2)
 * Length of kernel
 * Width of kernel
 * Asymmetry coefficient
 * Length of kernel groove

一共有三种小麦种类：Canadian, Koma, and Rosa
像之前一样我们希望能用它们的形态为他们分类

## Features and Feature engineering

有趣的是，在上面的特征中，其实Compactness并不是新的特征，而是从Area和Perimeter得到的。通常这样得到新的组合而成的特征是很有用处的，这被称作为feature engineering，它可能没有算法那么亮眼，但是它可能对最终的表现更加重要，因为选好一个feature可以比 一个好的算法但却用一个不好的feature表现的更好。

Compactness 也称作roundness圆度，通常用来衡量形状。比如如果有两个谷粒A和B，A比B大一倍，但如果A与B的形状是一样的，那它们的Compactness也还是一样的。一个谷粒越圆Compactness就越接近1，越是不圆就越接近0。

这样的特征就可以轻松衡量圆度，而不受到大小的影响。

另外也要注意，遇到较大的project的时候，要学会Feature selection，面对过多的特征，选择真正重要的那些特征。

## Nearest neighbor classification

在这个dataset之下，如果还用之前的简单的threshold，那我们得不到很好的结果，我们现在使用nearest neighbor这个classifier

如果我们考虑到每个data都是一个具有N个维度的点的话，我们就可以计算每个点之间的距离。

{% highlight python %}
def distance(p0, p1):
    return np.sum((p0 - p1)**2)

import numpy as np
p0 = np.array([1,2])
p1 = np.array([0,0])

def nn_classify(training_set, training_labels, new_example):
    dists = np.array([distance(t, new_example) for t in training_set])
    nearest = dists.argmin()
    return training_labels[nearest]
{% endhighlight %}

{% highlight python %}
def load_dataset(dataset_name):
    '''
    data,labels = load_dataset(dataset_name)

    Load a given dataset

    Returns
    -------
    data : numpy ndarray
    labels : list of str
    '''
    data = []
    labels = []
    with open('{0}.tsv'.format(dataset_name)) as ifile:
        for line in ifile:
            tokens = line.strip().split('\t')
            data.append([float(tk) for tk in tokens[:-1]])
            labels.append(tokens[-1])
    data = np.array(data)
    labels = np.array(labels)
    return data, labels

def fit_model(k, features, labels):
    '''Learn a k-nn model'''
    # There is no model in k-nn, just a copy of the inputs
    return k, features.copy(), labels.copy()


def plurality(xs):
    '''Find the most common element in a collection'''
    from collections import defaultdict
    counts = defaultdict(int)
    for x in xs:
        counts[x] += 1
    maxv = max(counts.values())
    for k in counts.keys():
        if counts[k] == maxv:
            return k
# 这里的K 就是选出离点最近的k个点
def predict(features, model):
    """Apply k-nn model"""
    # according to testing features to predict
    # model中包含的是training features and training labels
    k, train_feats, labels = model
    results = []
    for f in features: #这里的features是testing features
        label_dist = []
        # compute all distance
        for t, ell in zip(train_feats, labels):
            # 用向量范数来替代距离
            # 使用范数可以测量两个函数、向量或矩阵之间的距离。
            # 这里的f和t都包含七个数，代表七个feature
            label_dist.append((np.linalg.norm(f - t), ell))
        label_dist.sort(key=lambda d_ell:d_ell[0])
        #这里找到最近的K个点
        label_dist = label_dist[:k]
        #在这K个点之中，出现最多次的label被加入到results之中
        results.append(plurality([ell for _, ell in label_dist]))
    return np.array(results)
        
def accuracy(features, labels, model):
    #这里放入的是 testing features 和 testing labels
    preds = predict(features, model)
    return np.mean(preds == labels)

{% endhighlight %}

以上描述了knn这个模型，下面是Implementation

{% highlight python %}
features, labels = load_dataset('seeds')
def cross_validate(features, labels):
    error = 0.0
    for fold in range(10):
        training = np.ones(len(features), bool)
        training[fold::10] = 0 #使得fold位置和fold + 10, fold+20...位置变为0
        testing = ~training
        model = fit_model(1, features[training], labels[training])
        test_error = accuracy(features[testing], labels[testing], model)
        error += test_error
    
    return error / 10.0

error = cross_validate(features, labels)
error
{% endhighlight %}

```0.89523809523809528```

我们之前的方法是把所有七个特征都代入进一个向量，通过范数来衡量距离。但是我们可以normalize所有的特征到一个scale上，比如说使用normalize to z-score的办法。Z-score value是以标准差的单位来看，离均值有多远。
{% highlight python %}
z_score_features = features.copy()
z_score_features -= features.mean(0)
z_score_features /= z_score_features.std(0)
z_score_error = cross_validate(z_score_features, labels)
z_score_error
{% endhighlight %}

```0.94285714285714284```

normalize的过程：
$$f' = \frac{f - \mu}{\sigma}$$

在normalize to z_score之前，

![](http://screenshot.net/4jw17uk.jpg)

在normalize to z_score之后，

![](http://screenshot.net/9xe07b9.jpg)

## 我们也可以用sklearn来实现
{% highlight python %}
from sklearn.cross_validation import KFold
from sklearn.neighbors import KNeighborsClassifier
features, labels = load_dataset('seeds')
classifier = KNeighborsClassifier(n_neighbors=1)
kf = KFold(len(features), n_folds=5, shuffle = True)
means = []
for training ,testing in kf:
    # we fit a model for this fold, then apply it to the testing data with 'predict'
    classifier.fit(features[training], labels[training])
    prediction = classifier.predict(features[testing])
    
    # np.mean on an array of booleans return fraction of correct decisions of this fold
    curmean = np.mean(prediction == labels[testing])
    means.append(curmean)
# 估计这里答案会变化的原因是 cut the fold是随机选第一个位置的，而不是固定从第一个[0::10]
res = np.mean(means)
print '{:.1%}'.format(res)
{% endhighlight %}
``` 91.9% ```

### 使用leave-one-out
{% highlight python %}
n = len(features)
correct = 0.0
for ei in range(n):
    training = np.ones(n, bool)
    training[ei] = 0
    testing = ~training
    classifier.fit(features[training], labels[training])
    pred = classifier.predict(features[testing])
    correct += (pred == labels[testing])
    
correct = correct / n
print '{:.1%}'.format(correct[0])
{% endhighlight %}

### sklearn下的normalization
{% highlight python %}
# z-score 
from sklearn.pipeline import Pipeline
from sklearn.preprocessing import StandardScaler
classifier = KNeighborsClassifier(n_neighbors=1)
classifier = Pipeline([('norm', StandardScaler()), ('knn', classifier)])
# The Pipeline constructor takes a list of pairs (str,clf). Each pair corresponds to a step in
# the pipeline: the first element is a string naming the step, while the second element is the
# object that performs the transformation.
{% endhighlight %}
可以看出来Pipeline对classifer进行了改变，classifier会对数据先进行normalize

> After normalization, every feature is in the same units (technically, every feature is now dimensionless; it has no units) and we can more confidently mix dimensions.

{% highlight python %}
kf = KFold(len(features), n_folds=5, shuffle = True)
means = []
for training ,testing in kf:
    # we fit a model for this fold, then apply it to the testing data with 'predict'
    classifier.fit(features[training], labels[training])
    prediction = classifier.predict(features[testing])
    
    # np.mean on an array of booleans return fraction of correct decisions of this fold
    curmean = np.mean(prediction == labels[testing])
    means.append(curmean)
res = np.mean(means)
print '{:.1%}'.format(res)
{% endhighlight %}
``` 93.8% ```

> while a few dimensions are dominant in the original data, after normalization, they are all given the same importance.

在normalization之后，数据包含的七个特征的权重都变得一样了。

