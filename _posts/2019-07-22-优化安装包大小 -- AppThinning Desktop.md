---
layout: post
title: "优化安装包大小 -- AppThinning Desktop"
subtitle: "JS"
date: 2019-07-22 09:00:00
author: "CatchZeng"
header-img: "img/post-bg-js-version.jpg"
tags:
  - JS
---

<span id="busuanzi_container_page_pv"></span>

## 一、前言
在文章[优化安装包大小](https://xiaozhuanlan.com/topic/3458629071)中，我们介绍了常见的优化安装包大小的方法，并写了一个脚本工具 [AppThinning](https://github.com/CatchZeng/AppThinning) ，帮助自动找到大文件，然后进行图片压缩。然而，实际运用中**图片的前期处理者经常是设计师**，脚本对于他们来说始终不够方便，于是诞生了AppThinning  Desktop。

## 二、AppThinning  Desktop

![](/img/in-post/post-js/appthinning-1.gif)

### 1、安装前准备

- 若使用 [ImageOptim](https://imageoptim.com/mac) 进行压缩，请先前往 https://imageoptim.com/mac 下载安装

![](/img/in-post/post-js/appthinning-2.png)

- 若使用 [tinypng](https://tinypng.com) 进行压缩，请先前往 https://tinypng.com/developers 获取 API Key

![](/img/in-post/post-js/appthinning-3.png)

### 2、安装

前往 https://github.com/CatchZeng/AppThinning/releases 下载 dmg 安装包，拖拽安装即可。

![](/img/in-post/post-js/appthinning-4.png)

注：由于苹果的 Gatekeeper 的保护，打开 AppThinning 可能会出现如下图所示的问题

![](/img/in-post/post-js/appthinning-5.png)

这时，可进入应用程序目录，右键点击 AppThinning 打开即可

![](/img/in-post/post-js/appthinning-6.png)

### 3、使用

1. 拖拽项目目录到 AppThinning
2. 设置参数
3. 点击开始

![](/img/in-post/post-js/appthinning-7.png)

## 三、小结

AppThinning Desktop 旨在帮助大家更方便地优化安装包大小，在使用过程中遇到问题或者有什么建议，可以给我留言。

<span id="busuanzi_container_page_pv">
本文总阅读量<span id="busuanzi_value_page_pv"></span>次
</span>