---
layout: post
title:  React-官网学习-QuickStart5-状态和生命周期
date:   2017-12-6
categories: react_learnning
tags: [React.js]
---
<big>考虑下</big> 之前章节学习的时钟的例子。在本章，我们将要学习怎样真正复用和封装这个‘时钟’，它会拥有自己的计时器并没秒自己更新自己。

到目前为止，我们只有一种途径更新UI。

那就是调用ReactDOM.render()方法来改变渲染输出。

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

在本章，我们将要学习怎样真正复用和封装这个‘时钟’，它会拥有自己的计时器并没秒自己更新自己。

让我们开始于封装它的外观。

{% highlight ruby %}

function Clock(props) {
  return (
    <div>
      <h1>Hello, world!</h1>
      <h2>It is {props.date.toLocaleTimeString()}.</h2>
    </div>
  );
}

function tick() {
  ReactDOM.render(
    <Clock date={new Date()} />,
    document.getElementById('root')
  );
}

setInterval(tick, 1000);

{% endhighlight %}

不过这样少了重要的一点：实际上，钟表应该有这么个细节实现，那就是钟表自身维护一个计时器然后每秒更新自己的UI。

理想情况是，我们想一次性写成然后让它自己更新：

{% highlight ruby %}

ReactDOM.render(
  <Clock />,
  document.getElementById('root')
);

{% endhighlight %}

为了实现这样，我们需要为组件引入State（状态）。

State（状态）有点像props（属性），但是state状态是私有的，而且完全由组件自身管理。

我们之前谈到过，说组件如果定义成了class形式（不是函数式）将会有很多额外的特性。那么State就是这样：一种只有class形式定义组件才可用的特性。

#### 把函数式转换成class形式

把一个类似钟表的组件，从函数式转换一个class形式，需要5个步骤：

1.创建一个es6新特性的class，和函数同名且继承自React.Component；

2.给它添加一个叫render()的新方法；

3.把函数体放到render()方法里；

4.把props都用this.props代替；

5.删除掉余下的空方法声明;

{% highlight ruby %}

class Clock extends React.Component {
  render() {
    return (
      <div>
        <h1>Hello, world!</h1>
        <h2>It is {this.props.date.toLocaleTimeString()}.</h2>
      </div>
    );
  }
}

{% endhighlight %}

'时钟'组件现在是CLass的而不是函数式了。

那就让我们使用额外诸如local State（本地状态）和lifecycle hook(生命周期回调钩子)的新特性吧。

#### 在class的基础上增加State状态

我们把props里面的数据移入state需要三步：

1.在render()方法中，把this.props.date换成this.state.date：

{% highlight ruby %}

class Clock extends React.Component {
  render() {
    return (
      <div>
        <h1>Hello, world!</h1>
        <h2>It is {this.state.date.toLocaleTimeString()}.</h2>
      </div>
    );
  }
}

{% endhighlight %}

2.添加class构造器并初始化this.state:

{% highlight ruby %}

class Clock extends React.Component {
  constructor(props) {
    super(props);
    this.state = {date: new Date()};
  }

  render() {
    return (
      <div>
        <h1>Hello, world!</h1>
        <h2>It is {this.state.date.toLocaleTimeString()}.</h2>
      </div>
    );
  }
}
{% endhighlight %}

留心我们怎么把props属性传递给这个基础构造器的：

{% highlight ruby %}

 constructor(props) {
    super(props);
    this.state = {date: new Date()};
  }

{% endhighlight %}

class组件总是带着props去调用基础构造器

3.从<Clock />元素中移除date属性:

{% highlight ruby %}

ReactDOM.render(
  <Clock />,
  document.getElementById('root')
);

{% endhighlight %}

过会儿我们将把定时器的代码再添加回组件自身。

现在结果看上去是这样：

{% highlight ruby %}

class Clock extends React.Component {
  constructor(props) {
    super(props);
    this.state = {date: new Date()};
  }

  render() {
    return (
      <div>
        <h1>Hello, world!</h1>
        <h2>It is {this.state.date.toLocaleTimeString()}.</h2>
      </div>
    );
  }
}

