---
layout:     post
title:      "为AppDelegate瘦身"
subtitle:   "runtime&load"
date:       2015-10-25 22:24:19
author:     "CatchZeng"
header-img: "img/post-bg-2015.jpg"
tags:
    - iOS
---

## 前言

项目开发了一段时间后，相信大家都会遇到AppDelegate中的didFinishLaunch函数不断增大带来的不便管理问题。在Java中我们常使用JVM的ClassLoader对类进行加载、连接、初始化等操作，在oc中对应的就是load和initialize。


## 瘦身
load和initialize在使用中需要注意的点：

 - 系统最多会调用一次（除非开发者主动调用）
 - 都能为程序初始化一些数据，创造适合的运行环境
 - 父类调用一定在子类之前
 - 由下图文档可知，**load是只要类或者category被加入runtime便会被调用**，而initialize是在类或者其子类的第一个方法被调用前调用。
![这里写图片描述](http://img.blog.csdn.net/20151024085728124)

我们可以利用**load是只要类或者category被加入runtime便会被调用**这个特性，来为AppDelegate瘦身：
![这里写图片描述](http://img.blog.csdn.net/20151024092327331)


----------


## 题外
1.搜索一下自己的项目，可以发现某些第三方库也是利用load方法来实现**自动运行**(如：IQKeyboardManager)。
![这里写图片描述](http://img.blog.csdn.net/20151024093003486)

![这里写图片描述](http://img.blog.csdn.net/20151024093137407)


2.由于**系统最多会调用一次（除非开发者主动调用）**，为了防止主动调用，可以在load方法中加上dispatch_once

![这里写图片描述](http://img.blog.csdn.net/20151024093529899)
