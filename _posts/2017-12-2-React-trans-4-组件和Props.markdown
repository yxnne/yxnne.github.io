---
layout: post
title:  React-官网学习-QuickStart4-组件和Props
date:   2017-12-2
categories: react_learnning
tags: [translations_React.js]
---
<big>Components(组件)</big> ,使你能够将UI划分成独立的，可复用的部件。每一个部件都能被独立考虑。

概念上，组件就类似于JavaScript的函数。他们都接受任意的输入(叫做Props)并返回一个用于描述屏幕模样的React Element(元素)。

#### 函数形式的和类形式的组件

最简单的方式定义一个组件是写一个JavaScript函数：

{% highlight ruby %}

function Welcome(props) {
  return <h1>Hello, {props.name}</h1>;
}

{% endhighlight %}

这是一个可用的React组件因为它接受了一个携带了数据的参数"props"(代表porperties，属性)并返回了一个React元素。我们称这样是函数方式的组件因为使用JavaScript写函数的方式去书写的。

你也可以用ES6的class去定义一个组件：

{% highlight ruby %}

class Welcome extends React.Component {
  render() {
    return <h1>Hello, {this.props.name}</h1>;
  }
}

{% endhighlight %}

上面两种方式对于React来说是一样的。

"类"有很多我们后续会讲到的额外特性，到那之前，我们都用简洁的函数方式。

#### 渲染一个组件

先前我们见到过React元素，元素代表DOM的标签。

{% highlight ruby %}

const element = <div />;

{% endhighlight %}

然而，元素也能代表用户定义的组件：(Welcome标签显然是用户定义的组件)

{% highlight ruby %}

const element = <Welcome name="Sara" />

{% endhighlight %}

当React发现一个元素代表的是用户自定义组件时，就把这些JSX属性作为一个对象整体传递给该组件。这个被传递的对象就是props。

例如，下面代码渲染了一个"Hello,Sara"在页面上

{% highlight ruby %}

function Welcome(props) {
  return <h1>Hello, {props.name}</h1>;
}

const element = <Welcome name="Sara" />;
ReactDOM.render(
  element,
  document.getElementById('root')
);

{% endhighlight %}

让我们重温下这个例子：

1.我们调用ReactDOM.render()，该方法里面有元素：<Welcome name="Sara" />

2.React调用Welcome组件，并且将{name: 'Sara'} 对象作为参数props。

3.Welcome组件返回了一个元素作为结果："<h 1>Hello, Sara</h 1>"

4.React高效的更新DOM

>附加说明：组件的首字母要大写，例如<div />代表了HTML的标签但<Welcome />表示React组件而且需要Welcome是闭合标签。

#### 组建组件

一个组件也能在输出时设计其他组件。这就使我们能够在任何层级使用相同的组件抽象。一个按钮，表单，对话框，屏幕。在React的APP中，所有这些都可以用组件来表示。

例如，我们构建另一个组件让它渲染Welcome多次：

{% highlight ruby %}

function Welcome(props) {
  return <h1>Hello, {props.name}</h1>;
}

function App() {
  return (
    <div>
      <Welcome name="Sara" />
      <Welcome name="Cahal" />
      <Welcome name="Edite" />
    </div>
  );
}

ReactDOM.render(
  <App />,
  document.getElementById('root')
);

{% endhighlight %}

通常，新构建的React App在最顶层只有一个组件，然而，如果你是集成React到已有项目中，你可能需要自底向上地开始，从类似于button这样的小组件开始逐渐的提升到视图的顶层。

#### 提取组件
别害怕把组件分割成更小的组件。

例如，考虑这个Comment组件：

{% highlight ruby %}

function Comment(props) {
  return (
    <div className="Comment">
      <div className="UserInfo">
        <img className="Avatar"
          src={props.author.avatarUrl}
          alt={props.author.name}
        />
        <div className="UserInfo-name">
          {props.author.name}
        </div>
      </div>
      <div className="Comment-text">
        {props.text}
      </div>
      <div className="Comment-date">
        {formatDate(props.date)}
      </div>
    </div>
  );
}

{% endhighlight %}

它接受属性：author(对象),text(字符串),和date(日期)，该组件是作为社交网站上的评论组件使用的。

这个组件改变起来是复杂的因为全是嵌套，想要复用其中的某一部分更是难上加难。让我们从中提取些组件出来。

首先，我们提取Avatar：

{% highlight ruby %}

function Avatar(props) {
  return (
    <img className="Avatar"
      src={props.user.avatarUrl}
      alt={props.user.name}
    />

  );
}

{% endhighlight %}

Avatar不需要知道自己是在Comment中被渲染。这就是为什么我们给它的参数命名更具有一般属性：user较与author。

我们推荐属性命名是从组件自身的视角触发而不是根据使用时上下文含义命名。

现在，我们简化了Comment一点点：

{% highlight ruby %}

function Comment(props) {
  return (
    <div className="Comment">
      <div className="UserInfo">
        <Avatar user={props.author} />
        <div className="UserInfo-name">
          {props.author.name}
        </div>
      </div>
      <div className="Comment-text">
        {props.text}
      </div>
      <div className="Comment-date">
        {formatDate(props.date)}
      </div>
    </div>
  );
}

{% endhighlight %}

接下来我们将提取一个UserInfo组件出来，它在user name的旁边渲染Avatar。

{% highlight ruby %}

function UserInfo(props) {
  return (
    <div className="UserInfo">
      <Avatar user={props.user} />
      <div className="UserInfo-name">
        {props.user.name}
      </div>
    </div>
  );
}

{% endhighlight %}

更进一步简化Comment：

{% highlight ruby %}

function Comment(props) {
  return (
    <div className="Comment">
      <UserInfo user={props.author} />
      <div className="Comment-text">
        {props.text}
      </div>
      <div className="Comment-date">
        {formatDate(props.date)}
      </div>
    </div>
  );
}

{% endhighlight %}

提取组件最开始可能是一件令人牢骚满腹的工作，但是在大项目中，拥有一对复用性好的组件价值很高。一个好的经验法则是：假如你的UI的一部分使用了多次，或者自己本身就太复杂了，那么这就是一个很好的可复用提取对象。

#### props是只读的

无论你声明组件是函数式还是类，你永远也不能改变props。

看看这个sum函数：

{% highlight ruby %}

function sum(a, b) {
  return a + b;
}

{% endhighlight %}

这样的函数被称为Pure(纯粹的)是因为它不尝试修改输入，且在输入相同的情况下输出也是有相同值。

相反的，下面的函数"不纯粹"因为它改变了输入的值：

{% highlight ruby %}

function withdraw(account, amount) {
  account.total -= amount;
}

{% endhighlight %}

React很灵活但是有一个严格的规定：

**所有组件必须如同"纯粹函数"那样对待它的props属性**


当然，APP的UI是动态的且时常改变。在下一节，我们介绍新的概念：state状态，状态允许React组件响应用户动作，网络响应和其他事件而随时改变输出状态的，使用状态并不违反这条规则。



[官网文章 Quick Start :Components and Props](https://reactjs.org/docs/components-and-props.html)
