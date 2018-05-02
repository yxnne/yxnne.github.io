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

如果你不用js打包器，而是用script标签引用React，那么React是存在全局域中的。

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

下面的代码不会按预期运行的：
```
import React from 'react';

// Wrong! This is a component and should have been capitalized:
// 错！应该用首字母大写的方式定义组件
function hello(props) {
  // Correct! This use of <div> is legitimate because div is a valid HTML tag:
  return <div>Hello {props.toWhat}</div>;
}

function HelloWorld() {
  // Wrong! React thinks <hello /> is an HTML tag because it's not capitalized:
  // 错！React认为，hello是个原生html标签
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
在前面的相关章节有更多关于“条件渲染”的知识。[QUICK START, Conditional Rendering](https://reactjs.org/docs/conditional-rendering.html)

##### 字符串字面值
你可以直接在属性中传递字符串，下面这两个写法没区别：
```
<MyComponent message="hello world" />

<MyComponent message={'hello world'} />

```
在你传递字符串字面值的时候，他的值就是HTML-unescaped（HTML保有值），所以下面两种也是相等的：
```
<MyComponent message="&lt;3" />

<MyComponent message={'<3'} />

```

这个问题关系不大，这里提及只是为了知识完整性。

##### Props属性的True默认值
你要是传递了一个“没有值”的props属性，它的默认值就是true，下面两种写法相等：
```
<MyTextBox autocomplete />

<MyTextBox autocomplete={true} />

```

一般来说我们不推荐这样使用，因为这样容易和ES6 Obeject Shorthand特性相冲突，在Shorthand特性中{foo}写法表示{foo:foo}而不是{foo:true}。这里的这种行为只是为了对应HTML的行为。

##### 展开属性
如果你已经将需要传递的props写成了一个对象，那么你也许需要使用...运算符，展开运算符，来讲整个props对象传递，下面两种写法等价。
```
function App1() {
  return <Greeting firstName="Ben" lastName="Hector" />;
}

function App2() {
  const props = {firstName: 'Ben', lastName: 'Hector'};
  return <Greeting {...props} />;
}
```
你也可以调出那些将要在组件中处理掉的属性同时使用...传递其他属性。
```
const Button = props => {
  const { kind, ...other } = props;
  const className = kind === "primary" ? "PrimaryButton" : "SecondaryButton";
  return <button className={className} {...other} />;
};

const App = () => {
  return (
    <div>
      <Button kind="primary" onClick={() => console.log("clicked!")}>
        Hello World!
      </Button>
    </div>
  );
};
```

上面的例子中，kind，props属性安全的“被消费”在传递进button元素之前，剩下的所有props通过...other对象传递，你可以看到，onClick和children都被传递下去了。
扩展运算符简单好用，但是很容易把不必要的props或者不可用的html属性传递到组件中，我们推荐有节制地使用这样的语法。

#### JSX中的子元素
JSX表达式包含开放和闭合标签，标签间的内容是依靠一个特殊的属性传递：props.children。有几种方式来传递子元素:

##### 字符串值
你可以将string放置在标签中，这时候props.children的值就是string本身。对于许多基于html元素来讲，这是有用的。
比如：
```
<MyComponent>Hello world!</MyComponent>
```
这就是一个可用的JSX，props.children的值就是“Hello World!”。Html值就是HTML-unescaped（HTML保有值），所以你也可以像写html那样写jsx：
```
<div>This is valid HTML &amp; JSX at the same time.</div>

```
JSX会移除行首尾的空格，也会移除空行，标签临近的会被移除，字符串中间的新行会被压缩成一个空格。所以一下几种情况渲染的东西都一样：
```
<div>Hello World</div>

<div>
  Hello World
</div>

<div>
  Hello
  World
</div>

<div>

  Hello World
</div>
```

##### JSX子元素
你可以提供更多JSX元素作为子元素，这对于展示嵌套的组件是有效的：
```
<MyContainer>
  <MyFirstComponent />
  <MySecondComponent />
</MyContainer>

