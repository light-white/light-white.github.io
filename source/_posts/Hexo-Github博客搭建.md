---
title: Hexo+Github博客搭建
date: 2016-04-27 16:50:26
tags:
- Hexo
- Github
- blog
---

# 1. Hexo介绍
Hexo 是一个简单地、轻量地、基于Node的一个静态博客框架。通过Hexo我们可以快速创建自己的博客，仅需要几条命令就可以完成。  
发布时，Hexo可以部署在自己的Node服务器上面，也可以部署github上面。对于个人用户来说，部署在github上好处颇多，不仅可以省去服务器的成本，还可以减少各种系统运维的麻烦事(系统管理、备份、网络)。所以，基于github的个人站点，正在开始流行起来….  
Hexo的官方网站： [http://hexo.io/](https://hexo.io/ )也是基于Github构建的网站。
# 2. 配置环境
安装Node（必须）  
作用：用来生成静态页面的  
到Node.js官网下载相应平台的最新版本，一路安装即可。  
安装Git（必须）  
作用：把本地的hexo内容提交到github上去.  
安装sublime 用来编辑文件  
在Github上new repository   
![](http://7xtgk5.com1.z0.glb.clouddn.com/16-4-27/1924680.jpg)

格式为your_user_name.github.io

# 3.安装hexo
打开git shell 利用 npm 命令即可安装  
npm install -g hexo  
这里我们可以使用hexo version查看一下版本  
我现在用的版本是3.2.0 本文也是基于Hexo3.2.0所写  
后续变更请查看官方文档，Hexo的官方网站： [http://hexo.io/](https://hexo.io/ )  
## 3.1创建hexo文件夹
安装完成后，在你喜爱的文件夹下（如D:\hexo）Hexo 即会自动在目标文件夹建立网站所需要的所有文件。  
hexo init your_user_name.github.io  
安装好后，我们就可以使用Hexo创建项目了。  
cd your_user_name.github.io  
进入目录，并启动Hexo服务器。  
Hexo server
这时端口4000被打开了，我们能过浏览器打开地址，[http://localhost:4000/](http://localhost:4000/ ) 。
![](http://7xtgk5.com1.z0.glb.clouddn.com/16-4-27/28198702.jpg)  
打开了本地预览模式  
# 4.全局配置
_config.yml是全局的配置文件
接下来我只写出我们会用到的部分

	# Hexo Configuration
	## Docs: https://hexo.io/docs/configuration.html
	## Source: https://github.com/hexojs/hexo/

	# Site
	title: Hexo#站点名，站点左上角
	subtitle: #副标题，站点左上角
	description: #站点描述
	author: John Doe#作者 站点左下角
	language:#语言 中文zh-CN
	timezone:#时区

	# Deployment
	## Docs: https://hexo.io/docs/deployment.html
	deploy: #部署配置
	  type: git
	  repo: https://github.com/your_user_name/ your_user_name.github.io.git
	branch: master

我们主要需要改的是deploy部分  
# 5.部署到github
当我们改好配置文件之后我们就可以把hexo部署到github上了  
在git shell中进入目录  
我们需要手动配置一点东西  
npm install hexo-renderer-ejs--save  
npm install hexo-renderer-stylus--save  
npm install hexo-renderer-marked--save  
npm install hexo-server—save  
然后生成静态页面  
hexo g  
部署到github  
hexo d  
# 6.创建新文章
hexo new 新文章  
在your_user_name.github.io/source/_posts下会生成 Hexo+Github博客搭建.md  
我们使用sublime 打开文件，以markdown语法编写

	title: 新的开始
	date: 2014-05-07 18:44:12
	updated	: 2014-05-10 18:44:12
	permalink: abc
	tags:
	- 开始
	- 我
	- 日记
	categories:
	- 日志
	- 第一天
