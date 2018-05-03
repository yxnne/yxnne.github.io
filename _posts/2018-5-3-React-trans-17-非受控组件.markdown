---
layout: post
title:  React-官网学习-Advanced-Guides5-非受控组件
date:   2018-5-3
categories: react_learnning
tags: [translations_React.js]
---
<big>大多数情况</big>，实现表单时我们推荐使用受控组件[Controlled Components](https://reactjs.org/docs/forms.html)，表单数据由React控制(大概就是使用state管理组件内部input的值，因为所有值都在state中，所有事可控的，故曰受控组件)。非受控组件是另一种替代方案，这种情况下表单值是由DOM元素自己管理的。

想写一个非受控组件，你可以使用ref来获得DOM本身的值，而不是写一个事件处理方法来更新每一个状态。
比如说，这段代码就是一个非受控组件接收一个单一的name:
```
class NameForm extends React.Component {
  constructor(props) {
    super(props);
    this.handleSubmit = this.handleSubmit.bind(this);
  }

  handleSubmit(event) {
    alert('A name was submitted: ' + this.input.value);
    event.preventDefault();
  }

  render() {
    return (
      <form onSubmit={this.handleSubmit}>
        <label>
          Name:
          <input type="text" ref={(input) => this.input = input} />
        </label>
        <input type="submit" value="Submit" />
      </form>
    );
  }
}

// 这里有ref的知识，callback ref，在ref 中传递一个函数，该函数会在挂载时调用。

```

一个非受控组件持有DOM的真实之源，有时使用非受控组件使集成React和非React代码变得简单。而且这样更轻，代码更少，更快，但是代码会污染。否则你就经常使用非受控组件了。

还不明白在特定的情况下改用那种组件？那你可以参考这里[Controlled and uncontrolled form inputs in React don't have to be complicated](https://goshakkk.name/controlled-vs-uncontrolled-inputs-react/)，也许有帮助。

#### 默认值

React渲染周期中，表单元素的value属性会重写DOM的value值。通常在非受控组件中，你会想要React分辨（设置）出元素的初始值，然后后续的更新至于非受控状态。这种需求要这样处理：你指出defaultValue属性来代替value。
```
render() {
  return (
    <form onSubmit={this.handleSubmit}>
      <label>
        Name:
        <input
          defaultValue="Bob"
          type="text"
          ref={(input) => this.input = input} />
      </label>
      <input type="submit" value="Submit" />
    </form>
  );
}
```

类似地，type="checkbox"和 type="radio"的input元素支持属性： defaultChecked, select和textarea支持属性 defaultValue。

#### file input 标签

Html中，input 标签type="file"
可以使用户从设备存储中选择一个或者多个文件来上传到服务端或者由js的file API处理一下。

{% highlight javascript %}
<input type="file" />
{% endhighlight %}

React中，file input往往是非受控组件，因为他的value由用户设置，且不是以编程的方式设置。

你应该使用File API来操作这些文件。下面的例子展示了怎样在提交控制方法中创建一个对DOM的ref来访问文件。
```
class FileInput extends React.Component {
  constructor(props) {
    super(props);
    this.handleSubmit = this.handleSubmit.bind(this);
  }
  handleSubmit(event) {
    event.preventDefault();
    alert(
      `Selected file - ${this.fileInput.files[0].name}`
    );
  }

  render() {
    return (
      <form onSubmit={this.handleSubmit}>
        <label>
          Upload file:
          <input
            type="file"
            ref={input => {
              this.fileInput = input;
            }}
          />
        </label>
        <br />
        <button type="submit">Submit</button>
      </form>
    );
  }
}

ReactDOM.render(
  <FileInput />,
  document.getElementById('root')
);
```



[官网文章 Advanced Guides :Uncontrolled Components](https://reactjs.org/docs/uncontrolled-components.html)


