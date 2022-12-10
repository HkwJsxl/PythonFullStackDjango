# Django

- 每一个文件夹都是一个Django项目

- Django1Urls：Django路由

- Django2Views：Django视图

- Django3Templates：Django模板

- Django4ORM：DjangoORM

- Django5Others：Django其他功能

## Django常用命令

### 基本命令

| **任务**                             | **命令**                                                     |
| ------------------------------------ | ------------------------------------------------------------ |
| 创建新项目                           | django-admin.py startproject project_name(注意: windows系统下请用django-admin startproject xxx) |
| 创建新应用                           | python manage.py startapp app_name (注意: 你需要先cd进入创建的项目文件夹) |
| 检测模型变化，生成新的数据库迁移文件 | python manage.py makemigrations [app_name](注意: app名字可选。如果一个项目包含多个app，而你只更改了其中一个app的模型，建议后面加入具体的app名) |
| 同步数据库与模型                     | python manage.py migrate                                     |
| 启动服务器                           | python manage.py runserver                                   |
| 创建超级用户                         | python manage.py createsuperuser                             |
| 修改用户密码                         | python manage.py changepassword username                     |
| 打开交互终端                         | python manage.py shellpython manage.py dbshell(数据库交互)   |
| 查看当前版本                         | python manage.py version                                     |

### 其它命令

| **命令**                               | **用途**                                                     |
| -------------------------------------- | ------------------------------------------------------------ |
| python manage.py flush                 | 清空数据库内容，只留下空表                                   |
| python manage.py test                  | 开始测试                                                     |
| python manage.py collectstatic         | 搜集静态文件                                                 |
| python manage.py createcachetable      | 创建缓存表                                                   |
| python manage.py check                 | 检测项目有没有问题                                           |
| python manage.py inspectdb [table]     | 根据已有数据库反向生成django模型。你可以选择数据表名字       |
| python manage.py makemessages          | 搜集所有的messages，可以生成指定文件格式如xml文件，供后期翻译 |
| python manage.py sendtestemail [email] | 发送测试邮件                                                 |
| python manage.py showmigrations        | 显示所有数据库迁移文件                                       |

## Django常用模块和语句

### 测试脚本

~~~python
# 脚本代码无论是写在应用下的tests.py还是自己单独开设py文件都可以
# 测试环境的准备 去manage.py中拷贝前四行代码 然后自己写两行
import os
import sys
def main():
    os.environ.setdefault('DJANGO_SETTINGS_MODULE', 'Django5Others.settings')
    import django
    django.setup()
~~~

### base

~~~python
from django.contrib import admin
from django.urls import path, re_path, include
    path('user/', include('user.urls'))  # 路由分发
from django.urls import register_converter
    register_converter(路由转换器的类名, '调用别名')
from django.shortcuts import render, HttpResponse, redirect
    return HttpResponse(data, status=404, content_type='application/json')
    return render(request, 'user/index.html', locals())
    return redirect('/user/')
from django.http import JsonResponse
    JsonResponse(dic, json_dumps_params={'ensure_ascii': False})
    JsonResponse(lst, safe=False)  # 序列化非字典数据类型
from django.views.decorators.http import require_http_methods
    @require_http_methods(["POST"])  # 只接收POST请求
from django.template.loader import get_template
    get_template()  # 获取模板文件
"""自定义过滤器"""
from django import template
    register = template.Library()
    @register.filter('hide_phone')
"""标签"""
{% include 'tem/advertisement.html' %}
{% extends 'tem/base.html' %}
{% block title %}
    {{ block.super }}  # 父级内容
    <title>base</title>
{% endblock title %}
"""反向解析"""
<a href="{% url 'base' %}">Base</a>
from django.urls import reverse
def base(request):
    base_page = reverse('base')
    print('reverse---', base_page)
    return render(request, 'tem/base.html')

"""序列化Queryset"""
from django.core import serializers
ret = models.Book.objects.all()
data = serializers.serialize("json", ret)

"""命名空间：namespace"""
urlpatterns = [
    path('author/', include(('author.urls', 'author'), namespace='author')),
]
{% url 'author:index' %}  # 使用

# 允许转义
from django.utils.safestring import mark_safe
res = mark_safe('<h1>xxx</h1>')
<p>转义:{{ ooo|safe }}</p>
<p>转义:{{ res }}</p>

