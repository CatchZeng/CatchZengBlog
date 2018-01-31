---
layout:     post
title:      "iOS Localizable.strings与Excel互相转换工具"
subtitle:   "iOS开源之路"
date:       2016-05-27 11:40:27 
author:     "CatchZeng"
header-img: "img/post-bg-ios.jpg"
tags:
    - iOS
---
<span id="busuanzi_container_page_pv"></span>

## 前言
相信做过多国化的朋友们一定为Localizable.strings的翻译工作而烦恼。先是写出需要用到的多国化字符串，然后交由翻译人员翻译成各国语言，最后再把翻译后的文档整理写入Localizable.strings。这个工作无聊、反复、繁琐，所以写了个[Localizable.strings与Excel互相转换的工具](https://github.com/CatchZeng/Localizable.strings2Excel)，来提高效率。

## Localizable.strings2Excel
iOS本地化文件（Localizable.strings）与Excel互相转换 & Localizable.strings 转换成android的strings.xml文件的Python脚本工具

#### 将iOS多个国家的Localizable.strings转换成excel
![strings to excel](https://github.com/CatchZeng/Localizable.strings2Excel/blob/master/imgs/stoe.jpg?raw=true)  

#### 将Android多个国家的strings.xml转换成excel
![strings to excel](https://github.com/CatchZeng/Localizable.strings2Excel/blob/master/imgs/atox.jpg?raw=true)  

#### 将excel转换成iOS多个国家的Localizable.strings以及Android的strings.xml
![excel to strings](https://github.com/CatchZeng/Localizable.strings2Excel/blob/master/imgs/etos.jpg?raw=true) 

#### 将单个iOS的Localizable.strings转换成Android的strings.xml
![strings to android xml](https://github.com/CatchZeng/Localizable.strings2Excel/blob/master/imgs/stox.jpg?raw=true) 

## ChangeLog

#### V0.5.0

1.支持将Android多个国家的strings.xml转换成excel

#### V0.4.0

1.支持多种语言一起转换

#### V0.3.0

1.支持Localizable.strings转换成android的strings.xml.

#### V0.2.0 

1.Fix bugs.

2.增加 -h(帮助命令)

3.增加 -t(目标文件地址)、-f(源文件地址)的命令参数

#### V0.1.0 

加入工程

## 使用方法

### 1.安装pyexcelerator组件

切换到pyexcelerator-0.6.4.1目录,执行sudo python setup.py install 安装

![安装pyexcelerator](https://github.com/CatchZeng/Localizable.strings2Excel/blob/master/imgs/installpy.jpg?raw=true)


### 2.安装xld组件

切换到xlrd-1.0.0目录,执行sudo python setup.py install 安装

### 3.使用脚本

python Localizable.py -f xxx/xxx -t xxx/xxx.xls :将多个国家的iOS Localizable.strings文件一起转换成xls文件

![stoeu](https://github.com/CatchZeng/Localizable.strings2Excel/blob/master/imgs/stoeu.jpg?raw=true)

python LocalizableStringsXml.py -f xxx/xxx -t xxx/xxx.xls :将多个国家的Android  strings.xml文件一起转换成xls文件

![xmltoe](https://github.com/CatchZeng/Localizable.strings2Excel/blob/master/imgs/xmltoe.jpg?raw=true)

python LocalizableBack.py -f xxx/xxx.xls -t xxx/xxx  :将xls文件转换成多个国家Localizable.strings文件 & Android 多个国家的strings.xml文件

![etosu](https://github.com/CatchZeng/Localizable.strings2Excel/blob/master/imgs/etosu.jpg?raw=true)

python LocalizableToStringXml.py -f xxx/xxx.strings -t xxx/xxx.xml : 将单个Localizable.strings转换成strings.xml文件

![stoau](https://github.com/CatchZeng/Localizable.strings2Excel/blob/master/imgs/stoau.jpg?raw=true)

## 最后
附上链接https://github.com/CatchZeng/Localizable.strings2Excel 欢迎star、fork、issue。
有问题可以到群里讨论 QQ群：157672725
