---
title: flask使用03
date: 2019-07-05 18:36:59
tags: [flask]
---

## **flask参数使用**

- **唯一标识id**

```python
import uuid
uuid.uuid4()
```

- **动态路由**

```python
@app.route('/cases/<int:id>')  # float string path(可包含斜杠/)
def get_case(id):
    id = request.args.get('id')
    return f'{id}'
    return f'{id}'
```



<!-- more -->



- **常用路由参数**

```python
@app.route('/cases/', methods=['POST', 'GET'], endpoint='cases', redirect_to='/', defaults={'id':3})
@app.route('/cases/', methods=['POST', 'GET'], endpoint='cases', defaults={'id':3}) #  endpoint视图函数别名
def get_case(id):
    print(id)
    # return redirect('/')
    return 'hello'
```

**参数：**

- endpoint：视图函数别名
- redirect_to='/'：访问/cases/，直接重定向到/，不会运行试图函数内容
- redirect('/')：重定向到/，会运行试图函数内容
- defaults={'id':3}：设置默认参数，也可以在是视图函数中设置（id=3、id=None）



## **使用MVC思想**

urls.py集中注册路由，views.py存放视图函数，app.py开启服务器

```python
# app.py
from flask import Flask, request, redirect

app = Flask(__name__)
from test.flask03.urls import *  # 防止互相导入时产生问题，需要用时，只引用对方的代码，不运行引用导入代码 
```

```python
# urls.py
from test.flask03.app import app
from test.flask03 import views

app.add_url_rule('/case', view_func=views.cases)
app.add_url_rule('/', view_func=views.index)
```

```python
# views.py
def index():
    return 'index'


def cases():
    return 'cases'
```

**作业：**

请用装饰器注册 3 个路由 ：

1、首页 /、列举所有测试项目 /projects、获取单个测试项目 /project 加id；

2、路由单独放到 views.py 文件，主运行文件放到 app.py 文件；

3、所有 url 不具备 '/' 的兼容性。

参考：
作者示例文件，[点击查看](<https://github.com/icon-python/python-study/tree/master/flask04>)