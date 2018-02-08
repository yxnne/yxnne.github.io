---
layout: post
title:  React-官网学习-QuickStart6-处理事件
date:   2017-12-7
categories: react_learnning
tags: [translations_React.js]
---
<big>处理事件</big> ，用React,和用DOM非常相似，就是些语法差异：

* React用驼峰法命名而不是纯小写;

* 事件处理函数的设置使用JSX而不是字符串;

比方说,这是HTML：

{% highlight javascript %}

<button onclick="activateLasers()">
  Activate Lasers
</button>

{% endhighlight %}

React，和它略有不同：

{% highlight javascript %}

<button onClick={activateLasers}>
  Activate Lasers
</button>

{% endhighlight %}

另一处差异是，在React中，你不能通过返回false来阻止默认行为。你得显示地调用preventDefault()方法。举例来说，在普通HTML中，要想阻止打开新页面的超链接行为的话，你可以这样：

{% highlight javascript %}

<a href="#" onclick="console.log('The link was clicked.'); return false">
  Click me
</a>

{% endhighlight %}

在React中，就是这样：

{% highlight javascript %}

function ActionLink() {
  function handleClick(e) {
    e.preventDefault();
    console.log('The link was clicked.');
  }

  return (
    <a href="#" onClick={handleClick}>
      Click me
    </a>
  );
}

{% endhighlight %}

这里，e代表了一个合成事件。React定义了不少合成事件，但你也不用担心浏览器兼容问题因为都是根据w3c文档定义的。[这里参考更多的关于合成事件的说明](https://reactjs.org/docs/events.html)。

当你使用es6定义class作为组件时，事件处理器的一种常规的模式是class里面定义方法。例如，Toggle这个组件渲染了一个按钮，允许你在“on”和“off”之间切换:

{% highlight javascript %}

class Toggle extends React.Component {
  constructor(props) {
    super(props);
    this.state = {isToggleOn: true};

    // This binding is necessary to make `this` work in the callback
    // ‘this’绑定是必要的
    this.handleClick = this.handleClick.bind(this);
  }

  handleClick() {
    this.setState(prevState => ({
      isToggleOn: !prevState.isToggleOn
    }));
  }

  render() {
    return (
      <button onClick={this.handleClick}>
        {this.state.isToggleOn ? 'ON' : 'OFF'}
      </button>
    );
  }
}

ReactDOM.render(
  <Toggle />,
  document.getElementById('root')
);

{% endhighlight %}

你要小心那些JSX里面的**‘this’**。JavaScript中，类方法默认不绑定。要是你忘记了绑定this.handleClick却把它传递给了onClick，那么调用的时候，this就是undefined了。

这可不是React的特有行为，这是[JavaScript函数运行机制](https://www.smashingmagazine.com/2014/01/understanding-javascript-function-prototype-bind/)决定的。通常，要是引用函数时没有()跟着,就像onClick={this.handleClick}，你就得用bind()来绑定函数。

要是你讨厌用bind，这也有两种方法绕过它：如果你正在使用实验性“ public class fields ”（公共类属性）语法，你就能使用类属性绑定函数调用：


{% highlight javascript %}

class LoggingButton extends React.Component {
  // This syntax ensures `this` is bound within handleClick.
  // Warning: this is *experimental* syntax.
  // 这种语法保证this是绑定了handleClick的
  // 警告:这是实验性语法(估计不太靠谱)
  handleClick = () => {
    console.log('this is:', this);
  }

  render() {
    return (
      <button onClick={this.handleClick}>
        Click me
      </button>
    );
  }
}

{% endhighlight %}

这种语法是被[ Create React App 从头创立ReactAPP](https://github.com/facebookincubator/create-react-app)所支持的。

你要是不用这种语法,你也可以使用箭头函数到onClick调用时：

{% highlight javascript %}

class LoggingButton extends React.Component {
  handleClick() {
    console.log('this is:', this);
  }

  render() {
    // This syntax ensures `this` is bound within handleClick
    return (
      <button onClick={(e) => this.handleClick(e)}>
        Click me
      </button>
    );
  }
}

{% endhighlight %}

这种写法的问题在于:每当LoggingButton被渲染时都会有一个新调用函数产生。大多数这样没毛病。但是，假如调用被当做属性props传递给下级组件，这些组件可能会再额外的重新渲染一番。我们通常推荐构造器中绑定法或者使用类属性法避免这种问题。

##### 事件处理中的参数传递

循环中，常有事件处理时需要传递其他参数的情况。例如，id是行id，下面两种都是可行的：

{% highlight javascript %}

<button onClick={(e) => this.deleteRow(id, e)}>Delete Row</button>

<button onClick={this.deleteRow.bind(this, id)}>Delete Row</button>

{% endhighlight %}

以上分别使用箭头函数和函数原型绑定，一样的都行。

这两种情况下，参数‘e’都代表了React的事件，都会在传递id参数之后被传递。在箭头函数中，我们显示的传递了它，但是在绑定中它被自动传递了。

[官网文章 Quick Start :Handling Events](https://reactjs.org/docs/handling-events.html)
