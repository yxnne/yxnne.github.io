---
layout: post
title:  React-官网学习-QuickStart8-Lists和Keys(列表与键)
date:   2017-12-11
categories: react_learnning
tags: [translations_React.js]
---
<big>首先</big> ，让我们回顾下JavaScript中的列表。

下面的例子里，我们使用函数map()来遍历一个列表的元素，并得到元素值得两倍。map()函数返回这个两倍于旧元素新的数组并打印log：

看看这两个组件：

{% highlight javascript %}

const numbers = [1, 2, 3, 4, 5];
const doubled = numbers.map((number) => number * 2);
console.log(doubled);

{% endhighlight %}

代码在控制台输出[2 , 4 , 6 , 8 , 10]。

在React中，将数组转换成元素列表几乎是相同的。

#### 渲染多个组件

使用花括号{}来构造一个元素集合或把JSX写在里面。

下面 ，我们通过map()函数遍历一个number数组。让每个元素都返回自己的'<li>'。最终，我们把（一个<li>的集合）结果赋给变量 listItems。

{% highlight javascript %}

const numbers = [1, 2, 3, 4, 5];
const listItems = numbers.map((number) =>
  <li>{number}</li>
);

{% endhighlight %}

我们用<ul>标签把整个listItem包括起来，并在DOM中渲染:

{% highlight javascript %}

ReactDOM.render(
  <ul>{listItems}</ul>,
  document.getElementById('root')
);

{% endhighlight %}

这段代码呈现了一个从1--5的列表。

#### 基本列表元素

通常你需要渲染组件里面的列表。

我们重构先前的例子，使它接受一个数字数组参数的并且输出一个无序列表。

{% highlight javascript %}

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

{% highlight javascript %}

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

{% highlight javascript %}

const numbers = [1, 2, 3, 4, 5];
const listItems = numbers.map((number) =>
  <li key={number.toString()}>
    {number}
  </li>
);

{% endhighlight %}

挑出Key最好的方法是使用一个独立标识于其他兄弟元素的字符串。最常见的就是使用数据里面的ID作为Key：

{% highlight javascript %}

const todoItems = todos.map((todo) =>
  <li key={todo.id}>
    {todo.text}
  </li>
);

{% endhighlight %}

万不得已时（当你没有一个稳定的ID来渲染元素时），你可能使用元素索引作为key。

{% highlight javascript %}

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

{% highlight javascript %}

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

{% highlight javascript %}

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

{% highlight javascript %}

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

{% highlight javascript %}

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

{% highlight javascript %}

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

{% highlight javascript %}

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