ReactDOM.render(
  <Clock />,
  document.getElementById('root')
);

{% endhighlight %}

下面，我们将把定时器的代码再添加回组件自身并每秒更新它。

#### 添加Class的生命周期方法

应用Application是由很多组件组成，那么在当一个组件销毁时，把它所持有的资源释放就显得尤为重要。

我们写这样一个计时器：在最开始的时候Clock被渲染到DOM上的时候'安装'计时器，这在React里面叫mounting（挂载）。

同样我们在Clock移除的时候清除这个计时器，这在React里面叫unmounting（卸载）。

我们在组件类的一些特殊的方法中写一些代码，那么当组件卸载或者挂载的时候代码就会执行：

{% highlight ruby %}

class Clock extends React.Component {
  constructor(props) {
    super(props);
    this.state = {date: new Date()};
  }
  //whenever  mounting
  componentDidMount() {

  }
  //whenever unmounting
  componentWillUnmount() {

  }

  render() {
    return (
      <div>
        <h1>Hello, world!</h1>
        <h2>It is {this.state.date.toLocaleTimeString()}.</h2>
      </div>
    );
  }
}

{% endhighlight %}

这些方法叫声明周期方法（钩子函数）。

方法：componentDidMount()在组件被渲染到DOM之后调用，这是个安装定时器的好时机：

{% highlight ruby %}

  componentDidMount() {
    this.timerID = setInterval(
      () => this.tick(),
      1000
    );
  }

{% endhighlight %}

注意我们怎样在this的右边保存定时器的ID（定时器的ID是定时器的标志）。

好吧，既然this.props是被React自己调用的，this.state有特殊的含义，如果你还需要存储一些其他不需要在显示层输出的信息，那么你也可以自主地随便加些字段属性。

当componentWillUnmount()调用的时候我们销毁定时器：

{% highlight ruby %}

  componentWillUnmount() {
    clearInterval(this.timerID);
  }

{% endhighlight %}

最后，我们实现一个tick()方法，让组件每秒都运行这个方法.

它将使用this.setState（）来调度更新组件的当前状态。

{% highlight ruby %}

class Clock extends React.Component {
  constructor(props) {
    super(props);
    this.state = {date: new Date()};
  }

  componentDidMount() {
    this.timerID = setInterval(
      () => this.tick(),
      1000
    );
  }

  componentWillUnmount() {
    clearInterval(this.timerID);
  }

  tick() {
    this.setState({
      date: new Date()
    });
  }

  render() {
    return (
      <div>
        <h1>Hello, world!</h1>
        <h2>It is {this.state.date.toLocaleTimeString()}.</h2>
      </div>
    );
  }
}

ReactDOM.render(
  <Clock />,
  document.getElementById('root')
);

{% endhighlight %}

现在，它每秒都在运行。

让我们快速回顾发生了什并整理方法调用的次序：

1.当<Clock />被传递到ReactDOM.render(),React调用了组件Clock的构造方法.组件需要呈现当前时间，它初始化了this.state状态，初始化的值是一个包含了当前时间的对象。我们稍后会更新这个状态。

2.React然后调用了Clock组件的render方法。这就是为啥React知道往页面上呈现什么的原因。React而后会根据<Clock />的输出更新DOM。

3.当Clock的输出插入到DOM的时候，React调用了生命周期方法：componentDidMount()，在方法里面，Clock组件去委托浏览器每一秒都调用组件的方法tick()一次。

4.每当浏览器调用了一次tick()方法。Clock组件通过setState()设置一个包含当前时间的对象来调度UI更新。感谢这个setState()调用，React得以知晓state状态发生了变化，然后就再次调用render()方法更新其所呈现。这时，this.state.date在render()方法里面已经发生了变化，所以渲染输出就是当前的时间，React于是相应的更新DOM。

5.如若Clock最终从DOM中被移除，React就调用componentWillUnmount()保证定时器被停止。

#### 正确使用状态