```

你也可以混合不同类型的子元素，所以，你可以将字符串和JSX子元素混合用。这是JSX和html另一点相像之处，所以，下面的既能JSX中使用，也能HTML中使用：
```
<div>
  Here is a list:
  <ul>
    <li>Item 1</li>
    <li>Item 2</li>
  </ul>
</div>
```
React组件也能返回医嘱对象，对象数组：
```
render() {
  // No need to wrap list items in an extra element!
  return [
    // Don't forget the keys :)
    <li key="A">First item</li>,
    <li key="B">Second item</li>,
    <li key="C">Third item</li>,
  ];
}
```

##### JS表达式作为子元素
任何js表达式都能在一放在一对花括号中，举例来说，下面的表达式等价：
```
<MyComponent>foo</MyComponent>

<MyComponent>{'foo'}</MyComponent>

```

这种通常在渲染一个任意长度JSX列表时格外有用，比如，下面渲染了一个HTML的列表：
```
function Item(props) {
  return <li>{props.message}</li>;
}

function TodoList() {
  const todos = ['finish doc', 'submit pr', 'nag dan to review'];
  return (
    <ul>
      {todos.map((message) => <Item key={message} message={message} />)}
    </ul>
  );
}
```
js表达式也可以混合其他类型的子元素写法，通常，代替字符串模板时非常有用。
```
function Hello(props) {
  return <div>Hello {props.addressee}!</div>;
}

```

##### 函数作为子元素

通常地，JSX中的JavaScript表达式的值作为字符串，React元素或者这些东西的列表。然而，props.children和其他props一样的，当你传递其他数据序列集合不仅仅是那些react知道如何渲染的集合时也能工作。比如，假设你有一个自定义组件，你可以让它持有一个回调函数作为props.children。
```
// Calls the children callback numTimes to produce a repeated component
function Repeat(props) {
  let items = [];
  for (let i = 0; i < props.numTimes; i++) {
    // 这里回调了
    items.push(props.children(i));
  }
  return <div>{items}</div>;
}

function ListOfTenThings() {
  return (
    <Repeat numTimes={10}>
      {(index) => <div key={index}>This is item {index} in the list</div>}
    </Repeat>
  );
}

//这个最终渲染的结果为：
This is item 0 in the list
This is item 1 in the list
This is item 2 in the list
This is item 3 in the list
This is item 4 in the list
This is item 5 in the list
This is item 6 in the list
This is item 7 in the list
This is item 8 in the list
This is item 9 in the list
```
向组件中添加的子组件可以是任何东西，只要React能够在渲染之前将他们转换成React能理解的东西。这一点不常用，不过如果你想对jsx的能力来点延伸时，它是可用的。
##### Booleans, Null, 以及 Undefined 将被忽略
false, null, udefined和true是可用的子组件，他们不会被渲染，下面这些jsx会被渲染成相同的东西：
```
<div />

<div></div>

<div>{false}</div>

<div>{null}</div>

<div>{undefined}</div>

<div>{true}</div>
```
这一点倒是在条件渲染中可用。这个JSX会在showHeader是True的时候渲染出Header组件。
```
<div>
  {showHeader && <Header />}
  <Content />
</div>
```

这里有个附加警告：一些falsy值[(类似false的值)](https://developer.mozilla.org/en-US/docs/Glossary/Falsy),比如0,依旧会被渲染。举例，下面的代码并不会如你期望那样，因为0间如果props.message是空的，0也将会被打印出来。
```
<div>
  {props.messages.length &&
    <MessageList messages={props.messages} />
  }
</div>
```
修复它的话就要保证你的表达式始终是一个布尔表达式：
```
<div>
  {props.messages.length > 0 &&
    <MessageList messages={props.messages} />
  }
</div>

```
反过来说，你要是想要这种false，true，null，undefined出现在输出中，你得用string()函数转换他们先：
```
<div>
  My JavaScript variable is {String(myVariable)}.
</div>
```

[官网文章 Advanced Guides :JSX In Depthe](https://reactjs.org/docs/jsx-in-depth.htmll)


