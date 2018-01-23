---
layout: post
title: 简单iOS的APP开发记录
date:   2017-09-24
categories: "Developing_iOS"
tags: [iOS, APP]
author: yxnne
comment: true
---

>这是在我快速的开发完公司iOS 原生App后，将开发每一次的记录整理起来的。我不是一个专业的iOS开发人员，所以很多东西需要记录，包括琐碎的各方面的知识点，上线的流程，甚至语法。

下面是当时的记录：

Web系统基本稳定，Android版本六月底也基本写完了，不过，领导又要求上iOS版本，作为一个软件开发者，要为老板分忧啊！哈哈，不会咋办，学呗，边学边做吧。草草的看了一遍《呆瓜牧场iOS》(Big Nerd Ranch)，直接上手吧~
__God bless me!__
作为一个iOS开发菜鸟，过程全程记录下，以便于以后回顾学习和鄙视自己。

#7 / 17 / 2017
项目初始化，在公司的阿里云的gitblit上新建了iOS项目。
确定页面按UI设计的安卓版本做。
计划先实现UI，再对接后台，项目难度不算大。

#7 / 18 / 2017
1.做了最基本的两个页面，登录页面 和 服务器配置页面
2.在登录页使用了UINavigationController，并让他变成透明的了
3.控件加上了Outlet和Action，等待后续具体实现吧 （用的xCode assistant 编辑模式还是比较快）

_**学到的东西**_
>  self.navigationController?.navigationBar.isTranslucent = true //改变透明度的属性

#7 / 19 / 2017
增加了跳转流程：
逻辑是这样：登录跳转到主页，主页上底部四个tab，每一个tab对应的viewController都给他集成了NavigationBar。
_这里有个问题，因为上一次提交的时候，在登录页面集成的navigationBar，想让他跳转到ServerConfiguration的时候跳转回来方便点，但是这样就导致了，从login登录成功跳转到主页的时候，主页也在navigationBar的栈里面，也就是说，主页左上角有个小箭头，一点击又返回登录页面了。_
当前的解决方法是，登录页面不集成navigationBar了，直接在主页的四个tab的ViewController上集成navigationbar。
登录页跳转到ServerConfig使用模态Segue(show Modally)
我知道这样不是最好....

_**学到的东西**_

>   跳转页面的方法：
>   self.performSegue(withIdentifier: "login", sender: self)
  
比如说登录：
```
         if checkLogin(userName, pwd){//如果用户名称密码输入正确
                print("username and password is correnct! and let do it one more step!")
                
                //这是呈现下一个页面的方法
                self.performSegue(withIdentifier: "login", sender: self)
            } else {//用户名密码不正确，提示下
            }
```
>   页面关闭方法：
```
           self.dismiss(animated: true, completion: nil)
```
_**心得感想**_
(.stroyboard)里面操作布局，增加约束现在比原来玩的⑥
PS:InterfaceBuilder(.stroyboard)从一个AndroidDeveloper的角度来看，是好还是不好呢？

#7 / 20 / 2017
做了这么几件事情：
1.用UIView 以及StackView和静态的TableView布局了ProfilePage(个人主页)，以及Outlet了未来将动态改变的一些控件；
2.在个人主页上使用了view添加了TapGestrueRecognizer响应点击事件，这个作为一个btn,提供了空的回调，以后待实现；
3.扩展了UIImage用来将一副图片进行圆形剪裁，扩展的类在extensions包下面；
4.在Profile（个人主页上）：导航栏的背景色，导航栏文字的颜色也设置了；
5.个人主页的顶上导航navigationItem添加了通往设置的按钮，并且增加了设置的ViewController；
6.设置的SettingViewController中利用生命周期方法控制TabBar的隐藏属性，解决了跳转到设置页面依然可见TabBar的问题；

_**学到的东西**_

1.view添加了TapGestrueRecognizer

