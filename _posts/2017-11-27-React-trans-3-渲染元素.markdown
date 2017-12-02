---
layout: post
title:  React-官网学习-QuickStart3-渲染元素
date:   2017-12-1
categories: react_learnning
tags: [React.js]
---
<big>Element(元素)</big> ,就是描述你在屏幕上见到的东西。

{% highlight ruby %}

const element = <h1>Hello, world</h1>;

{% endhighlight %}


不像浏览器的DOM元素，React元素就是简单对象，并且创建成本很低。ReactDOM负责更新浏览器DOM来适应ReactElement元素。

>注意：也许有人要对element（元素）和components（组件）的概念感到疑惑。我们将要在下一节介绍组件。元素是组件的组成部分，所以别急着往后翻阅，最好先看看这节吧。

#### 在DOM中渲染Element

看看你HTML文件某处的'<div>'标签。

{% highlight ruby %}

<div id="root"></div>

{% endhighlight %}

我们把它称作'root' DOM （根）节点是因为，在这里面的任何东西都得归ReactDOM管理。

使用React构建的APP通常只有一个根DOM节点。不过如果要是将React集成到已有项目中去，那就可能有很多孤立的根节点了，想要多少有多少。

要在一个根DOM节点中去渲染一个React Element元素，要把整个元素放在ReactDOM的render()方法中。

{% highlight ruby %}

const element = <h1>Hello, world</h1>;
ReactDOM.render(
  element,
  document.getElementById('root')
);

{% endhighlight %}

以上在页面上呈现HelloWorld。

#### 更新已渲染元素

React的元素是不可变的。一旦你创建了一个元素，你就不能改变其子元素或者是属性。元素就像电影里单独的某一帧：它代表了UI在特定时间点的的模样。

就目前我们所掌握的React知识而言，想要更新UI只要一个办法那就是重新创建一个新的元素并且将它渲染在DOM里面。

考虑下下面的例子

{% highlight ruby %}

function tick() {
  const element = (
    <div>
      <h1>Hello, world!</h1>
      <h2>It is {new Date().toLocaleTimeString()}.</h2>
    </div>
  );
  ReactDOM.render(
    element,
    document.getElementById('root')
  );
}

setInterval(tick, 1000);
{% endhighlight %}

以上例子中，通过setInterval()每秒钟都在调用ReactDOM.render()。
（也就是说，每秒都创建了一个新React元素，每次渲染的都是这个新元素）

>注意：实践中，大多数APP都只调用ReactDOM。render()一次。下一节中我们将学习到这样的代码怎样给它封装成有状态的组件。
我们建议不要跳过每个主题，因为他们是相互支撑的。

#### React只更新必须要更新的

ReactDOM比较当前组件，包括他的子组件的上一个状态，并且只让DOM做必要的更新来达到预期的状态。

你可以运行本节的最后一个例子并用浏览器工具证明这一点。

（确实，尽管每秒都在调用tick()，而tick中又重新创建元素并且将之渲染，但是在浏览器中的查看代码，结发现是DOM中只有"时间"( <h2>It is {new Date().toLocaleTimeString()}.</h2>)在变）

即使我们每次tick()的时候创建了整个UI树模型，但是也只有产生新内容的文字节点被更新。

我们的经验表明，比起整天考虑怎样改变UI这种问题，专注考虑UI应该是什么样子更能减少一些类型的Bug。


[官网文章 Quick Start :Rendering Elements](https://reactjs.org/docs/rendering-elements.html)
