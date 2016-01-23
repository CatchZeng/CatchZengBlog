---
layout:     post
title:      "AppStore上架问题ERROR ITMS-90049"
subtitle:   "ERROR ITMS-90049"
date:       2015-10-25 21:43:50
author:     "CatchZeng"
header-img: "img/post-bg-2015.jpg"
tags:
    - iOS
---
<span id="busuanzi_container_page_pv">
阅读<span id="busuanzi_value_page_pv"></span>
</span>

## 前言

昨天上架的时候报错
```
ERROR ITMS-90049: "This bundle is invalid. The bundle identifier contains disallowed characters. [See the section of the Application Programming Guide entitled The APplication Bundle.]"
```
最后在[贴吧](http://tieba.baidu.com/p/4116151851)里找到了方法，在这里记录一下。


## 解决方法

![这里写图片描述](http://img.blog.csdn.net/20151024163556206)

刚开始使用方法（二），结果可能因为我的工程问题而失败【使用此方法建议参考： [Xcode上传App到Appstore，报错Error ITMS-90049](http://blog.csdn.net/h1101723183/article/details/49339681) Q群的朋友 命 甴己造，已验证此方法可用，在此表示感谢！】

后面使用方法（一）：**删除了第三方的plist文件**，上传成功！

**关于方法（一）、（二）我建议大家先尝试（二），实在不行再用（一）**

**查找第三方plist文件的方法：**
在项目下面的Fliter中输入.plist，就可以找到工程中的所有plist文件，选择对应的删除就可以了。
![这里写图片描述](http://img.blog.csdn.net/20151024163752361)
