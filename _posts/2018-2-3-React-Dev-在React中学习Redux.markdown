---
layout: post
title:  在React中学习Redux
date:   2018-2-3
categories: Developing_React
tags: [Developing_React]
---

<big>Redux</big>,其实是管理状态的工具，不只是在React中能用，在其他里面也是可以的，只是React中用的比较多。

#### 关于Redux最基本概念
就是将状态在一处统一分发和管理。有这么几个概念性的东西：

* reducer：状态的处理逻辑，会根据action的类型来相应对状态值做出修改；

* store：就是统一管理状态的地方，从store中可以获取状态；也可以派发事件；
看下面一个最基本的例子：

{% highlight ruby %}

import { createStore } from 'redux';

// 根据action 变化state
// 这个就是reducer
function counter(state=0, action){
  switch(action.type){
    case 'ADD':
      return state + 1;
    case 'SUB':
      return state - 1;
    default:
      return 10;
  }
}

// ex1:建立 store
const store = createStore(counter);

// ex2:那么拿到状态就是用store.getState()
const init = store.getState();
console.log('init getStore is :',init)

// ex3:派发事件
store.dispatch({type:'ADD'});
console.log('dispatch getStore is :',store.getState())

{% endhighlight %}

上面代码，ex1处创建stroe，在createStore方法中传入的counter，这是一个reducer;
ex2处就是拿到状态的方法getState();
ex3派发事件，派发了一个时间后，reduce会自动的进行事件值得修改，另外，事件是一个对象{type:'event'}

* subscribe:给store维护的状态改变时，添加一个订阅回调函数，比如每当store状态变化时会做什么事。

{% highlight ruby %}

// 订阅事件
function listner(){
  console.log('now there are ', store.getState());
}

store.subscribe(listner);

store.dispatch({type:'ADD'});
store.dispatch({type:'SUB'});
store.dispatch({type:'ADD'});

{% endhighlight %}

事件被派发了三次，在派发之前还好用listner函数订阅了，所以listner也被调用了三次。

#### 结合React的小Demo

我们将组建的state都用store来管理，那么需要使用state值得地方用store.getState()得到值，需要修改state的地方用store.dispatch()派发事件;App的render()方法作为store订阅的回调函数，这样保证了每次state被修改都会重新渲染页面。看例子：

index.redux.js中代码

{% highlight ruby %}

import { createStore } from 'redux';
const ADD = 'ADD';
const SUB = 'SUB';
// 根据action 变化state
// 这个就是reducer
export function counter(state=0, action){
  switch(action.type){
    case ADD:
      return state + 1;
    case SUB:
      return state - 1;
    default:
      return 10;
  }
}
// action creator
// 对外暴露这些 action creator 函数来产生action对象
export function add(){
  return {type:ADD};
}
export function sub(){
  return {type:SUB};
}

{% endhighlight %}

上面代码中reducer就是对外输出的函数counter，reducer是根据不同的action逻辑来处理事件变化的；
另外还输出了两个函数add和sub作为action creater，专职产生action。

index.js中代码

{% highlight ruby %}

import React from 'react';
import ReactDom from 'react-dom';

import { createStore } from 'redux';
import { counter } from './index.redux';

import { add, sub } from './index.redux';

class App extends React.Component{

  render(){
    const store = this.props.store;
    const num = store.getState();
    return (
      <div>
        <h1>now is {num}</h1>
        <button onClick={()=>store.dispatch(add())}>ADD</button>
        <button onClick={()=>store.dispatch(sub())}>SUB</button>
      </div>
    );
  }
}

const store = createStore(counter);

function render(){
  ReactDom.render(<App store={store}/>,document.getElementById('root'));
}

render();

store.subscribe(render);
//ReactDom.render(<App/>,document.getElementById('root'));


{% endhighlight %}


以上代码中用reducer(在index.redux.js中定义的counter)创建store，store用render函数订阅。
主体的组件‘<App/>’中store是通过this.props.store传入组件的，组件中引用值得时候都是store.getState()，需要改变state时都是store.dispatch()。

{% highlight ruby %}


{% endhighlight %}


{% highlight ruby %}


{% endhighlight %}


{% highlight ruby %}


{% endhighlight %}


{% highlight ruby %}


{% endhighlight %}


__路漫漫其修远兮，吾将上下而求索__

