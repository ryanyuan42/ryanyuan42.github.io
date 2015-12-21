---
layout: post
title: "building machine learning system in python(1)"
excerpt: "The Iris Dataset"
categories: articles
tags: [python, machine learning]
comments: true
share: true
---
Learning How to Classify with Real-world Examples
---

having machine learn how to perform task based on examples of each species so that it can classify images where the species are not marked.

It's called **classification** or **supervised learning**

接下来我们将要看看几个使用简单算法的小型的数据表。希望它能够帮助你了解**classification**的基本原理。

## The Iris dataset

The Iris dataset是一个来自1930年代的经典数据表，也是第一批统计分类（statistical classification）中的现代例子。

这个dataset是由Iris flowers组成，它们有好几种种类，可以有它们的形态（morphology）来进行分辨。虽然在今天这些花的种类由基因特征来分类，但是在1930s的时候，DNA还没被发现为是基因的携带者。

每种花都有以下的特征（measurement）：
 * Sepal length
 * Sepal width
 * Petal length
 * Petal width

总的来说，我们把数据中的任何measurement都叫做**features**

我们现在的问题是，如果我们看到一株新的花，我们能够通过它的特征来预测它的种类吗？

这就是一个**classification** or **suprevised learning** problem

## The First Stpe is visualization

{% highlight python %}
from matplotlib import pyplot as plt
from sklearn.datasets import load_iris
import numpy as np

data = load_iris()
features = data['data']
feature_names = data['feature_names']
target = data['target']
target_names = data['target_names']
plt.figure(figsize=(5,6))
plt.xlabel(feature_names[0])
plt.ylabel(feature_names[1])

for t, marker, c in zip(xrange(3), '>ox','rgb'):
    # We plot each class on its own to get different colored markers
    # features[target == 1, 0]可以把target == 1的特征取出来，后面的0可以把第一个特征取出来，也就是sepal length (cm)
    plt.scatter(features[target == t, 0],
                features[target == t, 1],
                marker = marker,
                c=c)
plt.legend(target_names,
           scatterpoints=1)

plt.figure(figsize=(5,6))
plt.xlabel(feature_names[0])
plt.ylabel(feature_names[2])
for t, marker, c in zip(xrange(3), '>ox','rgb'):
    plt.scatter(features[target == t, 0],
                features[target == t, 2],
                marker = marker,
                c=c)
plt.legend(target_names,
           scatterpoints=1,
           loc='upper left')
{% endhighlight %}