```
       let viewBtnTapRecognizerRight = UITapGestureRecognizer(
            target:self,action:#selector(repondTap(_:))
       )
       // 在两个view上添加手势识别器 两个view的点击事件是一样的
       viewLeftAsBtn.addGestureRecognizer(viewBtnTapRecognizerLeft)
```
2.导航栏的背景色

```
        //更改导航栏的背景色
        navigationController?.navigationBar.barTintColor = UIColor.green
        //更改导航栏的文字颜色 赋值号右边创建了一个字典对象，NSForegroundColorAttributeName是一个String类型的
        navigationController?.navigationBar.titleTextAttributes = [NSForegroundColorAttributeName: UIColor.white]
```

_**参考的文章**_

[关于StackView](http://www.jianshu.com/p/b2228ba2ffae)

[ViewController的生命周期](http://blog.csdn.net/qijianli/article/details/7826979/)

[修改导航栏样式](http://www.hangge.com/blog/cache/detail_962.html)

[圆形图片裁剪解决方案](http://www.hangge.com/blog/cache/detail_1535.html)

[一篇关于类似CardView的方案](https://stackoverflow.com/questions/28141021/do-card-view-with-swift)


#7 / 22 / 2017
这两天这要在完成主页设计：
主页是登录app后的第一个tab，该页面上除去navigationBar和TabBar之外分为四个区域，从上到下线性布局，依次是轮播banner，卡片1-3（图表展示）
这样子的话正常页面大小是放不下的，所以需要scrllView。
ScrollView这个东西在InterfaceBuidler上不太好控制，应该是我不会，不过时间有限，用代码在viewcontroller里面写吧。

_**学到的东西**_
>1.
一个UIView 的Frame决定了他能在页面上呈现的位置和大小，Frame需要给他一个CGRect作为参数。
UIScrollView还需要一个contentSize，这个是ScrollView的内部实际大小，决定了他滚动的范围
UIScrollView还要设置一个代理遵循UIScrollViewDelegate协议

```
let scrollview = UIScrollView()
        //全屏幕的frame尺寸
        scrollview.frame = view.bounds
        scrollview.delegate = self //代理
        //设置contentSize，实际内容大小
        scrollview.contentSize = CGSize(width:view.bounds.width , height:810)
//当然 ，最后要把这个scrollView添加到View上
```

>2.
这种get set的写法很有意思

```
    //卡片3 的位置
    var cardViewRect3 : CGRect{
        get{
            return CGRect(x:5,y:610,width:self.view.bounds.width - 10,height:190)
        }
    }
    
```
>3.
给一个View加上阴影让它类似于android的CardView

```
        //加阴影学习
        viewAdd_1.layer.cornerRadius = CGFloat(2.0)//加了个圆角
        let shadowPath = UIBezierPath(roundedRect: viewAdd_1.bounds, cornerRadius: 2.0)//路径
        viewAdd_1.layer.masksToBounds = false
      
        viewAdd_1.layer.shadowColor = UIColor.black.cgColor
        viewAdd_1.layer.shadowOffset = CGSize(width: 2, height: 2);
        
        viewAdd_1.layer.shadowOpacity = 0.5
        viewAdd_1.layer.shadowPath = shadowPath.cgPath
```

>4.
该UILabel文字大小但是不改变字体

```
 moreLabel.font = UIFont.systemFont(ofSize:11)
```

_**参考的文章**_

[关于闭包](http://blog.csdn.net/fengsh998/article/details/29353019)

[在项目中引用第三方库，例如Charts](http://www.jianshu.com/p/2781d28334cd)

[详解UIFont](http://www.jianshu.com/p/306f2ba8ffd9)

[UIView的属性](http://www.jianshu.com/p/1470cf94f288)

[怎样做一个自定义的CardView](https://stackoverflow.com/questions/28141021/do-card-view-with-swift)

[UIScrollView的一些设置](http://www.jianshu.com/p/dd654ae429c4)

[简单使用UIImage](http://www.hangge.com/blog/cache/detail_534.html)


