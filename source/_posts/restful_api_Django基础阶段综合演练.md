---
title: restful_api_Django基础阶段综合演练
date: 2019-10-12 11:00:00
tags: [Django]
---

### 一、必做题

#### 1.课堂中对项目数据增删改查的接口实现中, 有哪些痛点?

<!-- more -->

**提示：**

​	a.写出你认为的所有痛点

 痛点：

- 对模型和json字符串间的正反序列化操作频繁，却不能封装

- 重复代码多，冗余度高

- 请求数据验证过多且重复，处理很麻烦

- 返回状态响应码没有语义化

  

#### 2.通过思维导图总结restful风格的规范

**提示：**

​	a.上传xmind文件或者将思维导图截图上传

 http://naotu.baidu.com/file/2bb8dfdc9ba85f78f6bbb16219403ee9?token=2cb7b44d90649107

 

### 二、选做题

#### 1.使用已学知识实现对Interface数据的增删改查接口

**提示：**

​	a.将程序代码上传

**interfaces/views.py**

```python
import json

from django.http import JsonResponse
# Create your views here.
from django.views import View

from interfaces.models import Interface


class InterfacesList(View):
    def get(self, request):
        interfaces = Interface.objects.all()
        interface_list = []
        for interface in interfaces:
            interface = interface.__dict__
            interface.pop('_state')
            interface_list.append(interface)
        return JsonResponse(interface_list, safe=False)

    def post(self, request):
        json_data = request.body.decode('utf-8')
        model_data = json.loads(json_data, encoding='utf-8')
        interface = Interface.objects.create(**model_data)
        interface_dict = interface.__dict__
        interface_dict.pop('_state')
        return JsonResponse(interface_dict)


class InterfacesDetail(View):
    def get(self, request, pk):
        interface = Interface.objects.get(pk=pk)
        interface_dict = interface.__dict__
        interface_dict.pop('_state')
        return JsonResponse(interface_dict)

    def put(self, request, pk):
        json_data = request.body.decode('utf-8')
        model_data = json.loads(json_data, encoding='utf-8')
        interface = Interface.objects.get(pk=pk)
        interface.name = model_data.get("name", None)
        interface.url = model_data.get("url", None)
        interface.data = model_data.get("data", None)
        interface.projects_id = model_data.get("projects_id", None)
        interface.save()
        interface_dict = interface.__dict__
        interface_dict.pop('_state')
        return JsonResponse(interface_dict, status=201)

    def delete(self, request, pk):
        interface = Interface.objects.get(pk=pk)
        interface.delete()
        return JsonResponse(None, safe=False, status=204)
```

**interfaces/urls.py**

```python
from django.urls import path

from interfaces import views

urlpatterns = [
    path('', views.InterfacesList.as_view()),
    path('<int:pk>/', views.InterfacesDetail.as_view()),
]
```

**learnDjango/urls.py**

```python
from django.urls import path, include

from projects.views import index

urlpatterns = [
    path('projects/', include('projects.urls')),
    path('interfaces/', include('interfaces.urls')),
]
```

