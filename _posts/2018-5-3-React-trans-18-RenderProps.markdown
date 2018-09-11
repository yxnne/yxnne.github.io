---
layout: post
title:  React-官网学习-Advanced-Guides-Render属性
date:   2018-9-11
categories: react_learnning
tags: [translations_React.js]
---
<big>所谓Render Prop</big>，涉及一个使用值为函数得的props在React组件间共享代码的简单技术。

携带render props的组件，这个props是一个返回一个React元素的函数并且通过调用该函数来代替其自身的渲染逻辑。

```
<DataProvider render={data => (
  <h1>Hello {data.target}</h1>
)}/>

```

使用了该项技术的类库有ReactRouter,Downshif,React Motion等。

这篇文章我们将会讨论为什么render props是有用的，以及怎样自己写一个。


#### Cross-Cutting Concerns 横切点来使用Render Props

React中，组件是基本复用单元。但是对于一个组件封装另一个需要相同state的组件来说，共享state或者行为并不总是显而易见。

比如，下面的代码是追踪鼠标位置的：

```
class MouseTracker extends React.Component {
  constructor(props) {
    super(props);
    this.handleMouseMove = this.handleMouseMove.bind(this);
    this.state = { x: 0, y: 0 };
  }

  handleMouseMove(event) {
    this.setState({
      x: event.clientX,
      y: event.clientY
    });
  }

  render() {
    return (
      <div style={{ height: '100%' }} onMouseMove={this.handleMouseMove}>
        <h1>Move the mouse around!</h1>
        <p>The current mouse position is ({this.state.x}, {this.state.y})</p>
      </div>
    );
  }
}

```

当鼠标在屏幕移动，组件将鼠标的x,y坐标展示在p标签中。

现在的问题是：在另一组件中，我们如何复用次行为？换句话，如果一个组件需要知道鼠标位置，我们能否将这个行为封装起来，以便我们能在组建中共享起来？

既然组件是React的基本单元，那么让我们一点点尝试重构代码：用一个Mouse组件封装我们想要的行为：

```
// The <Mouse> component encapsulates the behavior we need...
class Mouse extends React.Component {
  constructor(props) {
    super(props);
    this.handleMouseMove = this.handleMouseMove.bind(this);
    this.state = { x: 0, y: 0 };
  }

  handleMouseMove(event) {
    this.setState({
      x: event.clientX,
      y: event.clientY
    });
  }

  render() {
    return (
      <div style={{ height: '100%' }} onMouseMove={this.handleMouseMove}>

        {/* ...but how do we render something other than a <p>? */}
        <p>The current mouse position is ({this.state.x}, {this.state.y})</p>
      </div>
    );
  }
}

class MouseTracker extends React.Component {
  render() {
    return (
      <div>
        <h1>Move the mouse around!</h1>
        <Mouse />
      </div>
    );
  }
}
```


#### file input 标签



[官网文章 Advanced Guides :Render Props](https://reactjs.org/docs/render-props.html)


