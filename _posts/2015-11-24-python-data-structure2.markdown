---
layout: post
title: "python data structure(1)"
excerpt: "Searching"
categories: articles
tags: [python, data structure, Hash]
comments: true
share: true
---
##Searching
一个搜索结果一般会对一个东西有没有出现这个问题，回答`True` or `False`两者其一。当然有时候也会要你返回这个东西是在哪里找到的。

在CS的世界中，有太多不同搜索的方法，现在要看看这些算法是如何工作的，以及它们相比起来如何。

###The Sequential Searching
pythong中有list可以用来存储数据，当数据被这样存储的时候，我们说他们有线性或是序列的关系。每一个数据都存储在一个位置，这个位置则是相当于其它数据确定的。既然我们有它们的index，那么我们就可以去访问它。这样第一种搜索方法就成立了，**The Sequential Searching**。

工作方式如下：

![Figure 1: Sequential Search of a List of Integers](http://interactivepython.org/courselib/static/pythonds/_images/seqsearch.png)

那么它的时间复杂度很明显就是$O(n)$，因为要遍历list中所有的值

| case            | Best Case     | Worst Case  | Average Case|
| -------------   |:-------------:| :----------:|:------------:|
| item present    |    1          |     $$n$$   |    $$n/2$$  |
| item not present|  $$n$$        |      $$n$$  | $$n$$       |

###The Binary Searching
这种搜索方法利用了排好序的list的特性。Binary Search将从中间开始搜索，如果item是我们在找的，那搜索过程结束，如果不是，我们可以利用排好序的特点，比较大小，排除掉一半的数量。
{% highlight python%}
def binarySearch(alist, item):
	    first = 0
	    last = len(alist)-1
	    found = False
	
	while first<=last and not found:
		midpoint = (first + last)//2
		if alist[midpoint] == item:
			found = True
	    else:
			if item < alist[midpoint]:
				last = midpoint-1
			else:
				first = midpoint+1
	return found
{% endhighlight %}

这个方法的原理就是先从中间开始比较，比较之后就把list切为一半，在剩下的一半中再去寻找。也就是不断把list分为一半的过程。

####过程分析
* 第一次分裂，剩下有$$n/2$$个值
* 第二次分裂，剩下有$$n/4$$个值
* ...

假设最糟的情况，我们需要一直分裂list，直到这个list只有一个值为止。那么可见$$n/2^i = 1$$，我们就可以得到$$i = log(n)$$ 所以时间复杂度是$$O(log(n))$$.

###Hashing 哈希表

到目前为止，我们实现了从$O(n)$到$O(logn)$的改进，接下来我们将通过Hash table来实现$$O(1)$$。

在哈希表中的每一个位置，我们把它叫做一个slot，这个slot可以装下一个值，并且以整数命名，从0开始。也就是说我们可以有叫做0的slot，叫做1的slot，叫做2的slot.... 在初始状态下面，Hash Table是没有装任何值的，所以每个slot都是空的。下面就是一个size为11的哈希表，它有11个slot，分别命名为0,1,2...

![Hash table](http://interactivepython.org/courselib/static/pythonds/_images/hashtable.png)

####Hash Function 哈希函数
[Hash Table_Wiki](https://zh.wikipedia.org/wiki/%E5%93%88%E5%B8%8C%E8%A1%A8#.E5.A4.84.E7.90.86.E7.A2.B0.E6.92.9E)
![enter image description here](http://hujiaweibujidao.github.io/images/hashbasics.png)

哈希函数创造了一种映射关系，将储存的item作为输入，然后输出0到m-1的值，也就代表了我们的slot。

#####常见的哈希函数：
* Remainder function, 取余数
也就是把储存的item，除以哈希表的size，返回余数。拿之前size=11的例子来说，假如item是54,26,93,17,77,31，那就应该那他们除以11，得到余数。

Table 4: Simple Hash Function Using Remainders
|   Item      |     Hash Value|
|:-----------:|:-------------:|
|54	          |        10     |
|26           |      	4       |
|93|	5|
|17|	6|
|77|	0|
|31|	9|

一旦我们有了Hash Value，我们就可以把这些items存放进Hash Table了。
![Figure 5: Hash Table with Six Items](http://interactivepython.org/courselib/static/pythonds/_images/hashtable2.png)

完成了这项工作之后，我们如果再想搜索一个值，我们就可以把它代进之前的Hash Function，比如说77，我们就代入哈希函数，得到0，也就是它的存放位置了。

这样一来，搜索的时间复杂度就是$O(1)$了

但是Hash Tabel也容易遇到问题，比如说可能出现**冲突**（collision）。比如说如果44存放在slot 1的话，44使用hash function得到的Hash value依旧是0，也就和原来的77冲突了。

###哈希函数
可见，好的哈希函数应该只会产生独一的值。（比如说$$y=sinx$$这个函数，不同的$$x$$可以得到同样的$$y$$，而对于$$y=2^x$$这样的函数，每个$$x$$都有唯一$$y$$与之对应）

####Folding method：分组求和再取余数
将原数字串分成几部分，然后求和再取余数。比如说我要存进一个电话号码，510-944-2288，我可以对它进行这样的处理，$$(51,09,44,22,88)$$，$$51+09+44+22+88=214$$。假设还是size为11的哈希表，$$214\%11 = 5$$。所以我们应该把它存在slot 5里

####Mid-squre method：平方值的中间两位数取余数
比如$$44^2=1,936$$，取出来中间两位的93，然后$$93\%11=5$$

####ord函数
对于字符串，可以用ord函数来把字符串转换为数值，Python中`ord`可以把返回unicode point，比如`ord('a') = 97`。

![enter image description here](http://interactivepython.org/courselib/static/pythonds/_images/stringhash.png)

代码实现起来是这样：
{% highlight python %}
def hash(astring, tablesize):
    sum = 0
    for pos in range(len(astring)):
        sum = sum + ord(astring[pos])

    return sum%tablesize
{% endhighlight %}

但是这样做的话，我们就会发现其实如果把string中的字母顺序打乱，我们得到的值还是一样的。cat和tac都会得到4。

为了防止这样的情况发生，我们可以在Hash function中对字母进行加权处理。
![enter image description here](http://interactivepython.org/courselib/static/pythonds/_images/stringhash2.png)
99*1 + 97*2 + 116*3 = 641， 641%11 = 3

####总结
Hash function一定要足够效率，简单，不然的话还不如使用binary或者是sequential。

###Collision Resolution 解决冲突
哈希表中冲突时难免的，所以每当不同的item挤进了一个slot里面的时候，我们必须要定下一个方法来解决这个冲突，腾出一个位置。

####Open Addressing 开放寻址	
* **Linear probing**(线性探测)，就是说每发生一个collision的时候，我们就沿着slot，往下一个个的找开放的位置，一旦找到了，就把这个值放进去。
比如说我们之前的使用的remainder method例子，(54,26,93,17,77,31,44,55,20)， 当存进44的时候，发生了collision，使用线性探测，44被存进了slot 1中。
![enter image description here](http://interactivepython.org/courselib/static/pythonds/_images/linearprobing1.png)
* 这样做的缺点是，容易造成聚集现象（cluster）。比如说我们寻找20的时候，Hash value是9，而9存放的是31，但是我们不能返回`False`,因为我们清楚可能会有collision的出现。所以我们被迫进行一个sequential search，从slot 10开始，直至找到20。
![A Cluster of Items for Slot 0](http://interactivepython.org/courselib/static/pythonds/_images/clustering.png) *A Cluster of Items for Slot 0*
* 一种解决聚集现象的方法是跳跃式地查找下一个空位(plus n)
![enter image description here](http://interactivepython.org/courselib/static/pythonds/_images/linearprobing2.png)
* 在出现了collision之后，寻找另一个slot的过程称为rehashing。用线性搜寻的话rehash function就是$$newhashvalue=rehash(oldhashvalue)$$，其中$$rehash(pos)=(pos+skip)%sizeoftable$$
* quadratic probing(平方探测)，遇到了collision之后，一开始的hash value是h，接下去就探测h+1, h+4, h+9, h+16...是不是空位。
![Figure5.11](http://hujiaweibujidao.github.io/images/quadraticprob.png)
* chain(利用链表连接)
 ![chain](http://hujiaweibujidao.github.io/images/chain.png)

##代码实现

还记得python中的dictionary，这种数据结构就是key和value之间的一一对应关系。我们把这种想法称作为map。
####一个map应该有以下的操作：
* Map()
* put(key, val)
* get(key)
* del
* len()
* in, return True or False

接下来我们将使用两个List来建立一个哈希表，一个list是slots，另一个叫做data

我们这里采用simple remainder method，collision resolution则采用线性探测，"plus 1"的rehash function。

![Analysis of Hashing](http://hujiaweibujidao.github.io/images/hashanalysis.png)

> 散列表的载荷因子定义为：α = 填入表中的元素个数 / 散列表的长度
> 载荷因子即load factor，如果α很小，那发生collision的概率也会比较小，而α很大，那发生collision的概率就会很大
> α是散列表装满程度的标志因子。由于表长是定值，α与“填入表中的元素个数”成正比，所以，α越大，表明填入表中的元素越多，产生冲突的可能性就越大；反之，α越小，标明填入表中的元素越少，产生冲突的可能性就越小。实际上，散列表的平均查找长度是载荷因子α的函数，只是不同处理冲突的方法有不同的函数。
> 对于开放定址法，荷载因子是特别重要因素，应严格限制在0.7-0.8以下。超过0.8，查表时的CPU缓存不命中（cache missing）按照指数曲线上升。因此，一些采用开放定址法的hash库，如Java的系统库限制了荷载因子为0.75，超过此值将resize散列表。

Hash Table代码如下（size = 11, remainder method, simple linear plus 1 probing）

{% highlight python%}
# -*- coding: utf-8 -*-
"""
Python Data Structure
"""
class HashTable:
	"""HashTable"""
	def __init__(self):
		self.size = 11
		self.slots = [None] * self.size
		self.data = [None] * self.size

	def hashfunction(self, key, size):
		return key%size

	def rehash(self, oldhash, size):
		return (oldhash+1)%size
	
	def put(self, key, data):
		hashvalue = self.hashfunction(key, len(self.slots))

		if self.slots[hashvalue] == None:
			self.slots[hashvalue] = key
			self.data[hashvalue] = data
            
		else:
			# already exsit this key
			if self.slots[hashvalue] == key:
				self.data[hashvalue] = data # replace
			# collision occurs
			else:
				nextslot = self.rehash(hashvalue, len(self.slots))
				while self.slots[nextslot] != None and self.slots[nextslot] != key:
					nextslot = self.rehash(nextslot, len(self.slots))

				if self.slots[nextslot] == None:
					self.slots[nextslot] = key
					self.data[nextslot] = data

				elif self.slots[nextslot] == key:
					self.data[nextslot] = data  #replace



	def get(self, key):
		startslot = self.hashfunction(key, len(self.slots))

		data = None
		stop = False
		found = False
		pos = startslot
		while self.slots[pos] != None and not found and not stop:
			if self.slots[pos] == key:
				found = True
				data = self.data[pos]
			else:
				pos = self.rehash(pos, len(self.slots))
				if pos == startslot:
					stop = True

		return data

	def __getitem__(self, key):
		return self.get(key)

	def __setitem__(self, key, data):
		if None not in self.slots and key not in self.slots:
			self.size += 1
			self.slots = self.slots + [None]
			self.data = self.data + [None]
			return self.put(key, data)
		else:
			return self.put(key, data)

{% endhighlight %}

在最后的 \__setitem__函数中加上了一个动态增加slots的功能，以防止slots沾满，添加不下新的Key而导致无限循环的错误。

但事实上应该在载荷因子0.75左右的时候就扩容，以防止之后需要的比较（comparison）大幅上升。

*Nov 24, 2015*

Happy Thanksgiving!