关于setState()，你得了解3点：

##### 不要直接更新状态

举例，这样不会重新渲染一个组件：

{% highlight ruby %}

// Wrong
this.state.comment = 'Hello';

{% endhighlight %}

取而代之，用setState():

{% highlight ruby %}

// Correct
this.setState({comment: 'Hello'});

{% endhighlight %}

只有一个地方可以允许你直接设置属性，那就是构造器里。

##### 状态的更新可能不同步（异步）

React有可能会收集一批setState()调用以后统一更新。

那是因为this.props和this.state也可能不同步，你不能依靠这些值去推算下一个状态。

举例来说，用下面的代码来更新计数器counter可能要失败：

{% highlight ruby %}

// Wrong
this.setState({
  counter: this.state.counter + this.props.increment,
});

{% endhighlight %}

修复它，需要用setState()的另一种形式：setState()接受一个函数作为参数而不是对象。那个作为参数的函数的第一个参数是先前状态（previous state），第二个参数是当前属性：

{% highlight ruby %}

// Correct
this.setState((prevState, props) => ({
  counter: prevState.counter + props.increment
}));

{% endhighlight %}

上面我们用到了箭头函数，不过用常规的函数它也能正常工作：

{% highlight ruby %}

// Correct
this.setState(function(prevState, props) {
  return {
    counter: prevState.counter + props.increment
  };
});

{% endhighlight %}

##### 状态的更新是合入式的

当你调用setState()，React将你提供的对象合入当前的状态。

举例说，你的状态可能包含了一些彼此独立的变量：

{% highlight ruby %}

constructor(props) {
    super(props);
    this.state = {
      posts: [],
      comments: []
    };
  }

{% endhighlight %}

你可以分别在不同的setState()中更新他们：

{% highlight ruby %}

componentDidMount() {
    fetchPosts().then(response => {
      this.setState({
        posts: response.posts
      });
    });

    fetchComments().then(response => {
      this.setState({
        comments: response.comments
      });
    });
  }

{% endhighlight %}

合入式是浅的，所以this.setState({comments})也保留了this.state.posts的完整，但却完全代替了过去的this.state.comments。

##### 数据向下走

不管是子组件还是父组件，都不知道一个确定的组件彼是有状态还是没有状态，并且他们也不关心定义方式是函数式还是类形式。

这就是为什么我们把state状态叫做本地的或者封装的。因为对于别的组件来说它们是不可访问的。

一个组件可以选择把自己的state状态以props属性的方式下发给子组件：


{% highlight ruby %}

<h2>It is {this.state.date.toLocaleTimeString()}.</h2>

{% endhighlight %}

这同样适用于用户自定义组件中：

{% highlight ruby %}

<FormattedDate date={this.state.date} />

{% endhighlight %}

组件FormattedDate收到data在它的props中，而且不知道是否data来自于Clock的state或者props或者其他：

{% highlight ruby %}

function FormattedDate(props) {
  return <h2>It is {props.date.toLocaleTimeString()}.</h2>;
}

{% endhighlight %}

这就是通常所说的"自顶向下"或者说"单向性"(unidirectional)数据流。任何状态都是属于一些特殊的组件，任何数据或由状态衍生的UI只能影响那些下层的组件。

如果你将组件树看作是props瀑布，那么每一个组件的state就像是那额外的水源，这些水源在任意点加入瀑布并只向下流动。

为了展示所有的组件都是完全孤立的，我们创建了一个APP组件，渲染了三个<Clock>

{% highlight ruby %}

function App() {
  return (
    <div>
      <Clock />
      <Clock />
      <Clock />
    </div>
  );
}

ReactDOM.render(
  <App />,
  document.getElementById('root')
);

{% endhighlight %}

在React APP中，一个组件有很多状态或者一个状态也没有是由组件的实现细节决定，而这些细节也是可能变化的。你可以把丰富状态的组件放到鲜有状态的组件之中，反之亦然。

[官网文章 Quick Start :State and LifeCycle](https://reactjs.org/docs/state-and-lifecycle.html)
