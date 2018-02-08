---
layout: post
title:  React-官网学习-QuickStart7-根据条件渲染
date:   2017-12-10
categories: react_learnning
tags: [translations_React.js]
---
<big>在React中</big> ，你可以创建封装了你所需要行为的不同组件，接着，你也能根据应用的状态，来选择渲染这些组件的部分行为。

根据条件渲染在React中的工作方式类似JavaScript。用JavaScript的操作符像：if或条件运算符来创造一些表征当前状态的元素，从而更新相关UI界面。

看看这两个组件：

{% highlight javascript %}

function UserGreeting(props) {
  return <h1>Welcome back!</h1>;
}

function GuestGreeting(props) {
  return <h1>Please sign up.</h1>;
}

{% endhighlight %}

我们创建了这个‘greeting’组件，是呈现UserGreeting还是呈现GuestGreeting取决于用户的登录状态：

{% highlight javascript %}

function Greeting(props) {
  const isLoggedIn = props.isLoggedIn;
  if (isLoggedIn) {
    return <UserGreeting />;
  }
  return <GuestGreeting />;
}

ReactDOM.render(
  // Try changing to isLoggedIn={true}:
  <Greeting isLoggedIn={false} />,
  document.getElementById('root')
);

{% endhighlight %}

这个例子渲染了不同的‘greeting’，根据的就是isLoggedIn这个属性的值。

#### 元素变量

你可以用变量来储存一个元素。这样在组件中那些不需要变更的部分维持现状是，能帮助你条件性渲染组件（需要变更）的部分。

看看这两个新的组件，Logout登出和Login登入按钮：

{% highlight javascript %}

function LoginButton(props) {
  return (
    <button onClick={props.onClick}>
      Login
    </button>
  );
}

function LogoutButton(props) {
  return (
    <button onClick={props.onClick}>
      Logout
    </button>
  );
}

{% endhighlight %}

接着我们创建一个‘状态满满’的组件：LoginControl。

该组件会渲染<LoginButton/>和<LogoutButton/>当中的一个，渲染谁取决于当前它的状态。它也渲染了之前的例子<Greeting/>组件：

{% highlight javascript %}

class LoginControl extends React.Component {
  constructor(props) {
    super(props);
    this.handleLoginClick = this.handleLoginClick.bind(this);
    this.handleLogoutClick = this.handleLogoutClick.bind(this);
    this.state = {isLoggedIn: false};
  }

  handleLoginClick() {
    this.setState({isLoggedIn: true});
  }

  handleLogoutClick() {
    this.setState({isLoggedIn: false});
  }

  render() {
    const isLoggedIn = this.state.isLoggedIn;

    let button = null;
    if (isLoggedIn) {
      button = <LogoutButton onClick={this.handleLogoutClick} />;
    } else {
      button = <LoginButton onClick={this.handleLoginClick} />;
    }

    return (
      <div>
        <Greeting isLoggedIn={isLoggedIn} />
        {button}
      </div>
    );
  }
}

ReactDOM.render(
  <LoginControl />,
  document.getElementById('root')
);

{% endhighlight %}

声明一个变量而且使用if语句是一个不错的方法来达到条件化渲染组件的目的，有时候你可能想用更简短的语法。下面介绍下载JSX中的行内条件判断。

#### 行内的IF和逻辑&&操作符

任何JSX只要写在花括号里都是可嵌入的。这里面包括了JavaScript的'&&'(且运算符) 。这对于行内条件元素可能比较方便：

{% highlight javascript %}

function Mailbox(props) {
  const unreadMessages = props.unreadMessages;
  return (
    <div>
      <h1>Hello!</h1>
      {unreadMessages.length > 0 &&
        <h2>
          You have {unreadMessages.length} unread messages.
        </h2>
      }
    </div>
  );
}

const messages = ['React', 'Re: React', 'Re:Re: React'];
ReactDOM.render(
  <Mailbox unreadMessages={messages} />,
  document.getElementById('root')
);

{% endhighlight %}


这样之所以奏效，是因为在JavaScript中，true && 表达式 ，值永远等于表达式，且false && 表达式 永远等于false。

因此，要是条件的值是true(成立)，那么&&右边的元素就会被输出；条件不成立，React就会忽略和跳过右边的。

#### 行内的If-Else 使用条件运算符

条件化的行内渲染的另一种方法是使用JavaScript的三目运算符 condition ? true : false。

下面的例子中我们就用这种写法条件化渲染了一小段文字：

{% highlight javascript %}

render() {
  const isLoggedIn = this.state.isLoggedIn;
  return (
    <div>
      The user is <b>{isLoggedIn ? 'currently' : 'not'}</b> logged in.
    </div>
  );
}

{% endhighlight %}

它也能用于更大的表达式，尽管它也不太清楚发生什么：

{% highlight javascript %}

render() {
  const isLoggedIn = this.state.isLoggedIn;
  return (
    <div>
      {isLoggedIn ? (
        <LogoutButton onClick={this.handleLogoutClick} />
      ) : (
        <LoginButton onClick={this.handleLoginClick} />
      )}
    </div>
  );
}

{% endhighlight %}

就像JavaScript，选哪一种风格是取决于你和你的团队认为哪一种风格可读性强，这由你决定。但也要记得，任何时候，条件太多太复杂，那么这时候就是提取组件的好时机。

#### 防止组件渲染

少数情况下，你可能想要组件吧自己隐藏，即使组件本身被其他组件渲染着。这样你需要return null 来替代组件本身的输出。

下面的例子中，<warnningBanner/>被渲染与否决定于一个叫warn属性props。如果这个prop属性值为false，那么这个组件将不会被渲染。

{% highlight javascript %}

function WarningBanner(props) {
  if (!props.warn) {
    return null;
  }

  return (
    <div className="warning">
      Warning!
    </div>
  );
}

class Page extends React.Component {
  constructor(props) {
    super(props);
    this.state = {showWarning: true}
    this.handleToggleClick = this.handleToggleClick.bind(this);
  }

  handleToggleClick() {
    this.setState(prevState => ({
      showWarning: !prevState.showWarning
    }));
  }

  render() {
    return (
      <div>
        <WarningBanner warn={this.state.showWarning} />
        <button onClick={this.handleToggleClick}>
          {this.state.showWarning ? 'Hide' : 'Show'}
        </button>
      </div>
    );
  }
}

ReactDOM.render(
  <Page />,
  document.getElementById('root')
);

{% endhighlight %}

从一个组件的render的方法返回null不会影响第一个生命周期方法。举例来说，componentWillUpdate和componentDidUpdate依然会被调用。

[官网文章 Quick Start :Conditional Rendering](https://reactjs.org/docs/conditional-rendering.html)
