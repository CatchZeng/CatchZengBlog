---
layout:     post
title:      "Learning OpenCV with iOS：图像亮度和对比度"
subtitle:   "OpenCV"
date:       2018-07-02 09:38:27 
author:     "CatchZeng"
header-img: "img/post-opencv.jpg"
tags:
    - OpenCV
---
<span id="busuanzi_container_page_pv"></span>

## 一、前言

[上一篇](http://catchzeng.com/2018/06/24/Learning-OpenCV-with-iOS-图像混合与ROI/)我们讲解了OpenCV的图像混合与ROI。本篇主要向大家介绍下图像变换的调整亮度和对比度。按惯例，先来一张效果图。

![](/img/in-post/post-opencv/lightness-1.gif)


## 二、图像变换

#### 算子
一般的图像处理算子都是一个函数，它接受一个或多个输入图像，并产生输出图像。
###### 单输入
```
g(x) = h(f(x))
```

###### 多输入
```
g(x) = h(f0(x)......fn(x))
```

根据算子的不同，图像变换分为两大类：

* 像素变换 - 点操作
* 邻域操作 - 区域

#### 像素变换

这类变换操作的是点。根据输入像素值（有时可加上某些全局信息或参数）计算相应的输出像素值。
这类算子包括亮度和对比度调整 ，以及颜色校正和变换。

#### 邻域操作

这类变换操作的是区域（大块的，非点操作）。
这类算子包括卷积、特征提取、梯度计算、模糊等。
后续文章中我们将一一讲解，这里不再赘述。

## 三、调整图像亮度和对比度

```
g(i, j) = αf(i,j) + β  其中α>0，β是增益变量
```
* f(i,j) 表示源图像像素
* g(i,j) 表示输出图像像素

#### 一些理解

* 亮度越亮其实看起来越“白”，在图像处理中就是像素值越靠近255。β变量是个增益变量，β越大，像素值就越靠近255，自然就提高了亮度。所以，**β常常被用来控制图像的亮度**。

* α对像素的操作是乘法，乘法会”扩大差距“，打个比方：1x10 = 10, 2x10 = 20。改变α会将像素值的差距扩大，也就是改变了对比度。所以，**α常常被用来控制图像的对比度**。


#### 魔铠变身

![](/img/in-post/post-opencv/lightness-2.gif)

```
+(UIImage *)transform:(UIImage *)image alpha:(double)alpha beta:(double)beta {
    Mat src;
    UIImageToMat(image, src);
    
    Mat dst = Mat(src.size(), src.type());
    
    int rows = src.rows;
    int cols = src.cols;
    Mat m;
    src.convertTo(m, CV_32F);
    for (int row = 0; row < rows; row++) {
        for (int col = 0; col < cols; col++) {
            if(src.channels() == 1) {//单通道
                float v = m.at<Vec3f>(row, col)[0];
                dst.at<uchar>(row, col) = saturate_cast<uchar>(v*alpha + beta);
            } else if (src.channels() == 3) { //3通道
                for (int i=0; i< src.channels(); i++) {
                    float v = m.at<Vec3f>(row, col)[i];
                    dst.at<Vec3b>(row, col)[i] = saturate_cast<uchar>(v*alpha + beta);
                }
            }  else if (src.channels() == 4) { //4通道
                for (int i=0; i< src.channels(); i++) {
                    float v = m.at<Vec4f>(row, col)[i];
                    dst.at<Vec4b>(row, col)[i] = saturate_cast<uchar>(v*alpha + beta);
                }
            }
        }
    }
    UIImage* result = MatToUIImage(dst);
    return result;
}
```
###### 注：

* 做像素点操作时应当注意数据类型的转换

```
    src.convertTo(m, CV_32F);
```

* 图像的通道多样，处理图片时需要做多通道的处理

```
if(src.channels() == 1) {//单通道
    ...
} else if (src.channels() == 3) { //3通道
    ...
}  else if (src.channels() == 4) { //4通道
    ...
}
```

## 四、小结

本篇主要介绍了图像变换的概念，并通过例子讲解了亮度和对比度的调整。本篇的内容相对简单，但是对像素点的操作是基本功，特别是在实现自定义算法时，需要好好掌握。在今后的学习中亮度和对比度的调整是很多图像处理的前期工作，也需要理解透彻。今天就到这了，有疑问的朋友可以给我留言，see you later!

<span id="busuanzi_container_page_pv">
本文总阅读量<span id="busuanzi_value_page_pv"></span>次
</span>