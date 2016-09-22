---
layout:     post
title:      "使用Cordova开发iOS App -- 环境搭建"
subtitle:   "iOS开发"
date:       2016-09-20 09:38:27 
author:     "CatchZeng"
header-img: "img/post-bg-2015.jpg"
tags:
    - iOS
---
<span id="busuanzi_container_page_pv"></span>


## 一、简介
[Cordova](http://cordova.apache.org/)大家可能比较陌生，但肯定听过 PhoneGap ，Cordova 就是 PhoneGap 被 Adobe 收购后所改的名字。它是一个可以让 JS 与原生代码互相通信的一个库，并且提供了一系列的插件类（如 JS 直接操作本地数据库的插件类）。

#### Cordova优缺点
优点: 降低开发成本一次开发多平台套用，版本更新容易可动态更新。

缺点: cordova其实就是在原生app上加了一个webView,并提供一套JS与原生代码交互的类库。交互效果一般，且访问原生控件时受限,常常无法满足业务需求。

#### Cordova插件
为了解决Cordova的缺点，就出现了cordova插件的概念。即由各个平台的开发者实现webView中无法实现的功能，并打包成插件供前端开发人员调用，这样来优化cordova app的体验问题。

## 二、文章安排
本系列文章主要围绕Cordova for iOS部分讲解，主要分为如下几个部分：

1.环境搭建

2.开发iOS插件

3.打包插件及JS脚本

## 三、环境搭建

#### 1.检测是否安装了node.js
打开终端输入```npm --version```，若无打印版本则需先安装[node.js](https://nodejs.org/en/)
![node.js](/img/in-post/post-cordova1/1.png)

#### 2.命令行安装cordova
 ``` npm install -g cordova ```

#### 3.创建项目
```cordova create testCordovaProject com.catchzeng.testCordova testCordova```
![create](/img/in-post/post-cordova1/2.png)

参数说明：

testCordovaProject: 项目文件夹名称

com.catchzeng.testCordova: 项目的bundleID

testCordova:项目名称

#### 4.添加平台
```
cd testCordovaProject/
sudo cordova platform add ios
```
![add platform](/img/in-post/post-cordova1/3.png)

添加成功后可在项目的platforms文件夹中看到ios文件夹,进入后就可以看到ios工程了。
![iOS project](/img/in-post/post-cordova1/4.png)

iOS工程运行结果
![result](/img/in-post/post-cordova1/5.png)

#### 5.Cordova开发体验
修改Staging->www->index.html
![result](/img/in-post/post-cordova1/6.png)

```
<div class="app">
      <h1>Apache Cordova</h1>
      <div id="deviceready" class="blink">
          <p class="event listening">Connecting to Device</p>
          <p class="event received">Device is Ready</p>
          <h1 align="center">
                HI，iOSDeveloper.
           </h1>
      </div>
</div>
```
再次运行项目，可以看到界面上显示了HI，iOSDeveloper.
![result](/img/in-post/post-cordova1/7.png)

至此，环境搭建完毕。下一篇将讲解[为Cordova开发iOS插件](http://www.jianshu.com/p/96079fcccb4e)。