---
title: flask初识
date: 2019-06-29 14:18:02
tags: [python]
---
## 1.wsgi面试题
**1)什么是wsgi?**
- WSGI（Web Server Gateway Interface，Web服务器网关接口）则是Python语言中1所定义的Web服务器和Web应用程序之间或框架之间的通用接口标准。
- WSGI就是一座桥梁，桥梁的一端称为服务端或网关端，另一端称为应用端或者框架端，WSGI的作用就是在协议之间进行转化。WSGI将Web组件分成了三类：Web 服务器（WSGI Server）、Web中间件（WSGI Middleware）与Web应用程序（WSGI Application）。
- Web Server接收HTTP请求，封装一系列环境变量，按照WSGI接口标准调用注册的WSGI Application，最后将响应返回给客户端。
<!-- more -->
![img](/img/python/flask/wsgi.png)
**2)什么是Flask，有什么优点？**
Flask是一个Web框架，就是提供一个工具，库和技术来允许你构建一个Web应用程序。这个Web应用程序可以是一些Web页面，博客，wiki，基于Web的日里应用或商业网站。
**优点**
Flask属于微框架（micro-framework)这一类别，微架构通常是很小的不依赖外部库的框架。 
- 框架很轻量 
- 更新时依赖小 
- 专注于安全方面的bug

**Flask的依赖**
- Werkzeug 一个WSGI工具包（web服务网关接口（Python Web Server Gateway Interface，缩写为WSGI）是为python语言定义的web服务器和web应用程序或框架之间的一种简单而通用的借口，其他语言也有类似的接口）
- jinja2模板引擎

**3).Django和Flask有什么区别？**
**Flask**
- 轻量级web框架，默认依赖两个外部库：jinja2和Werkzeug WSGI工具
- 适用于做小型网站以及web服务的API，开发大型网站无压力，但架构需要自己设计
- 与关系型数据库的结合不弱于Django，而与非关系型数据库的结合远远优于Django
**Django**
- 重量级web框架，功能齐全，提供一站式解决的思路，能让开发者不用在选择上花费大量时间。
- 自带ORM(Object-Relational Mapping 对象关系映射)和模板引擎，支持jinja等非官方模板引擎。
- 自带ORM使Django和关系型数据库耦合度高，如果要使用非关系型数据库，需要使用第三方库
- 自带数据库管理app
- 成熟，稳定，开发效率高，相对于Flask，Django的整体封闭性比较好，适合做企业级网站的开发。
- python web框架的先驱，第三方库丰富
	
## 2.flask简单使用
```python
from flask import Flask, request, render_template

# 初始化application
# pep8 pep257
app = Flask(
    __name__,
    template_folder='html_file',
)


# 添加路由
@app.route('/')
def index():
    # a = request
    args = request.args
    # 表单
    # file
    # json
    name = args.get('username')
    print(name)
    return render_template('index.html')


# 运行服务器
app.run(port=5000)
```

## 3.作业拓展--装饰器自定义web路由
- **使用方法装饰器完成web路由功能**
```python
#!/usr/bin/env python
# -*- coding: utf-8 -*-
# @Time    : 2019/6/29 13:27
# @Author  : icon
# @File    : func_decorator.py


from wsgiref.simple_server import make_server

URL_FUNC_DICT = dict()


def route(url):
    def set_func(func):
        URL_FUNC_DICT[url] = func
        # def call_func(*args, **kwargs):
        #     return func(*args, **kwargs)
        #
        # return call_func

    return set_func


@route('/')
def index():
    return ['<h1>index</h1>'.encode()]


def application(env, start_response):
    url = env['PATH_INFO']
    start_response('200 OK', [('Content-Type', 'text/html;charset=utf-8')])
    try:
        return URL_FUNC_DICT[url]()
    except Exception as e:
        return ["不存在该url：{}".format(e).encode()]


server = make_server('', 6001, application)
server.serve_forever()
```
- **使用类装饰器完成web路由功能**
```python
#!/usr/bin/env python
# -*- coding: utf-8 -*-
# @Time    : 2019/6/29 13:47
# @Author  : icon
# @File    : class_decorator.py
from wsgiref.simple_server import make_server


class WSGIapp:
    def __init__(self):
        self.routes = {}

    def route(self, url=None):
        def decorator(func):
            self.routes[url] = func
            return func

        return decorator

    def __call__(self, environ, start_response):
        path = environ['PATH_INFO']
        if path in self.routes:
            status = '200 OK'
            response_headers = [('Content-Type', 'text/plain')]
            start_response(status, response_headers)
            return self.routes[path]()
        else:
            status = '404 Not Found'
            response_headers = [('Content-Type', 'text/plain')]
            start_response(status, response_headers)
            return '404 Not Found!'


app = WSGIapp()


@app.route('/')
def index():
    return ['index']


@app.route('/hello')
def hello():
    return ['hello world']


httpd = make_server('', 8888, app)
httpd.serve_forever()
```