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

上面代码，ex1处创建stroe，在createStore方法中传入的counter，这是一个reducer;<br>
ex2处就是拿到状态的方法getState();<br>
ex3派发事件，派发了一个时间后，reduce会自动的进行事件值得修改，另外，事件是一个对象{type:'event'}<br>

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

1.我们将组建的state都用store来管理，那么需要使用state值得地方用store.getState()得到值，需要修改state的地方用store.dispatch()派发事件;App的render()方法作为store订阅的回调函数，这样保证了每次state被修改都会重新渲染页面。看例子：

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

上面代码中reducer就是对外输出的函数counter，reducer是根据不同的action逻辑来处理事件变化的；<br>
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


以上代码中用reducer(在index.redux.js中定义的counter)创建store，store用render函数订阅。<br>
组件App中的store是通过this.props.store传入组件的，组件中引用值得时候都是store.getState()，需要改变state时都是store.dispatch()。<br>

2.可以将App组建组建和index.redux.js的耦合解除：

{% highlight ruby %}

// App Component
class App extends React.Component{

  render(){
    const store = this.props.store;
    const num = store.getState();
    //让 action creator 来自于 this.props
    const addOne = this.props.addOne 
    const subOne = this.props.subOne
    return (
      <div>
        <h1>now is {num}</h1>
        <button onClick={()=>store.dispatch(addOne())}>ADD</button>
        <button onClick={()=>store.dispatch(subOne())}>SUB</button>
      </div>
    );
  }
}

// in render()
ReactDom.render(<App store={store} addOne={add} subOne={sub}/>,
document.getElementById('root'));

{% endhighlight %}

3.处理异步事件 **redux-thunk**

异步处理需要一个中间件，常见的库有一个叫redux-thunk，使用npm在项目中安装。在项目创建store的时候引用：

{% highlight ruby %}
// 引入
import thunk from 'redux-thunk';

// ...
const store = createStore(counter, applyMiddleware(thunk));

{% endhighlight %}
createStore()放入第二个参数，该参数是applyMiddleware(thunk)，这样就引入了这个中间件。

{% highlight ruby %}

// 归零的一个action creator
export function zero(){
  return {type:ZERO};
}

// 异步的 action creator
// 返回函数, 执行异步
export function asyncToZero(){
  return (dispatch) => {
    setTimeout(()=>{
      dispatch(zero());
    }, 2000);
  }
}

{% endhighlight %}
asyncToZero是一个action creator 它希望不要立即修改state，所以这里模拟2秒之后修改，可以看到，使用redux-thunk想要异步动作时，只要return一个函数就好，这个函数接受dispatch作为参数，dispatch是一个函数，用来在异步时发送Action事件。<br>
当然，在counter这个reducer中也要添加相关的操作。
{% highlight ruby %}

export function counter(state=0, action){
  switch(action.type){
    case ADD:
      return state + 1;
    case SUB:
      return state - 1;
    case ZERO:
      return 0;
    default:
      return 10;
  }
}

{% endhighlight %}

#### react-redux
将React和Redux结合起来，当然有更简单的方法 -- react-redux。安装依然是npm安装($ cnpm i --save react-redux)

使用react-redux简化在哪里呢？主要有两点：

* Provider 组件简化subscribe

* connect 简化组件获取数据过程

改写代码： 

和Provider相关的修改：

{% highlight ruby %}

// import { Provider } from 'react-redux';
// ...

// render()中 用Provider组件包裹App组件并将store作为参数传递
ReactDom.render(
  (
    <Provider store={store}>
      <App />
    </Provider>
  ),
  document.getElementById('root')
);


{% endhighlight %}

和connect相关的修改：

{% highlight ruby %}

import { connect } from 'react-redux';

// 在App定义完之后需要使用connect再次封装下
// 再封装 APP组件
function mapStateProps(state){
  return {num:state};
}

const actionCreators = { add, sub, asyncToZero };

App = connect(mapStateProps, actionCreators)(App);

{% endhighlight %}
这里使用connect封装APP，前一个括号中第一个参数理解为APP中需要的状态，第二个参数理解为actioncreator的集合。

##### 使用装饰器，将connect写的简单点
需要一个插件就是：babel-plugin-transform-decorators-legacy(npm安装之：$ cnpm i --save babel-plugin-transform-decorators-legacy )。<br>

