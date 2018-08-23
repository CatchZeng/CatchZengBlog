---
layout:     post
title:      "Learning OpenCV with iOS：掩膜操作"
subtitle:   "OpenCV"
date:       2018-06-14 09:38:27 
author:     "CatchZeng"
header-img: "img/post-bg-ios.jpg"
tags:
    - OpenCV
---
<span id="busuanzi_container_page_pv"></span>

## 一、前言

[上一篇](http://catchzeng.com/2018/06/12/Learning-OpenCV-with-iOS-Hello-OpenCV/)我们简单讲解了OpenCV的概念和基础架构。本篇主要向大家介绍下图像处理中一个比较重要的概念 --  掩膜操作。开始前我们先看下利用矩阵掩膜操作来加强图像对比度的效果。

![增强对比度](https://upload-images.jianshu.io/upload_images/943491-74296305be0931e8.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


## 二、开胃菜-Mat对象

![Mat](http://upload-images.jianshu.io/upload_images/943491-1bda24a84044ede2.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

我们用眼睛看到的是图像，而计算机却不认识。于是，人们使用数值的形式来记录图像，比如用RGB值记录图像的每个点，以此来表示图像。就如上图，我们看到的是一辆车，而计算机“看到”的是一个包含图像值的矩阵。OpenCV的Mat对象对应的就是矩阵。Mat提供了许多便捷的API来创建、操作矩阵。

#### Mat基础操作

```
Mat image = Mat(240, 320, CV_8UC3);
```
第一个参数是rows，该矩阵的行数；第二个参数是cols，该矩阵的列数；第三个参数是该矩阵元素的类型。这句话表示创建一个大小为240×320的矩阵，里面的元素为8位unsigned型，通道数(channel)有3个。

```
image.create(480, 640, CV_8UC3);
```
分配（或重新分配）image矩阵，把大小设为480×640，类型设为CV8UC3。

```
Mat image = Mat(3, 3, CV_32F, Scalar(5));
```
定义并初始化一个3×3的32bit浮点数矩阵，每个元素都设为5。

```
uchar* ptr = image.ptr(row);
```
指针操作，表示拿到image第row行的指针

```
uchar* output = image.ptr(row);
output[1] = value;
```
利用指针修改图像，表示修改image第row行的第2个数据为value。


#### Mat常用成员介绍
1、data

Mat对象中的一个指针，指向存放矩阵数据的内存(uchar* data)

2、dims

矩阵的维度，3*4的矩阵维度为2维，3*4*5的矩阵维度为3维

3、channels

矩阵通道，矩阵中的每一个矩阵元素拥有的值的个数，比如说 3 * 4 矩阵中一共 12 个元素，如果每个元素有三个值，那么就说这个矩阵是 3 通道的，即 channels = 3。常见的是一张彩色图片有红、绿、蓝三个通道。

4、rows

矩阵的行数

5、cols

矩阵的列数

#### Mat与IplImage

OpenCV1使用基于C接口定义的图像存储格式IplImage存储图像。IplImage直接暴露内存，如果忘记释放内存，就会造成内存泄漏。

从OpenCV2开始，开始使用Mat类存储图像，具有以下优势：

*  图像的内存分配和释放由Mat类自动管理

* Mat类由两部分数据组成：矩阵头(包含矩阵尺寸、存储方法、存储地址等)和一个指向存储所有像素值的矩阵(根据所选存储方法的不同，矩阵可以是不同的维数)的指针。Mat在进行赋值和拷贝时，只复制矩阵头，而不复制矩阵，提高效率。如果矩阵属于多个Mat对象，则通过引用计数来判断，当最后一个使用它的对象，则负责释放矩阵。

* 可以使用clone和copyTo函数，不仅复制矩阵头还复制矩阵。

## 三、掩膜操作

数字图像处理中的掩膜的概念是借鉴于PCB制版的过程，在半导体制造中，许多芯片工艺步骤采用光刻技术，用于这些步骤的图形“底片”称为掩膜（也称作“掩模”），其作用是：在硅片上选定的区域中对一个不透明的图形模板遮盖，继而下面的腐蚀或扩散将只影响选定的区域以外的区域。 
图像掩膜与其类似，用选定的图像、图形或物体，对处理的图像（全部或局部）进行遮挡，来控制图像处理的区域或处理过程。 
光学图像处理中,掩模可以是胶片、滤光片等。数字图像处理中,掩模为二维矩阵数组,有时也用多值图像。

是不是概念看得一头雾水，没事的，我第一次看概念的也是一样样的。下面我以例子来辅助大家了解掩膜。

#### 抠下铠的头

![铠](https://upload-images.jianshu.io/upload_images/943491-b7718fa0c6ef972f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

接下来我们以代码角度分析下究竟什么是掩膜。
```
    // image为铠的图片
    Mat src;
    UIImageToMat(image, src);
    
    Mat mask = Mat::zeros(src.size(), CV_8UC1);
    Rect2i r = Rect2i(120, 80, 100, 100);
    mask(r).setTo(255);
    
    Mat dst;
    src.copyTo(dst, mask);
```

第一步建立与原图一样大小的mask图像，并将所有像素初始化为0，因此全图成了一张全黑色图。
第二步将mask图中的r区域的所有像素值设置为255,也就是整个r区域变成了白色。
```
 Mat mask = Mat::zeros(src.size(), CV_8UC1);
 mask(r).setTo(255);
```

![mask图片](https://upload-images.jianshu.io/upload_images/943491-9c82240d29373e2a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

使用mask将原始图src拷贝到目的图dst上。
```
src.copyTo(dst, mask);
```

这个拷贝的动作完整版本是这样的：

原图(src)与掩膜(mask)进行与运算后得到了结果图（dst）。

其实就是原图中的每个像素和掩膜中的每个对应像素进行与运算。比如1 & 1 = 1；1 & 0 = 0；

比如一个3 * 3的图像与3 * 3的掩膜进行运算，得到的结果图像就是：

![mask.png](https://upload-images.jianshu.io/upload_images/943491-d1f9d8c3d7b09c66.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

所以，mask就是位图，来过滤像素。如果mask像素的值是非0的，我就保留，否则就丢弃。

因为我们上面得到的mask中，感兴趣的区域是白色的，表明感兴趣区域的像素都是非0，而非感兴趣区域都是黑色，表明那些区域的像素都是0。一旦原图与mask图进行与运算后，得到的结果图只留下原始图感兴趣区域的图像了。也正剩下铠的头部了。

#### 增强对比度

矩阵的掩膜操作就是根据掩膜来重新计算每个像素的像素值，掩膜(mask)也被称为kernel。
通过掩膜操作实现图像对比度提高的公式如下。
```
I(i,j) = 5*I(i,j) - [I(i-1,j) + I(i+1,j) + I(i,j-1) + I(i,j+1)]
```
注：这里看不懂不要紧，先看具体的实现，回头我们再一起回顾这里。

上面的公式，转换成矩阵就如下图所示

![kernel](https://upload-images.jianshu.io/upload_images/943491-2a0575afcffcc610.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

红色是中心像素，从上到下，从左到右对每个像素做同样的处理操作，具体过程如下图，深灰色底表示原图像，每次移动kernel便根据公司计算新值并更新矩阵。最终得到的结果就是对比度提高之后的输出图像。

![kernel](https://upload-images.jianshu.io/upload_images/943491-a5ba14b90afb4f08.gif?imageMogr2/auto-orient/strip)

具体的代码如下：
```
    // image为铠的图片
    Mat src;
    UIImageToMat(image, src);
    
    int cols = (src.cols-1) * src.channels();
    int offset = src.channels();
    int rows = src.rows;
    
    Mat dst = Mat(src.size(), src.type());
    for (int row = 1; row < rows-1; row++) {
        uchar* previous = src.ptr(row-1);
        uchar* current = src.ptr(row);
        uchar* next = src.ptr(row+1);
        uchar* output = dst.ptr(row);
        for (int col = offset; col < cols; col++) {
            output[col] = saturate_cast<uchar>(5*current[col] - (current[col-offset] + current[col +offset] + previous[col] + next[col]));
        }
    }

/*
注：
saturate_cast<uchar>(-100)，返回0
saturate_cast<uchar>(288)，返回255
saturate_cast<uchar>(100)，返回100
这个函数的功能是确保RGB值范围在0~255之间。
*/
```

效果
![增强对比度](https://upload-images.jianshu.io/upload_images/943491-74296305be0931e8.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

接下来我们来回顾下上面的那个公式
```
I(i,j) = 5*I(i,j) - [I(i-1,j) + I(i+1,j) + I(i,j-1) + I(i,j+1)]
```
其实这个公式就是5倍的中心像素减去周边的四个像素之和。
我们举两个例子来看下这个公式的结果。

![demo1](https://upload-images.jianshu.io/upload_images/943491-bb4a09b443016d77.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![demo2](https://upload-images.jianshu.io/upload_images/943491-a2f4113946251462.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

我们可以**大致**看到若是中心点的值大于周围，则计算后的结果会将中心点与周围的值差距拉得更大；
若是中心点的值小于周围，则计算后的结果也会将中心点与周围的值差距拉大。这样“大的大，小的小”结果不就是对比明显了吗，也就是提高了对比度。

大家会发现这样做掩膜操作也太麻烦了。这个时候我们就找OpenCV来帮个忙，看看它是怎么实现的。

```
    Mat src;
    UIImageToMat(image, src);
    
    Mat dst;
    Mat kernel = (Mat_<char>(3, 3) << 0, -1, 0, -1, 5, -1, 0, -1, 0);
    filter2D(src, dst, src.depth(), kernel);
```
一个filter2D搞定！定义如下：
```
void filter2D( InputArray src, OutputArray dst, int ddepth,
                            InputArray kernel, Point anchor=Point(-1,-1),
                            double delta=0, int borderType=BORDER_DEFAULT );
```
其中src与dst是Mat类型变量、depth表示位图深度，有32、24、8等。

## 四、小结

本篇主要介绍了Mat对象的基本用法，并通过两个例子讲解了掩膜操作的原理和实现。下一篇还是会以这样的形式讲解OpenCV的其他知识，有更好建议的朋友可以给我留言，see you later!