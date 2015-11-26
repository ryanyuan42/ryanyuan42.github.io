---
layout: post
title: "python data structure(2)"
excerpt: "Sorting"
categories: articles
tags: [python, data structure, Sorting]
comments: true
share: true
---
Sorting
-----
CS中的世界中有许多许多的排序算法，今天就让我们来一探究竟。

在进入算法之前，我们应该先考虑可以用来分析一个排序过程的操作。

 1. 首先，一定要可以比较两个值的大小，另外，要能够排序一个集合，我们应该有一个系统的方法来比较值，以此看他们是不是没有顺序。比较的次数，就像在之前的Searching之中，应该是一个衡量排序算法比较好的方法。
 2. 其次，如果值不在正确的位置上的时候，我们应该可以去交换它们，但交换会耗费许多资源，所以交换的次数也应该考虑到衡量算法的好坏之中。

###The Bubble Sort 冒泡法
冒泡法其实就是比较相邻的值的大小，然后交换那些不在顺序中的值，比如说，54与26比较，这不是我们期望的顺序，我们交换这两个值；接着54和93比较，这是我们期望的顺序，我们不交换这两个值，以此类推。
![bubble sort](http://interactivepython.org/courselib/static/pythonds/_images/bubblepass.png)

如果list中有n个值，那么在第一次传递中，就有n-1对需要比较。

当第二次传递开始时，最大值93已经在应该的位置上了，所以只要对list中剩下的值进行冒泡就可以了，所以第二次传递会有n-2次的比较。一共会有n-1次的传递。

代码实现起来是这样：
{% highlight python %}
def bubbleSort(alist):
	for passnum in range(len(alist)-1, 0, -1):
		for n in range(passnum):
			if alist[n] > alist[n+1]:
				temp = alist[n]
				alist[n] = alist[n+1]
				alist[n+1] = temp

	return alist
{% endhighlight %}

但是这样做有点太浪费了。比如说如果在某一个传递过程中没有任何交换，那证明这个list已经是排序好了的。
比如说[1,2,3,5,4]
按照之前的原理，它应该进行4次的传递过程，虽然每一次都会有比较发生，但是不是每一次都有交换。

比如第一次过后，list变成了[1,2,3,4,5]，那么第二次会去sort [1,2,3,4]的序列，结果发现没有exchange发生，那么我们停止之后的传递过程。

这样的做法被称作是**short_bubble_sort**

代码实现如下：
{% highlight python %}
def short_bubbleSort(alist):
	exchange = True
	passnum = len(alist) - 1 # max = n - 1
	while passnum > 0 and exchange:
		exchange = False
		for n in range(passnum):
			if alist[n] > alist[n+1]:
				exchange = True
				temp = alist[n]
				alist[n] = alist[n+1]
				alist[n+1] = temp
		passnum -= 1

	return alist
{% endhighlight %}

我们来分析一下Bubble Sort的效率，之前分析到，第一次传递有n-1次比较，第二次有n-2次比较，....第n-1次有1次比较。

所以Total comparison = $$\frac{n(n-1)}{2}$$可见这个算法的时间复杂度为$$O(n^2)$$

###The Selection Sort  选择排序
选择排序就是每一次传递过程，只做一次交换。
原理就是先找到这次传递过程中的最大值，然后把它放到正确的位置上面。这依旧是需要n-1次传递。
![selection sort](http://interactivepython.org/courselib/static/pythonds/_images/selectionsortnew.png)

代码实现如下：
{% highlight python %}
def selectionSort(alist):
	passnum = len(alist) - 1
	while passnum > 0:
		maxium = max(alist[:(passnum+1)])
		maxium_pos = alist.index(maxium)

		temp = alist[maxium_pos]
		alist[maxium_pos] = alist[passnum]
		alist[passnum] = temp

		passnum -= 1
	
	return alist
{% endhighlight %}

Selection的时间复杂度依旧是$$O(n^2)$$。但是由于交换次数的减少，选择排序法一般要快一点。

###The Insertion Sort 插入排序
每次都假设前面的部分是排好序的，然后每次把数值插入到排好序的那部分中去。
![Insertion Sort](http://interactivepython.org/courselib/static/pythonds/_images/insertionsort.png)

![details](http://interactivepython.org/courselib/static/pythonds/_images/insertionpass.png)

代码实现如下：
{% highlight python %}
def insertionSort(alist):
	for index in range(1, len(alist)):

		currentvalue = alist[index]
		pos = index

		while pos > 0 and alist[pos-1] > currentvalue:
			alist[pos] = alist[pos - 1]
			pos -= 1

		alist[pos] = currentvalue

	return alist
{% endhighlight %}

即使这样我们依旧需要n-1次的传递来排序，最大的比较次数依旧是n-1之和。所以它的时间复杂度还是$$O(n^2)$$

值得注意的是，Insertion采用不是exchange的方法，而是shifting，即先比较大小，小则让大的数字向后挪，直到找到比currentvalue小的数字为止。

    

> One note about shifting versus exchanging is also important. In general, a shift operation requires approximately a third of the processing work of an exchange since only one assignment is performed. In benchmark studies, insertion sort will show very good performance.


###The Shell Sort
![enter image description here](http://interactivepython.org/courselib/static/pythonds/_images/shellsortA.png)
Figure 6: A Shell Sort with Increments of Three

![enter image description here](http://interactivepython.org/courselib/static/pythonds/_images/shellsortB.png)
Figure 7: A Shell Sort after Sorting Each Sublist

看图应该很好理解，就是把原本是9个数的list，分成3个sublist，然后去sort这三个sublist，最后整合为一个list。然后再对这个list使用标准的insertion sort

![enter image description here](http://interactivepython.org/courselib/static/pythonds/_images/shellsortC.png)
Figure 8: ShellSort: A Final Insertion Sort with Increment of 1

下面这段代码用的一开始是$n/2$个sublist，接着是$n/4$...
其实当increments = 1的时候，也就是一个insertion sort了，但是由于之前的排序，元素已经差不多排好了位置，最后一次的insertion sort所需要的shift也就变少了。

所以总结下来，ShellSort就相当于insertionSort的积分。
代码实现如下：
{% highlight python %}
def shellSort(alist):
    sublistcount = len(alist)//2
    while sublistcount > 0:

      for startposition in range(sublistcount):
        gapInsertionSort(alist,startposition,sublistcount)

      print("After increments of size",sublistcount,
                                   "The list is",alist)

      sublistcount = sublistcount // 2

def gapInsertionSort(alist,start,gap):
    for i in range(start+gap,len(alist),gap):

        currentvalue = alist[i]
        position = i

        while position>=gap and alist[position-gap]>currentvalue:
            alist[position]=alist[position-gap]
            position = position-gap

        alist[position]=currentvalue
{% endhighlight %}   

值得一提的是这样做会使得时间复杂度落在$$O(n)$$和$$O(n^2)$$之间
###Merge Sort
![enter image description here](http://interactivepython.org/courselib/static/pythonds/_images/mergesortA.png)
Figure 10: Splitting the List in a Merge Sort
![enter image description here](http://interactivepython.org/courselib/static/pythonds/_images/mergesortB.png)
Figure 11: Lists as They Are Merged Together

很明显的分治策略，但是自己用代码实现只能想到spilit，不知道该如何merge起来，或者说自己用来merge的方法太丑陋了。下面的算法真是精彩，通过直接的recursive，并且在后面加判断代码。

也就是说，刚开始会先把list都spilit成1的小list，然后由于之前已经建立那些mergeSort的frame，他们会在收集到排好序的小list之后，再对take in 的参数alist产生影响，就相当于merge了一样！

{% highlight python %}
def mergeSort(alist):
    if len(alist)>1:
        mid = len(alist)//2
        lefthalf = alist[:mid]
        righthalf = alist[mid:]

        mergeSort(lefthalf)
        mergeSort(righthalf)

        i=0
        j=0
        k=0
        while i < len(lefthalf) and j < len(righthalf):
            if lefthalf[i] < righthalf[j]:
                alist[k]=lefthalf[i]
                i=i+1
            else:
                alist[k]=righthalf[j]
                j=j+1
            k=k+1

        while i < len(lefthalf):
            alist[k]=lefthalf[i]
            i=i+1
            k=k+1

        while j < len(righthalf):
            alist[k]=righthalf[j]
            j=j+1
            k=k+1

    return alist
{% endhighlight %}

虽然没能写出这个代码，但是还是可以分析这个算法的，就像二分法一样，它需要分裂$$logn$$次，由于还是要重组起来，而每一次分裂都要再做n次的比对，所以它的时间复杂度是$$O(nlogn)$$

###The Quick Sort 快排

明天看快排，今天内容有点多，可能需要理解一会儿。


----------
*To be continued*
