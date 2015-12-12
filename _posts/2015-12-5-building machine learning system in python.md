---
layout: post
title: "building machine learning system in python(0)"
excerpt: "First Machine Learning"
categories: articles
tags: [python, machine learning]
comments: true
share: true
---
##Machine Learning
机器学习的目标就是通过给机器几个例子，教会机器处理任务。

###本系列将交给你什么
这个系列是我学习building machine learning system in python的笔记和过程。它将提供一些广泛的学习算法(learning algorithm)，以及使用它们的时候的注意事项。

我们知道一些机器学习的算法，比如SVM（support vector machines），NNS（nearest neighbor search）。但是事实上，我们大部分的时间都会花在下面：

 1. 阅读我们的数据并且进行清洗
 2.  探索并且理解我们输入的数据
 3.  分析如何最好的把数据呈现给我们的学习算法
 4.  选择正确的模型和学习算法
 5.  正确地评估最后的表现。

###Basic Numpy


```python
import numpy
```


```python
numpy.version.full_version
```
    '1.9.3'
```python
from numpy import *
```


```python
import numpy as np
```


```python
a = np.array([0,1,2,3,4,5])
a
```




    array([0, 1, 2, 3, 4, 5])



narray.ndim gives you the dimension of the array
---


```python
a.ndim 
```


narray.shape gives the tuple of arrary dimensions
----


```python
a.shape
```




    (6,)




```python
t = np.zeros((5,3,4))
t.shape
```




    (5, 3, 4)




```python
a
```




    array([0, 1, 2, 3, 4, 5])



Transform a array to 2D matrix
----


```python
b = a.reshape((3,2))
b
```




    array([[0, 1],
           [2, 3],
           [4, 5]])




```python
b.shape
```




    (3, 2)




```python
b.ndim
```




    2




```python
b[1][0] = 77
b
```




    array([[ 0,  1],
           [77,  3],
           [ 4,  5]])




```python
a
```




    array([ 0,  1, 77,  3,  4,  5])



It shows numpy avoids copies wherever possible
----
only if u use a copy()


```python
c = a.reshape((3,2)).copy()
c
```




    array([[ 0,  1],
           [77,  3],
           [ 4,  5]])




```python
c[0][0]=-99
c
```




    array([[-99,   1],
           [ 77,   3],
           [  4,   5]])




```python
a
```




    array([ 0,  1, 77,  3,  4,  5])



Another advantage of NumPy is that operation are propagated to individual elements
---


```python
a * 2
```




    array([  0,   2, 154,   6,   8,  10])




```python
a ** 2
```




    array([   0,    1, 5929,    9,   16,   25])




```python
[1,2,3,4,5] ** 2
```


    ---------------------------------------------------------------------------

    TypeError                                 Traceback (most recent call last)

    <ipython-input-46-2617e316bd48> in <module>()
    ----> 1 [1,2,3,4,5] ** 2
    

    TypeError: unsupported operand type(s) for ** or pow(): 'list' and 'int'


Indexing
----


```python
a
```




    array([ 0,  1, 77,  3,  4,  5])




```python
a[np.array([2,3,4])]
```




    array([77,  3,  4])




```python
a > 4
```




    array([False, False,  True, False, False,  True], dtype=bool)




```python
a[a>4]
```




    array([77,  5])




```python
a[a>4] = 4
a
```




    array([0, 1, 4, 3, 4, 4])



clip会将所有的数值都缩在指定的区间中


```python
a.clip(0,4)
```




    array([0, 1, 4, 3, 4, 4])




```python
a.clip(0,3)
```




    array([0, 1, 3, 3, 3, 3])



Handling non-existing values
---


```python
c = np.array([1, 2, np.NAN, 3, 4])
c
```




    array([  1.,   2.,  nan,   3.,   4.])




```python
np.isnan(c)
```




    array([False, False,  True, False, False], dtype=bool)




```python
c[~np.isnan(c)]
```




    array([ 1.,  2.,  3.,  4.])




```python
np.mean(c[~np.isnan(c)])
```




    2.5



Comparing Runtime behaviors
----


```python
import timeit
```


```python
normal_py_sec = timeit.timeit('sum(x*x for x in xrange(1000))', number = 10000)
naive_py_sec = timeit.timeit('sum(na*na)', setup="import numpy as np; na=np.arange(1000)", number = 10000)
good_np_sec = timeit.timeit('na.dot(na)', setup = "import numpy as np; na = np.arange(1000)", number = 10000)
```


```python
print normal_py_sec, naive_py_sec, good_np_sec
```

    1.04627522128 2.08086375548 0.0352430844598
    


```python
na = np.arange(1000)
```


```python
t = np.array([1,2,3,4])
```


```python
t.dot([2,3,4,5]) # dot 是narray之间的乘法
```




    40



Learning Scipy
---


```python
import scipy, numpy
```


```python
scipy.dot is numpy.dot
```




    True



Scipy可以提供许多在NumPy的array之间的算法

Our first Machine Learning application
----


```python
import scipy as sp
```


```python
data = sp.genfromtxt("D:\Working Space\Machine Learning\source code\BuildingMachineLearningSystemsWithPython-first_edition\ch01\data\web_traffic.tsv",
                     delimiter = "\t")
```


```python
data.shape
```




    (743, 2)



Preprocessing and cleaning the data
---


```python
x = data[:, 0]
y = data[:, 1]
```


```python
sp.sum(sp.isnan(y))
```




    8



~可以使得所有的布尔值反过来


```python
x = x[~sp.isnan(y)]
y = y[~sp.isnan(y)]
```


```python
import matplotlib.pyplot as plt
```


