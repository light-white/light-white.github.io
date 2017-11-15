---
title: Django+uWSGI+Nginx部署Django
date: 2017-04-07 15:58:25
tags:
- Python
- Django
- Nginx
- uWSGI
---
踩了两天的坑终于把Django配好了
在这里，我会介绍如何使用uWSGI+Nginx做链接来部署Django项目
# 环境介绍
- Ubuntu 14.04.4 LTS
- Python 3.4.3
- uWSGI 2.0.15
- Nginx 1.4.6
- Django 1.11

# 安装uWSGI
Ubuntu下已经有了python3的版本我们就不多说了  
使用python3中的pip3安装uWSGI  
```shell
pip3 install uWSGI
```
## 测试uWSGI
新建一个测试文件`test.py`
```python
#test.py
def application(env, start_response):
    start_response('200 OK', [('Content-Type','text/html')])
    return [b"Hello World"]
```
运行uWSGI  
```shell
uwsgi --http :8000 --wsgi-file test.py
```
访问`127.0.0.1:8000`
网页上显示`Hello World`，测试成功

# 安装Nginx
使用apt-get安装Nginx
```shell
sudo apt-get install nginx
```
## 配置Nginx
Nginx配置文件在`/etc/nginx/nginx.conf`
其中最后两行引入了配置文件
```
include /etc/nginx/conf.d/*.conf;
include /etc/nginx/sites-enabled/*;
```
在`/etc/nginx/sites-enabled`文件夹下有一个`default`
将默认的`80`端口改为一个不常用的端口  这里我改成了8099  
```
server {
        listen 8099 default_server;
        listen [::]:8099 default_server ipv6only=on;

        root /usr/share/nginx/html;
        index index.html index.htm;

        server_name localhost;

        location / {
            try_files $uri $uri/ =404;
        }
}
```
# 书写配置文件
在我们的Django项目下要创建一个uwsgi的配置文件，一个Nginx的配置文件还需要一个uwsgi_params文件
,该文件可以从[uwsgi_params](https://github.com/nginx/nginx/blob/master/conf/uwsgi_params)下载
## uwsgi配置文件
在django项目目录下创建demo_uwsgi.ini  
```
[uwsgi]
# 对外提供 http 服务的端口
http = :9000

#the local unix socket file than commnuincate to Nginx   用于和 nginx 进行数据交互的端口
socket = 127.0.0.1:8001

# the base directory (full path)  django 程序的主目录
chdir = /home/django/demo

# Django's wsgi file
wsgi-file = demo/wsgi.py

# maximum number of worker processes
processes = 4

#thread numbers startched in each worker process
threads = 2

#monitor uwsgi status  通过该端口可以监控 uwsgi 的负载情况
stats = 127.0.0.1:9191

# clear environment on exit
vacuum          = true

# 后台运行,并输出日志
daemonize = /var/log/uwsgi.log
```
启动uwsgi`uwsgi demo_uwsgi.ini`
## Nginx配置文件
在django项目目录下创建`django-nginx.conf`  
```
# the upstream component nginx needs to connect to
upstream django {
    # server unix:///path/to/your/mysite/mysite.sock; # for a file socket
    server 127.0.0.1:8001; # 与uwsgi通信端口
}

# configuration of the server
server {
    # 对外监听端口
    listen      80;
    # the domain name it will serve for
    server_name 112.74.58.180; # substitute your machine's IP address or FQDN
    charset     utf-8;

    # max upload size
    client_max_body_size 75M;   # adjust to taste

    # Django media
    location /media  {
        alias /path/to/your/mysite/media;
    }

    location /static {
        alias /home/django/demo/static;
    }

    # Finally, send all non-media requests to the Django server.
    location / {
        uwsgi_pass  django;
        include     /home/django/demo/uwsgi_params;
    }
}
```
写完配置文件，要将配置文件链接到Nginx下  
```
sudo ln -s /home/django/demo/django_nginx.conf /etc/nginx/sites-enabled/
```
## Django 配置
在django项目下的settings.py中添加
`ALLOWED_HOSTS = ['本机ip','127.0.0.1','localhost']`
关于静态文件的处理方法请看我的前一篇博客 {% post_link Django1.10静态文件 %}
然后在项目目录下执行
```
python3 manage.py collectstatic
```
# 最终测试
执行
```
nginx -s reload
uwsgi --ini /home/django/demo/demo_uwsgi.ini
```
## 总结
其中主要踩坑的地方有
1. Nginx的默认端口修改
2. uwsgi配置中http端口并不是访问用的端口
3. Django静态文件要用`python3 manage.py collectstatic`收集
