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

## 二、AppThinning Desktop

![](https://images.xiaozhuanlan.com/photo/2019/76370c9c56f622f2f66b37fa8f7dbab7.gif)

### 1、安装前准备

- 若使用 [ImageOptim](https://imageoptim.com/mac) 进行压缩，请先前往 https://imageoptim.com/mac 下载安装

![](https://images.xiaozhuanlan.com/photo/2019/93a194f93e81c4732b8ba2239bf21ce4.png)

- 若使用 [tinypng](https://tinypng.com) 进行压缩，请先前往 https://tinypng.com/developers 获取 API Key

![](https://images.xiaozhuanlan.com/photo/2019/32357545ca566b71a3b69294c3078aec.png)

### 2、安装

前往 https://github.com/CatchZeng/AppThinning/releases 下载 dmg 安装包，拖拽安装即可。

![](https://images.xiaozhuanlan.com/photo/2019/2b2e5413eb6ae80c54d2c4614b4edf56.png)

### 3、使用

1. 拖拽项目目录到 AppThinning
2. 设置参数
3. 点击开始

![](https://images.xiaozhuanlan.com/photo/2019/daff3781c04e70acccc70f5e1f96db9a.png)

## 三、小结

AppThinning Desktop 旨在帮助大家更方便地优化安装包大小，在使用过程中遇到问题或者有什么建议，可以给我留言。

<span id="busuanzi_container_page_pv">
本文总阅读量<span id="busuanzi_value_page_pv"></span>次
</span>