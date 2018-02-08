---
layout: post
title:  简单使用 react-responsive 做页面响应式开发 
date:   2017-12-20
categories: Developing_React
tags: [Developing_React]
---
<big>响应式</big> 和 自适应 不是一个概念：自适应，即在不同尺寸的屏幕上，可以自动适应屏幕尺寸，这样的不同屏幕的页面样子都是一样的。响应式大概就是，根据不同屏幕，适配不同代码，来决定是显示大屏幕代码还是小屏幕代码，是适配移动端屏幕还是pc短屏幕。

还是担心脑子不好，所以尽管接触浅显但还是记下吧，以防日后见忘~~

#### react-responsive

[react-responsive](https://www.npmjs.com/package/react-responsive)是最好的，最好用的支持react的media-query模块。

这个模块够直接：你把不同适配的需求整理出来，这些需求满足条件就会被渲染，而且你随便改变屏幕尺寸的时候都会响应变化。

#### 安装还是npm

{% highlight javascript %}

$ npm install react-responsive --save

{% endhighlight %}

#### 使用起来不啰嗦

{% highlight javascript %}

class Root extends React.Component{

  render(){
    //这里是程序入口
    return (
        <div>
          {/* PC */}
          <MediaQuery query='(min-width: 1224px)'>
            <PCIndex/>
          </MediaQuery>

          {/* Moblie */}
          <MediaQuery query='(max-Width: 1224px)'>
            <MobileIndex/>
          </MediaQuery>
        </div>
    )
  }
}

{% endhighlight %}

解释下：屏幕最小是1224px时，加载PC端，反之，加载移动端。

#### 另外：属性 max-device-width 和 max-width 的区别

简言之就是带上device就是设备宽度，一台设备的设备属性当然是不会改变的，所以假设你pc端打开的网页使用了 max-device-width ，那么缩放你得浏览器，不产生响应效果。当然，你要是换了设备，自然会得到预先设置好的效果。

[参考文章：区别](https://www.cnblogs.com/futai/p/5015538.html)

[相关文章：css3 media 属性](http://blog.csdn.net/a810600562/article/details/64437702)

__路漫漫其修远兮，吾将上下而求索__

