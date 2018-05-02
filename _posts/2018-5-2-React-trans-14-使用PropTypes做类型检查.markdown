---
layout: post
title:  React-官网学习-Advanced-Guides2-使用PropTypes做类型检查
date:   2018-5-2
categories: react_learnning
tags: [translations_React.js]
---
<big>记住</big>版本15.5之后，React.PropTypes已经被移除到其他包中，请使用[prop-types库](https://www.npmjs.com/package/prop-types)。
这里是代码变更记录[ a codemod script](https://reactjs.org/blog/2017/04/07/react-v15.5.0.html#migrating-from-reactproptypes)。

随着你的APP规模的增长，在类型检查方面会发现越来越多的bug。对有些应用来说，你可以使用js扩展来检查类型，比如flow和typescript。但是，即使你不适用这些，react也有类型检查的能力。为了能使对组件类型检查可用，你得分配额外的propsTypes属性。
```
import PropTypes from 'prop-types';

class Greeting extends React.Component {
  render() {
    return (
      <h1>Hello, {this.props.name}</h1>
    );
  }
}

Greeting.propTypes = {
  name: PropTypes.string
};
```
PropTypes输出一组校验器来保证接收到的数据是可用的。在这个例子中，我们使用PropTypes.string。当一个不合法的值作为props传入时，在控制台中会产生警告，性能上的一些原因，propTypes只用在开发模式。

#### PropTypes

这个例子列出了所提供的不同的校验器：
```
import PropTypes from 'prop-types';

MyComponent.propTypes = {
  // You can declare that a prop is a specific JS type. By default, these
  // are all optional.
  optionalArray: PropTypes.array,
  optionalBool: PropTypes.bool,
  optionalFunc: PropTypes.func,
  optionalNumber: PropTypes.number,
  optionalObject: PropTypes.object,
  optionalString: PropTypes.string,
  optionalSymbol: PropTypes.symbol,

  // Anything that can be rendered: numbers, strings, elements or an array
  // (or fragment) containing these types.
  optionalNode: PropTypes.node,

  // A React element.
  optionalElement: PropTypes.element,

  // You can also declare that a prop is an instance of a class. This uses
  // JS's instanceof operator.
  optionalMessage: PropTypes.instanceOf(Message),

  // You can ensure that your prop is limited to specific values by treating
  // it as an enum.
  optionalEnum: PropTypes.oneOf(['News', 'Photos']),

  // An object that could be one of many types
  optionalUnion: PropTypes.oneOfType([
    PropTypes.string,
    PropTypes.number,
    PropTypes.instanceOf(Message)
  ]),

  // An array of a certain type
  optionalArrayOf: PropTypes.arrayOf(PropTypes.number),

  // An object with property values of a certain type
  optionalObjectOf: PropTypes.objectOf(PropTypes.number),

  // An object taking on a particular shape
  optionalObjectWithShape: PropTypes.shape({
    color: PropTypes.string,
    fontSize: PropTypes.number
  }),

  // You can chain any of the above with `isRequired` to make sure a warning
  // is shown if the prop isn't provided.
  requiredFunc: PropTypes.func.isRequired,

  // A value of any data type
  requiredAny: PropTypes.any.isRequired,

  // You can also specify a custom validator. It should return an Error
  // object if the validation fails. Don't `console.warn` or throw, as this
  // won't work inside `oneOfType`.
  customProp: function(props, propName, componentName) {
    if (!/matchme/.test(props[propName])) {
      return new Error(
        'Invalid prop `' + propName + '` supplied to' +
        ' `' + componentName + '`. Validation failed.'
      );
    }
  },

  // You can also supply a custom validator to `arrayOf` and `objectOf`.
  // It should return an Error object if the validation fails. The validator
  // will be called for each key in the array or object. The first two
  // arguments of the validator are the array or object itself, and the
  // current item's key.
  customArrayProp: PropTypes.arrayOf(function(propValue, key, componentName, location, propFullName) {
    if (!/matchme/.test(propValue[key])) {
      return new Error(
        'Invalid prop `' + propFullName + '` supplied to' +
        ' `' + componentName + '`. Validation failed.'
      );
    }
  })
};
```

#### 需要单独的子元素

PropTypes.element可用描述出仅仅一个子元素的被传递的需求。
```
import PropTypes from 'prop-types';

class MyComponent extends React.Component {
  render() {
    // This must be exactly one element or it will warn.
    const children = this.props.children;
    return (
      <div>
        {children}
      </div>
    );
  }
}

MyComponent.propTypes = {
  children: PropTypes.element.isRequired
};

```

#### 默认Props值
通过分配属性defaultProps，你可以定义props的默认值：

```
class Greeting extends React.Component {
  render() {
    return (
      <h1>Hello, {this.props.name}</h1>
    );
  }
}

// Specifies the default values for props:
Greeting.defaultProps = {
  name: 'Stranger'
};

// Renders "Hello, Stranger":
ReactDOM.render(
  <Greeting />,
  document.getElementById('example')
);

```
如果你使用Babel转换器比如transform-class-properties ，你也能使用static属性声明defaultProps。这种语法还没有最终定型而且需要在浏览器工作之前转换。详见[class fields proposal](https://github.com/tc39/proposal-class-fields)。
```
class Greeting extends React.Component {
  static defaultProps = {
    name: 'stranger'
  }

  render() {
    return (
      <div>Hello, {this.props.name}</div>
    )
  }
}

```

defaultProps将用于保证this.props.name在没有从父组件分配值的时候，拥有一个默认值。propType检查发生在defaultProps之后，所以，类型检查也用于defaultProps。






[官网文章 Advanced Guides :Typechecking With PropTypes](https://reactjs.org/docs/typechecking-with-proptypes.html)


