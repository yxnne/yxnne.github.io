---
layout: post
title: 简单iOS的APP开发记录_第二周
date:   2017-09-26
categories: "Developing_iOS"
tags: [iOS, APP]
author: yxnne
comment: true
---

>这是在我快速的开发完公司iOS 原生App后，将开发每一次的记录整理起来的。我不是一个专业的iOS开发人员，所以很多东西需要记录，包括琐碎的各方面的知识点，上线的流程，甚至语法。

### 7 / 24 / 2017
过了个周末
做了这么一件事：在项目中引入一个图表的第三方库，Charts，就是android框架的MPAndroidCharts的移植版，Android项目就用的这个，所以API相对熟悉些。
将项目引入后，针对自己的项目需求封装了几个工具类：RadarChartUtil，LineCHartUtil，PieChartUtil。

_**学到的东西**_

>UIColor透明
```
//clear是透明的意思
self.view.backgroundColor = UIColor.clearColor() 
```
_**问题没解决**_
Swift怎么写匿名内部类？或者说匿名实现一个接口协议？？

_**参考的文章**_
[Charts官方](https://github.com/danielgindi/Charts)

[Charts简单使用Demo](http://www.jianshu.com/p/7ea8f2b99abe)

[Charts写柱状图](http://www.cnblogs.com/huangbiyong/p/6909156.html)

[Charts常用API介绍](http://www.cnblogs.com/lwk151124/p/5840784.html)

[Swift结构体](http://www.cnblogs.com/gcb999/p/3810188.html)

[Swift结构体和类](http://www.jianshu.com/p/f99d48666383)

[Swift闭包简介](http://blog.csdn.net/tonny_guan/article/details/49122169)

[Swift Date类(部分api有更新)](http://blog.csdn.net/mo_xiao_mo/article/details/52612443)

[Swift Date类](http://www.jianshu.com/p/a6275cc54e04)

[Swift 日历类](http://www.jianshu.com/p/2d3195f9556e)

[元素的绝对定位](http://www.hangge.com/blog/cache/detail_965.html#)

### 7 / 25 / 2017
APP的首页做好了：最顶上是高200的轮播图，下来是几张Charts（Pie和Line），整体放到UIScrollView里面。
今天主要实现了下轮播图。
轮播图的主要思路就是：利用ScrollView，计算你得内容的尺寸的和设置成的ContentSize的相关尺寸，然后设置Scroll的代理，利用定时器控制计算ContentSize的尺寸差补offset。
轮播的指示是使用的UIPageControl,就是小圆点。
轮播的点击时间我的实现是给每张图片上添加Tap的手势监听，在监听的回调函数里面调用代理方法，当然这个代理得事先写一个协议，并且view类中持有引用。

>对了，图片要设置点击事件等交互之前要设置下允许交互
imageView.userInteractionEnabled = true

_**主要参考的文章**_
[框架是参照这篇文章的](http://www.cnblogs.com/zhyunfe/p/6064146.html)

[两张图片实现轮播的方案，回头仔细学习下](http://www.jianshu.com/p/19a0019f8b5f)

[我自己的相关记录](http://www.jianshu.com/p/8e8aa3280668)

### 7 / 26 / 2017
iOS的项目看上去绕不开CocoaPods这个第三方库管理工具了。
Mac系统是自带ruby的。
我的Podfile中暂时是这样的
```
platform :ios, '8.0'
target 'SwsApp'do
pod 'XLPagerTabStrip', '~> 7.0'
end
use_frameworks!
```

[CocoaPods安装，本来是参考这个文章，不过除了些幺蛾子](http://code4app.com/article/cocoapods-install-usage)

[CocoaPods简介和安装](http://www.jianshu.com/p/3086df14ed08)

[也是一篇安装的CocoaPods](http://www.jianshu.com/p/ea7744d52313)

[淘宝ruby镜像官网](https://ruby.taobao.org/)

[有出错，这个参考了个百度百科](https://zhidao.baidu.com/question/1862891903252349347.html)

[CocoaPods官网](https://guides.cocoapods.org/using/pod-install-vs-update.html)

[最后安装好了但是使用pod install的时候报错了，参考这个StackOverFlow的方法](https://stackoverflow.com/questions/40191140/cocoapods-1-1-1-target-overrides-the-always-embed-swift-standard-libraries)

### 7 / 29 / 2017
安装完成CocoaPods之后，弄了两天的顶部tab的第三方框架，实验了很多：主要是XLPagerTabStrip，因为GitHub上面star很多这个框架但是实际使用到本项目里面发现并不好用：如果没用底部的tab好像还可以。加上底部的tab之后，顶部的tab对于当前的Controller的view 的控制尺寸的计算似乎有偏差。
因为时间实在是有限，放弃研究了，多一层跳转吧。
添加了雷达图的工具方法。基于Charts框架。
_**主要参考的文章**_

[XLPagerTabStrip](https://github.com/xmartlabs/XLPagerTabStrip)

__学而不思则罔，思而不学则殆__

