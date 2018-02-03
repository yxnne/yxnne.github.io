---
layout: post
title:  在React中学习Redux
date:   2018-2-3
categories: Developing_React
tags: [Developing_React]
---

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

// 建立 store
const store = createStore(counter);

// 那么拿到状态就是用store.getState()
const init = store.getState();
console.log('init getStore is :',init)

// 派发事件
store.dispatch({type:'ADD'});
console.log('dispatch getStore is :',store.getState())


{% endhighlight %}

{% highlight ruby %}

// 订阅事件
function listner(){
  console.log('now there are ', store.getState());
}

store.subscribe(listner);

store.dispatch({type:'ADD'});
store.dispatch({type:'ADD'});
store.dispatch({type:'ADD'});

{% endhighlight %}




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
<big></big>  

#### 

 
#### 安装还是npm

{% highlight ruby %}



{% endhighlight %}

#### 使用起来不啰嗦

{% highlight ruby %}



{% endhighlight %}




__路漫漫其修远兮，吾将上下而求索__

