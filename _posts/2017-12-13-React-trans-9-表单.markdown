---
layout: post
title:  React-官网学习-QuickStart8-表单
date:   2017-12-12
categories: react_learnning
tags: [translations_React.js]
---
<big>React中，表单元素</big>和其他元素的工作有些差异 ，因为表单元素天生包括一些内置状态。举例来说，这个原生HTML表单接受一个name:


{% highlight ruby %}

<form>
  <label>
    Name:
    <input type="text" name="name" />
  </label>
  <input type="submit" value="Submit" />
</form>

{% endhighlight %}

该表单具有原生HTML行为：当用户点击提交，浏览修的页面。你希望React中也有这样的行为也是奏效的。但是更普遍的场景中，使用一个JavaScript函数处理表单提交，并且访问用户在表单中输入的数据是很方便的。标准的方式来完成上述功能使用技术叫“控制组件”（Controlled Component）。

#### 控制组件

HTML中，像<input>,<textarea>和<select>这样的元素，通常维持他们自己的状态并根据用户输入更新。在React中，可变的状态通常在组件的state中体现并且只能由setState()更新。

将React状态变成“真理的唯一源头”，我们就能将这两点结合起来。接着，渲染了表单的React组件也能在用户随之而来的输入中控制着表单。一个由React控制着的输入表单元素在这种方式下就叫“控制组件”。

举例，如果我们想将先前的例子变成点击提交后输出name，我们可以把表单写成像控制组件那样：

{% highlight ruby %}

class NameForm extends React.Component {
  constructor(props) {
    super(props);
    this.state = {value: ''};

    this.handleChange = this.handleChange.bind(this);
    this.handleSubmit = this.handleSubmit.bind(this);
  }

  handleChange(event) {
    this.setState({value: event.target.value});
  }

  handleSubmit(event) {
    alert('A name was submitted: ' + this.state.value);
    event.preventDefault();
  }

  render() {
    return (
      <form onSubmit={this.handleSubmit}>
        <label>
          Name:
          <input type="text" value={this.state.value} onChange={this.handleChange} />
        </label>
        <input type="submit" value="Submit" />
      </form>
    );
  }
}

{% endhighlight %}

代码在控制台输出[2 , 4 , 6 , 8 , 10]。

在React中，将数组转换成元素列表几乎是相同的。

#### 渲染多个组件

使用花括号{}来构造一个元素集合或把JSX写在里面。

下面 ，我们通过map()函数遍历一个number数组。让每个元素都返回自己的'<li>'。最终，我们把（一个<li>的集合）结果赋给变量 listItems。

{% highlight ruby %}

const numbers = [1, 2, 3, 4, 5];
const listItems = numbers.map((number) =>
  <li>{number}</li>
);

{% endhighlight %}

我们用<ul>标签把整个listItem包括起来，并在DOM中渲染:

{% highlight ruby %}

ReactDOM.render(
  <ul>{listItems}</ul>,
  document.getElementById('root')
);

{% endhighlight %}

这段代码呈现了一个从1--5的列表。

#### 基本列表元素

通常你需要渲染组件里面的列表。

我们重构先前的例子，使它接受一个数字数组参数的并且输出一个无序列表。

{% highlight ruby %}

function NumberList(props) {
  const numbers = props.numbers;
  const listItems = numbers.map((number) =>
    <li>{number}</li>
  );
  return (
    <ul>{listItems}</ul>
  );
}

const numbers = [1, 2, 3, 4, 5];
ReactDOM.render(
  <NumberList numbers={numbers} />,
  document.getElementById('root')
);

{% endhighlight %}

代码运行后，你发现有一个错误，说你需要给你的列表的元素一个Key(**bundle.js:1289 Warning: Each child in an array or iterator should have a unique "key" prop.**)。“key”是一个特殊的字符串属性，当你构造列表时，你需要提供这个属性。下一节中我们将讨论为什么这点如此重要。

让我们在numbers.map()中分配key给每个列表项来修复这个丢失了key的问题。

{% highlight ruby %}

function NumberList(props) {
  const numbers = props.numbers;
  const listItems = numbers.map((number) =>
    <li key={number.toString()}>
      {number}
    </li>
  );
  return (
    <ul>{listItems}</ul>
  );
}

const numbers = [1, 2, 3, 4, 5];
ReactDOM.render(
  <NumberList numbers={numbers} />,
  document.getElementById('root')
);

{% endhighlight %}

#### Keys

Key帮助React识别哪个元素发生了变更，是增加了？或者被移除了。数组中的元素应该被赋予一个具有稳定性的Key：

{% highlight ruby %}

const numbers = [1, 2, 3, 4, 5];
const listItems = numbers.map((number) =>
  <li key={number.toString()}>
    {number}
  </li>
);

{% endhighlight %}

挑出Key最好的方法是使用一个独立标识于其他兄弟元素的字符串。最常见的就是使用数据里面的ID作为Key：

{% highlight ruby %}

