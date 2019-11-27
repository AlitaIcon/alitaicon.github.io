---
title: flask使用04--可插拔视图函数View
date: 2019-07-06 15:40:40
tags: [flask]
---

- **Class-View**

<!-- more -->

```python
import time
from flask import Flask, request
from flask.views import View

app = Flask(__name__)


def log_time(f):
    def decorator(*args, **kw):
        st = time.time()
        res = f(*args, **kw)
        print(f'{time.time() - st}')
        return res
    return decorator


class ProjectView(View):
    methods = ["GET", "POST"]
    decorators = [log_time, ]

    def get(self):
        return 'get'

    def post(self):
        return 'post'

    def dispatch_request(self):  # 分配请求
        dispatch_pattern = {'GET': self.get, 'POST': self.post}
        return dispatch_pattern.get(request.method)()


f = ProjectView.as_view('project')
app.add_url_rule('/project', view_func=f)

if __name__ == '__main__':
    app.run(debug=True)
```
- **method-View**
```python
import time
from flask import Flask
from flask.views import MethodView

app = Flask(__name__)


def log_time(f):
    def decorator(*args, **kw):
        st = time.time()
        res = f(*args, **kw)
        print(f'{time.time() - st}')
        return res
    return decorator


class MyMethodView(MethodView):
    methods = ["GET", "POST"]
    decorators = [log_time,]

    def get(self):
        return 'get'

    def post(self):
        return 'post'


f = MyMethodView.as_view('user')
app.add_url_rule('/project', view_func=f, methods=["GET", "POST", "PUT", "DELETE"])

if __name__ == '__main__':
    app.run(debug=True)
```
- **flask常见处理请求参数**
```python
@app.route('/', methods=['GET', 'POST'])
def index():
    a = request
    # 默认get 请求 # request, 请求相关，flask,app, wsgi: 环境变量
    get_data = request.args
    form_data = request.form  # 表单
    json_data = request.json  # json # header {appliction/json}
    file_data = request.files  # file
    # AJAX, json, 请求投，XHR:
    return 'index'
```
**1.通过requests发送请求**
```python
import requests
res = requests.request(method='post', url='http://127.0.0.1:5000/', json={'username':'admin'})
```
**2.通过打断点查看请求参数**
![img](/img/python/flask/flask-request.png)


参考：
查看源代码，[点击查看](<https://github.com/icon-python/python-study/tree/master/flask05>)



