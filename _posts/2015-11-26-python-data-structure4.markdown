---
layout: post
title: "python data structure(3)"
excerpt: "abstract data structure"
categories: articles
tags: [python, data structure]
comments: true
share: true
---
python 内置数据结构的分析
---

###List

####List各个操作的复杂程度
![enter image description here](http://hujiaweibujidao.github.io/images/listoptime.png)

{% highlight python %}
def test1():
    l = []
    for i in range(1000):
        l = l + [i]

def test2():
    l = []
    for i in range(1000):
        l.append(i)

def test3():
    l = [i for i in range(1000)]

def test4():
    l = list(range(1000))
{% endhighlight %}

{% highlight python %}
from timeit import Timer
t1 = Timer("test1()", "from __main__ import test1")
print("concat ",t1.timeit(number=1000), "milliseconds")
t2 = Timer("test2()", "from __main__ import test2")
print("append ",t2.timeit(number=1000), "milliseconds")
t3 = Timer("test3()", "from __main__ import test3")
print("comprehension ",t3.timeit(number=1000), "milliseconds")
t4 = Timer("test4()", "from __main__ import test4")
print("list range ",t4.timeit(number=1000), "milliseconds")

('concat ', 2.8755872091465218, 'milliseconds')
('append ', 0.16156369398242854, 'milliseconds')
('comprehension ', 0.05943448886932545, 'milliseconds')
('list range ', 0.01438837025122508, 'milliseconds')
{% endhighlight %}

显然可以看出，使用list的加法最慢，append更快，而直接用range生成一个list是最快的。

{% highlight python %}
x = list(range(2000000))
pop_zero = Timer("x.pop(0)","from __main__ import x")
print("pop_zero ",pop_zero.timeit(number=1000), "milliseconds")
x = list(range(2000000))
pop_end = Timer("x.pop()","from __main__ import x")
print("pop_end ",pop_end.timeit(number=1000), "milliseconds")
('pop_zero ', 1.9101738929748535, 'milliseconds')
('pop_end ', 0.00023603439331054688, 'milliseconds')
{% endhighlight %}

可以看出来pop(0)显著慢于pop( )，这是因为pop(0)的效率是$$O(n)$$，而pop( )的效率是$$O(1)$$

![enter image description here](http://interactivepython.org/courselib/static/pythonds/_images/poptime.png)

*Figure 3: Comparing the Performance of pop and pop(0)*

###Dictionary
####Dictionary的各个操作的性能
![enter image description here](http://hujiaweibujidao.github.io/images/dictionary.png)

可以看出dictionary的复杂程度几乎都是$$O(1)$$的，这也反映出Dictionary应该与Hash Table很相近，建立dic的过程就是建立映射关系。

{% highlight python %}
import timeit
import random

for i in range(10000,1000001,20000):
    t = timeit.Timer("random.randrange(%d) in x"%i,"from __main__ import random,x")
    x = list(range(i))
    lst_time = t.timeit(number=1000)
    x = {j:None for j in range(i)}
    d_time = t.timeit(number=1000)
    print("%d,%10.3f,%10.3f" % (i, lst_time, d_time)
{% endhighlight %}

####dic和list的对比图
![enter image description here](http://hujiaweibujidao.github.io/images/compare.png)


###利用Stack做一个括号检查器
{% highlight python %}
from pythonds.basic.stack import Stack

def parChecker(symbolString):
	s = Stack()
	balanced = True
	index = 0
	while index < len(symbolString) and balanced:
		symbol = symbolString[index]
		if symbol in "({[":
			s.push(symbol)
		else:
			if s.isEmpty():
				balanced = False
			else:
				top = s.pop()
				if not matches(top,symbol):
					balanced = False

		index += 1

	if balanced and s.isEmpty():
		return True
	else:
		return False
def matches(open,close):
    opens = "([{"
    closers = ")]}"
    return opens.index(open) == closers.index(close)
{% endhighlight %}

###利用Stack将十进制数转化为二进制

![enter image description here](http://interactivepython.org/courselib/static/pythonds/_images/dectobin.png)


{% highlight python %}
from pythonds.basic.stack import Stack

def divideBy2(decNumber):
    remstack = Stack()

    while decNumber > 0:
        rem = decNumber % 2
        remstack.push(rem)
        decNumber = decNumber // 2

    binString = ""
    while not remstack.isEmpty():
        binString = binString + str(remstack.pop())

    return binString
{% endhighlight %}

这里通过stack.pop()的方法把最后一个值取出来

{% highlight python %}
from pythonds.basic.stack import Stack

def baseConverter(decNumber,base):
    digits = "0123456789ABCDEF"

    remstack = Stack()

    while decNumber > 0:
        rem = decNumber % base
        remstack.push(rem)
        decNumber = decNumber // base

    newString = ""
    while not remstack.isEmpty():
        newString = newString + digits[remstack.pop()]

    return newString
{% endhighlight %}

之后还有两种数据结构

 1. Queue，就是先进先出法
 2. Deque，就是左右两边都可以插入和删除的队列
 

明天看二叉树模型和Unordered list，应该就是C++的链表。


----------
*To be continued...*
python 内置数据结构的分析
---

###List

####List各个操作的复杂程度
![enter image description here](http://hujiaweibujidao.github.io/images/listoptime.png)

{% highlight python %}
def test1():
    l = []
    for i in range(1000):
        l = l + [i]

def test2():
    l = []
    for i in range(1000):
        l.append(i)

def test3():
    l = [i for i in range(1000)]

def test4():
    l = list(range(1000))

from timeit import Timer
t1 = Timer("test1()", "from __main__ import test1")
print("concat ",t1.timeit(number=1000), "milliseconds")
t2 = Timer("test2()", "from __main__ import test2")
print("append ",t2.timeit(number=1000), "milliseconds")
t3 = Timer("test3()", "from __main__ import test3")
print("comprehension ",t3.timeit(number=1000), "milliseconds")
t4 = Timer("test4()", "from __main__ import test4")
print("list range ",t4.timeit(number=1000), "milliseconds")

('concat ', 2.8755872091465218, 'milliseconds')
('append ', 0.16156369398242854, 'milliseconds')
('comprehension ', 0.05943448886932545, 'milliseconds')
('list range ', 0.01438837025122508, 'milliseconds')
{% endhighlight %}

显然可以看出，使用list的加法最慢，append更快，而直接用range生成一个list是最快的。

{% highlight python %}
x = list(range(2000000))
pop_zero = Timer("x.pop(0)","from __main__ import x")
print("pop_zero ",pop_zero.timeit(number=1000), "milliseconds")
x = list(range(2000000))
pop_end = Timer("x.pop()","from __main__ import x")
print("pop_end ",pop_end.timeit(number=1000), "milliseconds")

 ('pop_zero ', 1.9101738929748535, 'milliseconds')
 ('pop_end ', 0.00023603439331054688, 'milliseconds')
 {% endhighlight %}

可以看出来pop(0)显著慢于pop( )，这是因为pop(0)的效率是$$O(n)$$，而pop( )的效率是$$O(1)$$

![enter image description here](http://interactivepython.org/courselib/static/pythonds/_images/poptime.png)

*Figure 3: Comparing the Performance of pop and pop(0)*

###Dictionary
####Dictionary的各个操作的性能
![enter image description here](http://hujiaweibujidao.github.io/images/dictionary.png)

可以看出dictionary的复杂程度几乎都是$$O(1)$$的，这也反映出Dictionary应该与Hash Table很相近，建立dic的过程就是建立映射关系。

{% highlight python %}
import timeit
import random

for i in range(10000,1000001,20000):
    t = timeit.Timer("random.randrange(%d) in x"%i,"from __main__ import random,x")
    x = list(range(i))
    lst_time = t.timeit(number=1000)
    x = {j:None for j in range(i)}
    d_time = t.timeit(number=1000)
    print("%d,%10.3f,%10.3f" % (i, lst_time, d_time)
{% endhighlight %}

####dic和list的对比图
![enter image description here](http://hujiaweibujidao.github.io/images/compare.png)


###利用Stack做一个括号检查器
{ % highlight python %}
from pythonds.basic.stack import Stack

def parChecker(symbolString):
	s = Stack()
	balanced = True
	index = 0
	while index < len(symbolString) and balanced:
		symbol = symbolString[index]
		if symbol in "({[":
			s.push(symbol)
		else:
			if s.isEmpty():
				balanced = False
			else:
				top = s.pop()
				if not matches(top,symbol):
					balanced = False

		index += 1

	if balanced and s.isEmpty():
		return True
	else:
		return False
def matches(open,close):
    opens = "([{"
    closers = ")]}"
    return opens.index(open) == closers.index(close)
{% endhighlight %}

###利用Stack将十进制数转化为二进制

![enter image description here](http://interactivepython.org/courselib/static/pythonds/_images/dectobin.png)


{% highlight python%}
from pythonds.basic.stack import Stack

def divideBy2(decNumber):
    remstack = Stack()

    while decNumber > 0:
        rem = decNumber % 2
        remstack.push(rem)
        decNumber = decNumber // 2

    binString = ""
    while not remstack.isEmpty():
        binString = binString + str(remstack.pop())

    return binString
{% endhighlight %}

这里通过stack.pop()的方法把最后一个值取出来

{% highlight python%}
from pythonds.basic.stack import Stack

def baseConverter(decNumber,base):
    digits = "0123456789ABCDEF"

    remstack = Stack()

    while decNumber > 0:
        rem = decNumber % base
        remstack.push(rem)
        decNumber = decNumber // base

    newString = ""
    while not remstack.isEmpty():
        newString = newString + digits[remstack.pop()]

    return newString
{% endhighlight %}

之后还有两种数据结构

 1. Queue，就是先进先出法
 2. Deque，就是左右两边都可以插入和删除的队列
 

