---
layout: post
title:  React Router 4 简单使用
date:   2017-12-18
categories: Developing_React
tags: [Developing_React]
---
<big>React-Router</big> 是官方推荐的React前端路由器，需要注意的是版本2和4差异还是比较大的。

#### 安装配置React-Router

安装的方式还是npm,或者cnpm，终端进入项目根目录下,敲:

{% highlight ruby %}

$ npm i --save react-router-dom 

{% endhighlight %}

在react router4  分为dom版本和native版本，所以开发web应用的时候是，npm安装的应该是react-router-dom 

#### 最基本使用

#### Route路由

{% highlight ruby %}

import React from 'react';
import ReactDOM from 'react-dom';
import AppRootComponent from './index';
import ComponentList from './component/test_router';

import {
  BrowserRouter as Router,
  Route,
  Switch
} from 'react-router-dom';

class Root extends React.Component{

  render(){

    return (

        <Router>
          <Switch>
            <Route exact path="/" component={AppRootComponent}/>
            <Route path="/list" component={ComponentList}/>
          </Switch>
        </Router>
    )
  }
}

ReactDOM.render(
  <Root/>,
  document.getElementById('example')
);


{% endhighlight %}

上述代码的意思就是：

* 当地址栏输入:"your_server_ip：port/ "时，去加载组件AppRootComponent；

* 当地址栏输入:"your_server_ip：port/list "时，去加载组件ComponentList；

**遇到问题**

尽管这样配置的，但是在最开始输入跳转list的时候，比如：localhost:8080/list,页面显示<big>Cannot GET /list</big>。

解决的办法是：在webpack.config.js中devServer对象中配置historyApiFallback:true属性。

这个属性的意义在于：

>它通过这个路径来访问后台，所以会出现404，而把historyApiFallback设置为true那么所有的路径都执行index.html。

#### Route路由嵌套

以前在React Route 2中,Router可以嵌套起来，例如下面写法，假设访问localhost/detail后就会显示AppRootComponent组件中嵌套了ComponentInMain组件：

{% highlight ruby %}

<Router>

    <Route exact path="/" component={AppRootComponent}>
	
		<Route path="/detail" component={ComponentInMain}/>

    </Route>
     
</Router>

{% endhighlight %}

当然在组件AppRootComponent中的合适位置中要这样：

{% highlight ruby %}

{this.props.children}

{% endhighlight %}

不过这样的方法在React Router 4中是行不通的，在v4^版本，不让在"<Route>"中嵌套，嵌套的话会有warnning而且页面效果出不来：

>Warnnig: you should not use <Route component > and <Route children> in the same route ;<Route children> will be ignored.


所以：react router 4 中实现类似嵌套的效果：

{% highlight ruby %}

<Router>
    <Switch>
        <Route exact path="/" component={AppRootComponent}>
        </Route>

        <AppRootComponent>
            <Route path="/detail" component={ComponentInMain}/>
        </AppRootComponent>

     </Switch>
</Router>

{% endhighlight %}

组件AppRootComponent中的合适位置上还是用{this.props.children}，但是明显的的，组件内嵌套路由不会用Route包裹Route了，而是直接将Route放在组件内。

其实这样写倒是更好理解的，因为我记得this.props.children本来不就是组件使用时标签内放的东西么。

另外，举个小例子再说说，Switch标签中的东西的切换是这样的规则：

{% highlight ruby %}

<Router>
    <Switch>
        <Route exact path="/" component={A}> </Route>

        <B>
            <Route path="/b1" component={B1}/>
            <Route path="/b2" component={B2}/>
        </B>

        <Route path="/c" component={C}/>

     </Switch>
</Router>

{% endhighlight %}

上述例子中：
* "/"匹配后页面渲染A组件

* "/b1"匹配后，渲染的应该是<B><B1></B>这个嵌套形式的，同理"/b2"也一样

* "/c"匹配后页面渲染C组件

* 另外，为什么"/"要加参数 exact 是因为要是不加的话，凡是带有"/"的都会匹配到"/"下，也就是说，"/b1"和"/"结果是一样的。

[被多处引用的官方小例](https://reacttraining.com/react-router/web/example/basic)

#### 基本跳转Link

<Link>标签的使用就像a标签的使用方式一样，Link的功能就是，跳转路由。Link中的属性to就是目的路由地址。差不多就是**a-->Link**,**href-->to**。比如在Header组件的render方法中，return :

{% highlight ruby %}

    return (

      <header className={headerCss.miniHeader} onClick={this.switchHeader.bind(this)}>
      
        {Tittle}

        {/*注释:Link 链接*/}

        <ul>
          <li><Link to={'/detail'}>Root</Link></li>
          <li><Link to={'/list'}>List</Link></li>
        </ul>
      </header>
    )

{% endhighlight %}

上述代码中，点击ul中的第一个li就路由到"/detail"对应页面,点击第二个就路由到"/list"对应页面。

当然，前提条件是"/detail"、"/list"都已经配置了。

#### 页面间参数传递

* 定义参数

{% highlight ruby %}

<Route path="/list/:id" component={ComponentList}/>

{% endhighlight %}

Router中这样配置path="/list/:id"，冒号后面就是参数的名字

传递的参数会在URL中显示比如/list/123456

在页面组件中接收参数用 this.props.match.params.参数名字 ，这样就拿到了这个参数

* 传递参数

比如之前例子中的Link to属性中将/list变成 /list/12345 再次点击 该Link标签后，跳转时将把12345作为id参数来传递。

{% highlight ruby %}

<li><Link to={'/list/12345'}>List</Link></li>

{% endhighlight %}

* 使用参数

接收参数的组件使用"{this.props.match.params.你的参数名}"来获得参数。

__路漫漫其修远兮，吾将上下而求索__

