---
layout: post
title: 简单iOS的APP开发记录_第五周
date:   2017-10-8
categories: "Developing_iOS"
tags: [iOS, APP]
author: yxnne
comment: true
---
>这是在我快速的开发完公司iOS 原生App后，将开发每一次的记录整理起来的。我不是一个专业的iOS开发人员，所以很多东西需要记录，包括琐碎的各方面的知识点，上线的流程，甚至语法。

#### 8 / 18 / 2017
针对部门详情页面，集成第三方popDialog辅助实现选择科室，admin可以选择，其他不行。
使用协议在ViewController之间交换数据。
学习使用xib文件控制布局界面。

* 在ViewController之间交换数据。

```
/**
 定义的接口协议用于在VC之间传数据，这里用作dialog返回协议
 */
protocol MyChooseProtocal {
    /**返回被选择的String*/
    func choosenString() ->String
    /**返回被选择的String在[]中的Index*/
    func choosenIndex() ->Int
}

/**另一个VC，实现MyChooseProtocal：和宿主通信*/
extension MyXibSelectDepartmentViewController:MyChooseProtocal {
    func choosenString() ->String{
        return departStrings[choosenIndex()]
    }
    func choosenIndex() ->Int{
        return departPicker.selectedRow(inComponent: 0)
    }
}

//ViewController中持有引用
        var chooseDepartProtocalImpl:MyChooseProtocal?
//设置
        let chooseDepartVC = MyXibSelectDepartmentViewController(nibName: "MySelectDepartmentViewController", bundle: nil)
       
        chooseDepartProtocalImpl = chooseDepartVC

//该调用的时候调用
        //构造确定按钮
        let buttonTwo = DefaultButton(title: "确定", height: 60) {
              //self.label.text = "You rated \(ratingVC.cosmosStarRating.rating) stars"
              print("get the Choosen String is:\(String(describing: self.chooseDepartProtocalImpl?.choosenString()))")
        }
```

_**主要参考的文章**_

[xib使用介绍](http://www.cocoachina.com/swift/20160728/17218.html)

[xib使用介绍另一个](http://blog.csdn.net/heipingguowenkong/article/details/51545728)

[PopupDialog官方简介](https://github.com/Orderella/PopupDialog)

[分段选择器SegmentController用法](http://www.jianshu.com/p/05f756d11f3d)

#### 8 / 22 / 2017
这两天身体不适。
1.对接部门详情接口完成（除了第一个图，第一个图的系统接口暂时不支持全院）。没有什么技术难点，工作量的问题。
2.部门员工和个人数据基本上都对接成功了，个别地方界面显示可能需要修改下。明天重点看定时查询

tableView的刷新
```
//tableView的刷新
  tableView.reloadData()
```

#### 8 / 23 / 2017

对接历史事件，对接实时事件(今日)。技术难度不大，工作量问题。

实时事件Timer定时查询
```
//开启定时任务
        myTimer =  Timer.scheduledTimer(timeInterval:TimeInterval(intervalTime),
                                        target:self,
                                        selector:#selector(doTimmerTask),
                                        userInfo:nil,repeats:true)
//注销定时任务
        myTimer?.invalidate()
//任务
    //事件调度任务回调
    func doTimmerTask(){
        print(#function)
    }

```
用时间组件得到一个月前的时间
```
    /**
     Date -> Date 得到一个月前的时间
     */
    static func getDateByAddingDays(baseDate date :Date , addingOffsets offset:Int) ->Date{
        
        let calendar = Calendar.current
        var offSetComponents = DateComponents.init()
        offSetComponents.day = offset
        //这个方法
        let dateBefore = calendar.date(byAdding: offSetComponents, to: date)
        
        return dateBefore!
    }
//使用
getDateByAddingDays(baseDate:Date(),addingOffsets:-30)
```
_**主要参考的文章**_

[Swift的Date()](http://www.jianshu.com/p/a6275cc54e04)

[Swift 类型的检査与转换is,as,AnyObject,Any](http://blog.csdn.net/longshihua/article/details/50608655)

[swift中UIDatePicker的使用](http://blog.csdn.net/potato512/article/details/52704281)

[Swift - 日期选择控件（UIDatePicker）的用法](http://blog.csdn.net/swift_vip/article/details/51726580)

[iOS开发Swift—时间器（Timer）的使用](http://blog.csdn.net/liumude123/article/details/54571895)

#### 8 / 24 / 2017
>Today is My Birthday，@yxnne，28，Good Luck！

今天有些进展，核心功能和后台对接完成啦~~~
主要对接之前遗留的数据接口：
1.部门的第一张卡片的图表，之前不支持admin查全院;
2.主页的第三张卡片图表，所有角色一周依从率变化，阿贵帮忙重写了下;
3.设备概要(在android版本没接入)
没有太新的技术点。

_**主要参考的文章**_

[iOS swift3 Timer 的正确使用](http://www.jianshu.com/p/8fb1f010f3f6)

[swift Dictionary 字典](http://blog.csdn.net/zhang_biao_1991/article/details/31344785)

[Swift Dictionary Set Array](http://www.jianshu.com/p/67a0cf4dad2d)

#### 8 / 28 / 2017

基本完成了设置页面：设置两个定时事件的查询时间（包括写入持久层以及启动时读取的相关逻辑）以及退出登录等。
启动页面完成。
那么App的第二阶段就暂时这样了。

_**主要参考的文章**_

[LaunchScreen.storyboard使用介绍](http://www.cnblogs.com/741162830qq/p/4878247.html)


__学而不思则罔，思而不学则殆__