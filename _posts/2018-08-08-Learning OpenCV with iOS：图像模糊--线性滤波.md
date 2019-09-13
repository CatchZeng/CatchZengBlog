---
layout:     post
title:      "Learning OpenCV with iOS：图像模糊--线性滤波"
subtitle:   "OpenCV"
date:       2018-08-08 09:38:27 
author:     "CatchZeng"
header-img: "img/post-opencv.jpg"
tags:
    - OpenCV
---
<span id="busuanzi_container_page_pv"></span>

## 一、前言

[上一篇](http://catchzeng.com/2018/07/02/Learning-OpenCV-with-iOS-图像亮度和对比度/)我们讲解了OpenCV的图像亮度和对比度调整。本篇主要向大家介绍下图像模糊。按惯例，先来一张效果图。

![铠玩模糊](https://upload-images.jianshu.io/upload_images/943491-9f839b59fc174f06.gif?imageMogr2/auto-orient/strip)


## 二、模糊

所谓**模糊**，可以先简单理解为每一个像素都取周边像素的平均值。

![模糊](https://upload-images.jianshu.io/upload_images/943491-421e3e62d8da7f52.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

上图中，2是中间点像素值，周边像素都是1。

![模糊](https://upload-images.jianshu.io/upload_images/943491-6f0f0a5da441812d.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

中间点取周围点的平均值，就会变成1。在数值上叫**平滑**。在图形上，就产生了**模糊**效果，也就是中间点失去了细节。

![模糊](https://upload-images.jianshu.io/upload_images/943491-bc2ce865b85b330b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 三、图像模糊

图像模糊是opencv常见的操作，使用模糊操作的原因是为了给图像**预处理**时降低噪声影响。
Smooth和Blur是opencv图像模糊的API，其背后的原理其实是数学的卷积操作

![卷积](https://upload-images.jianshu.io/upload_images/943491-10def553303932ed.jpeg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

其中权重核h(k,l) 为“滤波系数”。上面的式子可以简记为： 

![卷积](https://upload-images.jianshu.io/upload_images/943491-d3247f1ba60ce51f.jpeg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

通常这些**卷积算子**计算都是线性操作，所以又叫**线性滤波**。

## 四、线性滤波

### 1、均值滤波

均值滤波是典型的线性滤波算法，它是指在图像上对目标像素给一个**模板**，该模板包括了其周围的临近像素，再用模板中的全体像素的平均值来代替原来像素值。

还记得[第二篇](http://catchzeng.com/2018/06/14/Learning-OpenCV-with-iOS-掩膜操作/)里所讲**掩膜操作**吧，均值滤波的过程跟掩膜操作极其相似。

![均值滤波](https://upload-images.jianshu.io/upload_images/943491-306a532247d4b325.gif)

滤波操作：在9x9上面有3x3的窗口，从左到右，从上到下移动，白色的每个像素点值之和取平均值赋给中心红色像素作为它处理之后的像素值。其中，**模板**就是3x3的窗口，红色格子为**目标像素**，白色格子为周围的**临近像素**。

此类操作被称为**卷积计算**，而模板和kernel就是卷积计算中的**卷积算子**。

opencv提供了均值滤波（模糊）的API
```
/** 
@param src 输入图像
@param dst 输出图像
@param ksize 模糊kernel大小
@param anchor 锚点; 默认值为Point(-1,-1) ，表示在锚点在kernel的中心
@param borderType 查看#BorderTypes
 */
blur( InputArray src, OutputArray dst,
                        Size ksize, Point anchor = Point(-1,-1),
                        int borderType = BORDER_DEFAULT );
```


#### 铠玩模糊

![铠玩模糊](https://upload-images.jianshu.io/upload_images/943491-9f839b59fc174f06.gif?imageMogr2/auto-orient/strip)

```
+ (UIImage *)blur:(UIImage *)image sizeX:(int)sizeX sizeY:(int)sizeY {
    Mat src;
    UIImageToMat(image, src);
    
    Mat dst;
    blur(src, dst, cv::Size(sizeX, sizeY));
    
    UIImage* result = MatToUIImage(dst);
    
    return result;
}

class BlurViewController: UIViewController {

    @IBOutlet weak var imageView: UIImageView!
    @IBOutlet weak var resultImageView: UIImageView!
    
    private var sizeX: Int32 = 3
    private var sizeY: Int32 = 3
    
    override func viewDidLoad() {
        super.viewDidLoad()
    }
    
    @IBAction func onSliderValueChanged(_ sender: UISlider) {
        sizeX = Int32(sender.value)
        transform()
    }
    
    @IBAction func onSlider2ValueChanged(_ sender: UISlider) {
        sizeY = Int32(sender.value)
        transform()
    }
    
    private func transform() {
        let image = OpenCV.blur(imageView.image, sizeX: sizeX, sizeY: sizeY)
        resultImageView.image = image
    }
}
```

#### 一些思考

##### 滤波操作为何能将图像模糊？

滤波操作其实是用周围的像素平均值作为目标像素的值，经过这样的处理后其实就是整个图像像素值差距缩小。差距缩小了自然就比较没有辨识度了，也就是模糊了。

##### 为何在铠玩模糊中，只改变Size的x大小会让铠在x轴方向模糊？

这题就当讨论题吧，有兴趣的朋友可以在文章的评论处讨论。
提示：改变x的值其实就是改变kernel的形态，单独将x增加，就相当于kernel的形态变成宽度大于高度的长方形。

##### 均值模糊有没有什么问题？

我们知道图像都是**连续的**，**越靠近的点关系越密切，越远离的点关系越疏远**，均值模糊只是简单的取平均，没有分配**权重**，肯定存在不合理之处。相比之下，加权平均更合理，距离越近的点权重越大，距离越远的点权重越小。
下面我们就来看下带权重的高斯滤波。

### 2、高斯滤波

#### 正态分布

![正态分布](https://upload-images.jianshu.io/upload_images/943491-a883c94a4950b72e.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

如上图，正态分布是一种钟摆形曲线，越接近中心，取值越大，越远离中心，取值越小。
计算平均值的时候，我们只需要将中心点作为原点，其他点按照其在正态曲线上的位置，分配权重，就可以得到一个加权平均值。

#### 高斯函数

##### 一维函数

![一维高斯函数](https://upload-images.jianshu.io/upload_images/943491-e3a63c466aaf2b73.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
σ：标准差，在这里又叫做高斯半径。
σ2：方差。
f(x)：概率
μ：均值，即期望。

在计算平均值的时候，中心点就是原点，所以μ等于0。可得简化后的函数：

![简化后的函数](https://upload-images.jianshu.io/upload_images/943491-327146860c1bcafa.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

根据一维函数，可以推导得到二维函数：

![二维高斯函数](https://upload-images.jianshu.io/upload_images/943491-4428118897919429.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

图像是二维的，所以通常处理图像时，我们使用二维高斯函数。

##### 计算例子

假定中心点的坐标是（0,0），那么距离它最近的8个点的坐标如下：

![坐标](https://upload-images.jianshu.io/upload_images/943491-0f621d9ee8289b44.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

假设σ=1.5，则权重矩阵如下：

![权重矩阵](https://upload-images.jianshu.io/upload_images/943491-df79b4cfe9e67eaf.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

这9个点的权重总和等于0.4787147，归一化后得到最终的权重矩阵：

![归一化](https://upload-images.jianshu.io/upload_images/943491-b10892137ebdd773.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

假设现有图像矩阵如下：

![图像矩阵.jpg](https://upload-images.jianshu.io/upload_images/943491-fe3faf6c3a2429a1.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

与权重相乘后得到的矩阵如下：

![矩阵](https://upload-images.jianshu.io/upload_images/943491-40e93558114e798a.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

将这9个值相加就是中心点的最终值：13.9401236。而通过均值滤波得到的结果是13.5。


#### 均值与高斯哪家强

```
@param sigmaX Gaussian kernel standard deviation in X direction.
@param sigmaY Gaussian kernel standard deviation in Y direction; if sigmaY is zero, it is set to be
equal to sigmaX, if both sigmas are zeros, they are computed from ksize.width and ksize.height,
respectively (see #getGaussianKernel for details); to fully control the result regardless of
possible future modifications of all this semantics, it is recommended to specify all of ksize,
sigmaX, and sigmaY.
@param borderType pixel extrapolation method, see #BorderTypes

@sa  sepFilter2D, filter2D, blur, boxFilter, bilateralFilter, medianBlur
 */
CV_EXPORTS_W void GaussianBlur( InputArray src, OutputArray dst, Size ksize,
                                double sigmaX, double sigmaY = 0,
                                int borderType = BORDER_DEFAULT );
```
```
+ (UIImage *)gaussianblur:(UIImage *)image sizeX:(int)sizeX sizeY:(int)sizeY {
    Mat src;
    UIImageToMat(image, src);
    
    Mat dst;
    GaussianBlur(src, dst, cv::Size(sizeX, sizeY), 11);
    
    UIImage* result = MatToUIImage(dst);
    
    return result;
}
```

![均值](https://upload-images.jianshu.io/upload_images/943491-fe8d7a23f71b0c53.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![高斯](https://upload-images.jianshu.io/upload_images/943491-702ca4a10ec28fed.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

仔细观看可以看到，高斯模糊图像的轮廓较均值的清晰些，没有那么“模糊”。


## 五、小结

本篇主要介绍了图像模糊的概念，并通过例子讲解了均值模糊和高斯模糊。模糊经常在图像预处理降时使用到，需要好好掌握其原理，以便于应对不同情况。今天就到这了，有疑问的朋友可以给我留言，咱们下篇见！

<span id="busuanzi_container_page_pv">
本文总阅读量<span id="busuanzi_value_page_pv"></span>次
</span>