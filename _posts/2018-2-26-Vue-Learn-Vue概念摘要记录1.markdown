---
layout: post
title:  Vue学习:概念记录
date:   2018-2-26
categories: Vue
comment: true
tags: [Vue]
---
Vue (读音 /vjuː/，类似于 view) 是一套用于构建用户界面的渐进式框架。与其它大型框架不同的是，Vue 被设计为可以自底向上逐层应用。----官网介绍


[官网](https://cn.vuejs.org/)是最权威的参考，我的笔记其实也是摘要，大部分来自官网

索引：
1.<a href="#r1">Vue对比React</a>
2.<a href="#r2">Vue笔记:实例</a>
3.<a href="#r3">Vue笔记:模板语法</a>


#### Vue对比React
<a name="r1"></a>
React 和 Vue 有许多相似之处，它们都有：

* __使用 Virtual DOM__
提供了响应式 (Reactive) 和组件化 (Composable) 的视图组件。
将注意力集中保持在核心库，而将其他功能如路由和全局状态管理交给相关的库。
由于有着众多的相似处，我们会用更多的时间在这一块进行比较。这里我们不只保证技术内容的准确性，同时也兼顾了平衡的考量。我们需要承认 React 比 Vue 更好的地方，比如更丰富的生态系统。

* 运行时性能
React 和 Vue 都是非常快的，所以速度并不是在它们之中做选择的决定性因素。对于具体的数据表现，可以移步这个第三方 benchmark，它专注于渲染/更新非常简单的组件树的真实性能。

* 优化
在 React 应用中，当某个组件的状态发生变化时，它会以该组件为根，重新渲染整个组件子树。

在 Vue 应用中，组件的依赖是在渲染过程中自动追踪的，所以系统能精确知晓哪个组件确实需要被重渲染。

* JSX vs Templates

JSX的优势：
1.你可以使用完整的编程语言 JavaScript 功能来构建你的视图页面。比如你可以使用临时变量、JS 自带的流程控制、以及直接引用当前 JS 作用域中的值等等。

2.开发工具对 JSX 的支持相比于现有可用的其他 Vue 模板还是比较先进的 (比如，linting、类型检查、编辑器的自动完成)。

模板的优势：
1.对于很多习惯了 HTML 的开发者来说，模板比起 JSX 读写起来更自然。这里当然有主观偏好的成分，但如果这种区别会导致开发效率的提升，那么它就有客观的价值存在。

2.基于 HTML 的模板使得将已有的应用逐步迁移到 Vue 更为容易。这也使得设计师和新人开发者更容易理解和参与到项目中。

3.你甚至可以使用其他模板预处理器，比如 Pug 来书写 Vue 的模板。

* vue-cli和creact-react-app
creact-react-app不允许在项目生成时进行任何配置，而 Vue 支持 Yeoman-like 定制。
creact-react-app只提供一个构建单页面应用的单一模板，而 Vue 提供了各种用途的模板。
creact-react-app不能用用户自建的模板构建项目，而自建模板对企业环境下预先建立协议是特别有用的。

* Native对比
React Native 能使你用相同的组件模型编写有本地渲染能力的 APP (iOS 和 Android)。能同时跨多平台开发，对开发者是非常棒的。相应地，Vue 和 Weex 会进行官方合作，Weex 是阿里巴巴发起的跨平台用户界面开发框架，同时也正在 Apache 基金会进行项目孵化，Weex 允许你使用 Vue 语法开发不仅仅可以运行在浏览器端，还能被用于开发 iOS 和 Android 上的原生应用的组件。
Weex 还在积极发展，成熟度也不能和 React Native 相抗衡。

* 向下扩展性

React 学习曲线陡峭，在你开始学 React 前，你需要知道 JSX 和 ES2015，因为许多示例用的是这些语法。你需要学习构建系统，虽然你在技术上可以用 Babel 来实时编译代码，但是这并不推荐用于生产环境。

就像 Vue 向上扩展好比 React 一样，Vue 向下扩展后就类似于 jQuery。你只要把如下标签放到页面就可以运行：

```

<script src="https://cdn.jsdelivr.net/npm/vue"></script>

```

然后你就可以编写 Vue 代码并应用到生产中，你只要用 min 版 Vue 文件替换掉就不用担心其他的性能问题。

由于起步阶段不需学 JSX，ES2015 以及构建系统，所以开发者只需不到一天的时间阅读指南就可以建立简单的应用程序。


#### Vue实例
<a name="r2"></a>

* 每个 Vue 应用都是通过用 Vue 函数创建一个新的 Vue 实例开始的：
一个 Vue 应用由一个通过 new Vue 创建的根 Vue 实例，以及可选的嵌套的、可复用的组件树组成。

```

var vm = new Vue({
  // 选项
})

```

* 数据/方法


```

var data = {
  newTodoText: '',
  a: 0,
  hideCompletedTodos: false,
  todos: [],
  error: null
}


// 该对象被加入到一个 Vue 实例中
var vm = new Vue({
  data: data
})

// 它们引用相同的对象！
vm.a === data.a // => true

```

当这些数据改变时，视图会进行重渲染。值得注意的是只有当实例被创建时 data 中存在的属性才是响应式的。也就是说如果你添加一个新的属性，比如：

这里唯一的例外是使用 Object.freeze()，这会阻止修改现有的属性，也意味着响应系统无法再追踪变化。

* 生命周期方法

```

// 比如 created 钩子可以用来在一个实例被创建之后执行代码：
new Vue({
  data: {
    a: 1
  },
  created: function () {
    // `this` 指向 vm 实例
    console.log('a is: ' + this.a)
  }
})

```

**不要**在选项**属性**或**回调**上使用**箭头函数**，比如 created: () => console.log(this.a) 或 vm.$watch('a', newValue => this.myMethod())。因为箭头函数是和父级上下文绑定在一起的，this 不会是如你所预期的 Vue 实例，经常导致 Uncaught TypeError: Cannot read property of undefined 或 Uncaught TypeError: this.myMethod is not a function 之类的错误。

![生命周期钩子官方例图](https://cn.vuejs.org/images/lifecycle.png)

#### Vue 模板语法
<a name="r3"></a>

Vue.js 使用了基于 HTML 的模板语法，允许开发者声明式地将 DOM 绑定至底层 Vue 实例的数据。所有 Vue.js 的模板都是合法的 HTML ，所以能被遵循规范的浏览器和 HTML 解析器解析。

在底层的实现上，Vue 将模板编译成虚拟 DOM 渲染函数。结合响应系统，Vue 能够智能地计算出最少需要重新渲染多少组件，并把 DOM 操作次数减到最少。

* 数据绑定最常见的形式就是使用“Mustache”语法 (双大括号) 的文本插值


```

<span>Message: {{ msg }}</span>

```


 v-once 指令，你也能执行一次性地插值，当数据改变时，插值处的内容不会更新。

```

<span v-once>这个将不会改变: { { msg } }</span>

```
* 绑定原生html属性，那么不能用双花括号语法（胡子语法），而是要使用v-bind：

```
<div v-bind:id="dynamicId"></div>


// 在布尔特性的情况下，它们的存在即暗示为 true，v-bind 工作起来略有不同，在这个例子中：

<button v-bind:disabled="isButtonDisabled">Button</button>
// 如果 isButtonDisabled 的值是 null、undefined 或 false，则 disabled 特性甚至不会被包含在渲染出来的 <button> 元素中。
```

* 绑定中使用js表达式
注意是表达式不是语句：

```

{ { number + 1 } }

{ { ok ? 'YES' : 'NO' } }

{ { message.split('').reverse().join('') } }

<div v-bind:id="'list-' + id"></div>

// 这是语句，不是表达式  
// { { var a = 1 } }

// { { } }中流控制也不会生效，请使用三元表达式  
// 不要这么写：
if (ok) { return message } 

```

* 指令 v-XXXX
指令 (Directives) 是带有 v- 前缀的特殊属性。指令属性的值预期是单个 JavaScript 表达式 。

指令的职责是，当表达式的值改变时，将其产生的连带影响，响应式地作用于 DOM。

当前接触了几个指令：
1. v-if
2. v-on
3. v-bind

指令可以带参数，比如：

```

<a v-bind:href="url">...</a>

<a v-on:click="doSomething">...</a>

```

针对上述两种指令，有一种简写方法：



```
<!-- 完整语法 -->
<a v-bind:href="url">...</a>

<!-- 缩写 -->
<a :href="url">...</a>


<!-- 完整语法 -->
<a v-on:click="doSomething">...</a>

<!-- 缩写 -->
<a @click="doSomething">...</a>

```


__少壮不努力，老大徒伤悲__