# 查看内部sql语句的方式1(方式2setting.py配置)
queryset对象.query
~~~

### 静态文件配置

~~~python
# 我们将网站所使用的静态文件默认都放在static文件夹下
-static
    --js
    --css
    --img
    其他第三方文件
STATIC_URL = '/static/'  # 访问静态文件的令牌
STATICFILES_DIRS = [
    os.path.join(BASE_DIR,'static'),
]
# 静态文件动态解析
{% load static %}
<link rel="stylesheet" href="{% static 'xxx.css' %}">
<script src="{% static 'xxx.js' %}"></script>
~~~

### ORM

~~~python
from django.db import models
    class Stu(models.Model)  # 模型表
# 高阶查询
from django.db.models import F, Q, Max, Min, Sum, Count, Avg
~~~

### Setting.py

~~~python
"""自动加斜杠"""
APPEND_SLASH = True（默认）
"""数据库配置"""
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.mysql',
        'HOST': 'localhost',
        'PORT': '3306',
        'USER': 'root',
        'PASSWORD': '123456',
        'NAME': 'orm',
    }
}
# __init__.py
import pymysql
pymysql.install_as_MySQLdb()
"""ORM打印SQL"""
LOGGING = {
    'version': 1,
    'disable_existing_loggers': False,
    'handlers': {
        'console':{
            'level':'DEBUG',
            'class':'logging.StreamHandler',
        },
    },
    'loggers': {
        'django.db.backends': {
            'handlers': ['console'],
            'propagate': True,
            'level':'DEBUG',
        },
    }
}
~~~

## AJAX

~~~python
"""坑：处理完Ajax请求后，可能要刷新页面才能跳转到新的页面"""
"""基础语法"""
$.ajax({
    url: '{% url "register" %}',
    type: 'post',
    data: {
        'csrfmiddlewaretoken': '{{ csrf_token }}'
    },
    success: function (res) {
        
    }
})
"""发送文件"""
"""
ajax发送文件需要借助于js内置对象FormData
"""
<script>
    // 点击按钮朝后端发送普通键值对和文件数据
    $('#btn_submit').on('click',function () {
        // 1 需要先利用FormData内置对象
        let formDateObj = new FormData();
        // 2 添加普通的键值对
        formDateObj.append('username',$('#d1').val());
        formDateObj.append('password',$('#d2').val());
        // 3 添加文件对象
        formDateObj.append('myfile',$('#d3')[0].files[0])
        // 4 将对象基于ajax发送给后端
        $.ajax({
            url:'',
            type:'post',
            data:formDateObj,  // 直接将对象放在data后面即可
            // ajax发送文件必须要指定的两个参数
            contentType:false,  // 不需使用任何编码 django后端能够自动识别formdata对象
            processData:false,  // 告诉你的浏览器不要对你的数据进行任何处理
            success:function (args) {
            }
        })
    })
</script>
"""
总结:
	1.需要利用内置对象FormData
    // 2 添加普通的键值对
        formDateObj.append('username',$('#d1').val());
        formDateObj.append('password',$('#d2').val());
        // 3 添加文件对象
        formDateObj.append('myfile',$('#d3')[0].files[0])
	2.需要指定两个关键性的参数
    	contentType:false,  // 不需使用任何编码 django后端能够自动识别formdata对象
        processData:false,  // 告诉你的浏览器不要对你的数据进行任何处理
	3.django后端能够直接识别到formdata对象并且能够将内部的普通键值自动解析并封装到request.POST中 文件数据自动解析并封装到request.FILES中
"""
~~~

## 中间件

~~~python
MIDDLEWARE = [
    # 
    'django.middleware.security.SecurityMiddleware',
    # 
    'django.contrib.sessions.middleware.SessionMiddleware',
    # 
    'django.middleware.common.CommonMiddleware',
    # 
    'django.middleware.csrf.CsrfViewMiddleware',
    # 
    'django.contrib.auth.middleware.AuthenticationMiddleware',
    # 
    'django.contrib.messages.middleware.MessageMiddleware',
    # 
    'django.middleware.clickjacking.XFrameOptionsMiddleware',
]
~~~

## 扩展

~~~python
Jquery插件：https://www.jq22.com/
AJAX弹窗：https://sweetalert2.github.io/
~~~