```python
%matplotlib inline
```


```python
plt.scatter(x,y)
plt.title("Web traffic over the last month")
plt.xlabel("Time")
plt.ylabel("Hits/hour")
plt.xticks([w*7*24 for w in xrange(10)], ['week %i'%w for w in range(10)])
plt.autoscale(tight=True)
plt.grid()
```


![png](http://screenshot.net/qgk1jtk.jpg)


Choosing the right model and learning algorithm
----
现在我们已经有了一个对数据明确的印象了，接下来我们要做的是
 * 找到数据背后的模型
 * 用这个模型去找到将来这个web server需要拓展的时候，也就是突破我们的limit of 10000 per hour

## 在搭建我们的一个模型之前
每当我们建立模型的时候，总会出现一些approximation error。这样的误差会指导我们选择正确的模型。这里我们用squarred distance to real data来描述这个误差。


```python
def error(f, x, y):
    return sp.sum((f(x) - y)**2)
```

### 假设模型是一根直线
我们假设模型是一根直线的话，唯一的挑战就是如何用直线得到最小的误差。
Scipy有个函数`polyfit()`可以帮助我们做这件事


```python
fp1, residuals, rank, sv, rcond = sp.polyfit(x, y, 1, full = True)
```

这里我们用`full = True`得到了一些其他的额外信息，一般我们只需要斜率和截距, fp1中包含了我们的直线的参数


```python
fp1
```




    array([   2.59619213,  989.02487106])



所以我们的直线方程是 f(x) = 2.59619213 * x + 989.02487106

我们用`poly1d()`来建立一个模型方程


```python
f1 = sp.poly1d(fp1)
```


```python
error(f1, x, y)
```




    317389767.33977801




{% highlight python %}
fx = sp.linspace(0, x[-1], 1000) 
plt.scatter(x,y)
plt.title("Web traffic over the last month")
plt.xlabel("Time")
plt.ylabel("Hits/hour")
plt.xticks([w*7*24 for w in xrange(10)], ['week %i'%w for w in range(10)])
plt.autoscale(tight=True)
plt.grid()
plt.plot(fx, f1(fx), 'b',linewidth = 2)
plt.legend(["d=%i"% f1.order], loc = "upper left")
{% endhighlight %}




    <matplotlib.legend.Legend at 0x10cc7f70>




![png](http://screenshot.net/25o2dhj.jpg)


接下来用点复杂的
---


{% highlight python %}
f2p = sp.polyfit(x, y, 2)
print f2p
{% endhighlight %}

    [  1.05322215e-02  -5.26545650e+00   1.97476082e+03]
    


{% highlight python %}
f2 = sp.poly1d(f2p)

print error(f2, x, y)
{% endhighlight %}

    179983507.878
    

我们的这里函数是 f(x) = 0.0105322215 \* x \*\* 2 - 5.26545650 * x + 1974.76082


{% highlight python %}
fx = sp.linspace(0, x[-1], 1000) 
plt.figure(figsize=[8,6])
plt.scatter(x,y,s=8,alpha=0.6)
plt.title("Web traffic over the last month")
plt.xlabel("Time")
plt.ylabel("Hits/hour")
plt.xticks([w*7*24 for w in xrange(10)], ['week %i'%w for w in range(10)])
plt.autoscale(tight=True)
plt.grid()
plt.plot(fx, f1(fx), 'b',linewidth = 2)
plt.plot(fx, f2(fx), 'g', linewidth = 2)
plt.legend(["d=%i"% f1.order], loc = "upper left")
{% endhighlight %}




    <matplotlib.legend.Legend at 0x10b28b50>




![png](http://screenshot.net/qvj1yun.jpg)



{% highlight python %}
f3p = sp.polyfit(x,y,3)
{% endhighlight %}


{% highlight python %}
f3 = sp.poly1d(f3p)
print error(f3, x, y)
{% endhighlight %}

    139350144.032
    


{% highlight python %}
f100p = sp.polyfit(x,y,100)
f100 = sp.poly1d(f100p)
{% endhighlight %}




{% highlight python %}
fx = sp.linspace(0, x[-1], 1000) 
plt.figure(figsize=[8,6])
plt.scatter(x,y,s=8,alpha=0.6)
plt.title("Web traffic over the last month")
plt.xlabel("Time")
plt.ylabel("Hits/hour")
plt.xticks([w*7*24 for w in xrange(10)], ['week %i'%w for w in range(10)])
plt.autoscale(tight=True)
plt.grid()
plt.plot(fx, f1(fx), 'b',linewidth = 2)
plt.plot(fx, f2(fx), 'g', linewidth = 2)
plt.plot(fx, f3(fx), color = '#94a0b6', linewidth = 2, linestyle = '--')
plt.plot(fx, f100(fx), color = 'r', linewidth = 2)
plt.legend(["d=%i"% f1.order], loc = "upper left")
plt.legend(["d=%i"% f2.order], loc ="upper left")
{% endhighlight %}




    <matplotlib.legend.Legend at 0x11e63990>




![png](http://screenshot.net/ynd0gfq.jpg)


然而当我们仔细看这幅图的时候，我们会开始想，这个模型真的表示了数据产生的过程吗？

我们发现我们提供的d越大，比如100，越来越容易出现摆动的表现，看起来，我们的模型好像有点太fit我们的data了。

也就是说它不仅捕捉到了trend，同时也捕捉到了noise。这样的表现我们称作overfitting

####到了现在我们有几个选择：
* 选择其中一个多项式模型
* 换成另外一种更复杂的模型.
* 重新考虑我们的data，然后重新开始。
