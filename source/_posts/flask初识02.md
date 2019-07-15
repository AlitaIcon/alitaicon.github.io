---
title: flask初识02-使用步骤
date: 2019-07-03 21:35:26
tags: [flask]
---

## flask使用步骤：
- 1.初识化app；
- 2.添加路由，配置视图函数(view function)；
- 3.开启服务

<!-- more -->

```python
import time
from flask import Flask, render_template

# 初始化app
app = Flask(__name__,
            template_folder='html_file',
            static_url_path='/src',   # 以'/'开头，指定url访问静态文件的路径
            static_folder='src'       # 指定返回html文件存放路径
            )
app.config['DEBUG'] = True
app.config['PORT'] = 5002


# 装饰器，--统计流量、钩子
def log_time(f):
    def decorator(*args, **kw):
        st = time.time()
        res = f(*args, **kw)
        print(f'{time.time() - st}')
        return res
    return decorator


#添加路由，url绑定视图函数
@app.route('/hello3')
@app.route('/hello')
@app.route('/')  
@log_time
def home():
    # redis, 保存.txt .yml
    # num += 1
    # 1、接受参数
    # 2、调用对应的函数去处理数据（model）
    # 3、构建响应结果
    # 保存到 src
    return render_template('index.html')
# 数据操作 sql , oracle

# 3、开启web服务器
if __name__ == '__main__':
    app.run(port=app.config['PORT'], debug=app.config['DEBUG'])
```

### 1.使用装饰器注意：
- 其他的装饰器必须要放到 app.route()下面
- 装饰器返回值必须是视图函数的返回值

### 2.配置文件的选择（DEBUG=True）
- .ini .yml 配置、config.py ；
- python数据类型：字典。k:v : 不可变字典 immutable_dict
- 类。不同的属性 setattr getattr

### 3.面试：
**debug的作用？**
调试、测试、开发使用debug
debug为True时，可以访问错误信息；自动重新启动。
生产环境使用uwsgi, nginx。

### 作业：
结合前端知识，实现一个简单的图床工具，用户通过前端上传图片，服务器保存图片，并将可以访问的 url 地址返回给前端。
参考：
作者示例文件，[点击查看](https://github.com/icon-python/python-study/tree/master/flask_class02)

