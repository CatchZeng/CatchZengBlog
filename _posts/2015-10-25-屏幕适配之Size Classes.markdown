---
layout:     post
title:      "屏幕适配之Size Classes"
subtitle:   "frame -> autoreszing -> autolayout -> Size Classes"
date:       2015-10-25 22:16:06
author:     "CatchZeng"
header-img: "img/post-bg-ios.jpg"
tags:
    - iOS
---

<span id="busuanzi_container_page_pv"></span>

## 前言

在适配ios8的时候，其实autolayout基本都可以满足，所以当时没有使用上Size Classes。这几天为了适配ios9用上了Size Classes。令我最开心的是，很方便地解决了**不同状态下不同布局**的问题（如：横屏和竖屏布局不同、iPad和iPhone布局不同）,下图是我用Size Classes做的一个demo。

![效果图](http://img.blog.csdn.net/20150924135208588)

## Size Classes的概念
**布局的进化史**
代码frame -> autoreszing(父子控件的关系ios6) -> autolayout(任何控件都可以产生关系ios7) -> Size Classes（抽象ios8）。

其实sizeclass是对设备的一种抽象。它不再有尺寸、横屏和竖屏的概念，只有（Compact、Any，Regular）的概念，即把设备分为**紧凑型、任意型、宽松型**（如下图）
![storyboard的展示形态](http://img.blog.csdn.net/20150924204829183)![抽象形态](http://img.blog.csdn.net/20150924204935390)

为了方便理解Size Classes，可以把它想象成一个**屏幕选择器**，可以对不同屏幕进行选择。我们需要注意的是**Size Classes仅仅是对屏幕进行了分类, 真正布局还是交给autolayout**

问题：**如何知道适配的设备使用那种型**？
解决：在storyboard中移动鼠标选中某一个型，可以看到关于该型适应的信息（如下图）
![这里写图片描述](http://img.blog.csdn.net/20150924210540664)  该型表示 竖屏的iPhone

![这里写图片描述](http://img.blog.csdn.net/20150924210714273) 该型表示 横屏的iPhone

![这里写图片描述](http://img.blog.csdn.net/20150924210835075) 该型表示 竖屏的iPhone 和 ipad横竖屏

  
  
## Size Classes的实践


下面我们就照着gif图上的例子来讲一下如何在项目中使用Size Classes来解决问题。
1.首先建立一个singleview的工程，打开Main.storyboard，在wAny hAny的型下拖拽两个view和两个label
![这里写图片描述](http://img.blog.csdn.net/20150924212158909)


2.接下来先设置**所有状态下都需要的约束**

【我们希望蓝色的view在任何情况下都是正方形，设置其Aspect Ratio为1：1】
![这里写图片描述](http://img.blog.csdn.net/20150924212511678)


【我们希望红色的view在任何情况下大小都是44x44并且与lab名称顶部位置一直，设置其width 44 height 44  Align Top to 名称】
![这里写图片描述](http://img.blog.csdn.net/20150924212701257)

3.其他设置如下
![这里写图片描述](http://img.blog.csdn.net/20150924213136063)

![这里写图片描述](http://img.blog.csdn.net/20150924213228323)

这个时候我们会看到红色的警告，表示我们没有把自动布局的约束设置完全，那下一步我们该做的是在你需要的型下把剩余的约束补充完成。

4.选择型到 wCompact hRegular（选中后wCompact hRegular的背景会变蓝色,表示你已经进入到该型的模式下）
![这里写图片描述](http://img.blog.csdn.net/20150924213511441)

5.为该型补充剩余的约束

![这里写图片描述](http://img.blog.csdn.net/20150924213948955)

![这里写图片描述](http://img.blog.csdn.net/20150924214016180)

![这里写图片描述](http://img.blog.csdn.net/20150924214036219)

![这里写图片描述](http://img.blog.csdn.net/20150924214056416)

此时我们看到红色的警告已经没有，表示我们已经把该型的约束补充完毕
![这里写图片描述](http://img.blog.csdn.net/20150924214157176)


6.接下来，我们切换到wAny hCompact型下添加约束
![这里写图片描述](http://img.blog.csdn.net/20150924214333696)

7.添加的约束
![这里写图片描述](http://img.blog.csdn.net/20150924214538686)

![这里写图片描述](http://img.blog.csdn.net/20150924214603332)

![这里写图片描述](http://img.blog.csdn.net/20150924214620984)

![这里写图片描述](http://img.blog.csdn.net/20150924214636822)

直到红点消失！！
![这里写图片描述](http://img.blog.csdn.net/20150924214157176)

好了，兴奋的一刻来了运行一下，旋转一下是不是跟gif上的一样呢！


## 小结一下

1.先在 wAny hAny的型下设置所有状态下都需要的约束（有点像面向对象的基类）,能减少约束的重复设置
2.选择需要适配的型，进行约束的添加（有点像子类）
4.Size Classes实际上是**继承的思想在布局中的运用**，让我们能在一个storyboard中设置不同状态下的形态，免去了以前横屏一个xib，竖屏一个xib的麻烦。
5.Size Classes也为ios9的分屏适配带来便捷【该点会在下一期提及】

今天就到这里了，下期见！

demo源码：https://github.com/catchZeng/SizeClassDemo

