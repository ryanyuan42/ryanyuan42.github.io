Thanksgiving只能呆家里，开个Python Data Structure的坑

废话少说，现在就开始。

##Algorithm Analysis

先来比较一下下面两段代码的不同之处，他们都是解决相同的问题。

```
def sumOfN(n):
   theSum = 0
   for i in range(1,n+1):
       theSum = theSum + i

   return theSum

print(sumOfN(10))
```

```
def foo(tom):
    fred = 0
    for bill in range(1,tom+1):
       barney = bill
       fred = fred + barney

    return fred

print(foo(10))
```
上面两段代码都是用来求和的。
明显第二段代码更难读，
然而Algorithm analysis应该专注的点是the amount of computing resources that each algorithm uses. 

如果用time模块去计算上面代码使用的时间的话，
1,000,000次的叠加大概花了0.19 秒左右。

现在来看第三段代码，
```
def sumOfN3(n):
   return (n*(n+1))/2

print(sumOfN3(10))
``` 
同样是计算1,000,000的sum up，花掉的时间是0.00000095秒
如果用这段代码计算10,000,000, 100,000,000呢？

花掉的时间分别是0.00000095， 0.00000119

###结论
* 第三段代码的效率比之前的效率高很多
* 显然第三段代码的效率并不受到N大小的制约

##Big-O Notation
在sumOfN中，赋值语句有一个($$theSum = 0$$), 加上n的值，所以一共有n+1。 我们可以用一个$$T(n) = n + 1$$来表示这个函数。T(n)就是用来解决这个大小为n的问题的时间，一共花了n+1步骤。

所以可见在 theSum(n)代码中，用来解决这个问题的时间是随着n线性增长的。

而CS们更加喜欢来这样使用这个方法，比如说当 n 趋向无穷的时候，后面的1就不那么重要了，而剩下的，就拿来与其他代码比较效率。

**The order of magnitude** 就是用来描述$$T(n)$$中随着n增长得最快的那部分。

我们把它写作**$$O(f(n))$$**，而$$f(n)$$代表的就是$$T(n)$$的中的dominant part，也就是增长的最快的那一部分。

在上面那个$$T(n) = n + 1$$的例子中，由于当n趋近于无穷时，1就可以忽略不计了，那么我们可以简单地说，这段代码的运行时间就是$$O(n)$$

换一个例子说，假设说我们有一个算法，步骤数为$$T(n)=5n^2+27n+1005$$，显然其中最重要的是$$n^2$$，常数也不重要，所以$$T(n)$$的order of magnitude 是$$f(n)=n^2$$，或者就是$$O(n^2)$$

但是，n不可能真的变成无穷，所以我们需要把代码的实际操作中可能的表现去分类，有：
* Best case
* Worst case
* average case 
通常可见的order of magnitude是：
* $$1$$
* $$log(n)$$
* $$n$$
* $$nlogn$$
* $$n^2$$
* $$n^3$$
* $$2^n$$

增长速度$$1 < log(n) < n < nlog(n) < n^2 < n^3 < 2^n$$

![resources the code take](http://interactivepython.org/courselib/static/pythonds/_images/newplot.png)

最后一个例子：
{% highlight python%}
a=5
b=6
c=10
for i in range(n):
   for j in range(n):
      x = i * i
      y = j * j
      z = i * j
for k in range(n):
   w = a*k + 45
   v = b*b
d = 33
{% endhighlight %}

这里的$$T(n)=3n^2 + 2n + 4$$，那么时间复杂度就是$$O(n^2)$￥

以上就是Python Data Structure的准备部分。

By the way, Happy Thanksgiving！
