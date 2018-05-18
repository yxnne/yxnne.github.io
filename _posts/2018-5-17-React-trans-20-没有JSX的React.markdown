---
layout: post
title:  React-官网学习-Advanced-Guides8-没有JSX的React
date:   2018-5-18
categories: react_learnning
tags: [translations_React.js]
---
<big>JSX</big>可不是React的必须品。你要是不想在你的环境中安装编译器，不使用JSX的React的方法超级简单。

所有的JSX只是调用React.createElement(component, props, ...children)的语法糖。所以，任何能用JSX的地方都能用普通的JavaScript。

举例，这是用JSX写的：

```
class Hello extends React.Component {
  render() {
    return <div>Hello {this.props.toWhat}</div>;
  }
}

ReactDOM.render(
  <Hello toWhat="World" />,
  document.getElementById('root')
);

```
能被编译成不用JSX的代码：

```
lass Hello extends React.Component {
  render() {
    return React.createElement('div', null, `Hello ${this.props.toWhat}`);
  }
}

ReactDOM.render(
  React.createElement(Hello, {toWhat: 'World'}, null),
  document.getElementById('root')
);


```

如果你关心更多JSX转换成JavaScript的例子，你可以试试[Babel的在线编译器](https://babeljs.io/repl/#?presets=react&code_lz=GYVwdgxgLglg9mABACwKYBt1wBQEpEDeAUIogE6pQhlIA8AJjAG4B8AEhlogO5xnr0AhLQD0jVgG4iAXyJA)。

组件可以由字符串，或者React.Component的子组件，或者一个无状态函数提供。

如果你需要键入React.createElement很多次，一个常见的模式是赋值给简写：
```
const e = React.createElement;

ReactDOM.render(
  e('div', null, 'Hello World'),
  document.getElementById('root')
);

```

如果你使用 React.createElement 的缩写形式，那么使用没有JSX的React也是挺方便的。

另外，你可以去参考社区上的项目，例如 [react-hyperscript](https://github.com/mlmorg/react-hyperscript) 和 [hyperscript-helpers](https://github.com/ohanhi/hyperscript-helpers) 。它们都提供了一些精简的语法。


[官网文章 Advanced Guides :React Without JSX](https://reactjs.org/docs/react-without-jsx.html)


