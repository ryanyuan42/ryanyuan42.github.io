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

####Binary Heap

Binary Heap应该有的操作有如下：

> `BinaryHeap()` creates a new, empty, binary heap. 
> `insert(k)` adds a new item to the heap. 
> `findMin()` returns the item with the minimum key value, leaving item in the heap.
> `delMin()` returns the item with the minimum key value, removing the item from the heap. 
> `isEmpty()`returns true if the heap is empty, false otherwise.
> `size()` returns the number of items in the heap. buildHeap(list) builds a new heap from a list of keys.


明天完成Binary Heap和Binary Search Tree(二叉搜索树)

----------
To be continued...
