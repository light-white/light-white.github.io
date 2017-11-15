---
title: Windows环境下Mongodb安装及配置
date: 2016-10-27 18:32:32
tags:
- MongoDB
- Windows
---

# 1. MongoDB介绍
MongoDB 是由C++语言编写的，是一个基于分布式文件存储的开源数据库系统。  
在高负载的情况下，添加更多的节点，可以保证服务器性能。  
MongoDB 旨在为WEB应用提供可扩展的高性能数据存储解决方案。  
MongoDB 将数据存储为一个文档，数据结构由键值(key=>value)对组成。  
MongoDB 文档类似于 JSON 对象。字段值可以包含其他文档，数组及文档数组。  
MongoDB的官方网站： [https://www.mongodb.com/](https://www.mongodb.com/ )
# 2. 下载安装包
打开MongoDB官方网站点击Download,下载合适的msi安装包  
我这里下载的3.2.1版本的[mongodb-win32-x86_64-2008plus-ssl-3.2.10-signed.msi](https://www.mongodb.com/download-center?jmp=nav#community)  

# 3.安装MongoDB
双击安装包，开始安装，同意协议  
注意这一步，选择这一项  
![](http://7xtgk5.com1.z0.glb.clouddn.com//winMongo/Mongodb%20install.png)  
点击上面的会直接进入安装  
![](http://7xtgk5.com1.z0.glb.clouddn.com/winMongo/Mongodb%20location%20.png)  
我们在这里修改MongoDB的安装路径，这里我选择了安装到D盘  
接下来就直接安装就可以了
## 3.1配置MongoDB
安装完成后，我们需要对MongoDB进行一系列的配置  
最基本的我们要为MongoDB指定数据库的存放路径  
我在我的MongoDB目录D:\MongoDB下新建一个data目录
在data目录下创建db目录作为MongoDB数据库的存放路径  
创建Logs目录作为日志文件夹  
在命令行下，我们进入之前安装的MongoDB的bin目录下，执行  

	mongod --dbpath d:\MongoDB\data\db  
最后显示

	2016-10-27T09:23:20.055+0800 I NETWORK  [initandlisten] waiting for connections on port 27017  
表示启动成功，我们可以在浏览器输入[http://localhost:27017/](http://localhost:27017/ )
![](http://7xtgk5.com1.z0.glb.clouddn.com/winMongo/Mongodb%20port.png)  
现在就可以算初步配置完成

# 4.MongoDB安装Windows服务
我们要将MongoDB安装为Windows服务，首先要通过管理员权限启动cmd,进入bin目录下，执行

	mongod --dbpath "d:\MongoDB\data\db" --logpath "d:\MongoDB\data\logs\MongoDB.log" --logappend --install
--dbpath  设置数据库路径  
--logpath 设置log日志文件  
--logappend 使用追加的方式写日志  

接下来我们就可以使用

	net start MongoDB
来启动服务

如果我们想删除MongoDB服务的话，在bin目录下执行

	mongod.exe --remove --serviceName "MongoDB"
就可以删除MongoDB的服务了


# 5.编写配置文件
我们已经可以运行MongoDB服务了  
但是敲这么长的命令也很麻烦，这时候我们可以编写一个MongoDB的配置文件，使用配置文件来安装MongoDB服务  
我们继续在\data目录下新建etc目录，在etc目录下创建mongod.conf  
我门打开mongod.conf  书写配置信息  目前MongoDB配置文件语法使用[YAML](http://www.yaml.org/)

	systemLog:
	    destination: file
	    path: D:\MongoDB\data\logs\MongoDB.log #log日志文件路径
	    logAppend : true  #log日志追加模式，不开启这个的话，重启服务会覆盖上次的log文件
	net:
	    port: 27017 #端口号 默认为27017
	storage:
	    dbPath: D:\MongoDB\data\db #数据库路径

然后使用配置文件启动MongoDB

	mongod --config d:\MongoDB\data\etc\mongod.conf
打开[http://localhost:27017/](http://localhost:27017/ )查看是否启动成功

详细的配置文件写法请参照[Configuration File Options](https://docs.mongodb.com/manual/reference/configuration-options/)

我门现在可以使用config来创建MongoDB服务，使用管理员权限启动cmd,进入\bin目录

	mongod --config d:\MongoDB\data\etc\mongod.conf --install
右键我的电脑->管理->服务和应用程序->服务  
![](http://7xtgk5.com1.z0.glb.clouddn.com/winMongo/Mongodb%20service.png)  
可以看到我们的MongoDB服务已经运行  

到此MongoDB的安装就已经结束，为大家推荐一款MongoDB可视化工具[mongobooster](http://mongobooster.com/)  
![](http://7xtgk5.com1.z0.glb.clouddn.com/winMongo/Mongobooster.com.png)  
![](http://7xtgk5.com1.z0.glb.clouddn.com/winMongo/Mongobooster.png)
