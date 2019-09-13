---
layout:     post
title:      "Learning OpenCV with iOS：图像混合与ROI"
subtitle:   "OpenCV"
date:       2018-06-24 09:38:27 
author:     "CatchZeng"
header-img: "img/post-opencv.jpg"
tags:
    - OpenCV
---
<span id="busuanzi_container_page_pv"></span>

## 一、前言

[上一篇](http://catchzeng.com/2018/06/14/Learning-OpenCV-with-iOS-掩膜操作/)我们讲解了OpenCV的掩膜操作。本篇主要向大家介绍下图像处理中的图像混合。按惯例，先来一张效果图。

![](/img/in-post/post-opencv/mix-1.gif)


## 二、图片相加

要叠加两张图片，可以将Mat直接相加，相加两幅图片的形状（高度/宽度/通道数）必须相同。但这样的效果不一定是你想要的。我们来看一个例子。

```
    Mat src;
    Mat src2;
    UIImageToMat(image, src);
    UIImageToMat(image2, src2);
    
    Mat dst;
    dst = src + src2;
    
    UIImage* result = MatToUIImage(dst);
```

![](/img/in-post/post-opencv/mix-2.png)


## 三、线性混合
图像混合其实也是一种图片相加的操作，只不过两幅图片的**权重**不一样。
```
g(x) = α*f0(x) + β*f1(x)
```
OpenCV处理图像混合主要是根据线性混合函数，通过在0到1范围内改变α的值，使两幅图像或者视频产生在时间上的画面叠化得效果。实际上α和β的和不一定为1，只是为了防止图像出现过饱和的现象。

OpenCV的**addWeighted**函数便是对应线性混合操作。这个函数的作用是，计算两个图像的**加权和**。
```
void addWeighted(InputArray src1, double alpha, InputArray src2, double beta, double gamma, OutputArray dst, int dtype=-1);
```
* src1：需要加权的第一个数组，常常填一个Mat对象。
* alpha：第一个数组的权重值，0~1之间。
* src2：第二个数组，它需要和第一个数组拥有相同的尺寸和通道数。
* beta：第二个数组的权重值，一般为1-alpha。
* gamma：一个加到权重总和上的标量值。
* dst：输出的数组，它和输入的两个数组拥有相同的尺寸和通道数。
* dtype：输出阵列的可选深度，有默认值-1；当两个输入数组具有相同的深度时，这个参数设置为-1（默认值），即等同于src1.depth()。

#### 当后羿遇到阿珂

![](/img/in-post/post-opencv/mix-3.gif)

```
+ (UIImage *)addWeighted:(UIImage *)image image2:(UIImage *)image2 alpha:(double)alpha gamma:(double)gamma {
    Mat src;
    Mat src2;
    UIImageToMat(image, src);
    UIImageToMat(image2, src2);
    
    Mat dst;
    addWeighted(src, alpha, src2, gamma, 0, dst);
    
    UIImage* result = MatToUIImage(dst);
    
    return result;
}

class MixViewController: UIViewController {

    @IBOutlet weak var resultImageView: UIImageView!
    private let ake = UIImage(named: "ake")
    private let houyi = UIImage(named: "houyi")
    
    override func viewDidLoad() {
        super.viewDidLoad()
        
        resultImageView.image = OpenCV.addWeighted(ake, image2: houyi, alpha: 0.5, gamma: 0.5)
    }
    
    @IBAction func onSliderValueChanged(_ sender: UISlider) {
        let alpha = Double(sender.value)
        resultImageView.image = OpenCV.addWeighted(ake, image2: houyi, alpha: alpha, gamma: (1.0 - alpha))
    }
}
```

#### 一些思考

* 为何直接的图像相加效果较差，而线性混合后的效果较好？

可以这样简单理解：像素值范围为0~255，两张图的Mat直接相加，就是每个像素点的值相加，这样容易出现像素值较大的像素，像素“越大越白”，这样就出现了图像过曝的现象。而线性混合则是加上了权重，保证了像素值不至于过大，这样就不会出现过曝现象。以公式来说明就是
```
//相加
g(x) = 1*f0(x) + 1*f1(x)

//线性混合
g(x) = α*f0(x) + β*f1(x)
```
即**相加操作相当于α和β都等于1的线性混合**。
```

class MixViewController: UIViewController {

    @IBOutlet weak var resultImageView: UIImageView!
    private let ake = UIImage(named: "ake")
    private let houyi = UIImage(named: "houyi")
    
    override func viewDidLoad() {
        super.viewDidLoad()
        
        resultImageView.image = OpenCV.addWeighted(ake, image2: houyi, alpha: 1, gamma: 1)
    }
}
```

![](/img/in-post/post-opencv/mix-4.png)


* addWeighted方法要求src1和src2的尺寸和通道数相同，若是不同如何混合？
实现这个需要先熟悉图像处理另一个重要的概念--ROI。

## 四、感兴趣区域ROI(Region Of Interest)

在图像处理领域，我们常常需要设置ROI，来专注或者简化我们的工作过程 。也就是从图像中选择的一个图像区域，这个区域是我们图像分析所关注的重点。我们圈定这个区域，以便进行进一步处理。而且，使用ROI指定我们想读入的目标，可以减少处理时间，增加精度，给图像处理来带不小的便利。

定义ROI区域有两种方法，第一种是使用cv:Rect指定矩形区域
```
Mat imageROI=image(Rect(500,250,logo.cols,logo.rows));  
```

另一种定义ROI的方式是指定感兴趣行或列的范围（Range）
 ```
Mat imageROI=srcImage3(Range(250,250+logoImage.rows),Range(200,200+logoImage.cols));  
```

下面我们来看一个实例，展示如何利用ROI将一幅图加到另一幅图的指定位置。

#### 铠与Logo

![](/img/in-post/post-opencv/mix-5.gif)

```
+ (UIImage *)add:(UIImage *)image alphaExist:(BOOL)alphaExist on:(UIImage *)anotherImage atPosition:(CGPoint)position alpha:(CGFloat)alpha beta:(CGFloat)beta gamma:(CGFloat)gamma {
    Mat src;
    Mat dst;
    UIImageToMat(anotherImage, src);
    UIImageToMat(image, dst, alphaExist);
    
    //ROI
    short x = position.x;
    short y = position.y;
    Mat imageRoi;
    imageRoi = src(cv::Rect(x, y, dst.cols, dst.rows));
    
    //add image
    addWeighted(imageRoi, alpha, dst, beta, gamma, imageRoi);
    
    UIImage* result = MatToUIImage(src);
    
    return result;
}

class ROIViewController: UIViewController {

    @IBOutlet weak var resultImageView: UIImageView!
    private let logo = #imageLiteral(resourceName: "logo")
    private let sayHello = #imageLiteral(resourceName: "sayHello")
    
    override func viewDidLoad() {
        super.viewDidLoad()
        
        resultImageView.image = OpenCV.add(#imageLiteral(resourceName: "logo"), alphaExist: true, on: #imageLiteral(resourceName: "sayHello"), atPosition: CGPoint(x: 330, y: 0), alpha: 1.0, beta: 1.0, gamma: 0)
    }
    
    @IBAction func onSliderValueChanged(_ sender: UISlider) {
        resultImageView.image = OpenCV.add(#imageLiteral(resourceName: "logo"), alphaExist: true, on: #imageLiteral(resourceName: "sayHello"), atPosition: CGPoint(x: 330, y: 0), alpha: 1.0, beta: CGFloat(sender.value), gamma: 0)
    }
}
```

**注：**

* 在UIImage转换成Mat的时候，若有透明度记得将最后一个参数alphaExist设置为true

```
 UIImageToMat(image, dst, alphaExist);
```

* 在addWeighted的时候alpha表示第一个Mat的权重，beta表示第二个Mat的权重。在实例中，Logo图片其实是第二个Mat，所以对应的参数是beta。

```
    Mat imageRoi;
    imageRoi = src(cv::Rect(x, y, dst.cols, dst.rows));
    
    //add image
    addWeighted(imageRoi, alpha, dst, beta, gamma, imageRoi);
```


## 五、小结

本篇主要介绍了图像混合的概念，并通过例子讲解了线性混合的实现和ROI的应用。在今后的学习中ROI的使用度较高，需要好好掌握。ROI其实是“分而治之”思想在图像处理中的应用。好了，今天就到这了。有疑问的朋友可以给我留言，see you later!

<span id="busuanzi_container_page_pv">
本文总阅读量<span id="busuanzi_value_page_pv"></span>次
</span>