---
title: Windows下Python爬虫框架Scrapy安装
date: 2017-02-06 13:57:36
tags:
- Windows
- Python
- Scrapy
---
# 1. Scrapy介绍
Scrapy，Python开发的一个快速,高层次的屏幕抓取和web抓取框架，用于抓取web站点并从页面中提取结构化的数据。  
Scrapy吸引人的地方在于它是一个框架，任何人都可以根据需求方便的修改。  
Scratch，是抓取的意思，这个Python的爬虫框架叫Scrapy，大概也是这个意思吧，就叫它：小刮刮吧。  
Scrapy的官方网站：[https://scrapy.org/](https://scrapy.org/)  
# 2. Python安装
这步不用说了吧  没有Python怎么用Scrapy  
Python的官方网站：[https://www.python.org/](https://www.python.org/)  
我现在使用的是python3.6  
安装完python记得在控制台输入python,进入python命令行 成功
# 3. pip包管理器
pip是python中很好用的包管理器，我们将使用它来安装我们的Scrapy框架  
pip的下载地址：[https://pypi.python.org/pypi/pip](https://pypi.python.org/pypi/pip)    
![](http://7xtgk5.com1.z0.glb.clouddn.com/python_pip.png)  
我们下载下面`.tar.gz`压缩包,下载完成后解压到一个文件夹  
在控制台下输入  

    python setup.py install

安装完成后在控制台下输入pip显示出帮助信息 安装完成
# 4. 安装Scrapy
在控制台下输入  

    pip install scrapy
这时候可能会遇到依赖包安装失败  
这时候我们在这个网站手动下载依赖包
[http://www.lfd.uci.edu/~gohlke/pythonlibs/](http://www.lfd.uci.edu/~gohlke/pythonlibs/)  
例如`lxml‑3.7.2‑cp36‑cp36m‑win32.whl`中`cp36`指的是python3.6版本，后面`win32`指32位程序  
大家根据自己的python版本进行选择
下载到本地之后，使用pip进行安装  

    pip install 文件全名
例如  

    pip install lxml‑3.7.2‑cp36‑cp36m‑win32.whl
# 5. 创建Scrapy项目
在控制台中输入  

    scrapy startproject 项目名
就在当前文件夹下生成scrapy项目
