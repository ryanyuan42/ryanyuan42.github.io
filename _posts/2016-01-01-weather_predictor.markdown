---
layout: post
title: "Python Weather Predictor GUI"
excerpt: "A little implementation of python GUI"
categories: articles
tags: [python, GUI]
comments: true
share: true
---

放假一直在研究Barra Risk Model,本来想用Python做一个美股的implementation的，但是无奈没有数据...后来好在发现UCB有一个数据库可以提供数据支持，真是方便了许多，但是无奈进入数据库一定要去学校图书馆，而图书馆要等到一月三号才开门，这几天只好暂时把Barra Model放下做些其他的事情。
比如说看看Time Series，或者...写了一个天气预报的GUI。因为想到明年回去的时候可以写个抢课的程序来拯救世界，怎么样也要稍微懂一点GUI的知识，就用了一天时间看了看GUI编程。

Python里面GUI编程可以用自带的Tkinter，也可以用其他的库，这里我选择了PyQt来写GUI，因为对PyQt早有耳闻，在各个编程语言都有移植，里面几千条命令，虽然看不完但是需要的时候就查阅文档，比较方便

以下就是源码，也有注释。需要留心的是，其中的connect函数，它是通过信号（Signal）与槽（SLOT）来实现，即发出信号给槽，执行槽的命令。

比如说```self.city_btn.clicked.connect(self.city_holder)```也可以写成```self.connect(self.city_btn, QtCore.SIGNAL('clicked()'), self.city_holder)```

第二种就比较好理解了吧

{% highlight python %}
#!/usr/bin/env python2.7
# -*- coding: utf-8 -*-

# Author		 : Ryan Yuan
# Date  		 : Dec 31, 2015
# Python Version : 2.7

import requests
from bs4 import BeautifulSoup
import json

import sys
from PyQt4 import QtGui
from PyQt4 import QtCore
class weather_predictor(QtGui.QWidget):
	def __init__(self):
		super(weather_predictor, self).__init__()
		self.city = None
		self.province = None
		self.initUI()
		self.Done = False
		self.err_msg = None

	def initUI(self):
		self.city_btn = QtGui.QPushButton(u'城市', self)
		self.province_btn = QtGui.QPushButton(u'省份', self)
		self.lookup_btn = QtGui.QPushButton(u'今日天气', self)

		# Create a Table to show the result of weather
		self.res_table = QtGui.QTableWidget(self)
		self.res_table.setColumnCount(0)
		self.res_table.setRowCount(5)
		self.res_table.resize(200,200)

		self.city_btn.clicked.connect(self.city_holder)
		self.province_btn.clicked.connect(self.province_holder)
		self.lookup_btn.clicked.connect(self.run)
		# layout ---------------------*
		hbox = QtGui.QHBoxLayout()
		hbox.addStretch(1)
		hbox.addWidget(self.city_btn)
		hbox.addWidget(self.province_btn)
		hbox.addWidget(self.lookup_btn)

		# 把水平布局放到垂直布局中
		vbox = QtGui.QVBoxLayout()
		vbox.addStretch(1)
		vbox.addLayout(hbox)
		# layout ---------------------*

		self.setLayout(vbox)
		self.resize(250,300)
		self.setWindowTitle(u'Ryan的天气预报')
	

	def city_holder(self):
		text, ok = QtGui.QInputDialog.getText(self, u'城市', 
			u'输入你的城市')
		self.city = unicode(text.toUtf8(), encoding="UTF-8")


	def province_holder(self):
		text, ok = QtGui.QInputDialog.getText(self, u'省份', 
			u'输入你的省份')
		self.province = unicode(text.toUtf8(), encoding="UTF-8")



	def find_city_code(self):

		res = open('citylist.xml').read()
		soup = BeautifulSoup(res)

		return soup.find_all(d4=self.province, d2=self.city)[0]['d1']


	def predict(self):

		s = requests.Session()
		info = s.get('http://wthrcdn.etouch.cn/weather_mini?citykey='+self.find_city_code()).content
		data = json.loads(info)

		self.city = data['data']['city']
		self.date = data['data']['forecast'][0]['date']
		self.high = data['data']['forecast'][0]['high']
		self.low = data['data']['forecast'][0]['low']
		self.type = data['data']['forecast'][0]['type']

	def run(self):
		
		try:
			self.predict()
		except IndexError:
			self.err_msg = u'无法找到该省份中的该城市'
			
		# 每运行一次run()，就insert a new Column
		column = self.res_table.columnCount()
		self.res_table.insertColumn(column)

		if self.err_msg:
			self.res_table.setItem(0, column, QtGui.QTableWidgetItem(self.err_msg))
			self.err_msg = None
		else:
			# 第一个参数是row, 第二参数是column
			self.res_table.setItem(0, column, QtGui.QTableWidgetItem(self.city))
			self.res_table.setItem(1, column, QtGui.QTableWidgetItem(self.date))
			self.res_table.setItem(2, column, QtGui.QTableWidgetItem(self.high))
			self.res_table.setItem(3, column, QtGui.QTableWidgetItem(self.low))
			self.res_table.setItem(4, column, QtGui.QTableWidgetItem(self.type))


def main():
	app = QtGui.QApplication(sys.argv)
	app_icon = QtGui.QIcon()
	app_icon.addFile('icons/web.png', QtCore.QSize(32,32))
	app.setWindowIcon(app_icon)
	icon = weather_predictor()
	icon.show()
	sys.exit(app.exec_())

if __name__ == '__main__':
	main()
{% endhighlight %}


实现出来的效果如下：

![weather_predictor](http://screenshot.net/8q77ou7.jpg)

当然我也添加了捕捉错误的代码。

当然直接复制代码应该是不能运行的，因为我在国外访问一些网站有点慢，所以我就把citycode的xml直接下载下来了，省得每次查询城市代码还要访问网络。所以要让代码能够有用的话，需要一个citylist.xml和一个web.png 一个小logo...

Anyway, Happy New Year!

*Jan 1, 2016* 
