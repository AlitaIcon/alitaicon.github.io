---
title: webserver
date: 2019-06-28 13:22:12
tags: [python]
---

## 1.selenium-webserver

selenium1通过javascript注入，将指令操作封装为js方法执行

selenium2通过封装一套RESTful web service，开启一个webdriver端口服务，把所有操作指令转换为接口访问封装好的api（调用原生浏览器的方法），api去执行相应的程序处理，从而完成对浏览器的操作。

<!-- more -->	

```python
seleium  访问 /get?url=http://www.baidu.com  ===> 访问一个网站  driver.get('http://www.baidu.com')

/element?id=kw    ==>   元素定位，

import requests
from selenium import webdriver

driver = webdriver.Chrome(port=7000)
url = 'http://www.baidu.com'

driver.get(url)

self.execute(Command.GET, {'url': url})\

Command.GET   /get   {'url': url}

requests.post(url, data=)

url接口 :  '/session/$sessionId/url'

server_url = 'http://localhost:7000/session/{}/url'.format(driver.session_id)
res = requests.post(server_url, json={'url': url})
print(res)
```



## **2.Web应用的本质**

了解了HTTP协议和HTML文档，我们其实就明白了一个Web应用的本质就是：

1. 浏览器发送一个HTTP请求；
2. 服务器收到请求，生成一个HTML文档；
3. 服务器把HTML文档作为HTTP响应的Body发送给浏览器；
4. 浏览器收到HTTP响应，从HTTP Body取出HTML文档并显示。

所以，最简单的Web应用就是先把HTML用文件保存好，用一个现成的HTTP服务器软件，接收用户请求，从文件中读取HTML，返回。Apache、Nginx、Lighttpd等这些常见的静态服务器就是干这件事情的。



### **3.实现一个简单的webserver**

这个接口就是WSGI：Web Server Gateway Interface。

WSGI接口定义非常简单，它只要求Web开发者实现一个函数，就可以响应HTTP请求。我们来看一个最简单的Web版本的“Hello, web!”：

```python
def application(environ, start_response):
    start_response('200 OK', [('Content-Type', 'text/html')])
    return ['<h1>Hello, web!</h1>']
```

上面的application()函数就是符合WSGI标准的一个HTTP处理函数，它接收两个参数：

- environ：一个包含所有HTTP请求信息的dict对象；
- start_response：一个发送HTTP响应的函数。



在application()函数中，调用：

```python
start_response('200 OK', [('Content-Type', 'text/html')]) 
```

就发送了HTTP响应的Header，注意Header只能发送一次，也就是只能调用一次start_response()函数。start_response()函数接收两个参数，一个是HTTP响应码，一个是一组list表示的HTTP Header，每个Header用一个包含两个str的tuple表示。



通常情况下，都应该把Content-Type头发送给浏览器。其他很多常用的HTTP Header也应该发送。

然后，函数的返回值

```
"<h1>Hello, web!</h1>"
```

将作为HTTP响应的Body发送给浏览器。



有了WSGI，我们关心的就是如何从environ这个dict对象拿到HTTP请求信息，然后构造HTML，通过start_response()发送Header，最后返回Body。
- **实现一个简单的webserver代码**
```python
from wsgiref.simple_server import make_server
def app(env, start_response):
    # print(env)
    # env ：获取请求相关数据
    # start_response ：状态码，header
    url = env['PATH_INFO']
    if env['REQUEST_METHOD'].upper() == 'GET':
        if url == '/':
            index_html = "<h1>欢迎来到首页</h1>"
            start_response('200 ok', [('content-type', 'text/html; charset=utf-8'), ])
            return [index_html.encode()]
        if url == '/login':
            login_html = "<h1>登录页</h1>"
            start_response('200 ok', [('content-type', 'text/html; charset=utf-8'), ])
            return [login_html.encode()]
        if url == '/projects':
            projects_html = "<h1>项目列表页</h1>"
            start_response('200 ok', [('content-type', 'text/html; charset=utf-8'), ])
            return [projects_html.encode()]
        else:
            fail_html = "<h1>当前请求错误</h1>"
            start_response('404 error', [('content-type', 'text/html; charset=utf-8'), ])
            return [fail_html.encode()]
    else:
        method_fail_html = "<h1>不支持当前请求方式</h1>"
        start_response('404 error', [('content-type', 'text/html; charset=utf-8'), ])
        return [method_fail_html.encode()]
        

server = make_server('',6001,app)
server.serve_forever()
```

- **实现一个简单的webserver代码优化**
```python
from wsgiref.simple_server import make_server
import json


def index():
    index_html = "<h1>欢迎来到首页</h1>"
    return [index_html.encode()]


def login():
    login_html = "<h1>登录页</h1>"
    return [login_html.encode()]


def projects():
    projects_html = "<h1>项目列表页</h1>"
    return [projects_html.encode()]


def not_found():
    nf = {'msg': '404 not found'}
    return [json.dumps(nf).encode()]


def param(request):
    return request


patterns = {
    '/': index,
    '/login': login,
    '/projects': projects,
}


def app(env, start_response):
    url = env.get('PATH_INFO')
    params = env.get('QUERY_STRING')
    if url not in patterns:
        start_response('404 not found', [('content-type', 'application/json'), ])
        # res = not_found()
        res = param(params)
        return [res.encode()]
    res = patterns.get(url)
    if res is None:
        start_response('404 not found', [('content-type', 'text/html'), ])
        return [b'page not found']
    start_response('200 ok', [('content-type', 'text/html; charset=utf-8'), ])
    return res()


server = make_server('', 6001, app)
server.serve_forever()

```
