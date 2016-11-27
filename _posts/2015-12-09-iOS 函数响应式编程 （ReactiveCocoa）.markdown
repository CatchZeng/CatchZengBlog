---
layout:     post
title:      "iOS 函数响应式编程 （ReactiveCocoa）"
subtitle:   "ReactiveCocoa"
date:       2015-12-09 10:38:31
author:     "CatchZeng"
header-img: "img/post-bg-2015.jpg"
tags:
    - iOS
---
<span id="busuanzi_container_page_pv"></span>

## 前言
近来，Q群的几个朋友谈起如何使用ReactiveCocoa（以下简称RAC）开发的问题。大家觉得RAC很好，但不知如何运用它。在此整理一些对RAC的看法，希望对学习RAC的朋友有所帮助。本篇是前篇，主要涉及以下内容。

 - 现有的编程问题
 - 为何出现了这些问题
 - 编程思想的变化
 
## 现有的编程问题
 1. 状态量问题（类似Cache问题）
    在开发中时常需要一些状态量来辅助处理事务。比如：按下登录按钮后发起请求时需要一个状态量（isRequesting）来标注正在请求。
    ![状态量问题](http://leanote.com/api/file/getImage?fileId=566787a5ab6441601e001166)
    但是由于isRequesting的改变相对随意，也时常会忘记对它正确赋值，导致难以维护。这也就跟Cache中的难点（难以知道何时去更新Cache中的值）一样。另外，当状态量多起来时便会出现组合爆炸的问题（状态成指数级增长），不利于扩展及维护。

 2. Controller爆炸问题
    随着程序逻辑复杂度的提高，你是否也发现了App中一些ViewController的代码行数急剧增多，达到了2，3千行，甚至更多。这带来了许多问题:1.不利于后续维护2.不利于支撑UI的变动3.不利于复用 4….

 3. 消息传递机制方式繁多，带来的选择性问题
    iOS 开发中有着各种消息传递机制，包括 KVO、block、Notification、delegation以及 target-action 方式。各种消息传递机制使得开发者在做具体选择时感到困惑，虽然有不少大牛分享了他们选择的经验，但是在开发中始终觉得繁琐。重点是如果选择错了方式，极不利于代码质量的保证。

## 为何出现了这些问题
![1](http://leanote.com/api/file/getImage?fileId=56678bc5ab6441601e001182)      

![2](http://leanote.com/api/file/getImage?fileId=56678bd3ab6441616d00114d) 

![3](http://leanote.com/api/file/getImage?fileId=56679cdbab6441616d0013fb)
    
从上面几张图我们可以看出，app其实是一个输入-》处理-》输出的过程。而它随着输入输出的增多，开发的难度也越来越大。抽象一点来讲就是变化越多，控制越难。

传统的命令式编程，是以命令为主的，给机器提供一条又一条的命令序列让其原封不动的执行，这就限制了编程的灵活性。
变化（输入、输出）越来越多-》分支越来越多-》状态量问题
变化越来越多-》Handler处理的实务越来越多-》Controller爆炸问题（可通过MVVM模式解决）
变化越来越多-》需要不同的消息处理机制-》消息传递机制方式的选择问题
……

既然命令式编程不能满足我们的需求，那便寻求解决之法。于是我们想到了Functional Reactive Programming。

## 编程思想的变化  
最近关于是否使用RAC的讨论非常多。相当一部分的原因是RAC的学习成本高在团队协作中需要每个人都学会使用。使用了一段时间后，个人觉得RAC学习成本高的原因是编程思想上的改变。
RAC运用的响应式编程（Functional Reactive Programming,以下简称FRP）与传统的命令式编程思想差异较大。下面我们先来了解一下RAC使用的FRP思想。

## RAC中的函数式思想
 1. 高阶函数：函数作为参数来回传递。在oc中，block是被广泛使用的参数传递，它实际上是匿名函数。
例:
    ![高阶函数](http://leanote.com/api/file/getImage?fileId=56678d47ab6441616d0011d1)
    上面的例子中先使用filter（过滤）判断value是否有“catch”前缀，然后使用map（映射）将值映射成no more，最后输出。不难看出例子中的filter 和 map block正是高阶函数的应用。
 2. 惰性求值：只有当被使用到时，才会对其求值。在RAC中***信号只有被订阅了，才会触发***。
例:
    ![惰性求值](http://leanote.com/api/file/getImage?fileId=56678dc8ab6441601e001195)
将例子中的订阅部分注释，会发现虽然username改变了但是filter 和map不会触发，这就是惰性求值思想的应用。
在RAC中将***未被订阅的信号称为“冷信号”，将已被订阅的信号称为“热信号”***。
例2:
![惰性求值2](http://leanote.com/api/file/getImage?fileId=56678f0dab6441616d0011e5)
RACSequence只有当被使用到时，才会对其求值（当注释掉订阅的时候，断点是不触发的），这也是惰性求值思想的应用。

## RAC中的响应式思想
响应式编程是一种和事件流有关的编程模式，关注导致状态值改变的行为事件，一系列事件组成了事件流。一系列事件是导致属性值发生变化的原因。FRP非常类似于设计模式里的***观察者模式***。
例:
![RAC中的响应式思想](http://leanote.com/api/file/getImage?fileId=56679a9cab6441601e0013c7)
在传统命令式的编程中可解读成：a是 b + c 表达式的值。
而在响应式的编程中应解读成：建立了一个***动态的数据流关系***（当c或者b的值发生变化时，a的值自动发生变化）。

## 小结
本篇概述了RAC与传统编程方式的区别，下一篇开始，我们将利用RAC来开发一个完整的应用，以此来边掌握RAC的知识。这期就到这了，下期见。