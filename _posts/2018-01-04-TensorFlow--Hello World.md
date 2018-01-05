---
layout:     post
title:      "TensorFlow--Hello World"
subtitle:   "TensorFlow"
date:       2018-01-04 08:38:27
author:     "CatchZeng"
header-img: "img/post-bg-2015.jpg"
tags:
    - TensorFlow
---
<span id="busuanzi_container_page_pv"></span>

**欢迎加QQ群讨论：157672725**

## 简介

[TensorFlow™](http://www.tensorflow.org) 是一个采用**数据流图（data flow graphs）**，用于数值计算的开源软件库。**节点（Nodes）**在图中表示**数学操作**，图中的**线（edges）**则表示在节点间相互联系的**多维数据数组**，即**张量（tensor）**。它灵活的架构让你可以在多种平台上展开计算，例如台式计算机中的一个或多个CPU（或GPU），服务器，移动设备等等。TensorFlow 最初由Google大脑小组（隶属于Google机器智能研究机构）的研究员和工程师们开发出来，用于机器学习和深度神经网络方面的研究，但这个系统的通用性使其也可广泛用于其他计算领域。

## 概念介绍（数据流图、节点、线、张量）

**数据流图**用**节点**和**线**的有向图来描述数学计算。**节点** 一般用来表示施加的**数学操作**，但也可以表示数据**输入（feed in）的起点/输出（push out）的终点**，或者是**读取/写入持久变量（persistent variable）的终点**。**线**表示**节点**之间的输入/输出关系。这些数据**线**可以输运“size可动态调整”的多维数据数组，即**张量**（tensor）。张量从图中流过的直观图像是这个工具取名为“Tensorflow”的原因。一旦输入端的所有张量准备好，节点将被分配到各种计算设备完成异步并行地执行运算。

![数据流图](http://upload-images.jianshu.io/upload_images/943491-55c623beb0c057ae.gif?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


## Hello World
学习新语言时，我们通常都会写一个Hello World，来熟悉开发环境的搭建。TensorFlow也不例外。接下来，我将带大家一步步搭建TensorFlow的环境，向世界问好！

### 1.安装Anaconda[后面说明为何选择Anaconda]
前往[Anaconda](https://www.anaconda.com/download/#macos)下载安装包（这里以Mac系统为例，选择Python3.6version）
![Anaconda](http://upload-images.jianshu.io/upload_images/943491-b3e0cc827478d1cc.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

#### 2.启动Anaconda，选择环境点击创建，选择环境选项（这里以Python V3.5为例）
![Anaconda](http://upload-images.jianshu.io/upload_images/943491-e5abcd2b36fb1edd.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![创建环境](http://upload-images.jianshu.io/upload_images/943491-9e283e4c85ff495a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![环境选项](http://upload-images.jianshu.io/upload_images/943491-89380eb14ba6c11f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

#### 3.安装TensorFlow包

![选择All](http://upload-images.jianshu.io/upload_images/943491-0f7a50b0c22ba245.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![搜索TensorFlow并勾选](http://upload-images.jianshu.io/upload_images/943491-d963ab9ed5345a26.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![Apply](http://upload-images.jianshu.io/upload_images/943491-cfe1cdc8c164b85c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

#### 4.运行环境

![Run](http://upload-images.jianshu.io/upload_images/943491-cf80b1798d03c5ae.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

#### 5.编写Hello World
```python
>>> import tensorflow as tf
>>> hello = tf.constant('Hello, TensorFlow!')
>>> sess = tf.Session()
>>> print(sess.run(hello))
```
![Hello World](http://upload-images.jianshu.io/upload_images/943491-bf318f56b181702d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

自此TensorFlow的环境已搭建完成！

## 为何选择Anaconda？

Python易用，但用好却不易，其中比较头疼的就是包管理和Python不同版本的问题，特别是当你使用Windows的时候。为了解决这些问题，就有了不少发行版的Python，比如WinPython、Anaconda等，这些发行版将python和许多常用的package打包，方便Pythoners直接使用。个人尝试了很多类似的发行版，因为Anaconda强大而方便的包管理与环境管理的功能，最终选择了它。

## 参考链接
[https://www.tensorflow.org/install/](https://www.tensorflow.org/install/)

[https://www.anaconda.com](https://www.anaconda.com)

[http://python.jobbole.com/86236/](http://python.jobbole.com/86236/)