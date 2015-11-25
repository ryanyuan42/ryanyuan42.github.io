---
layout: post
title: "python统计list中重复次数"
excerpt: "一次算法小尝试."
categories: articles
tags: [python]
image:
  feature: so-simple-sample-image-2.jpg
comments: true
share: true
---
## **统计列表中的数值重复次数**
终于是考完了傻逼的考试，可以再次潜入到python的世界中。
上次说到有个算法，下次可以实现一下。算法的要求是这样的，

> #####有 n 个已排好序的 list 存着 integer ，返回其中出现次数大于 K 的数字，一个 list 中重复的数字只算出现一次，也就是说一个数在 n 个 list 中最多出现 n 次。结果按照出现的次数从小到大排序输出。

我刚开始觉得这个算法比较简单，不就是统计一下重复次数嘛，但是后面发现光说不练都是假的。
借由这个算法我也想到了一些其他的问题。

###如何统计list中的值的重复次数呢？

###在这之前...

刚开始觉得应该用两个循环来实现，第一次用来遍历所有的值，第二遍用来看是否还出现。
后面发现这种想法太天真了，这样只能统计了出现两次的值，当然可以用recursive来实现出现三次的，四次的，但是这样效率就变低了，而且应该是越跑越慢。

结果之后一不小心又遇到了Python list中的遍历删除问题。

####Python遍历删除问题：

这个问题是这样的：

{% highlight python %}
l = [1,2,3,4]
for n in l:
    l.remove(n)
    print l
{% endhighlight %}
这个example里面，我本以为这样会删除掉list中所有的元素，但是并不是这样。在第一次删除n = 1， list 成为[2,3,4]之后，n并没有往下变成2，而是变成了3。
具体原因，我猜应该是Python remove的问题，它可能并没有真正改变list?
我也不清楚了，已经发邮件给教授，希望他能回答我的问题。

但是我想出来解决这个问题的办法，就是这样：
{% highlight python %}
import copy
l = [2,3,3,4,4,6,6,6]
a = copy.copy(l)
for n in a:
    l.remove(n)
    print l
{% endhighlight %}
这样可以复制一个一模一样的list，这样做就可以达到希望把l中元素全部删掉的效果了。

Anyway,回到原来的问题上面。

###如何统计list中的值的重复次数呢？

遇到的问题太多，我已经想不起来最开始自己的想法，该怎么用recursive来写这个函数了。
所以就直接说正确的思路吧

####既然要统计重复的次数，应该先找到不存在重复的list

意思就是如果一个list里面有重复值的话，那应该先消除这个重复，获得一个没有重复的list。
{% highlight python %}
"""
简而言之就是list2是不含有重复的list，那么就先让他对l进行一个个的复制，如果说复制之后发现这个值已经在这里面了，就删掉它
所以list2添加进来的都是重复值中的最后一个，因为之前的都被删掉了。
"""
l = [1, 2, 3, 3, 4, 4, 6, 6, 6]
list2 = []
dic = {}
for n in l:
    if n in list2:
	list2.remove(n)
    list2.append(n)
{% endhighlight %}

那么要实现哪个值，重复了多少次，
显然应该使用dic来进行绑定。
{% highlight python %}

def count_times(l):
"""
This funciont count the repeating times of a value 
首先创建一个dic，它的key是消除重复后的值，**这么做的原因是方便之后用dic来提取，value都设置为0代表目前还未出现过次数**
然后遍历原始存在重复的list，l，中的值，如果在dic的key中，则key对应的value加1
"""
    dic = {}
    for n in list2:
	dic[n] = 0
    
    for n in l:
	count = 0
	if n in dic:
	    dic[n] += 1
    
    return dic
{% endhighlight %}

