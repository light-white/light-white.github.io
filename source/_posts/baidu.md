---
title: 百度Ueditor图片管理无法显示
date: 2017-02-17 15:03:48
tags:
- ueditor
- html
- jsp
---
今天在使用ueditor做新闻编辑时出现一点问题，图片上传之后后台管理界面获取不到  
![](http://7xtgk5.com1.z0.glb.clouddn.com/ueditor_imagemanage.png)  
打开控制台发现获取的是本地绝对路径

第一时间想到修改`config.json`中的文件访问路径

    /* 列出指定目录下的图片 */
    "imageManagerActionName": "listimage", /* 执行图片管理的action名称 */
    "imageManagerListPath": "/ueditor/jsp/upload/image/", /* 指定要列出图片的目录 */
    "imageManagerListSize": 20, /* 每次列出文件数量 */
    "imageManagerUrlPrefix": "http://localhost:8080/Meet/", /* 图片访问路径前缀 */
    "imageManagerInsertAlign": "none", /* 插入的图片浮动方式 */
    "imageManagerAllowFiles": [".png", ".jpg", ".jpeg", ".gif", ".bmp"], /* 列出的文件类型 */

将UrlPrefix修改为当前项目域名  
然后发现还有问题  这时候绝对路径跟在域名后面了  
配置文件`ueditor.config.js`中说`controller.jsp`是服务器统一请求接口路径  
我们修改一下他试试  
修改后代码如下  

    <%@ page language="java" contentType="text/html; charset=UTF-8"
    	import="com.baidu.ueditor.ActionEnter"
        pageEncoding="UTF-8"%>
    <%@ page trimDirectiveWhitespaces="true" %>
    <%

        request.setCharacterEncoding( "utf-8" );
    	response.setHeader("Content-Type" , "text/html");
    	String rootPath = application.getRealPath( "/" );
    	String action = request.getParameter("action");  
    	String result = new ActionEnter( request, rootPath ).exec();  
    	if( action!=null &&   
    	   (action.equals("listfile") || action.equals("listimage") ) ){  
    	    rootPath = rootPath.replace("\\", "/");  
    	    result = result.replaceAll(rootPath, "/");  
    	}  
    	out.write( result );
    %>
这时就能看到上传到服务器上的图片与文件了
