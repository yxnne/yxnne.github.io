---
layout: post
title:  React-官网学习-QuickStart2-JSX
date:   2017-12-1
categories: react_learnning
tags: [React.js]
---
<big>JSX</big> ,JavaScript的一种语法扩展。

看看下面这个变量声明：

{% highlight ruby %}

const element = <h1>Hello, world!</h1>;

{% endhighlight %}

这个有趣的语法既不是字符串也不是HTML。

它叫JSX，是一种JavaScript的语法扩展。我们推荐使用JSX来描述UI。JSX可能让你觉得是一种模板语言，可他具有JavaScript的全部功能。

JSX'创建'了React的"元素 elements"。以后我们将继续探索并渲染他们。下面是在开始React前一些关于JSX必要的基础。

#### 在JSX中嵌入JavaScript表达式

你可以在JSX中嵌入JavaScript表达式。这些表达式需要被花括号包裹。

举例像：2 + 2, user.firstName, and formatName(user) 这些都是表达式。

{% highlight ruby %}

function formatName(user) {
  return user.firstName + ' ' + user.lastName;
}

const user = {
  firstName: 'Harper',
  lastName: 'Perez'
};

const element = (
  <h1>
    Hello, {formatName(user)}!
  </h1>
);

ReactDOM.render(
  element,
  document.getElementById('root')
);

{% endhighlight %}

为了可读性，我们把JSX文件分成很多行，当然这不是必须的，但当这么做时，我们推荐使用用圆括号包裹起来，以防一些自动插入的分号导致的错误。

#### JSX也是一种表达式

编译之后，那些JSX代码就成了常规的JavaScript对象。

这就意味着你在JSX中可以使用if，使用for循环，把JSX分配给变量，接受参数以及作为函数的返回值。

{% highlight ruby %}

function getGreeting(user) {
  if (user) {
    return <h1>Hello, {formatName(user)}!</h1>;
  }
  return <h1>Hello, Stranger.</h1>;
}

{% endhighlight %}

#### 用JSX指定参数

你可能在用"引号"quotes来指定字符化的属性参数：

{% highlight ruby %}

const element = <div tabIndex="0"></div>;

{% endhighlight %}

你也可以用花括号包裹着JavaScript表达式作为属性参数。

{% highlight ruby %}

const element = <img src={user.avatarUrl}></img>;

{% endhighlight %}

指定这种JavaScript表达式的属性时，可别在花括号外面包裹引号。要么你用引号的方式指定属性，要么是大括号包裹JavaScript的方式，但不能两者都用。

>警示：JSX和JavaScript的关系比和Html更紧密，ReactDOM使用驼峰命名替代Html属性名。
比如class就变成了className,tabindex就变成了tabIndex。

#### 指定"孩子"


若一个标签是空，可以直接/>关闭它，就像XML。

{% highlight ruby %}

const element = <img src={user.avatarUrl} />;

{% endhighlight %}

JSX标签中也可以包含"孩子"标签。

{% highlight ruby %}

const element = (
  <div>
    <h1>Hello!</h1>
    <h2>Good to see you here.</h2>
  </div>
);

{% endhighlight %}

#### JSX防止注入攻击

JSX写入用户输入是安全的。

{% highlight ruby %}

const title = response.potentiallyMaliciousInput;
// This is safe:
const element = <h1>{title}</h1>;

{% endhighlight %}

默认情况下，ReactDOM在JSX被渲染都所有值使用escape()函数编码。因此这样可以确保任何非明确数值写入到App中。任何东西在渲染之前都装换成了字符串。这样就防止了XSS (cross-site-scripting) 攻击。

>跨站脚本攻击from_baudu:跨站脚本攻击(Cross Site Scripting)，为了不和层叠样式表(Cascading Style Sheets, CSS)的缩写混淆，故将跨站脚本攻击缩写为XSS。恶意攻击者往Web页面里插入恶意Script代码，当用户浏览该页之时，嵌入其中Web里面的Script代码会被执行，从而达到恶意攻击用户的目的。

#### JSX代表对象

Babel编译JSX编译成React.createElement()调用。下面两个例子一个意思。

{% highlight ruby %}

const element = (
  <h1 className="greeting">
    Hello, world!
  </h1>
);

{% endhighlight %}

{% highlight ruby %}

const element = React.createElement(
  'h1',
  {className: 'greeting'},
  'Hello, world!'
);

{% endhighlight %}

React.createElement() 会有一些检查来帮助你的代码出bug少点，不过在本质上，它这样创建元素：

{% highlight ruby %}

// Note: this structure is simplified
const element = {
  type: 'h1',
  props: {
    className: 'greeting',
    children: 'Hello, world'
  }
};

{% endhighlight %}

这就是React元素，你就当他们是在描述那些你希望呈现在屏幕上的东西。React读懂它们且用之去构建DOM并更新它们。

下一节我们将探索渲染React元素。

>提示：在你的代码编辑器中推荐使用Babel，这样JSX和ES6代码都会有高亮提示。

在以后的章节，我们会陆续介绍到React的其他用法，我们将考察React应用的构建模块：元素和组件（elements and components）。一旦掌握了他们，你就能利用可复用的小模块，构造出更复杂的App。本站的主题与之兼容的 [Oceanic Next](https://labs.voronianski.com/oceanic-next-color-scheme/) 。


[官网文章 Quick Start :JSX](https://reactjs.org/docs/introducing-jsx.html)
