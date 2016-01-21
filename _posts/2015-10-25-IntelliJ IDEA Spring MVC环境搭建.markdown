---
layout:     post
title:      "IntelliJ IDEA Spring MVC环境搭建"
subtitle:   "IntelliJ IDEA"
date:       2015-11-15 15:24:14
author:     "CatchZeng"
header-img: "img/post-bg-2015.jpg"
tags:
    - Java Web
---

<span id="busuanzi_container_page_pv">
阅读 <span id="busuanzi_value_page_pv"></span>
</span>

## 前言
IntelliJ在业界被公认为最好的java开发工具之一，尤其在智能代码助手、代码自动提示、重构、J2EE支持、Ant、JUnit、CVS整合、代码审查、 创新的GUI设计等方面的功能可以说是超常的。
	Spring MVC属于SpringFrameWork的后续产品，提供了构建 Web 应用程序的全功能 MVC 模块。使用 Spring 可插入的 MVC架构，易于同其它View框架（Tiles等）无缝集成，采用IOC便于测试。源于这些优点，越来越多开发者已将Spring MVC列为的首选。

----------
	
## 搭建过程

 1. .选中Spring并勾选Spring MVC，点击Next
![图片标题](http://leanote.com/api/file/getImage?fileId=5648258aab64416406000576)

 2. 勾选create project from template
![图片标题](http://leanote.com/api/file/getImage?fileId=56482963ab6441640600060a)

 3. 输入项目名称、保存位置、tomact位置等信息
![图片标题](http://leanote.com/api/file/getImage?fileId=56482a86ab6441640600060c)

 4. 点击run，启动
![图片标题](http://leanote.com/api/file/getImage?fileId=56482b5eab64416406000611)

 5. 浏览器输入Hello World表示环境搭建成功
![图片标题](http://leanote.com/api/file/getImage?fileId=56482c25ab644165680005be)

----------

## Spring MVC请求处理流程

 1. Spring MVC的设计是围绕DispatcherServlet展开的，DispatcherServlet负责将请求派发到特定的handler。通过可配置的handler mappings、view resolution、locale以及theme resolution来处理请求并且转到对应的视图。Spring MVC请求处理的整体流程如图：
![图片标题](http://leanote.com/api/file/getImage?fileId=56482d79ab64416406000618)

 2. 在web.xml中我们可以看到配置了一个DispatcherServlet，该Servlet拦截了 / 的所有请求
![图片标题](http://leanote.com/api/file/getImage?fileId=56482e09ab644165680005c4)

 3. 在mvc-dispatcher-servlet.xml中 component-scan 扫描指定的文件夹下的文件（这里指定了根目录）
![图片标题](http://leanote.com/api/file/getImage?fileId=56482fd4ab644165680005d5)

 4. 当请求发起时，Spring MVC扫描文件发现HelloController 中的 @Controller和@RequestMapping("/")注解便由DispatcherServlet转向HelloController来处理 / 请求
![图片标题](http://leanote.com/api/file/getImage?fileId=564830c4ab644165680005d9)

 5. 当HelloController返回hello变由mvc-dispatcher-servlet.xml的配置默认加上前后缀成为/WEB-INF/pages/hello.jsp，最后将Hello World输出
![图片标题](http://leanote.com/api/file/getImage?fileId=56483169ab64416406000626)