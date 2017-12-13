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

既然value属性是我们表单自己设置的，那显示的值永远是this.state.value，反映着‘真理的源头’。由于在每一次键盘输入后都会调用handleChange来更新state状态，显示在界面的数值就会根据用户输入更新。

使用控制组件，每一个可变状态将会关联一个处理函数。这使得它可以直接修改或者验证用户输入。举例来说，假如我们想强行的把name变成大写字母，我们可以再handleChange中这样写：

{% highlight ruby %}

handleChange(event) {
  this.setState({value: event.target.value.toUpperCase()});
}

{% endhighlight %}

#### 标签:textarea

在HTML中，textarea标签通过其子元素定义它的文本。

{% highlight ruby %}

<textarea>
  Hello there, this is some text in a text area
</textarea>

{% endhighlight %}

在React中，textarea标签使用value属性代替。用这样的方式，使用<textarea>的表单就可以写得和单独使用input的表单极其相似：

{% highlight ruby %}

class EssayForm extends React.Component {
  constructor(props) {
    super(props);
    this.state = {
      value: 'Please write an essay about your favorite DOM element.'
    };

    this.handleChange = this.handleChange.bind(this);
    this.handleSubmit = this.handleSubmit.bind(this);
  }

  handleChange(event) {
    this.setState({value: event.target.value});
  }

  handleSubmit(event) {
    alert('An essay was submitted: ' + this.state.value);
    event.preventDefault();
  }

  render() {
    return (
      <form onSubmit={this.handleSubmit}>
        <label>
          Name:
          <textarea value={this.state.value} onChange={this.handleChange} />
        </label>
        <input type="submit" value="Submit" />
      </form>
    );
  }
}

{% endhighlight %}

注意我们在构造器中初始化this.state.value，这样textarea一开始就有文字在里面。

#### 标签：select

HTML中，<select> 标签生成下拉表单，举例，下面的HTML生成喜好的列表：

{% highlight ruby %}

<select>
  <option value="grapefruit">Grapefruit</option>
  <option value="lime">Lime</option>
  <option selected value="coconut">Coconut</option>
  <option value="mango">Mango</option>
</select>

{% endhighlight %}

注意Coconut选项是默认初始选择的，因为设置了selected属性。React中，不用selected属性，用select根标签中的value属性。这样对于控制组件来说更方便因为你只需在一处更新，就像这样:

{% highlight ruby %}

class FlavorForm extends React.Component {
  constructor(props) {
    super(props);
    this.state = {value: 'coconut'};

    this.handleChange = this.handleChange.bind(this);
    this.handleSubmit = this.handleSubmit.bind(this);
  }

  handleChange(event) {
    this.setState({value: event.target.value});
  }

  handleSubmit(event) {
    alert('Your favorite flavor is: ' + this.state.value);
    event.preventDefault();
  }

  render() {
    return (
      <form onSubmit={this.handleSubmit}>
        <label>
          Pick your favorite La Croix flavor:
          <select value={this.state.value} onChange={this.handleChange}>
            <option value="grapefruit">Grapefruit</option>
            <option value="lime">Lime</option>
            <option value="coconut">Coconut</option>
            <option value="mango">Mango</option>
          </select>
        </label>
        <input type="submit" value="Submit" />
      </form>
    );
  }
}

{% endhighlight %}

总之，这样<input type="text">, <textarea>, 以及 <select> 工作起来就很相似-- 他们都接受value属性，通过value可以实现控制组件。

>注意：
你也能在value属性中传递一个数组,这样做能实现多选项选中：
<select multiple={true} value={['B', 'C']}>

#### 处理多种输入

当你需要处理多个被控制的input元素，你需要在每一个元素中添加一个name属性并且让处理函数根据event.target.name的值来决定做什么。

例如：

{% highlight ruby %}

class Reservation extends React.Component {
  constructor(props) {
    super(props);
    this.state = {
      isGoing: true,
      numberOfGuests: 2
    };

    this.handleInputChange = this.handleInputChange.bind(this);
  }

  handleInputChange(event) {
    const target = event.target;
    const value = target.type === 'checkbox' ? target.checked : target.value;
    const name = target.name;

    this.setState({
      [name]: value
    });
    alert(name + " : " + value);
  }

  render() {
    return (
      <form>
        <label>
          Is going:
          <input
            name="isGoing"
            type="checkbox"
            checked={this.state.isGoing}
            onChange={this.handleInputChange} />
        </label>
        <br />
        <label>
          Number of guests:
          <input
            name="numberOfGuests"
            type="number"
            value={this.state.numberOfGuests}
            onChange={this.handleInputChange} />
        </label>
      </form>
    );
  }
}

ReactDOM.render(
  <Reservation />,
  document.getElementById('root')
);

{% endhighlight %}

记下你如何使用ES6[计算属性名语法](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Object_initializer#Computed_property_names)来更新与input name一致的state:

{% highlight ruby %}

this.setState({
  [name]: value
});

{% endhighlight %}

这和ES5中这样的代码等价:

{% highlight ruby %}

var partialState = {};
partialState[name] = value;
this.setState(partialState);

{% endhighlight %}



{% highlight ruby %}



{% endhighlight %}

[官网文章 Quick Start :Form](https://reactjs.org/docs/forms.html)