![enter image description here](http://screenshot.net/lzlwf91.jpg)
![enter image description here](http://screenshot.net/d7lxcy3.jpg)

## Building our first classification model

如果我们的目标是去分开三种类型的花，我们已经可以提出一些建议了。比如说，光是petal length就可以把Setosa和其它的两种花分开。我们可以来找一下cutoff在哪里

{% highlight python %}
plength = features[:,2] #把每一行的第三个数取出来, 这就是petal length

max_setosa = plength[target == 0].max()
min_non_setosa = plength[target != 0].min()
print ('Maximum of setosa :%.1f' % max_setosa)
print ('Minimum of others :%.1f' % min_non_setosa)
{% endhighlight %}

```Maximum of setosa :1.9```
```Minimum of others :3.0```

这样就变得很清晰，凡是Setos这个花种，它的petal length花瓣长度最大不过1.9，而其它的花种，花瓣长度最小不过3.0

我们可以建立一个这样的模型，如果花瓣长度小于2，那就是一个Iris Setosa flower,否则的话，那就是Virginica or Versicolor

## 以上这就是我们的第一个简单的模型了

但我们不能为Virginica和Versicolor找到一个threshold.我们甚至可以看出从来都没有完美的separation出现。但我们可以试着用一些计算来试试看。

我们应该把setosa 和 viriginica,versicolor分开，以此找出virginica和versicolor的分界点 

{% highlight python %}
non_setosa_features = features[target != 0]
labels = []
for n in target:
    if n == 0:
        labels = np.append(labels,['setosa'])
    elif n == 1:
        labels = np.append(labels, ['versicolor'])
    else:
        labels = np.append(labels, ['virginica'])

is_setosa = (labels == 'setosa')

non_setosa_features = features[~is_setosa]
non_setosa_labels = labels[~is_setosa]
virginica = (non_setosa_labels == 'virginica')

best_acc = -1.0
for fi in xrange(non_setosa_features.shape[1]):
    # We are going to generate all possible thershold for this feature
    thresh = non_setosa_features[:,fi].copy()
    thresh.sort()
    # Now test all threshholds
    for t in thresh:
        # pred中包含了对于所有thresholds的检验后的结果
        pred = (non_setosa_features[:,fi] > t)
        acc = (pred == virginica).mean()
        if acc > best_acc:
            best_acc = acc
            best_fi = fi
            best_t = t 
  
print best_acc
print best_fi
print best_t
{% endhighlight %}
```
0.94
3
1.6
```
{% highlight python %}
plt.figure(figsize=(5,6))
plt.xlabel(feature_names[3])
plt.ylabel(feature_names[0])
for t, marker, c in zip(xrange(2), 'ox','rb'):
    plt.scatter(non_setosa_features[target == t, 3],
                non_setosa_features[target == t, 0],
                marker = marker,
                c=c)
plt.legend(target_names,
           scatterpoints=1,
           loc='upper left')

plt.axvline(best_t, c = 'black', lw = 1.5, ls = '--')
{% endhighlight %}

![enter image description here](http://screenshot.net/rkgwtgr.jpg)

## Evaluations - holding out data and cross-validation

虽然我们通过这样的方法最后还是得到了用来区别的threshold 

我们有了一个大概94%的accuracy，但是这有点用原因去找结果证明原因的感觉。我们想要用threshold来鉴定新的instance。

所以现在我们Hold out一部分实例，留下一部分实例。

我们把留下的数据用来做traning，而hold out用来作为testing。

在书上面的结果是

tranning error was 96.0%

Testing error was 90.0% (N=50)

通过这个例子我们可以总结一下：
 > The error on the training data is called a **training error** and is always an overly optimistic estimate of how well your algorithm is doing. We should always measure and report the **testing error**; the error on a collection of examples that were not used for training
 
但目前的问题是，我们只用了部分的data来做training，只用了部分的data来做testing。给training的data变多，给testing的data就要变少，理想情况下，我们希望能够把所有的data都用来training，所有的data都用来testing。

我们可以通过**cross-validation**的方式去做到。一个极致**cross-validation**的例子就是leave-one-out。就是把一个example从traning data中拿出去，然后得出model，然后看这个Model对拿出去的example适不适用。

这样做的坏处，我们被迫要对每一个data都这样做一次，也就是要做100次的training and testing

所以我们可以选择用x-fold-cross-validation，比如说这里我们用five-fold-cross-validation。也就是我们把数据分成五组，然后再对五组数据进行leave-one-out

如下图:

![enter image description here](http://screenshot.net/8jq9cjp.jpg)

但是这样做就要注意，要让每个fold里面的数据较为平衡，不能所有的数据都来自一种花类，那样得出的结果就不够可靠了。

> Although it was not properly recognized when machine learning was starting out, nowadays it is seen as a very bad sign to even discuss the training error of a classification system. This is because the results can be very misleading. We always want to measure and compare either the error on a held-out dataset or the error estimated using a cross-validation schedule


最后随便写一点东西吧，Final Weeks于12.18已经结束了，休息两天之后我再次开始了to be quant的路程，今天和家里人聊了聊，读master这件事还是要看，但是我心里明确的一点是如果不能读到MFE，我也不想读其他的什么项目了，老老实实工作也很好。
总之，我会尽我全力去申请，我希望回国之后我能说我把所有可以使用的时间都放在了如何学习成为一名quant上面。

然而，现在真是越来越觉得时间真是太短了，太多的东西要学，我到底能不能在这些时间里做完这些事情？
如果真的成功申请到了，费用又是不小的问题，想想爸妈积攒这么多年的钱我就这么花了，我真是不孝。

我能不能成功赚回来，真的就要看自己了，爸妈我会加油的。

但不管怎么样，我至少明白了一些事情。

话说回来，最近用了下google analytics，发现并没什么人看我的博客，到目前为止除了我自己就几个访问量吧，不过也无所谓，这里本来就是我记录我自己的学习过程的地方。
winter break的计划的话，继续学习python, time series，顺便开展一下我的Project吧。

I believe in working fucking hard and get paid. I'm a Chinese and I'm proud of the way I live my life. 

Don't dream it. Be it.

Fuck the rest.

