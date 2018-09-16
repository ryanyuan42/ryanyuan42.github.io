---
layout: post
title: "C# API for Okex and Bitmex"
excerpt: "creating GUI is fun"
categories: articles
tags: [C#]
comments: true
share: true
---

# Python to C#

Recently, I decided to switch Python to C# due to the low speed of Python. Python is great for many tasks and it's also very elegant as a
programming language. But to develop a trading engine, with high concurrency ability, Python might not be the right choice. C# can be very
fast, and more suitable to develop a large project such as a trading engine or a backtesting engine and it's also very handy to create GUI 
applications.  

Of course, Python is still going to help me a lot when I want to test my ideas.  

# C# API for Okex and Bitmex

Recently I found that ccxt api is no longer updated with okex (or I am being stupid), therefore, I think it might be a good time for me to
write its API to help me get more familiar with C#. Right now, I have completed the okex api as a dll and keep developing the trading app
as I want. GUI is a whole new area I've never been using with, so it's still a toy now, but the process is definitely fun.




![GUI recording](https://raw.githubusercontent.com/ryanyuan42/ryanyuan42.github.io/master/images/record1.gif)


# C# API for Bitmex

Recently I've finally finisehd my trading GUI for Bitmex. Basically, GUI handles GET request using websocket and handles POST request using RESTFUL. It's a very good project for me to start getting deeper into C#, especially the multi-threading in C#.  

![GUI recording2](https://raw.githubusercontent.com/ryanyuan42/ryanyuan42.github.io/master/images/Capture2.PNG)
