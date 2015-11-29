---
layout: post
title: "python data structure(4)"
excerpt: "Binary Tree"
categories: articles
tags: [python, data structure]
comments: true
share: true
---
Binary Tree 二叉树
----

用单独的一章来看完二叉树。

###Nodes and References

我们先用node + reference的方式来描述一个二叉树。

那它应该是这样的

![enter image description here](http://interactivepython.org/runestone/static/pythonds/_images/treerecs.png)

*Figure 2: A Simple Tree Using a Nodes and References Approach*

{% highlight python %}
class BinaryTree:
    def __init__(self,rootObj):
        self.key = rootObj
        self.leftChild = None
        self.rightChild = None
{% endhighlight %}        

这样的类可以我们就可以描述上面的图示，`left` 和`right`就将指向另外一个`BinaryTree`的实例。

如果我们要向这个二叉树添加一个left child，我们可以这样实现。

{% highlight python %}
def insertLeft(self,newNode):
    if self.leftChild == None:
        self.leftChild = BinaryTree(newNode)
    else:
        t = BinaryTree(newNode)
        t.leftChild = self.leftChild
        self.leftChild = t
{% endhighlight %}   

仔细看这个代码，我们可以看出，这个新添加的newNode总是连接着root的，因为它是插入进去的，就像之前的链表一样，也就是最后一个插入的是与root的连接的。

同样对于我们可以这么做

{% highlight python %}
def insertRight(self,newNode):
    if self.rightChild == None:
        self.rightChild = BinaryTree(newNode)
    else:
        t = BinaryTree(newNode)
        t.rightChild = self.rightChild
        self.rightChild = t
{% endhighlight %}   

###Parse Tree

比如说我们现在想利用Tree来解决一个数学计算的问题。

![enter image description here](http://interactivepython.org/runestone/static/pythonds/_images/meParse.png)

*Figure 3: A Simplified Parse Tree for ((7+3)∗(5−2))*

我们可以建立一个token的list，就拿上面的式子来说，我们的token应该是这样的，`['(', '3', '+', '(', '4', '*', '5' ,')',')']`。

我们可以根据观察定下以下的规则：

 1. 如果当前的token是`'('`，我们添加一个新的node作为当前的node的left child，并且当前Node下降到left child。
 2. 如果当前的token是加减乘除其中之一的话，就把当前node的root value设置为运算符，然后添加一个新的node作为当前node的rigth child，并且当前的node下降到right child。
 3. 如果当前token是个数字，就把当前node的root value设置为这个数，并且，当前node返回到它的Parent
 4. 如果当前token是`')'`，当前node返回到它的parent。

这样对于，`(3+(4*5))`来说，过程应该是这样的。

![enter image description here](http://interactivepython.org/runestone/static/pythonds/_images/buildExp1.png)
![enter image description here](http://interactivepython.org/runestone/static/pythonds/_images/buildExp3.png)
![enter image description here](http://interactivepython.org/runestone/static/pythonds/_images/buildExp3.png)
![enter image description here](http://interactivepython.org/runestone/static/pythonds/_images/buildExp4.png)
![enter image description here](http://interactivepython.org/runestone/static/pythonds/_images/buildExp5.png)
![enter image description here](http://interactivepython.org/runestone/static/pythonds/_images/buildExp6.png)
![enter image description here](http://interactivepython.org/runestone/static/pythonds/_images/buildExp7.png)
![enter image description here](http://interactivepython.org/runestone/static/pythonds/_images/buildExp8.png)

{% highlight python %}
from pythonds.basic.stack import Stack
from pythonds.trees.binaryTree import BinaryTree

def buildParseTree(fpexp):
    fplist = fpexp.split()
    pStack = Stack()
    eTree = BinaryTree('')
    pStack.push(eTree)
    currentTree = eTree
    for i in fplist:
        if i == '(':
            currentTree.insertLeft('')
            pStack.push(currentTree)
            currentTree = currentTree.getLeftChild()
        elif i not in ['+', '-', '*', '/', ')']:
            currentTree.setRootVal(int(i))
            parent = pStack.pop()
            currentTree = parent
        elif i in ['+', '-', '*', '/']:
            currentTree.setRootVal(i)
            currentTree.insertRight('')
            pStack.push(currentTree)
            currentTree = currentTree.getRightChild()
        elif i == ')':
            currentTree = pStack.pop()
        else:
            raise ValueError
    return eTree

pt = buildParseTree("( ( 10 + 5 ) * 3 )")
pt.postorder()  #defined and explained in the next section

def evaluate(parseTree):
    opers = {'+':operator.add, '-':operator.sub, '*':operator.mul, '/':operator.truediv}

    leftC = parseTree.getLeftChild()
    rightC = parseTree.getRightChild()

    if leftC and rightC:
        fn = opers[parseTree.getRootVal()]
        return fn(evaluate(leftC),evaluate(rightC))
    else:
        return parseTree.getRootVal()
{% endhighlight %}   

###Tree Traversals

当我们有一个树的数据结构的时候，我们肯定需要去访问其中的每一个Node去做一些事情，我们把这些访问叫做traversal。

有三种traversal的方法，分别是preorder, inorder和postorder
####preorder
preorder就是先访问root，然后再recursively访问左边的subtree，然后再recursively访问右边的subtree。

####inorder
inorder就是先recursivley访问左边的subtree，再访问root，最后recursively访问右边的subtree。

####postorder
postorder就是先recursively访问右边的subtree，再recursively访问左边的subtree，最后访问root

代码实现如下：

{% highlight python %}
def preorder(tree):
    if tree:
        print(tree.getRootVal())
        preorder(tree.getLeftChild())
        preorder(tree.getRightChild())

def postorder(tree):
    if tree != None:
        postorder(tree.getLeftChild())
        postorder(tree.getRightChild())
        print(tree.getRootVal())
        
def inorder(tree):
  if tree != None:
      inorder(tree.getLeftChild())
      print(tree.getRootVal())
      inorder(tree.getRightChild())
{% endhighlight %}   

###Priority Queues with Binary Heaps

之前有提到，Queue的数据结构就是先进先出（FIFO）。Queue的一个重要的变种就是priority queue。在priority queue之中，每个值的逻辑顺序都是由priority决定的。

用insert和sort去实现priority queues的话不免有些太不效率，insert的复杂度是$$O(n)$$，sort的复杂度是$$O(nlogn)$$。我们可以使用Binary Heap这样的数据结构去实现一个Priority Queue。Binary Heap可以让我们实现入队和离队的效率都达到$$O(logn)$$。

Binary Heap有两个很重要的变量，一个叫**min heap**，也就是最小的key（键值）永远在最前面，另一个叫**max heap**，也就是最大的key永远在最前面。


###Binary Heap

Binary Heap应该有的操作有如下：

> `BinaryHeap()` creates a new, empty, binary heap. 

> `insert(k)` adds a new item to the heap. 

> `findMin()` returns the item with the minimum key value, leaving item in the heap.

> `delMin()` returns the item with the minimum key value, removing the item from the heap. 

> `isEmpty()`returns true if the heap is empty, false otherwise.

> `size()` returns the number of items in the heap. buildHeap(list) builds a new heap from a list of keys.


####The Structure Property

Binary Tree的特点就是它的$$log n$$的特性，不过为了保证能够有$$log n$$的表现，我们需要保持二叉树的平衡。在二叉堆中，我们通过创造一个完整的二叉树（**complete binary tree**）来保持树的平衡。一个完整的二叉树应该在每一级上都有所有的nodes，当然除了最后一级。

下图就是一个完整的二叉树。

![enter image description here](http://interactivepython.org/courselib/static/pythonds/_images/compTree.png)

又由于这是一个完整的二叉树，我们都不需要用nodes + reference来表示它，因为如果一个parent的位置是p，那它的left child就在2p上，right child在2p+1上面。

![enter image description here](http://interactivepython.org/courselib/static/pythonds/_images/heapOrder.png)

Heap order就是parent的key比它们的child中的Key都要小。

我们可以建立一个Binary Heap的类。

{% highlight python %}
class BinHeap:
    def __init__(self):
        self.heapList = [0]
        self.currentSize = 0
{% endhighlight %}   

接下来我们需要实现一个`insert`的操作。但是为了保持二叉堆的结构特性，我们不能直接添加在最背后。

![enter image description here](http://interactivepython.org/courselib/static/pythonds/_images/percUp.png)

*Figure 2: Percolate the New Node up to Its Proper Position*

{% highlight python %}
def percUp(self,i):
    while i // 2 > 0:
      if self.heapList[i] < self.heapList[i // 2]:
         tmp = self.heapList[i // 2]
         self.heapList[i // 2] = self.heapList[i]
         self.heapList[i] = tmp
      i = i // 2
{% endhighlight %}   

这个`perCup`的方法可以轻松把新添加进来的值往上挪动到它应该待的位置。

{% highlight python %}
def insert(self,k):
    self.heapList.append(k)
    self.currentSize = self.currentSize + 1
    self.percUp(self.currentSize)
{% endhighlight %}  

而`insert`这个方法就是添加新的值到尾部，然后调用percup把它挪到应该的位置。

接下来可以看一看`delMin`这个操作了。

![enter image description here](http://interactivepython.org/courselib/static/pythonds/_images/percDown.png)

看图就是把5去掉，替换成最后一个值

{% highlight python %}
def percDown(self,i):
    while (i * 2) <= self.currentSize:
        mc = self.minChild(i)
        if self.heapList[i] > self.heapList[mc]:
            tmp = self.heapList[i]
            self.heapList[i] = self.heapList[mc]
            self.heapList[mc] = tmp
        i = mc

def minChild(self,i):
    if i * 2 + 1 > self.currentSize:
        return i * 2
    else:
        if self.heapList[i*2] < self.heapList[i*2+1]:
            return i * 2
        else:
            return i * 2 + 1
  
  def delMin(self):
    retval = self.heapList[1]
    self.heapList[1] = self.heapList[self.currentSize]
    self.currentSize = self.currentSize - 1
    self.heapList.pop()
    self.percDown(1)
    return retval
{% endhighlight %}  

`minChild`找到一个parent的Child中的最小值的位置，`percDown`则是将parent中的值和最小的Child进行交换。

现在我们如果要从一个List中建立一个Binary Heap的话，我们可以选择每次`insert`一个值，我们来分析一下`insert`这些值的过程，首先需要`insert`$$n$$个值，然后需要通过`percUp`的方法找到他们应该待的地方，`percUp`很明显是一个对数的算法，所以我们建立一个Binary Heap的时间复杂度应该是$$O(nlogn)$$。

但是，如果我们选择用一整个list来建立一个二叉堆，我们的时间复杂度是$$O(n)$$。

如下图：

![enter image description here](http://interactivepython.org/courselib/static/pythonds/_images/buildheap.png)

*Figure 4: Building a Heap from the List [9, 6, 5, 2, 3]*

代码实现如下：

{% highlight python %}
def buildHeap(self,alist):
    i = len(alist) // 2
    self.currentSize = len(alist)
    self.heapList = [0] + alist[:]
    while (i > 0):
        self.percDown(i)
        i = i - 1
{% endhighlight %}  


####总结
`percUp` 和 `percDown`是对整个list进行排序的方式，唯一不同就是从最上面开始排序，还是从最下面开始排序。`percUp`使用的是从指定的i位置，一直向上进行sort，而`percDown`使用的是从指定的i位置一直向下进行sort。

上面的代码创建一个Binary Heap使用的是`percDown`，而我坚信使用`percUp`一样可以写出来。

然而我为了用`percUp`来写这个算法，想了我可能有一个小时，我真是太蠢了。

也可以说我对使用`percDown`的`BuildHeap`分析不够透彻，

首先`BuildHeap`里面使用`percDown`，是对`percDown`加以限制的，也就是第一次先从倒数第二层到底部，第二次倒数第三层到底部，以此类推...

所以我们在使用`percUp`构建的时候，我们的结构应该是从第一次从正数第二层到顶点，第二次从正数第三层到顶点...

图示如下：

![enter image description here](http://screenshot.net/1801paz.jpg)

####错误的想法
认为实现perUp应该这样

![enter image description here](http://screenshot.net/d0oevh4.jpg)

这样不正确的原因，是如果这样做，第二步并没有对之前作出任何贡献，因为第一步已经确定好了底部到倒数第一层的顺序，第二步并不会改变它。然而第二步的调换顺序会使得第一步的顺序不再正确，而循环到此结束了。

代码实现如下：
{% highlight python %}
	def buildHeap(self, alist):
		i = 2
		self.currentSize = len(alist)
		self.heapList = [0] + alist
		while i <= len(alist):
			self.percUp(i)	
			i = i + 1

	def percUp(self,i):
		while i // 2 > 0:
			mc = self.minChild(i//2)
			if self.heapList[mc] < self.heapList[i // 2]:
				tmp = self.heapList[i // 2]
				self.heapList[i // 2] = self.heapList[mc]
				self.heapList[mc] = tmp
			i = i // 2`
{% endhighlight %}  

当然需要修改一下`percUp才行。

终于可以睡觉啦...




