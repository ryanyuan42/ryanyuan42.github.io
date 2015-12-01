---
layout: post
title: "python data structure(5)"
excerpt: "AVL Tree"
categories: articles
tags: [python, data structure]
comments: true
share: true
---
##Balanced  Binary Search Tree

我们要介绍一种特别的树结构，它能够让整个结构一直保持在平衡的状态。这种结构叫做**AVL Tree**，以它的发明者命名，G.M. Adelson-Velskii and E.M. Landis.

AVL Tree和之前的二叉搜索树没有很多不同，唯一的区别是tree的表现过程。为了创建一个**AVL Tree**，我们必须要计算每一个Node的**balance factor**。它的计算公式是这样的。

$$balanceFactor=height(leftSubTree)−height(rightSubTree)$$

每一个Node左边的subtree的高度减去右边subtree的高度就是我们的**balance factor**。

为了实现**AVL Tree**的结构，我们定义：一个tree处于balance的状态只要**balance factor = -1 ，0，or 1**。一旦某个Node的balance factor在这个范围之外，我们就需要用一种方法使得树重新平衡。

![enter image description here](http://interactivepython.org/courselib/static/pythonds/_images/unbalanced.png)

*Figure 1: An Unbalanced Right-Heavy Tree with Balance Factors*

##AVL Tree Performance AVL树的表现

我们可以先看看AVL树下面的最坏的情况。

为了符合AVL树的定义，假设我们先考虑树的高度为0,1,2,3，最坏的情况应该是下图所示：

![enter image description here](http://interactivepython.org/courselib/static/pythonds/_images/worstAVL.png)

我们可以看到，对于高度为一的数，一共有1+1 = 2个Node，对于高度为二的树，一共有1+1+2=4个Node...

我们可以总结如下，

$$Nh=1+Nh−1+Nh−2$$

h是树的高度，其中$$N_{0} = 1, N_{1} = 2$$ 

我们可以看到，这个Fib数列非常相近。Fib数列是这样的：

$$F_{0}=0, F_{1}=1, F_{i}=F_{i−1}+F_{i−2}, i≥2$$

我们可以找到很明显的规律，$$N_{h} = F_{h+3} - 1, h≥1$$ 

然后我们又知道当Fib数列越来越大的时候，$$F_{i}/F_{i-1}$$会越来越趋近$$Φ=\frac{\sqrt{5}+1}{2}$$。这样代入进去之后取对数，我们也可以大概估算出$$h = 1.44logN_{h}$$当然这应该是比较粗略的估计，但重要的是，我们找到了$$h与N_{h}$$之间是存在着线性关系。也就是说最糟情况下的**AVL Tree**的高度是取决于N的对数的。

而当N越来越大，趋向于无穷，AVL树的搜索也就被限制为$$O(logN)$$

###实现AVL Tree

当你插入一个新的leaf child的时候，leaf的balance factor肯定是0，但是每当加入一个Leaf child，我们都需要去更新parent的balance factor。所以如果新加进来的Child是个left child，那parent的balance factor+1，如果是right child，那parent的balance factor - 1。

所以这是个recursive的过程，

 1. 如果update到root，那recursive停止
 2. 如果parent的balance factor被调整为0，那也停止。

我们应该改写一下`_put`函数，然后添加一个新的`updateBalanceFactor`，它能够帮助我们在每添加一个新的Node的时候去update之前的parent的**balance factor**。

{% highlight python %}
def _put(self,key,val,currentNode):
    if key < currentNode.key:
        if currentNode.hasLeftChild():
                self._put(key,val,currentNode.leftChild)
        else:
                currentNode.leftChild = TreeNode(key,val,parent=currentNode)
                self.updateBalance(currentNode.leftChild)
    else:
        if currentNode.hasRightChild():
                self._put(key,val,currentNode.rightChild)
        else:
                currentNode.rightChild = TreeNode(key,val,parent=currentNode)
                self.updateBalance(currentNode.rightChild)

def updateBalance(self,node):
    if node.balanceFactor > 1 or node.balanceFactor < -1:
        self.rebalance(node)
        return
    if node.parent != None:
        if node.isLeftChild():
                node.parent.balanceFactor += 1
        elif node.isRightChild():
                node.parent.balanceFactor -= 1

        if node.parent.balanceFactor != 0:
                self.updateBalance(node.parent)
{% endhighlight %}

但是接下来我们要考虑，如果一个Node的balance factor超过了范围，我们应该怎么样设计`rebalance`这个函数。

为了使得AVL Tree重新平衡，我们使用**rotation**的方法。

![enter image description here](http://interactivepython.org/courselib/static/pythonds/_images/simpleunbalanced.png)

*Figure 3: Transforming an Unbalanced Tree Using a Left Rotation*

看图我们发现，A是处于一个unbalanced的状态的，明显是right-heavy，所以我们对它应用一个left rotation使它重新平衡。Rotation的过程分析：

 1. 把right Child B，变成root。
 2. 把原来的root变成新root的left Child
 3. 如果B已经有了一个left Child，就让这个left child变成A的right Child

![enter image description here](http://interactivepython.org/courselib/static/pythonds/_images/rightrotate1.png)

*Figure 4: Transforming an Unbalanced Tree Using a Right Rotation*

{% highlight python %}
def rotateLeft(self,rotRoot):
    newRoot = rotRoot.rightChild
    rotRoot.rightChild = newRoot.leftChild
    if newRoot.leftChild != None:
        newRoot.leftChild.parent = rotRoot
    newRoot.parent = rotRoot.parent
    if rotRoot.isRoot():
        self.root = newRoot
    else:
        if rotRoot.isLeftChild():
                rotRoot.parent.leftChild = newRoot
        else:
            rotRoot.parent.rightChild = newRoot
    newRoot.leftChild = rotRoot
    rotRoot.parent = newRoot
    rotRoot.balanceFactor = rotRoot.balanceFactor + 1 - min(newRoot.balanceFactor, 0)
    newRoot.balanceFactor = newRoot.balanceFactor + 1 + max(rotRoot.balanceFactor, 0)
{% endhighlight %}

但是我们需要了解最后的balance factor的计算公式。

![enter image description here](http://interactivepython.org/courselib/static/pythonds/_images/bfderive.png)

*Figure 5: A Left Rotation*

其中B,D是那两个Rotation涉及到的node, 我们可以知道:

$$newBal(B) = h_{A} - h_{C}$$

$$oldBal(B) = h_{A} - h_{D}$$

但我们也知道old height of D = $$1+max(h_C, h_E)$$

所以我们有

$$oldBal(B) = h_{A} - (1+max(h_C, h_E))$$

用newBal(B) - oldBal(B)整理可以得到，

$$newBal(B)−oldBal(B)=1+max(h_C,h_E)−h_C$$

接着，$$newBal(B)=oldBal(B)+1+max(h_C−h_C,h_E−h_C)$$,

同时$$h_E−h_C和−oldBal(D)$$是一样的。而且$$max(−a,−b)=−min(a,b)$$，也就是说，

$$newBal(B)=oldBal(B)+1+max(0,−oldBal(D))$$
$$newBal(B)=oldBal(B)+1−min(0,oldBal(D))$$

同理可以去找到
$$newBal(D)=oldBal(D)-1+min(newBal(B),0)$$ 

然后我们要注意有时候会出现用rotation调整完依旧还是不平衡的情况

![enter image description here](http://interactivepython.org/courselib/static/pythonds/_images/hardunbalanced.png)

*Figure 6: An Unbalanced Tree that is More Difficult to Balance*
![enter image description here](http://interactivepython.org/courselib/static/pythonds/_images/badrotate.png)

*Figure 7: After a Left Rotation the Tree is Out of Balance in the Other Direction*

比如上面这样

所以我们应该完善我们的条件：

 - 如果一个subtree需要左旋，首先检查right Child的balance情况，如果right child是left heavy(balance factor > 0)，那就先对right child进行右旋，再对原来的subtree的root做左旋
 - 如果一个subtree需要右旋，首先检查left Child的balance情况，如果left child是right heavy(balance factor < 0)，那就先对left child进行左旋，再对原来的subtree的root做右旋

附上完整的AVL Tree代码：

{% highlight python %}
class AVLTree(BinarySearchTree):
	def _put(self, key, val, currentNode):
		if key < currentNode.key:
			if currentNode.hasLeftChild():
				self._put(key, val, currentNode.leftChild)
			else:
				currentNode.leftChild = TreeNode(key, val, parent = currentNode)
				self.updateBalance(currentNode.leftChild)
		else:
			if currentNode.hasRightChild():
				self._put(key, val, currentNode.rightChild)
			else:
				currentNode.rightChild = TreeNode(key, val, parent = currentNode)
				self.updateBalance(currentNode.rightChild)	

	def updateBalance(self, node):
		if node.balanceFactor > 1 or node.balanceFactor < -1:
			self.rebalance(node)
			return
		if node.parent != None:
			if node.isLeftChild():
				node.parent.balanceFactor += 1
			
			elif node.isRightChild():
				node.parent.balanceFactor -= 1
			
			if node.parent.balanceFactor != 0:
				self.updateBalance(node.parent)

	def rotateLeft(self, rotRoot):
		newRoot = rotRoot.rightChild
		rotRoot.rightChild = newRoot.leftChild
		if newRoot.leftChild != None:
			newRoot.leftChild.parent = rotRoot
		newRoot.parent = rotRoot.parent
		if rotRoot.isRoot():
			self.root = newRoot
		else:
			if rotRoot.isLeftChild():
				rotRoot.parent.leftChild = newRoot
			else:
				rotRoot.parent.rightChild = newRoot

		newRoot.leftChild = rotRoot
		rotRoot.parent = newRoot
		rotRoot.balanceFactor = rotRoot.balanceFactor + 1 - min(newRoot.balanceFactor, 0)
		newRoot.balanceFactor = newRoot.balanceFactor + 1 + max(rotRoot.balanceFactor, 0)

	def rotateRight(self, rotRoot):
		newRoot = rotRoot.leftChild
		rotRoot.leftChild = newRoot.rightChild
		if newRoot.rightChild != None:
			newRoot.rightChild.parent = rotRoot
		newRoot.parent = rotRoot.parent
		if rotRoot.isRoot():
			self.root = newRoot
		else:
			if rotRoot.isLeftChild():
				rotRoot.parent.leftChild = newRoot
			else:
				rotRoot.parent.rightChild = newRoot

		newRoot.rightChild = rotRoot
		rotRoot.parent = newRoot
		rotRoot.balanceFactor = rotRoot.balanceFactor - 1 - max(newRoot.balanceFactor, 0)
		newRoot.balanceFactor = newRoot.balanceFactor - 1 + min(rotRoot.balanceFactor, 0)

	def rebalance(self,node):
	  if node.balanceFactor < 0:
	         if node.rightChild.balanceFactor > 0:
	            self.rotateRight(node.rightChild)
	            self.rotateLeft(node)
	         else:
	            self.rotateLeft(node)
	  elif node.balanceFactor > 0:
	         if node.leftChild.balanceFactor < 0:
	            self.rotateLeft(node.leftChild)
	            self.rotateRight(node)
	         else:
	            self.rotateRight(node)
{% endhighlight %}
	            
接着为Binary Search Tree添加一个按从左到右的顺序访问并且打印的功能，使用的**pop(0)**的部分真是太巧妙了！

{% highlight python %}
	
	def levelorder(self, node):
		nodes = []
		nodes.append(node)
		while len(nodes) > 0:
			current_node = nodes.pop(0)
			self.print_node(current_node)
			if current_node.leftChild:
				nodes.append(current_node.leftChild)
			if current_node.rightChild:
				nodes.append(current_node.rightChild)
	def print_node(self, node):
		if node.parent:
			print [node.key, node.payload, node.parent.key]
		else:
			print [node.key, node.payload]
			
{% endhighlight %}

这样一来，`get`函数的时间复杂度肯定是$$O(log_2n)$$了。但是`put`函数呢？

每一个新的Node都是作为一个leaf插入到二叉树中，需要判断大小+更新balance factor，我们知道更新factor需要$$log_2n$$，因为要访问每一级的parent。但是我们的`rotate`函数时间复杂度是$$O(1)$$。所以`put`函数也是$$O(log_2n)$$



##总结
![enter image description here](http://i65.tinypic.com/wbx94k.png)


至此，python data structure就结束了，这应该算是Python里的基本的数据结构了吧。当然还有Graph and Graph Algorithms 这就等到有空的时候再更新吧。

最后还有感谢[Problem Solving with Algorithms and Data Structures
](http://interactivepython.org/courselib/static/pythonds/index.html)网站提供的教程。我的整个数据结构都是学习它记录的笔记和过程。下次把链接贴到前面去。

剩下的日子就复习或者看看Time Series。因为data structure，Time Series被耽搁了好久。寒假继续开始数据挖掘的旅程。Scrapy作为数据获得的工具现在想想学习还是很有必要的。


*Dec 1, 2015*
