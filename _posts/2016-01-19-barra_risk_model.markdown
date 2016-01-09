---
layout: post
title: "Barra Risk Model Implementation"
excerpt: "A little implementation of python GUI"
categories: articles
tags: [python, GUI]
comments: true
share: true
---
Barra模型的实证研究
---

材料：Microsoft从2013-01-04至2016-01-07的三年基本面数据以及三年的行业数据。
工具：python, spss， ipython notebook

首先分析行业因子

我选取了一共十个行业因子，分别是

 * Basic_materials	
 * Conglomerates
 * Industrial	Utilities
 * Technology	Telecomm	
 * Health_Care	
 * Financials
 * Consumer_Service	
 * Oil & Gas

第一，对行业因子进行聚类分析

聚类分析的结果发现，这十个行业因子都离得较远。究其原因应该是我自己在选行业因子的时候没有选好，这几个应该都是大的板块（sector）而不是小的行业。所以进一步的聚类分析就没有太大的必要了。

第二，对风格因子与行业因子做KMO和Bartlett's Test

得到的结果是它们factor可以使用，并且significant，同时分别从行业因子与风格因子中各提取了三个主成分因子。

![enter image description here](http://screenshot.net/jx2m8cz.jpg)

![enter image description here](http://screenshot.net/7952dcq.jpg)

![enter image description here](http://screenshot.net/45r13i7.jpg)

![enter image description here](http://screenshot.net/jglm2um.jpg)

两组主成分提供的解释度分别超过80%和90%，并且也通过了KMO and Bartlett's Test.

唯一出现问题的地方是，

这两组主成分的相关性有点高，这会使得multi-variable regression不准确。

![enter image description here](http://screenshot.net/dz1v5ie.jpg)

但是在之后的回归中，对多重共线性的诊断中发现，并不存在显著的多重共线性。

![enter image description here](http://screenshot.net/3p3n0ux.jpg)

第三，最后对因子进行回归。

这也是最不尽如人意的地方。

回归的结果的模型对return的预测十分差，很大一部分原因我想并不是在correlation上面，而是在我的dataset上面。

因为我之后用Python对数据做了一次Ridge Regression，按道理ridge regression能解决多重共线性的问题。但是得到的回归结果和multivariable regression并没有太大的差别。

所以问题应该是出在我的dataset上面，当时在数据库里面因为不熟练，行业因子的数据，而我拿成了板块的数据，这样数据就太过general了，这样的行业因子，一只个股对它可能是Indifferent的。所以之前的factor analysis即使通过了，也不能作为行业因子来使用。

最后结果R square只有0.009左右...

另外我单独用风格因子去回归，发现R square还要更小一点，这倒是和材料里面的 

> 个股600031受到几乎全部行业因子影响。

还挺吻合的 也就是说对于那个个股的风格因子中的主成分几乎没有入选最后的模型，风格因子几乎无法描述模型。

感觉有点失望，没有得出想看到的结果，不过也相当于一次练手了，感觉也还挺不错，获得了很多和统计相关的知识。

因为python代码太长，源码就不贴出来了，主要就是用pandas, numpy这些对数据进行一些操作处理，其实用excel也可以完成，只是我感觉python用起来比较方便一点。

*Jan 9, 2016*

