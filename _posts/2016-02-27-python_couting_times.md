---
layout: post
title: "Python统计list中重复次数Bonus"
excerpt: "朝花夕拾？"
categories: articles
tags: [python]
comments: true
share: true
---

今天居然发现自己的博客居然会被别人看到，写过的一个关于Counting the times of a number in python list的算法居然能够帮助到远在波士顿的同学。也因为这个，明明还要去复习时序的我再次打开当时写下的算法，仔细看看发现当时的自己还是太幼稚了。于是决定重新写一下这个算法。

现在的我，遇到了这个问题，我想应该会用Recursion来写这个算法，就是当时记不起来的想法。

{% highlight python %}
def count_times_recursion(lst, x):
	"""
	This function take in a list(lst) and count how many times x shows up in this lst.
	"""
	if x not in lst:
		return 0
	else:
		lst.remove(x)
		return 1 + count_times_recursion(lst, x)
{% endhighlight %}

非常简单的一个使用recursion的算法。但是这样和当时的问题要求感觉好像不太一样了。当时的问题要求我得到一个dict，其中应该显示elem和elem显示的次数。

那么我会这样来写。

{% highlight python %}
def create_times_dict(lst):
	return {key: count_times_recursion(k) for key in lst}
{% endhighlight %}

Pretty clean! 

BUT THERE WAS A PROBLEM!

我突然意识到这样写会出现一个奇怪的结果，首先我在```count_times_recursion```中使用了```lst.remove(x)```,这是一件不是很好的事情，这代表着我在mutate my list。所以之后在建造dict的过程中可能会出现上次关于这个算法博客中的我提到的一个现象，也就是

{% highlight python %}
l = [1,2,3,4]
for n in l:
    l.remove(n)
    print l
{% endhighlight %}

也就是说1会跳到3，而不经过2，具体原因是因为for creates an iterator that is not consistent with the list. 可以认为，for创造了[1,2,3,4]，然后用这些indices去取出list中的数，所以当list remove了1，成为了[2,3,4]之后, indices并没有改变，因此取出了3.

所以到底应该如何解决这个问题呢？非常直观的想法是建造一个与remove相同功能的方法，只不过是返回一个新的lst。

所以这个函数应该创建一个新的list, 并在遇到第一个要删除的时候跳过它，然后在之后的遍历过程不用跳过它。我们可以用一个开关来控制我们的循环过程。

完整代码如下：

{% highlight python %}
def remove_elem(lst, x):
	new_lst = []
	ONCE = False
	for n in lst:
		if n == x and not ONCE:
			ONCE = True
		else:
			new_lst += [n]

	return new_lst

def count_times_recursion(lst, x):
	"""
	This function should coutn the times x show up in lst
	"""

	if x not in lst:
		return 0
	else:
		lst = remove_elem(lst, x)
		return 1 + count_times_recursion(lst, x)


def create_times_dict(lst):
	return {key: count_times_recursion(lst, key) for key in lst}
{% endhighlight %}

那么感兴趣的还是这个算法的效率如何？我们来比较一下好了。按道理...recursion应该比iteration更慢吧...

结果出来很失望...但是仔细想想也十分make sense... 

想象一个[1,1,1,1,1]的list，第一次remove需要1个Operation，第二次还是1个Opertaion...一共是n，然后还要乘上recursion的遍历，所以这整个算法应该是O(n^2)？ I'm not so sure...

结果如下

{% highlight python%}
the time of iterating 1e6 times using old version count times function is 3.399412

the time of iterating 1e6 times using new recursion count times function is 21.370472
{% endhighlight %}

但是结果告诉我并不是O(n^2)而是O(n)...下面是recursive_version调用不同次数花费时间的plot

![enter image description here](http://screenshot.net/owowdsw.jpg)

cs61a 教给我的recursively thinking这么辣鸡么... recursive version比old version慢了一个数量级...

Anyway, 应该去复习时序了！


----
28, Feb update

更新一下，recursion比iteration慢的原因是因为每次都要新开一个frame, the overheads of function calls.

而且时间复杂度也应该用不同长度的list来看，而不是重复的次数...=.=

用不同长度list产生出来的时间plot

![enter image description here](http://screenshot.net/nkyk5fp.jpg)

比较清晰的O(N^2)

---
19, March update

Finally, cs61a gives me the answer. I should use set to do this, which is the idea of hashing to count. I thought about this but I didn't realize I can do it this way.

Anyway, finally I found a way to create a dictionary, which is even better using the build-in function [].count(). A little more faster.

Here we go,

{% highlight python%}
def count_times_w_set(lst):
	unique = {n for n in lst}
	dic = {elem : 0 for elem in unique} 

	for elem in lst:
		if elem in dic:
			dic[elem] += 1
	return dic
{% endhighlight %}

{% highlight python%}
def count_times_inside_dic(lst):
	return {k : lst.count(k) for k in lst}
{% endhighlight %}

I tested it many times using timeit in Python. Using set is always a little bit faster than using lst.count()

{% highlight python%}
the time of iterating 1e6 times using count_times_w_set is 2.416925
the time of iterating 1e6 times using count_times_inside_dic is 2.100947
{% endhighlight %}

The Order of Growth of count_times_w_set is $$O(n)$$.

