---
layout:     post
title:      "【Learning OpenCV with iOS】(一) Hello OpenCV"
subtitle:   "OpenCV"
date:       2018-06-12 09:38:27 
author:     "CatchZeng"
header-img: "img/post-bg-ios.jpg"
tags:
    - OpenCV
---
<span id="busuanzi_container_page_pv"></span>

## 一、概念介绍
#### 图像处理、计算机视觉

[图像处理](https://baike.baidu.com/item/%E5%9B%BE%E5%83%8F%E5%A4%84%E7%90%86)，用计算机对图像进行分析，以达到所需结果的技术。又称影像处理。图像处理一般指**数字图像处理**。数字图像是指用工业相机、摄像机、扫描仪等设备经过拍摄得到的一个大的二维数组，该数组的元素称为**像素**，其值称为**灰度值**。图像处理技术一般包括图像压缩，增强和复原，匹配、描述和识别3个部分。

[计算机视觉](https://baike.baidu.com/item/计算机视觉)是一门研究如何使[机器](https://baike.baidu.com/item/%E6%9C%BA%E5%99%A8)“看”的科学，更进一步的说，就是是指用摄影机和电脑代替人眼对目标进行识别、跟踪和测量等[机器视觉](https://baike.baidu.com/item/%E6%9C%BA%E5%99%A8%E8%A7%86%E8%A7%89)，并进一步做图形处理，使电脑处理成为更适合人眼观察或传送给仪器检测的图像。作为一个科学学科，计算机视觉研究相关的理论和技术，试图建立能够从图像或者多维数据中获取‘信息’的[人工智能](https://baike.baidu.com/item/%E4%BA%BA%E5%B7%A5%E6%99%BA%E8%83%BD)系统。因为感知可以看作是从感官信号中提 取信息，所以计算机视觉也可以看作是研究如何使人工系统从图像或多维数据中“感知”的科学。

图像处理与计算机视觉区别：
图像处理侧重于对图像的“处理”（如：增强，还原，去燥，分割等）。
计算机视觉则重在使用计算机来模拟人的视觉，**模拟**才是计算机视觉的目的。

#### OpenCV

OpenCV（Open Source Computer Vision Library）于1999年由[Intel](https://baike.baidu.com/item/Intel)建立，如今由Willow Garage提供支持。
OpenCV是一个基于BSD许可（开源）发行的跨平台计算机视觉库，可以运行在[Linux](https://baike.baidu.com/item/Linux)、[Windows](https://baike.baidu.com/item/Windows)和[Mac OS](https://baike.baidu.com/item/Mac%20OS)操作系统上。它轻量级而且高效——由一系列 C 函数和少量 C++ 类构成，同时提供了Python、Ruby、MATLAB等语言的接口，实现了图像处理和计算机视觉方面的很多通用算法。OpenCV覆盖了计算机视觉的许多领域，如产品检测、信息安全、立体视觉和机器人、医学成像等。因为计算机视觉和机器学习关系密切，所以OpenCV还提供了MLL（Machine Learning Library）机器学习库。

常用链接：

[OpenCV官方主页](https://opencv.org)

[OpenCV github主页](https://github.com/opencv/opencv)

[OpenCV iOS tutorials](https://docs.opencv.org/3.1.0/d3/dc9/tutorial_table_of_content_ios.html)

## 二、环境搭建

“工欲善其事必先利其器”，接下来我们先搭建一下开发环境，借此了解下OpenCV的基本架构。

###### 1.创建项目

![创建项目](https://upload-images.jianshu.io/upload_images/943491-d42b8064456f791a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

注:这里以Objective-C作为语言是因为OpenCV接口为C++，使用Objective-C较好与其交互。

##### 2.加入opencv2.framework

前往https://opencv.org/releases.html下载iOS pack。
![iOS pack](https://upload-images.jianshu.io/upload_images/943491-101047bd286e5e0a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

将opencv2.framework加入项目
![加入项目](https://upload-images.jianshu.io/upload_images/943491-e0cbecf02e7fe2d5.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

将OpenCV所需的依赖库添加到项目中

* CoreGraphics.framework
* Foundation.framework
* UIKit.framework

如果要使用摄像头做视频处理，还需要添加以下依赖库：

* CoreVideo.framework
* CoreMedia.framework
* AssetsLibrary.framework

![framework](https://upload-images.jianshu.io/upload_images/943491-086ef4bf0182bf81.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

##### 3.Say Hello

新建OpenCV.h & OpenCV.mm，并添加以下代码
* 注：因为OpenCV是C++写的，所以引入项目中的文件需要使用Object-C++的兼容方式来写，具体的做法就是将需要导入OpenCV头文件的m文件改成mm文件

```
//
//  OpenCV.h
//  LearningOpenCV
//
//  Created by CatchZeng on 2018/6/12.
//  Copyright © 2018年 catch. All rights reserved.
//

#import <Foundation/Foundation.h>
#import <UIKit/UIKit.h>

@interface OpenCV : NSObject

+ (UIImage *)gray:(UIImage *)image;

@end
```

```
//
//  OpenCV.m
//  LearningOpenCV
//
//  Created by CatchZeng on 2018/6/12.
//  Copyright © 2018年 catch. All rights reserved.
//

//!!!opencv的头文件引入必须在其他头文件之前
#import <opencv2/opencv.hpp>
#import <opencv2/imgproc/types_c.h>
#import <opencv2/imgcodecs/ios.h>
#import <opencv2/core/core_c.h>
#import "OpenCV.h"
using namespace cv;

@interface OpenCV() {
}

@end

@implementation OpenCV

+ (UIImage *)gray:(UIImage *)image {
    Mat mat;
    UIImageToMat(image, mat);
    
    Mat grayMat;
    cvtColor(mat, grayMat, CV_BGR2GRAY);
    
    UIImage* result = MatToUIImage(grayMat);
    return result;
}

@end
```

新建视图控制器SayHelloViewController

```
class SayHelloViewController: UIViewController {
    
    @IBOutlet weak var imageView: UIImageView!
    @IBOutlet weak var grayImageView: UIImageView!
    
    override func viewDidLoad() {
        super.viewDidLoad()
        
        grayImageView.image = OpenCV.gray(#imageLiteral(resourceName: "sayHello"))
    }
}
```

效果

![效果](https://upload-images.jianshu.io/upload_images/943491-afb139a09ec5365d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


源码地址: https://github.com/CatchZeng/LearningOpenCV


## 三、OpenCV基础架构

OpenCV包含的内容较多，在学习之前先了解下它的整体模块架构，再选择自己感兴趣的部分重点学习，这样学起来比较得心应手。

鉴于此，下面我们一起来看下OpenCV的整体模块架构。

![OpenCV](https://upload-images.jianshu.io/upload_images/943491-18342122cfdc0d61.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

从上图可看到OpenCV的所有模块：

-  【calib3d】Calibration(校准)和3D这两个词的组合缩写。这个模块主要是相机校准和三维重建相关的内容，包括基本的多视角几何算法、单个立体摄像头标定，物体姿态估计，立体相似性算法，3D信息的重建等。

-  【core】核心功能模块，包含如下内容：
      - OpenCV基本数据结构
      - 动态数据结构
      - 绘图函数
      - 数组操作相关函数
      - 辅助功能与系统函数和宏
      - 与OpenGL的互操作

-  【feature2d】2D功能框架。包含如下内容：
      - 特征检测和描述
      - 特征检测器(Fearure Detectors) 通用接口
      - 描述符提取器(Description Extractors) 通用接口
      - 描述符匹配器(Description Eatchers) 通用接口
      - 通用描述符(Generic Description)匹配器通用接口
      - 关键点绘制函数和匹配功能绘制函数

-  【imgproc】Image 和Process 这两个单词的缩写组合，图像处理模块。包含如下内容：
      - 线性和非线性的图像滤波
      - 图像的几何变换
      - 其他的图像变换
      - 直方图相关
      - 结构分析和形状描述
      - 运动分析和对象跟踪
      - 特征检测
      - 目标检测等内容

-  【flann】Fast Library For Approximate Nearest Neighbors，高维的近似近邻快速搜索算法库，包含以下两个部分：

      - 快速近似最近邻搜索
      - 聚类

-  【highgui】高层GUI图形用户界面，包含媒体的输入输出、视频捕捉、图像和视频的解码编码、图形交互界面的接口等内容。

-  【ml】Machine Learning，机器学习模块， 基本上是统计模型和分类算法，包含如下内容：

      - 统计模型 （Statistical Models）
      - 一般贝叶斯分类器 （Normal Bayes Classifier）
      - K-近邻 （K-NearestNeighbors）
      - 支持向量机 （Support Vector Machines）
      - 决策树 （Decision Trees）
      - 提升（Boosting）
      - 梯度提高树（Gradient Boosted Trees）
      - 随机树 （Random Trees）
      - 超随机树 （Extremely randomized trees）
      - 期望最大化 （Expectation Maximization）
      - 神经网络 （Neural Networks）
      - MLData

-  【objdetect】目标检测模块，包含Cascade Classification（级联分类）和Latent SVM这两个部分。

-  【photo】Computational Photography，包含图像修复和图像去噪两部分。

-  【stitching】images stitching，图像拼接模块，包含如下部分：

      - 拼接流水线
      - 特点寻找和匹配图像
      - 估计旋转
      - 自动校准
      - 图片歪斜
      - 接缝估测
      - 曝光补偿
      - 图片混合

-  【video】视频分析组件，该模块包括运动估计，背景分离，对象跟踪等视频处理相关内容。

-  【Videostab】Video stabilization，视频稳定相关的组件，官方文档中没有多作介绍，不管它了。

看到到这里，相信大家已经对OpenCV的模块架构设计有了一定的认识，OpenCV其实就是这么多模块作为代码容器组合起来的一个SDK，了解这些可以更快的学习OpenCV了！

## 四、小结

本文从概念到环境搭建再到基本架构的角度介绍了OpenCV，让读者有一个宏观的认识。下一节开始，我将以原理+例子的形式一步步介绍OpenCV的各个模块。