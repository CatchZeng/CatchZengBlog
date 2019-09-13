---
layout:     post
title:      "Learning OpenCV with iOS：图像模糊--非线性滤波"
subtitle:   "OpenCV"
date:       2018-08-22 09:38:27 
author:     "CatchZeng"
header-img: "img/post-opencv.jpg"
tags:
    - OpenCV
---
<span id="busuanzi_container_page_pv"></span>

## 一、前言

[上一篇](http://catchzeng.com/2018/08/08/Learning-OpenCV-with-iOS-图像模糊-线性滤波/)我们讲解了OpenCV图像模糊中的线性滤波。本篇主要向大家介绍下非线性滤波。按惯例，先来看下效果图。

![给铠祛痘](https://upload-images.jianshu.io/upload_images/943491-726907844b4bbb83.gif?imageMogr2/auto-orient/strip)

![阿珂美颜](https://upload-images.jianshu.io/upload_images/943491-675364f9303d8c0c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 二、线性与非线性

[上一篇](http://catchzeng.com/2018/08/08/Learning-OpenCV-with-iOS-图像模糊-线性滤波/)中使用“卷积算子计算都是线性操作，所以又叫线性滤波”简单描述了线性滤波概念。下面我们详细了解下**线性滤波**与**非线性滤波**。

### 数学角度

数学里，一般说的**线性**，是说的线性映射：
线性 = **齐次性** + **可加性**
齐次性: f(ax)=af(x)
可加性: f(x+y)=f(x)+f(y)
**非线性**就是这两条至少之一不成立.

### 图像角度

**线性滤波**：两个信号之和的响应和他们各自响应之和相等（**可加性**）。换句话说，每个像素的输出值是一些输入像素的**加权和**。
**非线性滤波**：原始数据与滤波结果是一种**逻辑关系**，即通过比较一定邻域内的灰度值大小来实现的。

线性滤波器易于构造，并且易于从频率响应角度来进行分析。但是，线性滤波在处理**散粒噪声**（即图像偶尔会出现很大的值）的时候，无法将噪声像素去除，只能转换为更为柔和但仍然可见的散粒。

这时非线性滤波就该登场了。

## 三、非线性滤波

###  1、中值滤波（Median filter）

中值滤波是一种典型的非线性滤波技术，原理是用邻域像素灰度值的**中值**来代替该像素点的灰度值。

![滤波过程](https://upload-images.jianshu.io/upload_images/943491-306a532247d4b325.gif?imageMogr2/auto-orient/strip%7CimageView2/2/w/309)

![中值滤波](https://upload-images.jianshu.io/upload_images/943491-23b6de3e1a30d3ea.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

滤波操作：在9 x 9上面有3 x 3的窗口，从左到右，从上到下移动。将3 x 3窗口内的灰度值按顺序排列，然后取中值代替中心的灰度值。

中值滤波在一定的条件下可以克服常见线性滤波器，如均值滤波带来的图像细节模糊。而且对去除椒盐噪声非常有效，也常用于保护边缘信息, 保存边缘的特性使它在不希望出现边缘模糊的场合也很有用，是非常经典的平滑噪声处理方法。

#### 中值滤波与均值滤波比较

##### 中值滤波优势

在均值滤波中，将**噪声像素**与非噪声像素**一并放入**平均计算中，影响了输出。在中值滤波中，噪声像素**很难被选成中值**，所以几乎不会影响到输出。因此，中值滤波消除噪声和边缘保护方便都更胜一筹。

##### 中值滤波劣势

因为中值滤波要进行排序操作，所以处理的时间长，是均值滤波的5倍以上。


#### 给铠祛痘

![给铠祛痘](https://upload-images.jianshu.io/upload_images/943491-726907844b4bbb83.gif?imageMogr2/auto-orient/strip)

OpenCV提供了中值滤波的API

```
/** 
@param ksize aperture linear size; it must be odd and greater than 1, for example: 3, 5, 7 ...
 */
void medianBlur( InputArray src, OutputArray dst, int ksize );
```
**注意：ksize必须是奇数**

```
+ (UIImage *)medianBlur:(UIImage *)image size:(int)size {
    Mat src;
    UIImageToMat(image, src);
    
    int finalSize = size;
    if (size%2 == 0) {
        finalSize = size + 1;
    }
    Mat dst;
    medianBlur(src, dst, finalSize);
    
    UIImage* result = MatToUIImage(dst);
    
    return result;
}

class NolinearBlurViewController: UIViewController {

    @IBOutlet weak var resultImageView: UIImageView!
    let image = #imageLiteral(resourceName: "ddkai")
    
    @IBAction func onSliderValueChanged(_ sender: UISlider) {
        resultImageView.image = OpenCV.medianBlur(self.image, size: Int32(Int(sender.value)))
    }
}
```

#### 一些思考

##### 为何ksize必须是奇数？

因为如果ksize是偶数，那么将像素灰度值从小到大排列后，必然就没有**唯一的中值**。即使得出中值，那么又将那个作为中心像素呢？因此，中值滤波要求ksize必须是奇数。

![ksize](https://upload-images.jianshu.io/upload_images/943491-436b6cb8f29b527c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

#### 中值滤波有什么不适合的场景？

对一些**细节多**，特别是**线、尖顶**等细节多的图像不宜采用中值滤波。因为中值滤波会将这些细节也模糊掉。


###  2、双边滤波（Bilateral filter）

#### 空间域&像素值域

对于图像滤波来说，图像在**空间中**变化缓慢，因此**相邻的像素点会更相近**。但是这个假设在图像的**边缘处**变得不成立。如果在边缘处也用这种思路来进行滤波的话，即认为相邻相近，则得到的结果必然会**模糊掉边缘**。因为边缘两侧的点的像素值差别很大，所以权重还需考虑像素值。

因此，滤波不但要考虑**空间域**（以下简称空域），还需要考虑**像素值域**（以下简称值域）。

#### 滤波分析

* 均值滤波无法克服**边缘像素信息丢失**。原因是均值滤波是基于**平均权重**，没有考虑**空域**和**值域**。
* 高斯模糊部分克服了该缺陷（考虑了**空域**），但是无法完全避免，因为**没有考虑像素值的不同**，即没有考虑**值域**。

#### 双边滤波

双边滤波是一种非线性的滤波方法，具有**保边去噪**的效果。

![双边滤波](https://upload-images.jianshu.io/upload_images/943491-2efa467fe7d31a4b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

双边滤波的基本思路是同时考虑像素点的**空域**和**值域**。
双边滤波在考虑**值域**时，利用像素点的值的大小进行补充，因为边缘两侧的点的像素值差别很大，因此会使得其加权的时候权重具有很大的差别，从而使得只考虑自己所属的一边的邻域。可以理解成先根据像素值对要用来进行滤波的邻域做一个**分割或分类**，再给该点所属的类别相对较高的权重，然后进行**邻域加权求和**，得到最终结果。

在双边滤波器中，输出像素的值依赖于邻域像素值的加权值组合：

![双边滤波](https://upload-images.jianshu.io/upload_images/943491-073bcd78d1de5de6.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
* w(i,j,k,l): 加权系数, 取决于空域核和值域核的乘积。
* (i,j)，(k,l): 指两个像素点的坐标。

空域核：

![空域核](https://upload-images.jianshu.io/upload_images/943491-1b1b874780ab2317.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

值域核：

![值域核](https://upload-images.jianshu.io/upload_images/943491-175f62622c5d4770.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

双边滤波权重函数：

![双边滤波权重函数](https://upload-images.jianshu.io/upload_images/943491-0339aafde3fc3167.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

空域核（d）函数是根据**像素距离**选择权重，距离越近权重越大。
值域核（r）函数则是根据**像素的差异**来分配权值。如果两个像素值越接近，即使相距较远，也比差异大而距离近的像素点权重大。这点使得边缘（即相距近但差异大的像素点）的特性得以保留。

#### 阿珂美颜

![阿珂美颜](https://upload-images.jianshu.io/upload_images/943491-7326e1aa4b39d860.gif?imageMogr2/auto-orient/strip)

![阿珂美颜](https://upload-images.jianshu.io/upload_images/943491-675364f9303d8c0c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

OpenCV提供了双边滤波的API

```
/** 
_Sigma values_: 为了简单起见，可以将2 Sigma值设置为相同。
如果它们很小（<10）滤波器不会有太大的效果。
如果它们很大（＞150），它们将具有非常强烈的效果，使图像看起来“卡通化”。

_Filter size_: 大的滤波器（D> 5）非常慢，因此建议在进行实时处理应用程序时使用d＝5。对于需要重噪声过滤的离线应用程序可以试下d＝9。

@param src : 即源图像，需要为8位或者浮点型单通道、三通道的图像。
@param d：过滤过程中每个像素邻域的直径。如果这个值我们设其为非正数，那么OpenCV会从第五个参数sigmaSpace来计算出它来。
@param sigmaColor ：颜色空间滤波器的sigma值。这个参数的值越大，就表明该像素邻域内有更宽广的颜色会被混合到一起，产生较大的半相等颜色区域。
@param sigmaSpace：坐标空间中滤波器的sigma值，坐标空间的标注方差。
他的数值越大，意味着越远的像素会相互影响，从而使更大的区域足够相似的颜色获取相同的颜色。
当d>0，d指定了邻域大小且与sigmaSpace无关。否则，d正比于sigmaSpace。
 */ 
void bilateralFilter( InputArray src, OutputArray dst, int d,
                                   double sigmaColor, double sigmaSpace,
                                   int borderType = BORDER_DEFAULT );
```

```
+ (UIImage *)bilateralFilter:(UIImage *)image
                           d:(int)d
                  sigmaColor:(double)sigmaColor
                  sigmaSpace:(double)sigmaSpace {
    Mat src;
    UIImageToMat(image, src);
    
    if (src.channels() == 4) {
        cvtColor(src, src, CV_BGRA2BGR);
    }
    
    Mat dst;
    bilateralFilter(src, dst, d, sigmaColor, sigmaSpace);
    
    UIImage* result = MatToUIImage(dst);
    
    return result;
}


class BilateralFilterViewController: UIViewController {

    @IBOutlet weak var imageView: UIImageView!
    @IBOutlet weak var blurImageView: UIImageView!
    @IBOutlet weak var gBlurImageView: UIImageView!
    @IBOutlet weak var resultImageView: UIImageView!
    
    private var d: Int32 = 1
    private var color: Double = 1.0
    private var space: Double = 1.0
    
    override func viewDidLoad() {
        super.viewDidLoad()
        
        blurImageView.image = OpenCV.blur(imageView.image, sizeX: 3, sizeY: 3)
        gBlurImageView.image = OpenCV.gaussianblur(imageView.image, sizeX: 3, sizeY: 3)
    }

    @IBAction func onSliderValueChanged(_ sender: UISlider) {
        d = Int32(sender.value)
        transform()
    }
    
    @IBAction func onSlider2ValueChanged(_ sender: UISlider) {
        color = Double(sender.value)
        transform()
    }
    
    @IBAction func onSlider3ValueChanged(_ sender: UISlider) {
        space = Double(sender.value)
        transform()
    }
    
    private func transform() {
        resultImageView.image = OpenCV.bilateralFilter(imageView.image, d: d, sigmaColor: color, sigmaSpace: space)
    }
}
```

#### 小小经验

##### 如何选取合适的参数？

* 使用OpenCV API时可以先看下**API的注释文档**，比如在双边滤波的注释文档中对Sigma的取值做了说明。这些值一般都是**经验值**。
```
_Sigma values_: 为了简单起见，可以将2 Sigma值设置为相同。
如果它们很小（<10）滤波器不会有太大的效果。
如果它们很大（＞150），它们将具有非常强烈的效果，使图像看起来“卡通化”。
```

* 使用**滑竿**帮助快速调节参数，观察效果。

* 从**原理公式**出发，假定一些参数，观察其趋势，掌握规律。


## 四、小结

本篇主要介绍了非线性滤波的概念，并通过例子讲解了中值滤波和双边滤波。 非线性滤波的应用广泛，不但要掌握API的调用，更要明白各种滤波的原理，这样才能创造个性化的滤波，也许有一天你就创造出自己的美颜滤镜了。 今天就到这了，有疑问的朋友可以给我留言，咱们下篇见！

<span id="busuanzi_container_page_pv">
本文总阅读量<span id="busuanzi_value_page_pv"></span>次
</span>