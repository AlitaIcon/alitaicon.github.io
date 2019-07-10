---
title: flask05-请求与响应
date: 2019-07-10 17:30:08
tags: [flask]
---

## **1.make_response 响应**

make_response()，相当于DJango中的HttpResponse。

**1).返回内容**

```python
from flask import make_response

@app.route('/makeresponse/')
def make_response_function():
    response = make_response('<h2>羞羞哒</h2>')
    return response, 404
```

<!-- more -->

**2).返回页面**

```python
from flask import make_response

@app.route('/makeresponse/')
def make_response_function():
    temp = render_template('hello.html')
    response = make_response(temp)
    return response
```

\>>>注意：make_response 想要返回页面，不能直接写做：make_response('hello.html')，必须用render_template('hello.html')形式。

**3).返回状态码**

\>>>方式一：在make_response()中传入状态码

```python
from flask import make_response

@app.route('/makeresponse/')
def make_response_function():
    temp = render_template('hello.html')
    response = make_response(temp, 200)
    return response
```

\>>>方式二：直接return状态码

```python
from flask import make_response

@app.route('/makeresponse/')
def make_response_function():
    temp = render_template('hello.html')
    response = make_response(temp)
    return response, 200
```

- 将上传文件变成可访问资源：

```python
@app.route('/upload/<filename>')
def get_filename(filename):
    return send_from_directory(os.getcwd()+'/static/', filename)
```



**2.请求**

- 调试发送ajax请求：

```javascript
<p onclick="upload()">点击发送请求</p>
<script>
    function upload() {
        $.ajax({
            type:"POST",
            url:"http://127.0.0.1:5000",
            data:JSON.stringify({"user":"admin"}),
            dataType:"json",
            content_type:"application/json",
            success: function (data) {
                alert('success'+data)
            }
        })
    }
</script>
```

- 构造响应头：

```python
@app.route('/')
def index():
    # 方法1：
    # return json.dumps({"username": "alita"}), {"content-type":"application/json"}
    # 方法2： make_response() Response()
    # r = make_response(json.dumps({"username": "alita"}), {"content-type":"application/json"})
    # r.status = '203'
    # r.content_type = 'application/json'
    # 方法3： jsonify()
    r = jsonify({"username": "阿丽塔"})
    # 修改成 201
    # r.status = '201'
    return r, "202  ok"
```



**3.路由设计思路**

 前后端不分离，模板渲染：1）一个视图函数写一个url
- \# url: 获取所有的项目： /projects.txt, def list_projects
- \# url: 获取单个的项目内容： /project/<id>, def get_project() 模板
- \# url: 修改某个项目的内容： /project_edit/<id>, def edit_project()

前后端分离：rest method:get, post, delete,
- \# /project/<id> 类的视图：def get:单个资源
- \# /projects.txt
- \# DRF
- 熟悉
  file.read([size])从文件读取指定的字节数，如果未给定或为负则读取所有。

## **4.作业**
1、返回所有项目数据，包含：项目id, 项目名称，项目简介，创建时间，修改时间
2、根据id获取单个项目数据。
3、修改单个项目数据。
4、创建项目
5、删除项目

注意： 客户端不管是浏览器还是手机，postman, 只要根据接口文档要求发送了对应的请求，就能操作对应接口的资源。

参考：
作者示例文件，[点击查看](<https://github.com/icon-python/python-study/tree/master/flask06/homework>)

