---
layout: post
title: 简单iOS的APP开发记录_第三周
date:   2017-09-28
categories: "Developing_iOS"
tags: [iOS, APP]
author: yxnne
comment: true
---

>这是在我快速的开发完公司iOS 原生App后，将开发每一次的记录整理起来的。我不是一个专业的iOS开发人员，所以很多东西需要记录，包括琐碎的各方面的知识点，上线的流程，甚至语法。

### 8 / 1 / 2017
完成了部门人员依从率的Table集成。
实现了下拉刷新，也是使用了框架：DGElasticPullToRefresh

_**学到的东西**_
iOS列表类Table，似于Android的ListView
是需要将Cell定义好往里面填充的。Cell的样式可以在IB里面直接添加好。纯代码的添加样式也可以：
这是Cell ，  required init?(coder aDecoder: NSCoder)这个方法要是用IB的话不需要实现，但是不用IB需要指定位置
```
class MyEventsCell: UITableViewCell{
    @IBOutlet var labelName:UILabel!
    @IBOutlet var labelEvent:UILabel!
    @IBOutlet var labelTime:UILabel!
    
    //var isColorSetted = false
    
    
    required init?(coder aDecoder: NSCoder) {
        super.init(coder: aDecoder)
    }
    
    override init(style: UITableViewCellStyle, reuseIdentifier: String?) {
        super.init(style: style, reuseIdentifier: reuseIdentifier)
        
        self.selectionStyle = .default
        
        labelName = UILabel(frame:CGRect(x:40, y:10, width:80, height:30))
        labelEvent = UILabel(frame:CGRect(x:130, y:10, width:150, height:30))
        labelTime   = UILabel(frame:CGRect(x:260, y:10, width:150, height:30))
        
        labelName.font = UIFont.systemFont(ofSize: 14)
        labelEvent.font = UIFont.systemFont(ofSize: 14)
        labelTime.font = UIFont.systemFont(ofSize: 14)
        
        self.addSubview(labelName)
        self.addSubview(labelEvent)
        self.addSubview(labelTime)
        
    }
    
    
}
```
这是使用方法
```
//创建Table
        let tabelRect = CGRect(x:15,y:130,width:cardViewHeaderRect.width - 20,height:pageMainView.bounds.height - 30 )
        tableView = UITableView(frame:tabelRect,style: .plain)
        tableView.dataSource = self     // 数据的代理
        tableView.delegate = self       // 点击的代理
        tableView.register(MyDepartStaffRateCell.self, forCellReuseIdentifier: "staffRateCell")  // 注册Cell
        tableView.rowHeight = 40
        pageMainView.addSubview(tableView)
        
        //下拉刷新控件
        let loadingView = DGElasticPullToRefreshLoadingViewCircle()
        loadingView.tintColor = UIColor.white
        tableView.dg_addPullToRefreshWithActionHandler({ [weak self] () -> Void in
            DispatchQueue.main.asyncAfter(deadline: DispatchTime.now() + Double(Int64(1.5 * Double(NSEC_PER_SEC))) / Double(NSEC_PER_SEC), execute: {
                self?.tableView.dg_stopLoading()
                //执行
                print("下拉回调")
            })
            }, loadingView: loadingView)
        tableView.dg_setPullToRefreshFillColor( UIColor.colorWithHexString(hex: "#00C0B9"))
        tableView.dg_setPullToRefreshBackgroundColor(tableView.backgroundColor!)

```
_**主要参考的文章**_
[DGElasticPullToRefresh](https://github.com/gontovnik/DGElasticPullToRefresh)
[Swift出代码写cell](http://blog.csdn.net/syg90178aw/article/details/46981277)

### 8 / 2 / 2017
类似的完成事件相关的两个页面，table的方法和之前的差不多。
又利用IB快马加鞭的完成了deviceInfo页面，并没有技术难点。

### 8 / 4 / 2017
基本界面整体跑通。下周开始对接接口。接口沿用Android App的接口。

以后就要开始对接了。

__学而不思则罔，思而不学则殆__

