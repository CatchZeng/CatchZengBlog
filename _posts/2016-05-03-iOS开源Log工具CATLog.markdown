---
layout:     post
title:      "iOS开源Log工具CATLog"
subtitle:   "iOS开发见闻"
date:       2016-05-03 09:38:27 
author:     "CatchZeng"
header-img: "img/post-bg-2015.jpg"
tags:
    - iOS
---
<span id="busuanzi_container_page_pv"></span>

## 前言
做过Java开发的朋友，都会回想log4j。喜欢它的分级输出和Eclipse或Idea分颜色输出以及点击定位到类文件，在iOS中却只剩下NSlog。于是，开始寻找log4j的类似框架。CocoaLumberjack是最早使用的，其功能齐全、接口灵活、自定义方便等特性着实让人喜欢，但也遇到了[一些问题](http://m.oschina.net/blog/381201)。之后，看到Coneboy-k开源的[KKLog](https://github.com/Coneboy-k/KKLog)，其轻量、简洁、实用的API着实让我喜欢，再也没有什么配置、link等错误,但不支持格式化输出、颜色打印、点击跳转等让我觉得不够用。最后，参考了[KKLog](https://github.com/Coneboy-k/KKLog)设计了一个[Log工具](https://github.com/CatchZeng/CATLog)，在此，对Coneboy-k表示感谢。

## 说说到底想要什么样的Log
也许你会说，你还真挑，不就一个Log工具，你想要什么样的？

#### 必要的功能
1.支持设置日志级别
2.支持日志输出到文件
3.支持日志记录Crash信息

#### 进一步的要求
1.自动删除旧的日志文件
2.方便地自定义日志宏
3.支持颜色打印日志
4.支持点击日志信息跳转到代码中，方便定位错误信息

#### 还想要的
有个Mac客户端，调试的时候不连接数据线也能看日志

## CATLog
看看CATLog长什么样吧。
![CATLog](https://github.com/CatchZeng/CATLog/raw/master/CATLog.gif)

## 最后
附上链接[https://github.com/CatchZeng/CATLog](https://github.com/CatchZeng/CATLog) 欢迎star、fork、issue。
有问题可以到群里讨论 QQ群：157672725
