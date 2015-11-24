---
layout: post
title: "python模拟登录知乎"
excerpt: ""
categories: articles
tags: [python]
comments: true
share: true
---
Python 利用爬虫模拟登录知乎
------------------------------------
python是一个很好用的编程语言，目前自己也还只是一个Python使用者，在这里先写写python爬虫的使用方式吧。

python爬虫的原理其实很简单，把网页的源码扒下来，如果你用的是chrome，只要在一个网页右键-查看源码就可以了。

利用Python去做这么简单的工作的原因就是...
好玩吧=.=

模拟登录知乎，我用的工具为，python, beatifulsoup,requests, chrome后面两个为python中的库，安装方法用pip install在命令行中执行就可以了
{% highlight python %}
pip install requests
pip install bs4
{% endhighlight %}

如果安装出现关于vc的问题，可能是windows系统下的编译问题，可以去http://www.lfd.uci.edu/~gohlke/pythonlibs/这里找一些非官方的python库，好处就在于它们都是编译过的，不会出现问题。（在命令行中cd到文件夹，或者shift+右键-在此处打开命令窗口，pip install 文件名）

安装的细节就不再讲了。

##模拟登录

{% highlight python %}
import requests
import re
from bs4 import BeautifulSoup
{% endhighlight %}

对于网站有几种传输数据的方法，比较简单的是get和post，get就是向网页发送请求，然而网页传回给你数据，post就是把你的数据上传给网页。

###登录所需的数据
要模拟登录，就要观察登录的过程，所以我们可以先前往浏览器打开zhihu.com，进行登录，在chrome浏览器下F12，打开network，它可以帮助你监视网站的活动，记得要勾选Preserve log，以此来观察完整的登录活动。

###进行登录
![网络监控截图](http://i66.tinypic.com/2uszqki.png)
通过监视network的活动我们发现，点击登陆之后，出现了email，以及captcha.git。

可以发现真正登录知乎的url是http://www.zhihu.com/login/email
产生验证码的网站是http://www.zhihu.com/captcha.gif

并且在email中拉到最低，我们可以看到有四个数据传给了这个url，
分别是\_xsrf，password,  remember_me, email

_xsrf我们通过看网站源码可以找到。
 
{% highlight python %}
s = requests.Session()
soup = BeautifulSoup(s.get('http://www.zhihu.com/').content)
_xsrf = soup.find_all(type='hidden')[0]['value']
{% endhighlight %}

BeautifulSoup是用来解析的html的很好的工具，用法不赘述，可以查看文档，也可以查看网上其他爬虫教程。上面这段代码就是搜寻_xsrf的位置，因为在知乎的html中,\_xsrf是有一个标签type = 'hidden'的，所以通过这样的方式找到了\_xsrf

这里使用`requests.Session()`的原因是它能够保存cookie，而不只是一次性的访问。

还要提一下的是，登录网站，或者向网页传输数据的时候，大部分都需要一个表头header，有些网站反感爬虫的访问，但是可以模拟浏览器的表头，以此通过访问。下面是登录知乎的表头，多写一些保险。

Header同样可以在chrome F12之后的Request headers查看到
{% highlight python %}
    headers = {"Accept": "*/*",
           "Accept-Encoding": "gzip,deflate",
           "Accept-Language": "en-US,en;q=0.8,zh-TW;q=0.6,zh;q=0.4",
           "Connection": "keep-alive",
           "Content-Type":" application/x-www-form-urlencoded; charset=UTF-8",
           "User-Agent": "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_10_1) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/38.0.2125.111 Safari/537.36",
           "Referer": "http://www.zhihu.com/"
          }
{% endhighlight %}

贴出完整代码，
{% highlight python %}
    data = {}
    headers = {}
    s =  requests.session()
    
    soup = BeautifulSoup(s.get('http://www.zhihu.com/').content)
    _xsrf = soup.find_all(type='hidden')[0]['value']
    
    
    data = {'_xsrf': _xsrf,
            'email':'你的邮箱',
            'password':'你的密码',
            'rememberme': 'y'}
    
    
    headers = {"Accept": "*/*",
               "Accept-Encoding": "gzip,deflate",
               "Accept-Language": "en-US,en;q=0.8,zh-TW;q=0.6,zh;q=0.4",
               "Connection": "keep-alive",
               "Content-Type":" application/x-www-form-urlencoded; charset=UTF-8",
               "User-Agent": "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_10_1) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/38.0.2125.111 Safari/537.36",
               "Referer": "http://www.zhihu.com/"
              }
    
    captcha_url = 'http://www.zhihu.com/captcha.gif'
    captcha = s.get(captcha_url, stream=True)
    
    f = open('captcha.gif', 'wb')
    for line in captcha.iter_content(10):
        f.write(line)
    f.close()
    
    print u'请输入验证码'
    captcha_str = raw_input()
    data['captcha'] = captcha_str
    
    r = s.post(url='http://www.zhihu.com/login/email',
           data = data,
           headers = headers)
{% endhighlight %}

解释一下，由于以下代码运行，
{% highlight python %}
    f = open('captcha.gif', 'wb')
    for line in captcha.iter_content(10):
        f.write(line)
    f.close()
{% endhighlight %}
会在你当前目录下产生一个captcha.gif，把验证码输进去之后就可以登录成功了。

###如何判断是否登录成功了呢？

最后我们通过
{% highlight python %}
r = s.post(url='http://www.zhihu.com/login/email',
           data = data,
           headers = headers)
{% endhighlight %}
向网页发送了我们要登录的请求，网页就会把根据传输过去的数据把内容返还给r。

所以我们要判断是否成功登录很简单，
{% highlight python %}
print r.content
{% endhighlight %}
结果是


    {"r":0,
     "msg": "\u767b\u9646\u6210\u529f"
    }

这段字符应该是unicode，我们用print打印一下看看

{% highlight python %}
print u'\u767b\u9646\u6210\u529f'
{% endhighlight %}
结果是
```
登陆成功
```
至此我们模拟登录知乎已经成功了。

##到底有什么用？

其实没多大用处，模拟登录成功之后，我们就可以用我们之前定义的s来胡作非为了。

这是你再用s去get网站，就是登录进入知乎后的html了。

比如说，

    res = s.get('http://www.zhihu.com/people/jean-grean/followees', headers = headers, cookies = r.cookies)
    soup = BeautifulSoup(res.content)
    tag_with_followees = soup.find_all(re.compile(r'a'), class_ = 'zm-item-link-avatar')
    for n in range(len(tag_with_followees)):
        print tag_with_followees[n]['title']

试试看出现什么

以上的内容都要感谢知乎中的[怎样用python设计一个爬虫模拟登陆知乎?](http://www.zhihu.com/question/29925879)答案，给我了许多帮助，包括如何处理验证码，以及如何使用requests.Session来保存cookie, 如果用python自带的urllib2要保存cookie的话要麻烦许多，还要建立cookiejar来保存。

问题下面的代码已经不能成功登录知乎了，但是还是受益良多。

