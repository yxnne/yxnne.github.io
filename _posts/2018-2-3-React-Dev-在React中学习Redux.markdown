---
layout: post
title:  在React中学习Redux
date:   2018-2-3
categories: Developing_React
tags: [Developing_React]
---


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

// 建立 store
const store = createStore(counter);

// 那么拿到状态就是用store.getState()
const init = store.getState();
console.log('init getStore is :',init)

// 派发事件
store.dispatch({type:'ADD'});
console.log('dispatch getStore is :',store.getState())


-----------------------

// 订阅事件
function listner(){
  console.log('now there are ', store.getState());
}

store.subscribe(listner);

store.dispatch({type:'ADD'});
store.dispatch({type:'ADD'});
store.dispatch({type:'ADD'});

 




index.redux.js-----------------------------
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


改 index.js----------------------------------

import React from 'react';
import ReactDom from 'react-dom';

import { createStore } from 'redux';
import { counter } from './index.redux';

import {add} from './index.redux';

class App extends React.Component{

  render(){
    const store = this.props.store;
    const num = store.getState();
    return (
      <div>
        <h1>now is {num}</h1>
        <button onClick={()=>store.dispatch(add())}>ADD</button>
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
<big>Redux</big>,其实是管理状态的工具，不只是在React中能用，在其他里面也是可以的，只是React中用的比较多。

#### 基本思想
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

#### 使用起来不啰嗦

{% highlight ruby %}



{% endhighlight %}




__路漫漫其修远兮，吾将上下而求索__

