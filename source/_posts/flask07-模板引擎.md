---
title: flask07-模板引擎
date: 2019-07-12 10:21:38
tags: [flask]
---

## **全局错误处理**

**1.注册**

- 通过使用 errorhandler() 装饰函数来注册或者稍后使用 register_error_handler() 来注册。 记得当返回响应的时候设置出错代码:

```python
@app.errorhandler(werkzeug.exceptions.BadRequest)
def handle_bad_request(e):
    return 'bad request!', 400

# or, without the decorator
app.register_error_handler(400, handle_bad_request)
```

- 当注册时， werkzeug.exceptions.HTTPException 的子类，如 BadRequest ，和它们的 HTTP 代码是可替换的。 （ BadRequest.code == 400 ）

<!-- more -->

因为 Werkzeug 无法识别非标准 HTTP 代码，因此它们不能被注册。替代地，使用适 当的代码定义一个 HTTPException 子类，注册并 抛出异常类:

```python
class InsufficientStorage(werkzeug.exceptions.HTTPException):
    code = 507
    description = 'Not enough storage space.'

app.register_error_handler(InsuffcientStorage, handle_507)

raise InsufficientStorage()
```

出错处理器可被用于任何异常类的注册，除了 [**HTTPException**](http://werkzeug.pocoo.org/docs/exceptions/#werkzeug.exceptions.HTTPException) 子类或者 HTTP 状态码。 出错处理器可被用于特定类的注册，也可用于一个父类的所有子类的注册。



**2.处理**

在处理请求时，当 Flask 捕捉到一个异常时，它首先根据代码检索。如果该代码没 有注册处理器，它会根据类的继承来查找，确定最合适的注册处理器。如果找不到已 注册的处理器，那么 [**HTTPException**](http://werkzeug.pocoo.org/docs/exceptions/#werkzeug.exceptions.HTTPException) 子类会显示 一个关于代码的通用消息。没有代码的异常会被转化为一个通用的 500 内部服务器 错误。

例如，如果一个 [**ConnectionRefusedError**](https://docs.python.org/3/library/exceptions.html#ConnectionRefusedError) 的实例被抛出，并且一个出错处 理器注册到 [**ConnectionError**](https://docs.python.org/3/library/exceptions.html#ConnectionError) 和 [**ConnectionRefusedError**](https://docs.python.org/3/library/exceptions.html#ConnectionRefusedError) ，那么 会使用更合适的 [**ConnectionRefusedError**](https://docs.python.org/3/library/exceptions.html#ConnectionRefusedError) 来处理异常实例，生成响应。

当一个蓝图在处理抛出异常的请求时，在蓝图中注册的出错处理器优先于在应用中全 局注册的出错处理器。但是，蓝图无法处理 404 路由错误，因为 404 发生的路由级 别还不能检测到蓝图。



**3.实例**

```python
class UserError(Exception):
    pass


# URL 和 视图函数： 绑定关系==》 endpoint 端点
# 全局错误处理
# @app.errorhandler(UserError)
def server_error_500(error):
    # print(error, type(error))  # error:<class '__main__.UserError'>
    return render_template('error_500.html'), 500
    # return 'bad request!', 500
# 采用集中注册
app.register_error_handler(500, server_error_500)

@app.errorhandler(401)
def server_error(error):
    # return render_template('error_401.html')
    return jsonify({"msg": error.__dict__}), 401


@app.route('/')
def index():
    if not request.args.get('username'):
        # return redirect('/login')
        abort(500)
        # abort(401)
        # raise UserError("user error")  # 自定义错误类型
    return redirect(url_for('log'))
```

<br>

## **模板引擎**

- **循环变量**

| **变量**         | **描述**                                     |
| ---------------- | -------------------------------------------- |
| *loop.index*     | 当前循环迭代的次数（从 1 开始）              |
| *loop.index0*    | 当前循环迭代的次数（从 0 开始）              |
| *loop.revindex*  | 到循环结束需要迭代的次数（从 1 开始）        |
| *loop.revindex0* | 到循环结束需要迭代的次数（从 0 开始）        |
| *loop.first*     | 如果是第一次迭代，为 True 。                 |
| *loop.last*      | 如果是最后一次迭代，为 True 。               |
| *loop.length*    | 序列中的项目数。                             |
| *loop.cycle*     | 在一串序列间期取值的辅助函数。见下面的解释。 |

**1.实例**

```python
from flask import Flask, render_template, request, redirect, abort, url_for, jsonify, flash

app = Flask(__name__)
app.secret_key = 'flash secret_key'  # flash使用私钥
@app.route('/projects')
def all_projects():
    projects = [
        {"name": "项目1", "interface_num": 23, "create_time": "2019/1"},
        {"name": "项目2", "interface_num": 24, "create_time": "2019/1"},
        {"name": "项目3", "interface_num": 25, "create_time": "2019/1"},
    ]
    flash("flash test")
    # return jsonify({"msg":[p for p in projects]})
    return render_template('index.html', projects=projects)
```

html文件：

```html
<p>登录页</p>
<p>jinja2演示</p>
{{ projects }}
<p>flash</p>
{{ get_flashed_messages() }}
<p>jinja2循环语句</p>
{% for project in projects %}
    {% set name = project.name %}
    {% if not loop.last %}
        <div>序号：{{ loop.nextitem.name }}, 项目名称：{{ name }}， 接口数量：{{ project['interface_num'] }}，
            创建时间：{{ project['create_time'] }}</div>
    {% else %}
        <div>序号：{{ loop.index }}, 项目名称：{{ name }}， 接口数量：{{ project['interface_num'] }}，
            创建时间：{{ project['create_time'] }}</div>
    {% endif %}
{% endfor %}
```

**2.效果演示：**

![img](/img/python/flask/flask07.png)



## **拓展：**

- [flash()用法](https://zhuanlan.zhihu.com/p/23374605)
- [Flask的app的secret_key的含义和作用](https://www.crifan.com/flask_app_secret_key_meaning_and_usage/)
- [flaks_upload](https://www.cnblogs.com/cwp-bg/p/9971023.html)



### 参考：

作者示例文件，[点击查看](https://github.com/icon-python/python-study/tree/master/flask07)