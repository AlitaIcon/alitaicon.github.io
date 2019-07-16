---
title: flask07-可以访问类似的全局变量和函数
date: 2019-07-13 14:07:12
tags: [flask]
---
## **可以访问类似的全局变量和函数**
**1.flash消息闪现**

```python
@app.route('/flash')
def just_flash():
    flash('I am flash, who is looking for me?')
    return redirect(url_for('projects'))
```

```html
<p><a href="{{ url_for('just_flash') }}">flash</a></p>
{% for message in get_flashed_messages() %}
    <div>{{ message }}</div>
{% endfor %}
```

使用flash()函数发送的消息会存储在session中，在模板中使用全局函数get_flashed_messages()获取显示，调用get_flashed_messages()，session中的所有消息会被移除。

**2.内置上下文变量.**

- g
- request
- session
- config
- url_for
```python
@app.route('/login/<username>', endpoint='log')
def login(username):
    session['user'] = username
    return render_template('index.html')
```

<!-- more -->

```html
{% if session.user %}
    {% set user = session.user %}
    已登录
    {{ user }}
{% else %}
    未登录
{% endif %}
<p>全局变量</p>
<p>g:</p>
<p>{{ g }}</p>
<p>session:</p>
<p>{{ session }}</p>
<p>request:</p>
<p>{{ request }}</p>
<p>template_test</p>
```
![img](/img/python/flask/gsrc.png)
**3.函数**
1).自定义过滤器

```python
# @app.template_filter('s_time')
def strf_time(timestamp):
    return datetime.fromtimestamp(timestamp)
app.add_template_filter(strf_time,'s_time' )
```
```html
{% for project in projects %}
    {% set name = project.name %}
    {% if not loop.last %}
        <div>序号：{{ loop.nextitem.name }}, 项目名称：{{ name | capitalize |upper }}， 接口数量：{{ project['interface_num'] }}，
            创建时间：{{ project['create_time'] | s_time }}</div>
    {% else %}
        <div>序号：{{ loop.index }}, 项目名称：{{ name }}， 接口数量：{{ project['interface_num'] }}，
            创建时间：{{ get_n(project['create_time']) }}</div>
    {% endif %}
{% endfor %}
```
![img](/img/python/flask/filter.png)
2).template_test
```python
@app.template_test()
def jsoned(my_str):
    try:
        json.loads(my_str)
        return True
    except:
        return False
```
```html
<p>
    {% if my_json is jsoned %}
        {{ my_json }}
    {% else %}
         not a json
    {% endif %}
</p>
```
3).自定义上下文变量和函数
```python
@app.context_processor
def add_ctc():
    def get_now(timestamp):
        return datetime.fromtimestamp(timestamp)

    return {'get_n': get_now, 'temp_var': 20}
```
```html
<div>序号：{{ loop.index }}, 项目名称：{{ name }}， 接口数量：{{ project['interface_num'] }}，
    创建时间：{{ get_n(project['create_time']) }}</div>
<p>
{#    {{ get_n() }}<br>#}
    {{ temp_var }}
</p>
```
![img](/img/python/flask/context.png)
上下文处理器可以修饰多个函数，也就是我们可以定义多个上下文环境变量和函数。
4).内置过滤器
```python
{% if name %}
  <h1>Hello {{ name | upper | truncate(3, True) }}!</h1>
{% else %}
  <h1>Hello World!</h1>
{% endif %}


{# 字符串操作 #}
{# 当变量未定义时，显示默认字符串 #}
<p>{{ name | default('No name', true) }}</p>

{# 单词首字母大写 #}
<p>{{ 'hello' | capitalize }}</p>

{# 单词全小写 #}
<p>{{ 'XML' | lower }}</p>

{# 去除字符串前后的空白字符 #}
<p>{{ '  hello  ' | trim }}</p>

{# 字符串反转，返回"olleh" #}
<p>{{ 'hello' | reverse }}</p>

{# 格式化输出，返回"Number is 2" #}
<p>{{ '%s is %d' | format("Number", 2) }}</p>

{# 关闭HTML自动转义 #}
<p>{{ '<em>name</em>' | safe }}</p>

{% autoescape false %}
{# HTML转义，即使autoescape关了也转义 #}
<p>{{ '<em>name</em>' | escape }}</p>
{% endautoescape %}


{# 数值操作 #}
{# 四舍五入取整，返回13.0 #}
<p>{{ 12.8888 | round }}</p>

{# 向下截取到小数点后2位，返回12.88 #}
<p>{{ 12.8888 | round(2, 'floor') }}</p>

{# 绝对值，返回12 #}
<p>{{ -12 | abs }}</p>

{# 列表操作 #}
{# 取第一个元素 #}
<p>{{ [1,2,3,4,5] | first }}</p>

{# 取最后一个元素 #}
<p>{{ [1,2,3,4,5] | last }}</p>

{# 返回列表长度 #}
<p>{{ [1,2,3,4,5] | length }}</p>

{# 列表求和 #}
<p>{{ [1,2,3,4,5] | sum }}</p>

{# 列表排序，默认为升序 #}
<p>{{ [3,2,1,5,4] | sort }}</p>

{# 合并为字符串，返回"1 | 2 | 3 | 4 | 5" #}
<p>{{ [1,2,3,4,5] | join(' | ') }}</p>

{# 列表中所有元素都全大写。这里可以用upper,lower，但capitalize无效 #}
<p>{{ ['tom','bob','ada'] | upper }}</p>

{# 字典列表操作 #}
{% set users=[{'name':'Tom','gender':'M','age':20},
              {'name':'John','gender':'M','age':18},
              {'name':'Mary','gender':'F','age':24},
              {'name':'Bob','gender':'M','age':31},
              {'name':'Lisa','gender':'F','age':19}]
%}

{# 按指定字段排序，这里设reverse为true使其按降序排 #}
<ul>
{% for user in users | sort(attribute='age', reverse=true) %}
     <li>{{ user.name }}, {{ user.age }}</li>
{% endfor %}
</ul>

{# 列表分组，每组是一个子列表，组名就是分组项的值 #}
<ul>
{% for group in users|groupby('gender') %}
    <li>{{ group.grouper }}<ul>
    {% for user in group.list %}
        <li>{{ user.name }}</li>
    {% endfor %}</ul></li>
{% endfor %}
</ul>

{# 取字典中的某一项组成列表，再将其连接起来 #}
<p>{{ users | map(attribute='name') | join(', ') }}</p>

{# tojson #}
<script type="text/javascript">
var users = {{ users | tojson | safe }};
console.log(users[0].name);
</script>

{% filter upper %}
    This is a Flask Jinja2 introduction.
{% endfilter %}

{# 自定义过滤器 #}
{# 返回[1,3,5] #}
<p>{{ [1,2,3,4,5] | double_step }}</p>

{# 返回[2,3,4] #}
<p>{{ [1,2,3,4,5] | sub(1,4) }}</p>
```

### 参考：
相关文章：http://www.bjhee.com/jinja2-filter.html
jinja2官网全部过滤器：http://docs.jinkan.org/docs/jinja2/templates.html#builtin-tests
作者示例文件，[点击查看](https://github.com/icon-python/python-study/tree/master/flask08)