const todoItems = todos.map((todo) =>
  <li key={todo.id}>
    {todo.text}
  </li>
);

{% endhighlight %}

万不得已时（当你没有一个稳定的ID来渲染元素时），你可能使用元素索引作为key。

{% highlight ruby %}

const todoItems = todos.map((todo, index) =>
  // Only do this if items have no stable IDs
  <li key={index}>
    {todo.text}
  </li>
);

{% endhighlight %}

要是列表中的次序会改变，我们不推荐使用这种索引Key。这会造成性能问题还会导致关于组建状态的诸多问题。查看Robin Pokorny的论文[ in-depth explanation on the negative impacts of using an index as a key.](https://medium.com/@robinpokorny/index-as-a-key-is-an-anti-pattern-e0349aece318)。如果你选择不是分配一个明确的key，那么儿啊春天就会默认使用索引作为key。

有兴趣了解更多，看看[进一步解释为什么Key是必要的](https://reactjs.org/docs/reconciliation.html#recursing-on-children)。

#### 根据Key提取组件

Key只在数组相关的上下文中生效。

举例说明，要是你提取组件：ListItem，你需要把key保持在身处数组中的<ListItem>元素中，而不是<ListItem>定义时的<li>元素中。

**例子：错误用法**

{% highlight ruby %}

function ListItem(props) {
  const value = props.value;
  return (
    // Wrong! There is no need to specify the key here:
    <li key={value.toString()}>
      {value}
    </li>
  );
}

function NumberList(props) {
  const numbers = props.numbers;
  const listItems = numbers.map((number) =>
    // Wrong! The key should have been specified here:
    <ListItem value={number} />
  );
  return (
    <ul>
      {listItems}
    </ul>
  );
}

const numbers = [1, 2, 3, 4, 5];
ReactDOM.render(
  <NumberList numbers={numbers} />,
  document.getElementById('root')
);

{% endhighlight %}

**例子：正确用法**

{% highlight ruby %}

function ListItem(props) {
  // Correct! There is no need to specify the key here:
  return <li>{props.value}</li>;
}

function NumberList(props) {
  const numbers = props.numbers;
  const listItems = numbers.map((number) =>
    // Correct! Key should be specified inside the array.
    <ListItem key={number.toString()}
              value={number} />

  );
  return (
    <ul>
      {listItems}
    </ul>
  );
}

const numbers = [1, 2, 3, 4, 5];
ReactDOM.render(
  <NumberList numbers={numbers} />,
  document.getElementById('root')
);

{% endhighlight %}

一个好的经验是：元素在map()调用中使用key。

#### Key在兄弟元素中必须独一无二

Key的使用在一个数组中不能重复，可是，并不需要在全局中唯一。当我们构造了两个不一样的数组是，我们可以使用相同的key：

{% highlight ruby %}

function Blog(props) {
  const sidebar = (
    <ul>
      {props.posts.map((post) =>
        <li key={post.id}>
          {post.title}
        </li>
      )}
    </ul>
  );
  const content = props.posts.map((post) =>
    <div key={post.id}>
      <h3>{post.title}</h3>
      <p>{post.content}</p>
    </div>
  );
  return (
    <div>
      {sidebar}
      <hr />
      {content}
    </div>
  );
}

const posts = [
  {id: 1, title: 'Hello World', content: 'Welcome to learning React!'},
  {id: 2, title: 'Installation', content: 'You can install React from npm.'}
];
ReactDOM.render(
  <Blog posts={posts} />,
  document.getElementById('root')
);

{% endhighlight %}

Key的作用就像是给React暗示，但是它不能向下层组件传递。你要是需要相同的值，那你就另外一个prop名字，显式的把这个值传递。

{% highlight ruby %}

const content = posts.map((post) =>
  <Post
    key={post.id}
    id={post.id}
    title={post.title} />
);

{% endhighlight %}

上述例子中，组件Post能读懂props.id，但是不能读懂props.key。

#### 在map()中嵌入JSX

在上面的例子中，我们生命了一个ListItems变量并在里面写了JSX：

{% highlight ruby %}

function NumberList(props) {
  const numbers = props.numbers;
  const listItems = numbers.map((number) =>
    <ListItem key={number.toString()}
              value={number} />

  );
  return (
    <ul>
      {listItems}
    </ul>
  );
}

{% endhighlight %}

JSX允许在任何花括号{}中嵌入表达式，所以我们也能把JSX内联在在map()的返回中：

{% highlight ruby %}

function NumberList(props) {
  const numbers = props.numbers;
  return (
    <ul>
      {numbers.map((number) =>
        <ListItem key={number.toString()}
                  value={number} />

      )}
    </ul>
  );
}

{% endhighlight %}

有时候这种写法清晰，有时候这种写法也让人迷惑。就像JavaScript，为了可读性而把它提取为变量，这完全决定于你。有一点要记住，要是map()中嵌套的太复杂，那么这将是一个提取成组件的好时机。

[官网文章 Quick Start :Lists and Keys](https://reactjs.org/docs/lists-and-keys.html)
