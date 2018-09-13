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

现在Mouse组件封装了所有因为监听mousemove事件而关联的行为，同时将(x, y)坐标存储起来。但这依旧不是真正的复用。

就比如，我们假象一个Cat组件，它在屏幕上渲染一个抓老鼠的小猫。我们期望能用《Cat mouse={{x, y}}》来告知组件老鼠的坐标，这样来决定将这张图片展示在什么地方。

 一开始，你可能想将Cat渲染在Mouse中去：

 ```
class Cat extends React.Component {
  render() {
    const mouse = this.props.mouse;
    return (
      <img src="/cat.jpg" style={{ position: 'absolute', left: mouse.x, top: mouse.y }} />
    );
  }
}

class MouseWithCat extends React.Component {
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

        {/*
          We could just swap out the <p> for a <Cat> here ... but then
          we would need to create a separate <MouseWithSomethingElse>
          component every time we need to use it, so <MouseWithCat>
          isn't really reusable yet.
        */}
        <Cat mouse={this.state} />
      </div>
    );
  }
}

class MouseTracker extends React.Component {
  render() {
    return (
      <div>
        <h1>Move the mouse around!</h1>
        <MouseWithCat />
      </div>
    );
  }
}

 ```

这个方法对我们这个特殊的需求的确奏效。但是我们还没有达到真正以可重用的方式封装的目的。现在是，每次想要使用鼠标位置，我们都得创建一个新的满足我们特殊要求的组件（基本上是另一个MouseWithCat）。

Render Props技术这就来了：我们将给Mouse组件中传递一个函数的Props，该函数用来动态决定怎样渲染Render Props，这样来取代在Mouse中硬编码了一个Cat组件，并且高效率的变更其渲染输出。

```
class Cat extends React.Component {
  render() {
    const mouse = this.props.mouse;
    return (
      <img src="/cat.jpg" style={{ position: 'absolute', left: mouse.x, top: mouse.y }} />
    );
  }
}

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

        {/*
          Instead of providing a static representation of what <Mouse> renders,
          use the `render` prop to dynamically determine what to render.
        */}
        {this.props.render(this.state)}
      </div>
    );
  }
}

class MouseTracker extends React.Component {
  render() {
    return (
      <div>
        <h1>Move the mouse around!</h1>
        <Mouse render={mouse => (
          <Cat mouse={mouse} />
        )}/>
      </div>
    );
  }
}

```

现在，取代实际等于拷贝Mouse组件以及硬编码在render方法来解决这个特殊用例，我们提供名字是render的属性，这样Mouse组件可以动态的决定要渲染什么。

更正确的说，名为render的props是一个可以让组件知道要自己要渲染的东西。

这项技术使得我们共享的需求十分简单。为了达成目的，我们渲染Mouse时要用render props来告知当前鼠标的x，y坐标。

另一个有趣的事情是，大多数的高阶组件HOC可以用上这种常规组件携带render props的方法。比如说， 想要一个withMouse的HOC来替代Mouse组件，你可以简单的创造一个常规Mouse组件带上一个render props。

```

// If you really want a HOC for some reason, you can easily
// create one using a regular component with a render prop!
function withMouse(Component) {
  return class extends React.Component {
    render() {
      return (
        <Mouse render={mouse => (
          <Component {...this.props} mouse={mouse} />
        )}/>
      );
    }
  }
}


```

所以，使用名为render的props让两种模式都得以实现。

#### 使用Props不仅仅是render

记住这个很重要，这个模式叫“render props”，所以你没必要必须用一个名为render的 prop来使用该模式。事实上，任何一个函数props，其作用是提供组件的渲染元素，都叫render props。

尽管上面的例子都在使用render。但是我们可以简单的使用children属性。

```

<Mouse children={mouse => (
  <p>The mouse position is {mouse.x}, {mouse.y}</p>
)}/>

```
同时要记住，children属性实际不需要写在JSX的一串属性中。你可以将它直接写在组件内部。

```
<Mouse>
  {mouse => (
    <p>The mouse position is {mouse.x}, {mouse.y}</p>
  )}
</Mouse>

```

你在react-motion库中常见此技术。

既然该技术有点不寻常，你可能想在设计API的时候在propTypes中，明确的声明children应该是一个function。

```
Mouse.propTypes = {
  children: PropTypes.func.isRequired
};

```

### 警告

**在React.PureComponent中小心使用名为Render的Props**

当你在render中新建一个函数，那么使用这个技术就抵消了使用React.PureComponent的优点。这是因为钱比较对于新的props来讲会返回false（不相等），而每一次渲染都会造成一个新的render props产生（箭头函数对象）。

比如，继续我们上面提到的例子Mouse组件，如果Mouse组件集成了PureComponent，它就是这样的：

```
class Mouse extends React.PureComponent {
  // Same implementation as above...
}

class MouseTracker extends React.Component {
  render() {
    return (
      <div>
        <h1>Move the mouse around!</h1>

        {/*
          This is bad! The value of the `render` prop will
          be different on each render.
        */}
        <Mouse render={mouse => (
          <Cat mouse={mouse} />
        )}/>
      </div>
    );
  }
}

```

这个例子中，每次MouseTracker渲染后，就产生新的函数对象作为Mouse的属性，这样就是一开始就抵消了Mouse集成PureComponent的作用。

绕过这个问题的办法，就是你可以定义属性的值为一个实例方法，像这样：

```
class MouseTracker extends React.Component {
  // Defined as an instance method, `this.renderTheCat` always
  // refers to *same* function when we use it in render
  renderTheCat(mouse) {
    return <Cat mouse={mouse} />;
  }

  render() {
    return (
      <div>
        <h1>Move the mouse around!</h1>
        <Mouse render={this.renderTheCat} />
      </div>
    );
  }
}

```

那些你不能定义静态属性的例子中（比如你要关闭组件的属性后状态？），Mouse应该集成React。Component。

[官网文章 Advanced Guides :Render Props](https://reactjs.org/docs/render-props.html)