完整代码：
{% highlight python %}

    def create_no_repeate_list(l):
	"""
	This function create a list withou repetance
	"""
	list2 = []
	for n in l:
		if n in list2:
			list2.remove(n)
		list2.append(n)
	return list2

    def count_times(l):
	"""
	This funciont count the repeating times of a value 
	"""
	dic = {}
	list2 = create_no_repeate_list(l)
	for n in list2:
		dic[n] = 0

	for n in l:
		if n in dic:
			dic[n] += 1

	return dic
{% endhighlight %}

{% highlight python %}
>>> l = [1,2,1,2,2,4,5,5]
>>> print create_no_repeate_list(l)
>>> print count_times(l)
[1, 2, 4, 5]
{1: 2, 2: 3, 4: 1, 5: 2}
{% endhighlight %}

###当然...
还有更好的方法
比如说l.count() =.=
还有一个叫做counter的函数
{% highlight python %}
>>> from collections import Counter
>>> l_dict = Counter(l) 
>>> l = [1,2,1,2,2,4,5,5]
>>> print l_dict
Counter({2: 3, 1: 2, 5: 2, 4: 1})
{% endhighlight %}
返回的是一个应该是Counter实例？但是同样具备dictionary的基本操作。

### 回到最初的算法题
题目说道“一个 list 中重复的数字只算出现一次，也就是说一个数在 n 个 list 中最多出现 n 次”
所以我们可以用到之前的list2，它可以消除掉list中的重复
由于有N个list，那我们也会有N个list2
现在我们要统计N个list中的出现次数，就变得很简单了

完整代码如下：
{% highlight python %}
def no_repeate_list(a):
    no_repeate_list = []
    for n in a:
    	no_repeate_list.append(create_no_repeate_list(n))
    return no_repeate_list
    
    
def count_times_algorithm(a):
    list2 = no_repeate_list(a)
    dic_repeating_times = {}
    	
    #Initialize the dic_repeating_times
    for n in list2:
    	for x in n:
    	    dic_repeating_times[x] = 0
    
    for n in list2:
    	for x in n:
    	    dic_repeating_times[x] += 1 
    
    return dic_repeating_times
{% endhighlight %}

1出现2次，2出现2次，3出现1次，4出现2次，5出现1次，6出现1次

这样应该就解决了题目的要求

###小结一下
算法题真是充满了乐趣，自己算得连晚饭都没吃，自己花了三个小时来写这个算法，以及它的周边出现的问题的探究，不得不说自己的算法技巧还完全不够。
Python当然有很多现成的工具可以使用，但是毕竟是探究嘛，多一些自己的思考总是更好一些。
当然我猜Counter和list.count()可能要比我的方法快很多，当然我只是猜测，我的count_times()的时间复杂度应该是O(n)，所以究竟快多少下次再算算看。

###Bonus
还有就是关于Python的list的remove问题啦~

出乎意料教授回复的很快

> Hi Ryan, The for statement creates an iterator from the original list that becomes inconsistent with the list when the list contents change. This issue is well known; it's even discussed (although without much detail) in the official Python tutorial.
> https://docs.python.org/3/tutorial/controlflow.html#for-statements

下次再看其中的奥妙吧。

----
2015-11-23更新：
使用python自带的timeit()计算算法运算时间
{% highlight python %}
if __name__ == '__main__':
    t2 = timeit.Timer("count_times([1,2,3,3,4,5,5])", setup="from __main__ import count_times, create_no_repeate_list")
    t3 = timeit.Timer("Counter([1,2,3,3,4,5,5])", setup = "from collections import Counter")
    t4 = timeit.Timer("count_times_inside([1,2,3,3,4,5,5])", setup = "from __main__ import count_times_inside")
    print t2.timeit()
    print t3.timeit()
    print t4.timeit()

#结果
5.29143025914
9.22324511063
3.44918713441
{% endhighlight %}

结果是函数运行100万次的时间

我的算法比我想的要快一些，至少比Counter的方法要快，不过想想也没错，毕竟Counter是返回一个Instance

timeit其他的详细方法在http://python.usyiyi.cn/python_278/library/timeit.html上面
