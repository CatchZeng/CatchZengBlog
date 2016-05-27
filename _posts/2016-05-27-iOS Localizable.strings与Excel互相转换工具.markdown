---
layout:     post
title:      "iOS Localizable.strings与Excel互相转换工具"
subtitle:   "iOS开源之路"
date:       2016-05-27 11:40:27 
author:     "CatchZeng"
header-img: "img/post-bg-2015.jpg"
tags:
    - iOS
---
<span id="busuanzi_container_page_pv"></span>

## 前言
相信做过多国化的朋友们一定为Localizable.strings的翻译工作而烦恼。先是写出需要用到的多国化字符串，然后交由翻译人员翻译成各国语言，最后再把翻译后的文档整理写入Localizable.strings。这个工作无聊、反复、繁琐，所以写了个[Localizable.strings与Excel互相转换的工具](https://github.com/CatchZeng/Localizable.strings2Excel)，来提高效率。

## 效果
1.Localizable.strings转换成Excel
![Localizable.strings转换成Excel](https://github.com/CatchZeng/Localizable.strings2Excel/raw/master/imgs/stoe.jpg)

2.Excel转换成Localizable.strings
![Excel转换成Localizable.strings](https://github.com/CatchZeng/Localizable.strings2Excel/raw/master/imgs/etos.jpg)

## 使用方法
1.切换到pyexcelerator-0.6.4.1目录,执行```sudo python setup.py install``` 安装pyexcelerator组件。
![安装pyexcelerator](https://github.com/CatchZeng/Localizable.strings2Excel/raw/master/imgs/installpy.jpg)

2.执行 ```python Localizable.py```命令把Localizable.strings转换成Localizable.xls,然后交由翻译人员进行翻译。

3.将翻译后的Localizable.xls文件重命名为LocalizableBack.xls，然后执行```python LocalizableBack.py```把LocalizableBack.xls转换成LocalizableBack.strings，最后将LocalizableBack.strings重命名为Localizable.strings即可放入工程使用。


## 最后
附上链接[https://github.com/CatchZeng/Localizable.strings2Excel](https://github.com/CatchZeng/Localizable.strings2Excel) 欢迎star、fork、issue。
有问题可以到群里讨论 QQ群：157672725
