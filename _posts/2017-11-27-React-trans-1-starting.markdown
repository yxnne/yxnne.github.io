---
layout: post
title:  React-官网学习-QuickStart1-HelloWorld
date:   2017-11-30
categories: react_learnning
tags: [translations_React.js]
---
<big>React</big> ,来自于FaceBook,是一个JavaScript类库用于构建用户界面的。新的技术都是从HelloWorld开始。（参考的是[React官方文档](https://reactjs.org/docs/hello-world.html),算是简单的翻译了下吧）

关于这部分功能的核心对象是eventEmitter（事件发生器）。

#### Hello World
开始React的最简单方式是使用CodePen([一个在线编译平台，速度有点慢](https://codepen.io/pen?&editors=0010)),这样你就什么都不需要安装了。你要做的就是在浏览器的另一页，打开它，然后照着接下来的例子敲代码。如果你更偏向用本地环境开发，参照[安装引导](https://reactjs.org/docs/installation.html)

最简单的React例子就像这样：

{% highlight ruby %}

ReactDOM.render(
  <h1>Hello, world!</h1>,
  document.getElementById('root')
);

{% endhighlight %}

以上代码给页面渲染一个标题Hello World。

在以后的章节，我们会陆续介绍到React的其他用法，我们将考察React应用的构建模块：元素和组件（elements and components）。一旦掌握了他们，你就能利用可复用的小模块，构造出更复杂的App。

#### 关于JavaScript的一个说明

React是一个JavaScript库，所以，一切都是以你对JavaScript有一个基本认识为基础。要是你对这一点没什么自信，我们推荐你[温故下JavaScript的知识](https://developer.mozilla.org/en-US/docs/Web/JavaScript/A_re-introduction_to_JavaScript)，这样，你会轻松不少。

我们也用到了部分ES6的语法。我们尽量不用太多因为这是相对新一点的技术，不过你还是最好熟悉熟悉像[箭头函数]()、[类](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Classes)、[模板文字](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Template_literals)、[let](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/let)和[const](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/const).也可用Babel REPL 测试ES6代码编译成什么样子。

>注 to me ：箭头函数就类似拉姆达表达式，匿名函数
let声明变量类似于Swift的var
const类似于Swift的let



[官网文章 Quick Start :Hello World](https://reactjs.org/docs/hello-world.html)
