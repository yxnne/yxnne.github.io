---
layout: post
title:  React-官网学习-Advanced-Guides1-深入JSX
date:   2018-3-16
categories: react_learnning
tags: [translations_React.js]
---
<big>根本上说</big>，JSX是React提供给：React.createElement(component, props, ...children)函数的语法糖。

JSX代码：

```
<MyButton color="blue" shadowSize={2}>
  Click Me
</MyButton>

```
将被编译成：

```
React.createElement(
  MyButton,
  {color: 'blue', shadowSize: 2},
  'Click Me'
)
```
没有子元素的话，你也可以使用自闭和标签,所以：

```
<div className="sidebar" />
```
编译成：

```
React.createElement(
  'div',
  {className: 'sidebar'},
  null
)

```
你要是想测试测试这些明确的JSX被转换成怎样的javascript，可以试试 [the online Babel compiler](https://babeljs.io/repl/#?presets=react&code_lz=GYVwdgxgLglg9mABACwKYBt1wBQEpEDeAUIogE6pQhlIA8AJjAG4B8AEhlogO5xnr0AhLQD0jVgG4iAXyJA)

#### 明晰React元素类型

JSX标签的第一部分决定React元素的类型。
大写（首字母）的类型表示JSX标签是React组件。这些标签直接编译成该标签名的变量，所以，要是你的JSX是<Foo />，那么其实在作用域中有Foo这个变量。

##### React 必须在作用域中

既然JSX编译调用的React.createElement，那么React库一定要总是在你JSX代码的作用域中。
举例，下面所有的import都是必要的，尽管React和CustomButton没有直接使用在JavaScript代码中。
```
import React from 'react';
import CustomButton from './CustomButton';

function WarningButton() {
  // return React.createElement(CustomButton, {color: 'red'}, null);
  return <CustomButton color="red" />;
}

```

如果你不用js打包器，而是用scrit标签引用React，那么React是存在全局域中的。

##### 在JSX中使用'.'语法

你也可以通过点语法在JSX中来引用React组件。如果你只有一个模块，但却输出了很多组件，这样会很方便，比方说MyComponents.DatePicker 是一个组件，那么你就可以这样使用它：

```
import React from 'react';

const MyComponents = {
  DatePicker: function DatePicker(props) {
    return <div>Imagine a {props.color} datepicker here.</div>;
  }
}

function BlueDatePicker() {
  return <MyComponents.DatePicker color="blue" />;
}

```

##### 自定义的组件一定要首字母大写

当元素开头是小写字母，它表示div或span这种原生html标签，同时，编译时向React.createElement()中传递的是字符串。但像首字母大写的类型，像<Foo/> ,那么传递到React.createElement（）的是与定义或导入文件一致的组件变量引用。（就像开篇的例子中那样）

我们推荐使用首字母大写命名，如果你要是此前有小写开头的组件，那么在使用JSX之前先把他们变成首字母大写的。

下面打代码不会按预期运行的：
```
import React from 'react';

// Wrong! This is a component and should have been capitalized:
function hello(props) {
  // Correct! This use of <div> is legitimate because div is a valid HTML tag:
  return <div>Hello {props.toWhat}</div>;
}

function HelloWorld() {
  // Wrong! React thinks <hello /> is an HTML tag because it's not capitalized:
  return <hello toWhat="World" />;
}

```
来把hello变成Hello修复它：

```
import React from 'react';

// Correct! This is a component and should be capitalized:
function Hello(props) {
  // Correct! This use of <div> is legitimate because div is a valid HTML tag:
  return <div>Hello {props.toWhat}</div>;
}

function HelloWorld() {
  // Correct! React knows <Hello /> is a component because it's capitalized.
  return <Hello toWhat="World" />;
}
```

##### 运行时选择类型

你不能将通用表达式作为元素类型。如果你需要使用一个通用表达式来指明元素类型，那么先将他们分配给一个首字母大写的变量。这通常在根据props来渲染不同组件时用到。

```
import React from 'react';
import { PhotoStory, VideoStory } from './stories';

const components = {
  photo: PhotoStory,
  video: VideoStory
};

function Story(props) {
  // Wrong! JSX type can't be an expression.
  return <components[props.storyType] story={props.story} />;
}
```

修复这个错误，我们先分配类型给一个首字母大写的变量：

```
import React from 'react';
import { PhotoStory, VideoStory } from './stories';

const components = {
  photo: PhotoStory,
  video: VideoStory
};

function Story(props) {
  // Correct! JSX type can be a capitalized variable.
  const SpecificStory = components[props.storyType];
  return <SpecificStory story={props.story} />;
}

```

#### JSX中的props

JSX中分配props有几种不同的方式：

##### JavaScript表达式作为props值

使用一对花括号{}，你就能在JSX中把js表达式作为props传入，举例：

```
<MyComponent foo={1 + 2 + 3 + 4} />
```
对于MyComponent，props.foo的值是10，因为1+2+3+4已经计算过了。

if和for不算表达式，算语句，所以，不能出现在JSX中。取而代之，你可以将他们放在附近代码中，就像：

```
function NumberDescriber(props) {
  let description;
  if (props.number % 2 == 0) {
    description = <strong>even</strong>;
  } else {
    description = <i>odd</i>;
  }
  return <div>{props.number} is an {description} number</div>;
}

```
在前面的相关章节有更多关于条件渲染的知识。（QUICK START, Conditional Rendering）


-----------------------------------------------------not finish



import React from 'react';
import CustomButton from './CustomButton';

function WarningButton() {
  // return React.createElement(CustomButton, {color: 'red'}, null);
  return <CustomButton color="red" />;
}


//------------

import React from 'react';
import CustomButton from './CustomButton';

function WarningButton() {
  // return React.createElement(CustomButton, {color: 'red'}, null);
  return <CustomButton color="red" />;
}



import React from 'react';
import CustomButton from './CustomButton';

function WarningButton() {
  // return React.createElement(CustomButton, {color: 'red'}, null);
  return <CustomButton color="red" />;
}



















































[官网文章 Advanced Guides:JSX in Depth](https://reactjs.org/docs/thinking-in-react.html)
