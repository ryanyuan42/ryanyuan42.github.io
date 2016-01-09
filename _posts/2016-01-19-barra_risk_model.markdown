---
layout: post
title: "Barra Risk Model Implementation"
excerpt: "A little implementation of python GUI"
categories: articles
tags: [python, GUI]
comments: true
share: true
---
Barra模型的实证研究。

材料：Microsoft从2013-01-04至2016-01-07的三年基本面数据以及三年的行业数据。
工具: Python, SPSS 22

首先对数据进行处理以及运算，得到需要的风格因子，并且最后对因子做标准化处理。

之后分析行业因子

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

第三，对主成分因子做相关性检验。

发现到两组主成分因子之间存在高相关性。这样会使得回归结果误差较大，等待处理。

*Jan 9, 2016*