然后需要改下package.json中babel的配置，这样就支持装饰器写法了：


{% highlight ruby %}

"babel": {
    //.....
    "plugins":["transform-decorators-legacy"]
  },


{% endhighlight %}

最后,下面中注释的代码可以写成这种注解的形式：

{% highlight ruby %}
// function mapStateProps(state){
//   return {num:state};
// }

// const actionCreators = { add, sub, asyncToZero };

// App = connect(mapStateProps, actionCreators)(App);

@connect(
  state => ({num:state}), 
  {add, sub, asyncToZero}
)
class App extends React.Component{
  // ... 
}

{% endhighlight %}

另外，这里第一个箭头函数是从state里面取值，取需要的值，假如说state的只有一个值这种情况不常见，就像上面代码中，state中只有数值，这时需要一个别名来引用之，所以箭头函数的返回值是{num:state}，意思就是__向this.props中插入一个名为num,值为state的数据__。<br>
那么，假设state中不只是一个值，state中存放的键值对或者其他对象，比如声明reducer的时候：

{% highlight ruby %}

export function auth(state={isAuth:false, user:'yy'}, action){
  // ...
}

{% endhighlight %}

那么state中就至少有了这个对象{isAuth:false, user:'yy'},拿到这个对象得像这样：

{% highlight ruby %}

@connect(
  state=>(state.auth), { login }
)

{% endhighlight %}

state.auth中这个auth就是reducer的名字，这样相当于把auth这个reducer中的属性isAuth和user注入到当前组件了，对，是有点依赖注入的感觉。<br>

这时 state中的层级关系就是：<br>
&nbsp;state
&nbsp;&nbsp;auth
&nbsp;&nbsp;&nbsp;isAuth
&nbsp;&nbsp;&nbsp;user

&nbsp;&nbsp;other reducer
&nbsp;&nbsp;...

#### 多个reducer进行合并

有多个renducer，那么使用之前是要进行reducer合并的。redux库中提供了合并的方法：具体操作是：
新建一个文件，eg:reducers.js:

{% highlight ruby %}

import { combineReducers } from 'redux';  // 靠他来合并
import { counter } from './index.redux';  // reducer1 引入
import { auth } from './Auth.redux';      // reducer2 引入

export default combineReducers({counter, auth});

{% endhighlight %}

那么，记得在构造store的时候，就要用新合并的reducers了:

{% highlight ruby %}

import reducers from './reducer/reducers';
// ...
const store = createStore(reducers, compose(applyMiddleware(thunk), reduxDevTool));

{% endhighlight %}

这样，在使用的时候依然按需取值@connect中第一个参数是箭头函数，传入state，返回本组件需要用的state中的属性，第二个参数本组件需要使用的 action creator 的集合，这是对state能进行的操作。

#### Chrome插件 reduxDevTools

reduxDevTools，这是一个Chrome的插件，用来调试Redux(Chrome商店墙外搜索redux，第一个插件就是，标志是上下两张符号笑脸)。使用的方法需要在代码中添加插件的函数，这样就把插件和store关联起来。程序运行起来后在chrome中就可以使用。


{% highlight ruby %}
// 1.引入compose
import { createStore, applyMiddleware, compose} from 'redux';

// 2.如果 window.__REDUX_DEVTOOLS_EXTENSION__函数存在，就调用下
const reduxDevTool = window.__REDUX_DEVTOOLS_EXTENSION__?window.__REDUX_DEVTOOLS_EXTENSION__():f=>f;

// 3.重新创建 createStore
const store = createStore(counter, compose(applyMiddleware(thunk), reduxDevTool));

{% endhighlight %}

上面代码的第二步可以这样写 ：

{% highlight ruby %}

const reduxDevTool = window.__REDUX_DEVTOOLS_EXTENSION__ && window.__REDUX_DEVTOOLS_EXTENSION__();

{% endhighlight %}

#### 再简单说下

Redux果然是能将组件和状态解耦合，对State状态需要改变的代码也从组件中解耦了，不管是数据还是行为，需要使用的时候随时在组件之前注入就好。

__路漫漫其修远兮，吾将上下而求索__

