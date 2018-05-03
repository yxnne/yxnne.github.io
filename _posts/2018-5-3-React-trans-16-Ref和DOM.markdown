---
layout: post
title:  React-官网学习-Advanced-Guides4-Refs与DOM
date:   2018-5-3
categories: react_learnning
tags: [translations_React.js]
---
<big>Refs</big>提供了一种访问DOM节点或在render方法中创建的React元素的方法。
典型的React数据流中，props是父组件作用子组件的唯一的方式。要更改子组件，你需要用新的props来重新渲染他们。然而，存在一些少数情形需要你绕过这种典型的数据流来强制更改。被更改的子组件可以是React组件的实例，或者是DOM元素。对于这两种情况，React提供了“逃生舱”（处理方法）。


#### 什么时候应用Refs
关于应用Refs有少许不错的情形：

* 管理焦点，文本区域或媒体回放；
* 触发强制动画；
* 继承第三方DOM库；
当可以用直接声明解决问题的时候，一定要避免使用refs。
比方说，与其将open()和close()方法从Dialog中暴露出来，不如直接传一个props isOpen给它。

#### 不要过度使用Refs

你的第一倾向也许会是使用refs来做点什么。这种时候，三思，并进一步思考下state状态应该在组件层级结构中的什么地方。通常情况下，拥有state的合适地方是上级组件。你可以看看[Lifting State Up](https://reactjs.org/docs/lifting-state-up.html)是关于这里的指导。

>注意：下面的例子中已经更新到16.3版本的React.createRef()API,如果你使用早期版本，我们推荐使用[callback refs](https://reactjs.org/docs/refs-and-the-dom.html#callback-refs)代替

#### 创建Refs
使用React.createRef()方法来创建Refs，通过ref属性绑定到React元素。通常当某元素呗创建后，它的实例属性就分配给了Refs,所以在组件各处都有了它的引用。

```
class MyComponent extends React.Component {
  constructor(props) {
    super(props);
    this.myRef = React.createRef();
  }
  render() {
    return <div ref={this.myRef} />;
  }
}

```

#### 访问Refs
当一个Ref在render时被分配，那么对于某节点的引用通过访问ref的current属性。

```
const node = this.myRef.current;

```

ref的值依据节点类型不同而不同：
* 当ref属性应用在Html元素上时，在构造器内通过React.createRef()创造的ref引用接收基本的DOM元素作为自己的current属性。

* 当ref应用在由class创建的自定义的组件上时，ref的current属性接收已挂载的组件实例。

* __你不能把ref应用在函数组件上__，因为他们没有实例。

###### 添加DOM元素的Ref
下面的代码用ref保存DOM节点的引用：

```
class CustomTextInput extends React.Component {
  constructor(props) {
    super(props);
    // create a ref to store the textInput DOM element
    // 创建 Ref
    this.textInput = React.createRef();
    this.focusTextInput = this.focusTextInput.bind(this);
  }

  focusTextInput() {
    // Explicitly focus the text input using the raw DOM API
    // Note: we're accessing "current" to get the DOM node
    // 明确的获得焦点使用原生API
    // 注意，使用current属性来得到DOM节点
    this.textInput.current.focus();
  }

  render() {
    // tell React that we want to associate the <input> ref
    // with the `textInput` that we created in the constructor
    return (
      <div>
        <input
          type="text"
          ref={this.textInput} />

        <input
          type="button"
          value="Focus the text input"
          onClick={this.focusTextInput}
        />
      </div>
    );
  }
}
// 运行结果，点击button之后，input中获得焦点

```

React将会在组件mount挂载的时候分配current属性，同时，unmounted卸载的时候再把current置为null。ref的更新发生在componentDidMount和componentDidUpdate这两个生命周期函数之前。

###### 给Class组件添加Ref

如果我们想封装上例中CustomTextInput组件，在它被挂载之后就模拟点击了（获得焦点button），我们应该使用ref引用自定义的input并手动调用方法focusTextInput 。

```

class AutoFocusTextInput extends React.Component {
  constructor(props) {
    super(props);
    this.textInput = React.createRef();
  }

  componentDidMount() {
    this.textInput.current.focusTextInput();
  }

  render() {
    return (
      <CustomTextInput ref={this.textInput} />
    );
  }
}

```
要注意，这只有在组件是用class声明时才可用:
```
class CustomTextInput extends React.Component {
  // ...
}
```
#### Ref和函数组件
__你不能把ref应用在函数组件上__，他们没有实例:

```
function MyFunctionalComponent() {
  return <input />;
}

class Parent extends React.Component {
  constructor(props) {
    super(props);
    this.textInput = React.createRef();
  }
  render() {
    // This will *not* work!
    // 这没用
    return (
      <MyFunctionalComponent ref={this.textInput} />
    );
  }
}
```
要是真的要用ref，那就把组件转换成class组件，就像需要声明周期方法和state时候做的那样。
不过，你能在函数组件的“里面”使用ref，只有引用的组件是一个class组件，或者原生DOM元素:
```
function CustomTextInput(props) {
  // textInput must be declared here so the ref can refer to it
  // ref必须声明在这里
  let textInput = React.createRef();

  function handleClick() {
    textInput.current.focus();
  }

  return (
    <div>
      <input
        type="text"
        ref={textInput} />

      <input
        type="button"
        value="Focus the text input"
        onClick={handleClick}
      />
    </div>
  );
}

```

#### 暴露DOM的ref引用给父级元素

少数情形下，你可能想从父组件中访问子元素的DOM节点。这通常不被推荐因为这打破了组件的封装性，但有时确实有用，比如触发焦点或测量子元素的尺寸或位置。

当然你可以想上文中那样添加子元素的引用，但这也不是理想的解决方案，因为你只能得到组件实例而不是DOM节点。此外，这种方法再函数组件中也不可用。

如果你在使用React版本16.3或更高版本，我们推荐使用[ref forwarding](https://reactjs.org/docs/forwarding-refs.html)处理这种情形。__这种方法可以使组件选择暴露子组件的ref__，在专门介绍Ref Forwarding的文章中，你能发现一个详细的例子介绍具体方法。

你要是使用16.2或更低版本，或者说你想比ref forwarding更加弹性化，你可以使用这种[方法代替](https://gist.github.com/gaearon/1a018a023347fe1c2476073330cc5509)，明确传递一个ref作为一个prop。

如果可能，我们建议不要暴露DOM节点，尽管有时候是一种捷径。注意这种方法需要你添加一些代码在子组件中。如果你完全没有对于子组件的的控制权，最后你可以使用[findDOMNod()](https://reactjs.org/docs/react-dom.html#finddomnode)方法，这也是不被鼓励的！

#### 回调Refs
React还提供了另外的设置ref的方法叫做“callback ref”，这是一种在设置ref和取消设置ref时，更细粒度的控制。

和传递通过createRef()创建的ref不一样，你现在需要传递一个函数。该函数接受React组件实例或者HTML DOM元素作为参数。

下面的例子实现了一个通用的模式：在一个实例属性中，用ref回调存储对DOM节点的引用。
```
class CustomTextInput extends React.Component {
  constructor(props) {
    super(props);
    
    // 这就是以后要存储引用的实例属性
    this.textInput = null;

    this.setTextInputRef = element => {
      this.textInput = element;
    };

    this.focusTextInput = () => {
      // Focus the text input using the raw DOM API
      if (this.textInput) this.textInput.focus();
    };
  }

  componentDidMount() {
    // autofocus the input on mount
    this.focusTextInput();
  }

  render() {
    // Use the `ref` callback to store a reference to the text input DOM
    // element in an instance field (for example, this.textInput).
    // 用 ref 回调来存储inputDOM的引用
    return (
      <div>
        <input
          type="text"
          ref={this.setTextInputRef}
        />
        <input
          type="button"
          value="Focus the text input"
          onClick={this.focusTextInput}
        />
      </div>
    );
  }
}

```
当组件挂载时，React将会调用DOM元素的ref的回调函数，当组件卸载使，置之为null。ref回调函数在生命周期方法componentDidMount或componentDidUpdate之前调用。
你也可以在组件之间传递回调ref，如同你能传递那些经由React.createRef()创建的对象ref引用一样。
```
function CustomTextInput(props) {
  return (
    <div>
      <input ref={props.inputRef} />
    </div>
  );
}

class Parent extends React.Component {
  render() {
    return (
      <CustomTextInput
        inputRef={el => this.inputElement = el}
      />
    );
  }
}

```

上面的例子中，父组件将它的回调ref作为props(inputRef)传递给子组件CustomTextInput，与此同时，子组件也把相同的函数作为ref属性传递给了input元素。结果就是，父组件中的this.inputElement会被设置为CustomTextInput中的input的DOM节点。

#### 遗产API：String Refs

如果你早先用过React，你一定熟悉早先的API--ref作为一个字符串，像“textInput”，而且对DOM的访问是这样：this.refs.textInput。我们不建议使用字符串refs,因为字符串refs有些历史遗留问题，而且可能未来版本就会移除这种形式。

>注意，你要是现在还用this.refs.textInput形式来访问refs，我们建议使用回调模式（callback refs）或者 createRef API代替。

#### 回调refs的告诫 Caveats with callback refs

如果回调ref被定义成行内函数（箭头函数？ inline function），在更新期间它就会被调用两次，第一次调用传递null，再一次是传递DOM节点。这是因为每一次render函数执行时一个新的函数实例会被创建出，所以，React需要清理旧的ref引用同时分配新的。你可以通过把函数定义为class方法来避免这种情况，不过注意大多数时候这个也没什么关系。


[官网文章 Advanced Guides :Static Type Checking](https://reactjs.org/docs/static-type-checking.html)


