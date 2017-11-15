---
title: Django 1.10 静态资源配置
date: 2017-04-07 13:19:59
tags:
- Python
- Django
---
# 1. Django项目结构

    Project/
    │  db.sqlite3
    │  happy_uwsgi.ini
    │  manage.py
    ├─Project
    │  │  settings.py
    │  │  urls.py
    │  │  wsgi.py
    │  │  __init__.py
    ├─index
    │  │  admin.py
    │  │  apps.py
    │  │  models.py
    │  │  tests.py
    │  │  urls.py
    │  │  views.py
    │  │  __init__.py
    │  ├─migrations
    │  ├─static
    │  │  ├─css
    │  │  ├─js
    │  ├─templates
    │  │      index.html
    └─templates
Project是我的项目名，index是我的一个app名  
首先，要在我们的app目录下创建两个文件夹`templates`和`static`  
`templates`用来存放我们的模板html，`static`用来存放我们的静态资源

# 2. 修改settings.py  
在配置文件的最后，我们会找到`STATIC_URL = '/static/'`  
在它的下一行我们添加一行`STATIC_ROOT = os.path.join(BASE_DIR, 'static')`,然后保存

# 3. 修改urls.py
在项目名`Project`下的urls.py中导入两个库  
``` python
from django.conf import settings
from django.conf.urls.static import static
```
在末尾追加`+ static(settings.STATIC_URL, document_root = settings.STATIC_ROOT)`

完整的urls.py如下
```python
from django.conf.urls import url,include
from django.contrib import admin
from django.conf import settings
from django.conf.urls.static import static

urlpatterns = [
    url(r'^$', include('index.urls')),
    url(r'^admin/', admin.site.urls),
] + static(settings.STATIC_URL, document_root = settings.STATIC_ROOT)
```
# 4. 修改静态引用
在网页中所有的静态文件引用前要加上`/static/`
```html
<link rel="stylesheet" href="/static/css/style.css" type="text/css" />
<script src="/static/js/index.js"></script>
```
