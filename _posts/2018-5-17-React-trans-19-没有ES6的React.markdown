---
layout: post
title:  React-官网学习-Advanced-Guides7-没有ES6的React
date:   2018-5-17
categories: react_learnning
tags: [translations_React.js]
---
<big>通常情况下</big>,定义React组件要采用普通JS的class形式：

```
class Greeting extends React.Component {
  render() {
    return <h1>Hello, {this.props.name}</h1>;
  }
}

```
要是你不想用es6的class，你就要用create-react-class模块代替：

```
var createReactClass = require('create-react-class');
var Greeting = createReactClass({
  render: function() {
    return <h1>Hello, {this.props.name}</h1>;
  }
});

```

这个API和ES6中class其实很像，除了个别例外情况。

#### 声明默认props值

在函数形式，和ES6的class形式中defaultProps被作为组件自身的属性定义：

```

class Greeting extends React.Component {
  // ...
}

Greeting.defaultProps = {
  name: 'Mary'
};

```

而createReactClass()这种形式,你需要在这个函数的参数对象中定义一个方法getDefaultProps()：

```

var Greeting = createReactClass({
  getDefaultProps: function() {
    return {
      name: 'Mary'
    };
  },

  // ...

});


```


#### 设置初始的State值

ES6的class写法中，定义初始state是通过构造函数来给this.state直接赋值的:

```
class Counter extends React.Component {
  constructor(props) {
    super(props);
    this.state = {count: props.initialCount};
  }
  // ...
}

```

而createReactClass()这种形式,你需要提供一个getInitialState方法，让它返回初始的state：

```
var Counter = createReactClass({
  getInitialState: function() {
    return {count: this.props.initialCount};
  },
  // ...
});


```

#### 自动绑定

在ES6的class形式的React的组件中，方法与普通ES6的class的语义相同。这就意味着，它们不会自动绑定到当前实例。你将不得不显示地在构造函数中，使用.bind(this)：

```
class SayHello extends React.Component {
  constructor(props) {
    super(props);
    this.state = {message: 'Hello!'};
    // This line is important!
    this.handleClick = this.handleClick.bind(this);
  }

  handleClick() {
    alert(this.state.message);
  }

  render() {
    // Because `this.handleClick` is bound, we can use it as an event handler.
    return (
      <button onClick={this.handleClick}>
        Say hello
      </button>
    );
  }
}

```
createReactClass()不需要这样，它绑定了所以方法：

```
var SayHello = createReactClass({
  getInitialState: function() {
    return {message: 'Hello!'};
  },

  handleClick: function() {
    alert(this.state.message);
  },

  render: function() {
    return (
      <button onClick={this.handleClick}>
        Say hello
      </button>
    );
  }
});

```

这就意味着ES6的class对每一个方法都得整点样板化的代码,但好处就是在大型应用中性能略好一点。

如果这个样板代码对你毫无吸引力，你可以使用实验性的类属性语法，由babel提议的：

```
class SayHello extends React.Component {
  constructor(props) {
    super(props);
    this.state = {message: 'Hello!'};
  }
  // WARNING: this syntax is experimental!
  // Using an arrow here binds the method:
  handleClick = () => {
    alert(this.state.message);
  }

  render() {
    return (
      <button onClick={this.handleClick}>
        Say hello
      </button>
    );
  }
}

```

请注意上述语法是实验性的，语法还可能变化，或者这个建议并没有真正转化成语言的语法。

你想更安全点，这些是你为数不多的选择：

* 构造函数中绑定
* 使用箭头函数e.g. onClick={(e) => this.handleClick(e)}
* 继续使用createReactClass

#### Mixins
>注意ES6不支持Mixin。因此，当你使用React的class写法时，不支持mixins（混入）的。
同样，我们发现大量的问题是在使用mixins发现的，因此我们不推荐在新代码中使用mixin。
这个版块专门讨论mixins。

有时，不同模块会共享些相同方法或函数。这被称之为cross-cutting-concerns(横切关注点)。createReactClass可以使用mixins系统。

通用的一个场景是组件间歇性的自我更新。使用setInterval实现是很简单的，但关键是当你不再想使用它时，为了节约内存要清除它。React提供了生命周期，这使得你知晓组件的创建和销毁。让我们创造一个简单的mixin，它提供一个简单的setInterval函数，同时当组件要销毁时自动清除（定时器）。

```
var SetIntervalMixin = {
  componentWillMount: function() {
    this.intervals = [];
  },
  setInterval: function() {
    this.intervals.push(setInterval.apply(null, arguments));
  },
  componentWillUnmount: function() {
    this.intervals.forEach(clearInterval);
  }
};

var createReactClass = require('create-react-class');

var TickTock = createReactClass({
  mixins: [SetIntervalMixin], // Use the mixin
  getInitialState: function() {
    return {seconds: 0};
  },
  componentDidMount: function() {
    this.setInterval(this.tick, 1000); // Call a method on the mixin
  },
  tick: function() {
    this.setState({seconds: this.state.seconds + 1});
  },
  render: function() {
    return (
      <p>
        React has been running for {this.state.seconds} seconds.
      </p>
    );
  }
});

ReactDOM.render(
  <TickTock />,
  document.getElementById('example')
);
```

假如一个组件用了多个mixins，而且mixins定义了同样的声明周期方法（比如，当组件被销毁时，有很多mixins都做了类似清除工作），那么所有的产生的声明周期方法都会被调用。Mixins中定义的方法按照mixins列表中的顺序调用，它们都在组件自身的方法调用之后调用。



[官网文章 Advanced Guides :React Without ES6](https://reactjs.org/docs/react-without-es6.html)


