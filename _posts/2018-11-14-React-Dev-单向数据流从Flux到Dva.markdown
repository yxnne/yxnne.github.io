单向数据流：从 Flux 到 Redux 再到 Dva

# why we need '单向数据流'？

## 弊端

React 可以简单粗暴的理解为 View 视图层的解决方案，如果你的应用足够简单，简单到你可以直接在把少量的数据维护到 view 层本地。
但是当业务逻辑丰富起来之后，单纯 view 层维护数据会有很多问题：比如，不同页面或组件间共享数据的问题，共享数据一致性的问题。

当然上述问题不是引不引入所谓单向数据流思想的问题，而是是否将数据从视图层中解耦的问题。

对于这个问题，最经典的解决方案就是：数据模型和视图分离，他们之间的关系用控制器来维护。控制器是视图与数据之间的桥梁。

这就是所谓 MVC。

这种思想非常好，理想情况的 MVC 应该是：当视图中需要改变数据，先通过请求 Controller，由 Controller 驱动数据模型层更新。但实际应用中，往往会有很多情况 View 层直接更新了 Modal 层的数据，绕过了 Controller。这样就会越来越混乱，维护的成本变得很高。

## 单向数据流思想

上文中提到 MVC 在现实实践中可能并没有严格的按照理想情况来运转，核心的原因是 View 层可能直接接触了 Modal 层。那么解决这个问题的思路就很清晰：加一条限制，让所有需要更新 Modal 层的请求都经过“Controller”层，这就解决了 modal 层数据更新的问题。但当 modal 层中数据变化之后如何反馈到 view 层中呢？再加一条限制，就是 modal 层中的数据变更了之后，不经过 controller 而直接反馈到 view 层。

这样数据流就变成了这样：

> > > > 图 单向数据流闭环 和 传统 MVC

这就是单向数据流的思想。可以粗暴的理解为，严格限制数据流向版本的 MVC。

# Flux

Flux 框架是 Facebook 实现单向数据流思想的一种框架。在 Flux 框架中涉及：

- Store：数据模型层 Model，负责存储维护数据

- Dispatcher：按类型分发处理数据的请求的控制器。

- Action：处理数据类型的对象。

- View：视图，在 React 项目中为组件。

单向数据流关系表现如下：

> > > Flux 经典数据流关系图

## 结合 Demo：Flux 单向数据流怎么做

- 一个 Counter 计数器的例子：页面上有两个计数器，分别都有按钮"+"or"-"，点击之后对应的数字会增加或者减小（单位 1）

### Modal 层

model，体现在 Flux 中就是 Store，新建一个 CounterStore.js,在该文件中：

1. 两个计数器的初始数据

```
const counterValues = {
  c1: 10,
  c2: 20,
};

```

下来我们要明晰要用 Store 来做什么：首先，顾名思义，Store 是存储数据的地方，而且存储的不只有初始数据，而是当前的最新数据； 其次， Store 需要提供一种能力来对外输出最新的值，以及对外暴露更改自己所存储的值得方法；最后，当自己内部的值更新后，需要让外界了解（通知外部对象）。

为了实现这样的功能，借助了 EventEmitter 对象，让 Store 扩展 EventEmmiter： >>> EventEmmiter >>>>

```

const CHANGE_EVENT = "changed";

// CounterStore现在就要注册时间相应事件等功能
const CounterStore = Object.assign({}, EventEmitter.prototype, {
  // 对外暴露的get方法
  // 维护的数据其实就是 counterValues
  getCounterValues: function() {
    return counterValues;
  },
  // store发送变更的广播消息
  emitChange: function() {
    this.emit(CHANGE_EVENT);
  },
  // store自己注册广播消息的监听
  // 当收到广播，调用 callback
  addChangeListener: function(callback) {
    this.on(CHANGE_EVENT, callback);
  },
  // 移除广播监听
  removeChangeListener: function(callback) {
    this.removeListener(CHANGE_EVENT, callback);
  }
});

```

这样 Store 就有了存储数据的能力，通知数据变更以及变更之后处理回调的能力。但是数据是怎样变更的呢？这个也需要根据适当的逻辑来处理。根据 flux 模型，数据变更有且仅有 action 驱动。
那么反应到模型层中就是 new ModelValue = someFunction (action) .

