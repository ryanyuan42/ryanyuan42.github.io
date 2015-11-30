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



###Binary Search Tree

就像名字一样，我们希望用二叉树来提供搜索功能。

一个二叉搜索树应该有以下的功能：

    Map() Create a new, empty map.
    
    put(key,val) 
    Add a new key-value pair to the map. If the key is already in the map then replace the old value with the new value.
    
    get(key) Given a key, return the value stored in the map or None otherwise.
    
    del Delete the key-value pair from the map using a statement of the form del map[key].
    
    len() Return the number of key-value pairs stored in the map.
    
    in 
    Return True for a statement of the form key in map, if the given key is in the map.

二叉搜索树有一个重要的特性，就是比parent小的值都在左边的subtree里，而比parent大的值，都在右边的subtree里。

如图所示：

![enter image description here](http://interactivepython.org/courselib/static/pythonds/_images/simpleBST.png)

*Figure 1: A Simple Binary Search Tree*

我们把这个性质叫做`bst property`

原List是[70,31,93,94,14,23,73]，70作为root，31比70小，所以31在左边；93比70大，所以93在右边；接下去94因为比70和93都大，所以在93的右边，而73比70大，所以在70的右边，比93小，所以在93的左边...

为了实现二叉搜索书，我们还是使用Nodes + reference的方式来表示。

代码实现如下
{% highlight python %}
class BinarySearchTree:

    def __init__(self):
        self.root = None
        self.size = 0

    def length(self):
        return self.size

    def __len__(self):
        return self.size

    def __iter__(self):
        return self.root.__iter__()
class TreeNode:
   def __init__(self,key,val,left=None,right=None,
                                       parent=None):
        self.key = key
        self.payload = val
        self.leftChild = left
        self.rightChild = right
        self.parent = parent

    def hasLeftChild(self):
        return self.leftChild

    def hasRightChild(self):
        return self.rightChild

    def isLeftChild(self):
        return self.parent and self.parent.leftChild == self

    def isRightChild(self):
        return self.parent and self.parent.rightChild == self

    def isRoot(self):
        return not self.parent

    def isLeaf(self):
        return not (self.rightChild or self.leftChild)

    def hasAnyChildren(self):
        return self.rightChild or self.leftChild

    def hasBothChildren(self):
        return self.rightChild and self.leftChild

    def replaceNodeData(self,key,value,lc,rc):
        self.key = key
        self.payload = value
        self.leftChild = lc
        self.rightChild = rc
        if self.hasLeftChild():
            self.leftChild.parent = self
        if self.hasRightChild():
            self.rightChild.parent = self
{% endhighlight %}  


我们要给二叉搜索树提供一个添加node的功能，当然这个功能是加在`BinarySearchTree`这个类里面的。

{% highlight python %}
def put(self,key,val):
    if self.root:
        self._put(key,val,self.root)
    else:
        self.root = TreeNode(key,val)
    self.size = self.size + 1

def _put(self,key,val,currentNode):
    if key < currentNode.key:
        if currentNode.hasLeftChild():
               self._put(key,val,currentNode.leftChild)
        else:
               currentNode.leftChild = TreeNode(key,val,parent=currentNode)
    else:
        if currentNode.hasRightChild():
               self._put(key,val,currentNode.rightChild)
        else:
               currentNode.rightChild = TreeNode(key,val,parent=currentNode)
{% endhighlight %} 

算法原理很简单，查看当前的二叉树有没有root，没有就把新加来的变成root；有root的话就用helper function执行算法，算法原理就是如果Key比当前的小，摆左边，如果左边被占用了，那就对左边的Node再执行算法；如果key比当前的大，摆右边，如果右边被占用了，就对右边的Node再执行算法。

{% highlight python %}
def put(self,key,val):
    if self.root:
        self._put(key,val,self.root)
    else:
        self.root = TreeNode(key,val)
    self.size = self.size + 1

def _put(self,key,val,currentNode):
    if key < currentNode.key:
        if currentNode.hasLeftChild():
               self._put(key,val,currentNode.leftChild)
        else:
               currentNode.leftChild = TreeNode(key,val,parent=currentNode)
    else:
        if currentNode.hasRightChild():
               self._put(key,val,currentNode.rightChild)
        else:
               currentNode.rightChild = TreeNode(key,val,parent=currentNode)
{% endhighlight %}

同理我们需要一个`get`的功能，如果当前二叉树有root，那就调用helper function，如果当前二叉树没有root，那就返回`None`。Helper function中的算法就是利用二叉搜索树的特性，如果key比当前Node小，就去当前Node的左边再找，如果key比当前Node大，就去当前Node的右边再找。如果最后找不到，当前Node成了`None`，返回`None`

{% highlight python %}
def get(self,key):
    if self.root:
        res = self._get(key,self.root)
        if res:
               return res.payload
        else:
               return None
    else:
        return None

def _get(self,key,currentNode):
    if not currentNode:
        return None
    elif currentNode.key == key:
        return currentNode
    elif key < currentNode.key:
        return self._get(key,currentNode.leftChild)
    else:
        return self._get(key,currentNode.rightChild)

def __getitem__(self,key):
    return self.get(key)
{% endhighlight %}
 
然后是设计`in`的功能。

{% highlight python %}
def __contains__(self,key):
    if self._get(key,self.root):
        return True
    else:
        return False
{% endhighlight %}


最后我们要设计`delete`的方法，我们可以先设计一个这样的方法。

{% highlight python %}
def delete(self,key):
   if self.size > 1:
      nodeToRemove = self._get(key,self.root)
      if nodeToRemove:
          self.remove(nodeToRemove)
          self.size = self.size-1
      else:
          raise KeyError('Error, key not in tree')
   elif self.size == 1 and self.root.key == key:
      self.root = None
      self.size = self.size - 1
   else:
      raise KeyError('Error, key not in tree')

def __delitem__(self,key):
    self.delete(key)
{% endhighlight %}

接着我们会找到我们想要删除的Node，我们应该考虑下面三种情况：

 1. 这个Node没有Children
 2. 这个Node只有一个Child
 3. 这个Node有两个Children

如果没有children应该是这样：

![enter image description here](http://interactivepython.org/courselib/static/pythonds/_images/bstdel1.png)


{% highlight python %}
if currentNode.isLeaf():
    if currentNode == currentNode.parent.leftChild:
        currentNode.parent.leftChild = None
    else:
        currentNode.parent.rightChild = None
{% endhighlight %}

注意，这里删除的是Reference。

如果有一个Child的话，

![enter image description here](http://interactivepython.org/courselib/static/pythonds/_images/bstdel2.png)

我们要考虑的就是这个Node有的一个Child是LeftChild还是RightChild，如果是leftChild，我们又要问这个Node是Left还是Right，如果是Left，就把LeftChild和自己的parent对接...


{% highlight python %}
else: # this node has one child
   if currentNode.hasLeftChild():
      if currentNode.isLeftChild():
          currentNode.leftChild.parent = currentNode.parent
          currentNode.parent.leftChild = currentNode.leftChild
      elif currentNode.isRightChild():
          currentNode.leftChild.parent = currentNode.parent
          currentNode.parent.rightChild = currentNode.leftChild
      else:
          currentNode.replaceNodeData(currentNode.leftChild.key,
                             currentNode.leftChild.payload,
                             currentNode.leftChild.leftChild,
                             currentNode.leftChild.rightChild)
   else:
      if currentNode.isLeftChild():
          currentNode.rightChild.parent = currentNode.parent
          currentNode.parent.leftChild = currentNode.rightChild
      elif currentNode.isRightChild():
          currentNode.rightChild.parent = currentNode.parent
          currentNode.parent.rightChild = currentNode.rightChild
      else:
          currentNode.replaceNodeData(currentNode.rightChild.key,
                             currentNode.rightChild.payload,
                             currentNode.rightChild.leftChild,
                             currentNode.rightChild.rightChild)
{% endhighlight %}

如果是有两个Children，

![enter image description here](http://interactivepython.org/courselib/static/pythonds/_images/bstdel3.png)

*Figure 5: Deleting Node 5, a Node with Two Children*

如果删掉一个这样的节点，我们需要找到一个`successor`来替代它才行，这个`successor`被确保是只有一个Child。我们需要`findSuccessor` 和 `findMin`这两个Helper function，另外我们用`spliceOut`来去掉`successor`

{% highlight python %}
elif currentNode.hasBothChildren(): #interior
        succ = currentNode.findSuccessor()
        succ.spliceOut()
        currentNode.key = succ.key
        currentNode.payload = succ.payload
{% endhighlight %}

为了找到successor我们需要这样做：

 1. 如果Node有一个rightChild，那successor就是右边的subtree中的最小值。
2. 如果Node没有rightChild，并且它的parent的leftChild，那succ就是parent
3. 如果Node没有rightChild，并且它的parent的rightChild，那succ就是parent的succ（除了Node）

当然在我们的例子中，为了处理拥有两个Child的Node情况，只有第一种情况是符合的。但是剩下两种考虑在以后还会有用。 

以下的代码都是加在TreeNode的类里面。
{% highlight python %}
def findSuccessor(self):
    succ = None
    if self.hasRightChild():
        succ = self.rightChild.findMin()
    else:
        if self.parent:
               if self.isLeftChild():
                   succ = self.parent
               else:
                   self.parent.rightChild = None
                   succ = self.parent.findSuccessor()
                   self.parent.rightChild = self
    return succ

def findMin(self):
    current = self
    while current.hasLeftChild():
        current = current.leftChild
    return current

def spliceOut(self):
    if self.isLeaf():
        if self.isLeftChild():
               self.parent.leftChild = None
        else:
               self.parent.rightChild = None
    elif self.hasAnyChildren():
        if self.hasLeftChild():
               if self.isLeftChild():
                  self.parent.leftChild = self.leftChild
               else:
                  self.parent.rightChild = self.leftChild
               self.leftChild.parent = self.parent
        else:
               if self.isLeftChild():
                  self.parent.leftChild = self.rightChild
               else:
                  self.parent.rightChild = self.rightChild
               self.rightChild.parent = self.parent
{% endhighlight %}

对二叉分析树的分析，如果是balanced Tree，`put`函数的表现是$$O(logn)$$，但是很不幸有可能出现这种unbalanced 的情况。

![enter image description here](http://interactivepython.org/courselib/static/pythonds/_images/skewedTree.png)

*Figure 6: A skewed binary search tree would give poor performance*

这样`put`函数的表现就是$$O(n)$$

这样让我们很不爽！搞了这么半天，效率还是有可能被降到$$O(n)$$，这样不就和原来的链表一样了吗？链表的很多操作都涉及到Search的算法，而且算法也是最基本的sequential search。因此，我们引入**AVL树**的概念。

Python Data Structure快要结束了，下次更完AVL树，Graph algo有机会再看，一个愉快的感恩节。


