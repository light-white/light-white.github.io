---
title: 使用Scrapy抓取链家网租房信息
date: 2017-03-15 13:54:13
tags:
- MongoDB
- Python
- Scrapy
---
# 1. Scrapy介绍
Scrapy，Python开发的一个快速,高层次的屏幕抓取和web抓取框架，用于抓取web站点并从页面中提取结构化的数据。  
Scrapy吸引人的地方在于它是一个框架，任何人都可以根据需求方便的修改。  
Scratch，是抓取的意思，这个Python的爬虫框架叫Scrapy，大概也是这个意思吧，就叫它：小刮刮吧。  
Scrapy的官方网站：[https://scrapy.org/](https://scrapy.org/)  
关于Scrapy的安装可以看我之前的博客 [Windows下Python爬虫框架Scrapy安装](http://light-white.me/2017/02/06/Windows%E4%B8%8BPython%E7%88%AC%E8%99%AB%E6%A1%86%E6%9E%B6Scrapy%E5%AE%89%E8%A3%85/)  
# 2. 创建爬虫项目
在控制台执行  
`scrapy startproject myspider`  
目录结构如下:  

    └─myspider
        │  scrapy.cfg
        └─myspider
            │  items.py
            │  middlewares.py
            │  pipelines.py
            │  settings.py
            │  __init__.py
            ├─spiders
            │  │  __init__.py
            │  └─__pycache__
            └─__pycache__
# 3.编写爬虫
在spiders目录下 新建`lianjia_spiders.py`
``` python
# -*- coding: utf-8 -*-
import scrapy
import re

from scrapy.selector import Selector
from scrapy.http import Request
from lianjia.items import LianjiaItem
from scrapy.spiders import CrawlSpider, Rule
from scrapy.linkextractors import LinkExtractor

class LianjiaSpider(CrawlSpider):
    name = 'lianjia'
    allowed_domains = ['lianjia.com']
    rules = (
        Rule(LinkExtractor(allow=(r'^http://bj.lianjia.com/zufang/BJ[0-9]{10}', )), callback='parse_item',follow=True),
        Rule(LinkExtractor(allow=(r'^http://bj.lianjia.com/zufang/[0-9]{12}', )), callback='parse_item',follow=True),
    )

    def start_requests(self):
        yield scrapy.Request('http://bj.lianjia.com/zufang/')
        for i in range(2,101):
            yield scrapy.Request('http://bj.lianjia.com/zufang/pg%d/'%i)

    def parse_item(self, response):
        print("-----parse_item-----")
        selector = Selector(response)
        item = LianjiaItem()
        item['title'] = selector.xpath('/html/body/div[4]/div[1]/div/div[1]/h1/text()').extract_first()
        item['area'] = selector.xpath('/html/body/div[4]/div[2]/div[2]/div[2]/p[1]/text()').extract_first()
        item['price'] = selector.xpath('/html/body/div[4]/div[2]/div[2]/div[1]/span[1]/text()').extract_first()
        regex = selector.xpath('/html/body/script[12]/text()').extract()
        position = re.search('resblockPosition:(.+),',regex.pop())
        item['position'] = position.group()[:-1].split(':')[1]
        item['url'] = response.url
        return item

```
其中我们要编写一个类继承`scrapy.spiders`，不过这里我使用的`CrawlSpider`来做全站爬取  
其中`name`是用来标识一个爬虫，名字唯一  
`allowed_domains`是允许爬虫爬取的域  
其中还应该包含一个`start_urls`作为爬虫爬取的初始页面  
不过这里我使用`start_requests`来生成爬取列表了  
`rules`是`CrawlSpider`中的属性，用来匹配当前页面中的链接  
# 4.items  
items.py中我们编写要抓取的对象信息  
每次爬取页面都会返回一个item  
``` python
import scrapy

class LianjiaItem(scrapy.Item):
    # define the fields for your item here like:
    id = scrapy.Field()        #存入数据库的id
    title = scrapy.Field()     #房屋页面的标题
    area = scrapy.Field()      #房屋的面积
    price = scrapy.Field()     #每月房租
    position = scrapy.Field()  #房屋的经纬度信息
    url = scrapy.Field()       #网址
```
# 5.Selector选择器
我们要从页面中抓取数据，需要解析当前HTML页面  
Scrapy中自带了选择器，可以选择由XPath或CSS表达式指定的部分  
在这里我使用XPath进行界面的解析，在爬虫中可以看到  
`selector.xpath('/html/body/div[4]/div[2]/div[2]/div[2]/p[1]/text()').extract().pop()`  
`.extract_first()`是返回内容List中的第一个
在爬虫中每次都会创建一个item将抓取到的数据存入item 最后函数会返回一个item
# 6.Item pipelines
爬虫中返回的Item，都进入到pipelines进行处理
``` python
import json
import codecs
import pymongo

class LianjiaPipeline(object):

    collection_name = 'lianjia_items'

    def __init__(self, mongo_uri, mongo_db):
        self.mongo_uri = mongo_uri
        self.mongo_db = mongo_db

    @classmethod
    def from_crawler(cls, crawler):
        return cls(
            mongo_uri=crawler.settings.get('MONGO_URI'),
            mongo_db=crawler.settings.get('MONGO_DATABASE')
        )

    def open_spider(self, spider):
        self.client = pymongo.MongoClient(self.mongo_uri)
        self.db = self.client[self.mongo_db]

    def close_spider(self, spider):
        self.client.close()

    def process_item(self, item, spider):
        self.db[self.collection_name].insert(dict(item))
        return item
```
其中必须有`process_item`来处理爬虫返回的item  
`__init__`初始化方法
`from_crawler`用来读取当前爬虫配置  
`open_spider`爬虫打开时调用，在此进行数据库链接  
`close_spider`爬虫关闭时调用，在此关闭数据库链接  
# 总结
在最外层目录外使用命令行执行`scrapy crawl lianjia`  
其中`lianjia`是我在爬虫中设定的`name`  
简单的进行了试验，要关闭Scrapy的`ROBOTSTXT_OBEY = False`  
今天成功的爬取了2620条数据没有被封ip   
github:[lianjia_scrapy](https://github.com/light-white/lianjia_scrapy)