```

// 这个方法是将处理action的逻辑注册起来
// 这个所谓的dispatchToken是一个标识，多用于控制多个store间的顺序操作
CounterStore.dispatchToken = FluxDispatcher.register(action => {
  if (action.type === ActionTypes.ADD) {
  	// 可以看到，由不同的action type驱动counterValues中值得更新
  	// 更新完成后广播更新事件
    counterValues[action.counterCaption]++;
    CounterStore.emitChange();
  } else if (action.type === ActionTypes.SUB) {
    counterValues[action.counterCaption]--;
    CounterStore.emitChange();
  }
});

```

FluxDispatcher 是 flux 库最重要的东西，因为 action 是由 dispatcher 分发的，分发在哪里呢？就是 register 注册的这里。

### 控制层 Controller

Flux 模型的控制层主要就是 Action 和 Dispatcher。

上文中的 FluxDispatcher，其实就是这个：

```
// FluxDispatcher.js
import { Dispatcher } from "flux";

/**
 * 新建一个Dispather
 */
export default new Dispatcher();

```

Action.js 的工作是对外提供方法，使得 View 层调用某方法后，某个 Action 对象能够顺利的被派发到 Model 层中，派发的方法就是 dispatch():

```

// ActionType.js
// 定义 Type 类型

export const ADD = "add";

export const SUB = "sub";


// Action.js
import FluxDispatcher from "./FluxDispatcher";
import * as ActionTypes from "./ActionTypes";

/**
 * ActionCreator
 * 由dispatcher分发
 */
export const addOne = counterCaption => {
  FluxDispatcher.dispatch({
    type: ActionTypes.ADD,
    counterCaption: counterCaption
  });
};

export const subOne = counterCaption => {
  FluxDispatcher.dispatch({
    type: ActionTypes.SUB,
    counterCaption: counterCaption
  });
};


```

### 视图层 View

这里粘贴了 Component/Counter.js 的代码，是一个简单的 React 组件，在该组件的生命周期 mount 阶段，调用了 model 层中注册变更监听的方法，并将 this.this.onChange()方法作为变更监听的回调。可想而知，每当 Model 变更，那么 View 层中的回调方法 onChange()会被调用。
那么，在 this.onChange()方法中做了什么？
拿到最新的数据，然后 setState()触发 React 的 render 机制。
view 层想要更新 Model 层的使用，假设按钮的 onClick 方法出发了，就调用的是 Action 中对应的方法，由 Action 将更新的请求传递到 model 层。
这样数据流是不是很清晰的运转着...

```

import React, { Component } from "react";
import CounterStore from "../store/CounterStore";
import * as Actions from "../Actions";

export default class Counter extends Component {
  constructor(props) {
    super(props);

    //使用Store来初始化值
    this.state = {
      number: CounterStore.getCounterValues()[props.name]
    };
    this.addOne = this.addOne.bind(this);
    this.subOne = this.subOne.bind(this);
    this.onChange = this.onChange.bind(this);
  }

  addOne() {
    Actions.addOne(this.props.name);
  }

  subOne() {
    Actions.subOne(this.props.name);
  }

  // 当flux中数据变化的时候
  // 映射到view层
  // 那么要自动映射的话，需要注册该函数
  onChange() {
    const newNum = CounterStore.getCounterValues()[this.props.name];
    this.setState({ number: newNum });
  }

  // 在CounterStore中注册onchange
  componentDidMount() {
    CounterStore.addChangeListener(this.onChange);
  }

  // 在CounterStore中注销onchange
  componentWillUnmount() {
    CounterStore.removeChangeListener(this.onChange);
  }

  render() {
    return (
      <div>
        <button onClick={this.addOne}>+</button>
        {this.state.number}
        <button onClick={this.subOne}>-</button>
      </div>
    );
  }
}


```

[Flux 的计数器 Demo](https://codesandbox.io/s/wz052wvkpk)

# Redux

Redux 是实现单向数据流的另一框架，非 Facebook 出品，但是被 Facebook 乃至全世界认可。

## 思想的进化

Flux 框架实现了单向数据流的思想，但是不够完美：1. 繁琐，自己实现监听等功能。2.涉及多个 Store 更新数据时，牵扯时机控制问题。
在 Flux 上再增加限制：

1. 唯一数据源： 将应用中的 store 做成一个唯一的树结构的对象。

2. 状态只读：store 中的数据改变必须来自 Action 的派发。（这和 Flux 是一致的）

3. 纯函数来改变数据：纯函数即一个函数的返回结果只依赖于它的参数，并且在执行过程里面没有副作用，我们就把这个函数叫做纯函数（一个函数执行过程对产生了外部可观察的变化那么就说这个函数是有副作用的）。

[纯函数](https://blog.csdn.net/c_kite/article/details/79138814)

这里的模型为 newState = reducer(oldState, action)

新的模型对象 newState 是 reducer 的返回值，返回的具体值由 action 和旧对象的值决定。

以上就是 Redux 的三个基本原则。


## Redux 怎么做
使用Redux的项目通常会有这几个角色：
1. Store： 和Flux中store的概念完全一样，是用来存储数据的对象。
2. Reducer： 根据旧的状态和Action产生新的状态，纯函数。
3. Action与ActionCreator: Action相关的对象。
所以，使用Redux来实现之前的Demo会变成这样。

### Store
createStore(reducer, initState) 是redux构造一棵状态树时所使用的方法。这里构造出的store就具有了dispatch（分发Action），以及getState（得到最新的被存储的数据）的能力.
```
// Store.js
import { createStore } from "redux";
import reducer from "./Reducer";

const initState = {
  c1: 10,
  c2: 20,
  c3: 0
};

const store = createStore(reducer, initState);

export default store;

```

### Reducer

reducer是一个纯函数，核心思想就是 newState = reducer(oldState, action)
```
import * as ActionTypes from "./ActionTypes";

export default (state, action) => {
  const { counterCaption } = action;
  switch (action.type) {
    case ActionTypes.ADD:
      return {
        ...state,
        [counterCaption]: state[counterCaption] + 1
      };

    case ActionTypes.SUB:
      return {
        ...state,
        [counterCaption]: state[counterCaption] - 1
      };

    default:
      return state;
  }
};


```

### ActionCreator 

```
import * as Actions from "./ActionTypes";

// redux 的 action creator
export const add = counterCaption => {
  return {
    type: Actions.ADD,
    counterCaption: counterCaption
  };
};

export const sub = counterCaption => {
  return {
    type: Actions.SUB,
    counterCaption: counterCaption
  };
};


```

### View层的使用

```
import React, { Component } from "react";
import store from "../Store";
import * as Actions from "../Action";

export default class Counter extends Component {
  constructor(props) {
    super(props);

    this.state = this.getOwnState();

    this.onChangeCallback = this.onChangeCallback.bind(this);
  }

  componentDidMount() {
    store.subscribe(this.onChangeCallback);
  }
  componentWillUnmount() {
    store.unsubscribe(this.onChangeCallback);
  }
  onChangeCallback() {
    this.setState(this.getOwnState());
  }

  getOwnState() {
    const states = store.getState();
    return {
      number: states[this.props.caption]
    };
  }
  addOne() {
    store.dispatch(Actions.add(this.props.caption));
  }

  subOne() {
    store.dispatch(Actions.sub(this.props.caption));
  }

  render() {
    return (
      <div>
        <button onClick={() => this.addOne()}>+</button>
        {this.state.number}
        <button onClick={() => this.subOne()}>-</button>
      </div>
    );
  }
}


```
在这里就用到了store的一些核心的api：subscribe() / unsubscribe()用来订阅/取消订阅store中存储的数据； dispatch() 是分发一个action给reducer来处理的方法； getState()用来获得最新的数据。

[原生redux 的计数器 Demo](https://codesandbox.io/s/qx1l53z3l6)

### 树数据结构
不同的业务数据之间需要解耦合，需要在合理设计数据结构之后，将处理不同业务模型的数据分别处理，那就是好几个reducer了。在redux中可以通过中间件combineReducers，来将不同的reducer合并成一个reducer。
比如下面，对外输出了一个合并过的reducer对象：
```
import { combineReducers } from 'redux';

// 不同的reducers import
import { user } from './reducers/user';
import { msgLists } from './reducers/msgLists';
import { notes } from './reducers/notes';

// combine the reducers
export default combineReducers({ user, msgLists, notes });


// 基于合并过了的reducer构建store
const store = createStore(reducers, initState);

```
这样实际在redux中store的，这是什么结构呢？
```
state:{
	bizProps1:{}, bizProps2:{}, bizProps3:{} .....
}

``` 

## react-redux
### 具有React特色的进化
Redux，尽管看上去只是在React中使用，但是实际是可以应用到其他框架中的，只不过不常见。既然广泛的使用场景是React中，不妨加些React特色的东西，react-redux这个库就应运而生。
这个库提供了一个组件Provider和一个高阶组件Connect。
解决了什么问题？
1. 原生redux在view层依赖store，父组件，子组件，孙子组件都有可能从store中getState()或者dispatch()，的确有这样的需求，但是每一个组件都import store不是一个好办法。(Prvider组件解决的问题)

2. 又增加了限制：从store中取到的数据再react-redux中默认都是以Props的形式拿到的。(Connect组件解决的问题)

### 使用react-redux改进demo

1. 最外层组件使用Provider包裹，并将store注入：
```
import { Provider } from "react-redux";
import store from "./Store";

// ...
const Root = () => (
  <Provider store={store}>
    <App />
  </Provider>
);

// ...

const rootElement = document.getElementById("root");
ReactDOM.render(<Root />, rootElement);

```

2. 视图View中引入connect 
这些注释掉的都是相较于原生redux的不需要的。
```

import React, { Component } from "react";
// import store from "../Store";
import { connect } from "react-redux";
import * as Actions from "../Action";

class Counter extends Component {
  constructor(props) {
    super(props);

    // this.state = this.getOwnState();

    // this.onChangeCallback = this.onChangeCallback.bind(this);
  }

  // componentDidMount() {
  //   store.subscribe(this.onChangeCallback);
  // }
  // componentWillUnmount() {
  //   store.unsubscribe(this.onChangeCallback);
  // }
  // onChangeCallback() {
  //   this.setState(this.getOwnState());
  // }

  // getOwnState() {
  //   const states = store.getState();
  //   return {
  //     number: states[this.props.caption]
  //   };
  // }
  // addOne() {
  //   store.dispatch(Actions.add(this.props.caption));
  // }

  // subOne() {
  //   store.dispatch(Actions.sub(this.props.caption));
  // }

  render() {
    console.log(this.props);
    return (
      <div>
        <button onClick={() => this.props.add(this.props.caption)}>+</button>
        {this.props.numbers[this.props.caption]}
        <button onClick={() => this.props.sub(this.props.caption)}>-</button>
      </div>
    );
  }
}

export default connect(
  state => ({
    numbers: state
  }),
  {
    add: Actions.add,
    sub: Actions.sub
  }
)(Counter);

```

[react-redux 的计数器 Demo](https://codesandbox.io/s/x24mx2m204)

### react-redux 原理分析

1. Context : Context 是react的一个功能。组件传递props的时候都是由上向下的，就是爷爷传递给父亲，父亲传递给儿子。假设有一个属性父亲拿着确实没用，父亲唯一能做的就是原模原样的传递给儿子，这时候再经过父亲一级就很繁琐。Context就是为了解决祖先组件跨组件传递props的问题。基本原理就是，祖先组件将部分props声明为context之后，	其后代组件可以直接从context中拿到，避免没意义的层层传递。

2. Provider的作用：Provider就是应用的最顶级组件，在该组件中，store是必须的要传入的props，也就是redux所创建的存储对象。Provider将store声明为context，这样保证了凡是Provider的后代组件，都有能力拿到store的数据和方法。那么因为Provider作为顶级组件，整个应用就被赋予了随时获得数据，修改数据的能力。

3. 高阶组件connect：OutputComponent = connect(mapStateToProps, mapDispatchToProps, ...)(YourComponent)
高阶组件connect大致是这样的结构：
```
function connect(mapStateToProps, mapDispatchToProps){
	
	return function X(YourComponent){

		return class Outer extends Component{

			// 1. 在加载阶段从context中拿到store并注册监听
			// store.subscribe();

			render(){
				// 
				// 调用mapStateToProps函数，得到newValueProps， 
				// 对应的功能就是将模型层中的值作为props注入组件

				// 调用mapDispatchToProps函数，得到newActionProps
				// 对应的功能就是将修改模型层中的值的方法也作为props注入组件

				// 将这些props都注入到组件中
				return (<YourComponent ...newValueProps ...newActionProps />)
			}
		}
	}
}

```
基本原理就是上述伪代码，简单文字描述下：实际得到的组件为Outer, Outer是对YourComponent的包裹，同时增强了他的功能，具体就是，在Outer的加载生命周期阶段从context中拿到store，并注册监听。在render的return之前，将这些store中的数据或者actionCrator方法都以Props的形式交给子组件YourComponent。这样，YourComponent中使用数据或者改变数据就从，this.props中拿到。


PS：项目支持装饰器写法的时候，上述例子中：
```
OutputComponent = connect(mapStateToProps, mapDispatchToProps, ...)(YourComponent)

// 可以写成

@connect(mapStateToProps, mapDispatchToProps, ...)
YourComponent ...

```
## 处理异步的问题

### react-thunk

### react-saga

# Dva

## Dva 做了什么

# 注意事项
