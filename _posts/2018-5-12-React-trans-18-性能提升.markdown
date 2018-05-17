---
layout: post
title:  React-官网学习-Advanced-Guides6-优化性能
date:   2018-5-12
categories: react_learnning
tags: [translations_React.js]
---
<big>React内部</big>应用了许多巧妙的技术来减少更新UI的DOM操作那昂贵的开销。对于大部分应用，不用做太多性能优化方面额外的工作，React就能提供一个快捷的用户界面。不过，还是有几种方法来加速你的React应用。

#### 使用生产版本构建

如果你正在benchmarking（建立基准）或者正在经历性能问题，首先保证你测试的是最小化的生产版本。
默认地，React包含许多有用的警告。这在开发时很有用。但是，它会导致React大而且慢，所以在部署你的应用前，要先保证使用的是生产版本。

如果你也不知道你的构建过程是否正确，你可以通过安装Chrome插件：React Developer Tools for Chrome。如果在访问某生产版本的React站点的时候，插件的图标是深色背景的:
![深色背景的插件图标](https://reactjs.org/static/devtools-prod-d0f767f80866431ccdec18f200ca58f1-1e9b4.png)

如果你访问的开发版本的，图标会是一个红色背景的：

![红色背景图标](https://reactjs.org/static/devtools-dev-e434ce2f7e64f63e597edf03f4465694-1e9b4.png)

理想情况是，在开发中使用开发模式，在部署时使用生产模式。

下面是关于构建开发模式应用的指导：

#### Create React App
如果你的项目是使用这个脚手架构建的，运行
```
npm run build
```
这就会产生一个生产版本的APP，在项目的build目录下。

记住这只在部署前是必要的。常规开发过程中就用npm start就行。

#### 单一文件构建
为单一文件构建我们也提供了生产版本的React和React DOM：
```
<script src="https://unpkg.com/react@16/umd/react.production.min.js"></script>
<script src="https://unpkg.com/react-dom@16/umd/react-dom.production.min.js"></script>

```

记住这些React文件只用结尾是product.min.js时是生产版本。

#### Brunch
对构建大多数高效的Brunch生产版本，安装uglify-js-brunch插件：
```
# If you use npm
npm install --save-dev uglify-js-brunch

# If you use Yarn
yarn add --dev uglify-js-brunch

```

接着构建生产版本，在构建命令中加上-p参数:

```

brunch build -p

```

记住，你只需要部署之前这么做。开发的时候可别加-p或者应用插件，因为这样就将React那些有用的警告信息隐藏了，而且构建还很慢。

#### Browserify

对构建大多数高效的Browserify生产版本，安装这些插件：

```
# If you use npm
npm install --save-dev envify uglify-js uglifyify 

# If you use Yarn
yarn add --dev envify uglify-js uglifyify 

```

要创建生产版本，确保你加上了这些转换（次序很重要）：

* envify : 保证设置了正确的构建环境，要全局安装（-g）；

* uglifyify: 移除了开发的imports,(-g)全局安装；

* 最终，打包的结果被输出到uglify-js中整合；

例子如下：

```

browserify ./index.js \
  -g [ envify --NODE_ENV production ] \
  -g uglifyify \
  | uglifyjs --compress --mangle > ./bundle.js

```

>注意：包名叫uglify-js但是提供的二进制文件叫uglifyjs，这不是打印错误。

记住，你只需要部署之前这么做。开发的时候可别应用这些插件，因为这样就将React那些有用的警告信息隐藏了，而且构建还很慢。

#### Rollup

对于高效的Rollup生产构建来说，安装插件如下：

```

# If you use npm
npm install --save-dev rollup-plugin-commonjs rollup-plugin-replace rollup-plugin-uglify 

# If you use Yarn
yarn add --dev rollup-plugin-commonjs rollup-plugin-replace rollup-plugin-uglify 

```

要创建生产版本，确保你加上了这些转换（次序很重要）：

* replace ： 保证设置正确的打包环境；

* commonjs：提供对于 Commonjs语法支持；

* uglify：压缩生产最终版本；

```

plugins: [
  // ...
  require('rollup-plugin-replace')({
    'process.env.NODE_ENV': JSON.stringify('production')
  }),
  require('rollup-plugin-commonjs')(),
  require('rollup-plugin-uglify')(),
  // ...
]

```

完整的安装示例[看这里](https://gist.github.com/Rich-Harris/cb14f4bc0670c47d00d191565be36bf0)

记住，你只需要部署之前这么做。开发的时候可别应用这些插件，因为这样就将React那些有用的警告信息隐藏了，而且构建还很慢。

#### webpack

>注意：如果用的是Create React APP,请参照上文[这里](https://reactjs.org/docs/optimizing-performance.html#create-react-app),这小节涉及的是自己直接配置webpack的情况。

对于高效的webpack生产构建来说，请确保这些插件在你的配置中：
```

new webpack.DefinePlugin({
  'process.env.NODE_ENV': JSON.stringify('production')
}),
new webpack.optimize.UglifyJsPlugin()

```

在[webpack的文档中你可以了解更多知识](https://webpack.js.org/guides/production-build/)。

记住，你只需要部署之前这么做。开发的时候可别应用这些插件，因为这样就将React那些有用的警告信息隐藏了，而且构建还很慢。


#### 使用Chrome的性能分析选项卡，绘制组件性能图

在**开发模式**中，使用Chrome的性能分析工具，你可以看到组件挂载，更新和卸载，就像这样：
![性能分析图](https://reactjs.org/static/react-perf-chrome-timeline-64d522b74fb585f1abada9801f85fa9d-dcc89.png)

用Chrome做这些：
1. 暂时关闭所有Chrome插件，尤其是React DevTools。插件会影响结果的；

2. 确保运行应用时是开发模式；

3. 打开Chrome调试工具中Performance选项卡，点击record按钮；

4. 执行你想要描述的行为。为了防止Chorme挂掉，最好不要超过20秒；

5. 停下record；

6. React事件将在User Timing中被分组列出；

看看[Ben Schwarz.的这篇文章](https://building.calibreapp.com/debugging-react-performance-with-react-16-and-chrome-devtools-c90698a522ad)来了解更多细节。

注意一点，你得到的数据是相对，所以在生产版本中组件会渲染的快些。不过这些数据依旧可以帮你分析出那些由错误引起的不相关组件的更新，以及组件更新UI的深度很频率。

当下，Chrome，Edge和IE支持这种特性，但我们是使用标准的 User Timing API的，所以我们希望更多浏览器添加对这种特性的支持。

#### 可见的长列表

如果应用渲染了一个非常长的数据列表（成百上千条），我们推荐使用一种技术：“windowing”（视窗）。这种技术仅仅渲染在众多数据行中的一个子集，且可以极大地减少重新渲染组件所需的时间以及创建的DOM节点的数量。

[React Virtualized](https://bvaughn.github.io/react-virtualized/)是一种流行的视窗化库。他提供了一些流行可复用的列表，网格和表格组件。如果你想要为你的应用场景做特殊的定制化，你也可以构建自己的窗口组件，比如[twitter did](https://medium.com/@paularmstrong/twitter-lite-and-high-performance-react-progressive-web-apps-at-scale-d28a00e780a3)(打不开)。

#### 避免核对（Reconciliation）

(Reconciliation，一致，和解，服从。不太会翻译，文中意思是指，在更新UI之前对比组件的变化行为)

React在内部创建和维护一个被渲染的UI的代表。它包括从组件返回的React元素。这个代表避免了让React创建DOM节点以及不必要的直接访问现存的节点，因为那样是比操作一个js对象更慢的。有时，这被称为“Virtual DOM”虚拟DOM，react native中也是这个原理。

当组件的props或者state发生改变，React会通过对比新的返回元素和旧的，来决定是否必要更新真实的DOM。在他们不“相等”的时候，React会跟新DOM。

通过React Dev Tool工具，可以看到虚拟DOM的重新渲染：

* Chrome Browser Extension
* Firefox Browser Extension
* Standalone Node Package

在开发者面板中的React选项卡界面中，选择Highlight Updates： 

![选择Highlight Updates](https://reactjs.org/static/devtools-highlight-updates-97eda4825de476af4515435a0c36ca78-acf85.png)


在你的页面上操作，你应该能看到一些彩色的边框在你的组件周围时不时的闪现。这能让你察觉到那些没有必要的重新渲染。你可以从[Ben Edelstein.的博客](https://blog.logrocket.com/make-react-fast-again-part-3-highlighting-component-updates-6119e45e6833)中了解更多React Dev Tools的特性。
考虑下这个例子：

![todos例子](https://reactjs.org/highlight-updates-example-7a42123e91b1b460b1a65605d6ff0d2b.gif)

注意当输入第二个TODO项时，之前输入的第一个TODO伴随着每一次键盘敲击同时也在闪烁着。这意味着React也在重新渲染它。这就叫做“浪费的”渲染。我们都知道第一次输入已经不会改变，所以对它重新渲染没有必要，可React不知道这一点。

即使React只更新变化的DOM节点，重新渲染也是要耗费时间的。大多数时候这倒是无所谓，但是如果这种慢下来是可觉察到的，那你就得通过重写一个生命周期方法来加速了--shouldComponentUpdate，它会在重新渲染之前被触发。默认的实现是函数返回true，留下React来执行更新：
```

shouldComponentUpdate(nextProps, nextState) {
  return true;
}

```

如果你确切的知道某些情况你的组件不需要更新，那就在shouldComponentUpdate中返回false，这样就能跳过重新渲染的过程，包括组件中子组件的的渲染。
在多数时候，替代手动重写shouldComponentUpdate()的方法是吧组件继承自React.PureComponent。这和你实现一个“浅比较”（比较值）的shouldComponentUpdate()方法是一样的。

#### 实践shouldComponentUpdate 

这有一个组件树。每一个节点中，SCU表示shouldComponentUpdate ()方法返回的值，而vDOMEq 表示React元素是否相等。节点的颜色表示组件是不是真的被核对判定更新了。

![组件树](https://reactjs.org/static/should-component-update-5ee1bdf4779af06072a17b7a0654f6db-9a3ff.png)

因为C2节点shouldComponentUpdate 返回false，React不会去重新渲染C2,因此也不会调用C4和C5的shouldComponentUpdate 方法。

对于C1和C3来说，shouldComponentUpdate 返回true，所以React必须继续到他们的叶子节点去判断。对于C6因为React元素确实不相等，所以shouldComponentUpdate 返回true而将被更新。

下面注意C8。React不得不渲染该组件，但是因为元素判定返回的结果是与之前状态是相同的，那么它就不需要更新DOM了。

注意React只能去改变C6的DOM，这是不可避免的。对应C8，因为比较结果相同，C8逃过一劫（不用更新），而C2的子树以及C7，他们不用比较，直接在shouldComponentUpdate方法中就决定不用被更新了。

#### 例子

如果你的组件每一次变化都只是props.color和state.count两个变量引起的，你最好使用shouldComponentUpdate来检查他们：

```

class CounterButton extends React.Component {
  constructor(props) {
    super(props);
    this.state = {count: 1};
  }

  shouldComponentUpdate(nextProps, nextState) {
    if (this.props.color !== nextProps.color) {
      return true;
    }
    if (this.state.count !== nextState.count) {
      return true;
    }
    return false;
  }

  render() {
    return (
      <button
        color={this.props.color}
        onClick={() => this.setState(state => ({count: state.count + 1}))}>
        Count: {this.state.count}
      </button>
    );
  }
}

```

这段代码中，shouldComponentUpdate 只是检查了props.color和state.count的变化。如果所有的值不变，组件不更新。如果组件更加复杂化，你可以使用类似的方式来达成这种关于所有的state和props的“浅比较”来决定是否组件将要被更新。这种模式很常用，所以React提供了一个辅助类来完成这个逻辑--只要继承这个类React.PureComponent。所以，达到相同的目的，代码有了更加简化的方式：

```
class CounterButton extends React.PureComponent {
  constructor(props) {
    super(props);
    this.state = {count: 1};
  }

  render() {
    return (
      <button
        color={this.props.color}
        onClick={() => this.setState(state => ({count: state.count + 1}))}>
        Count: {this.state.count}
      </button>
    );
  }
}

```

大多数时候，React.PureComponent可以代替重写shouldComponentUpdate。它其实也只是做了“浅比较”，所以当你的props和state以一种浅比较无法察觉的变化改变时，这是不可用的。

这对于更加复杂的数据结构是一个问题。比如说，组件ListOfWords渲染一个单词和逗号的列表，他的父组件是WordAdder组件，父组件提供一个按钮，来向列表中添加一个单词。下面的代码无法实现这样的需求：

```

class ListOfWords extends React.PureComponent {
  render() {
    return <div>{this.props.words.join(',')}</div>;
  }
}

class WordAdder extends React.Component {
  constructor(props) {
    super(props);
    this.state = {
      words: ['marklar']
    };
    this.handleClick = this.handleClick.bind(this);
  }

  handleClick() {
    // This section is bad style and causes a bug
    const words = this.state.words;
    words.push('marklar');
    this.setState({words: words});
  }

  render() {
    return (
      <div>
        <button onClick={this.handleClick} />
        <ListOfWords words={this.state.words} />
      </div>
    );
  }
}

```

问题就在于PureComponent只是简单的做了新老this.props.words的值（引用）。在父组件的点击事件处理函数中，代码的确是改变了words列表，但尽管实际的words发生了变化，（浅比较后）新this.props.words的值还是相等于旧的（引用未变）。因此，本该因为插入新的单词而被重新渲染的实际并没有更新。

#### Not Mutating Data（非不可变数据）的力量

避免这种问题的最简单方法是避免让state或者props引用不可变值的。比如说，上述handleClick方法用这种方法重写：

```

handleClick() {
  this.setState(prevState => ({
    words: prevState.words.concat(['marklar'])
  }));
}


// 注意
// js中concat用于连接数组，返回的是新数组

```

ES6新特性中，数组展开语法更简单。你若是使用脚手架creat-react-app的话，这种语法就是默认支持的：
```
handleClick() {
  this.setState(prevState => ({
    words: [...prevState.words, 'marklar'],
  }));
};

```

相似地，你也可以用代码改变对象引用来避免不可变引用。比如说，有一个对象名叫colormap,我们希望一个写个函数，将colormap.right的值变为blue。我们这样写：

```
function updateColorMap(colormap) {
  colormap.right = 'blue';
}

```

这样子并没改变原始对象，我们可以用Object.assign方法：

```
function updateColorMap(colormap) {
  return Object.assign({}, colormap, {right: 'blue'});
}

```

现在这个函数返回的就是一个新对象了。Object.assign是ES6语法，需要“打腻子”（polyfill）。

对象展开属性是更简单的js不可变对象更新方法。


```
function updateColorMap(colormap) {
  return {...colormap, right: 'blue'};
}
```

你要是用Create React App脚手架的话，Object.assign和对象展开语法都是默认可用的。





#### 使用不可变数据结构

[Immutable.js ](https://github.com/facebook/immutable-js)是另一种解决方法。它能通过结构共享实现，提供不可变，持久的集合： 

* 不可变性：集合一旦被创建，就无法在另一刻改变
* 持久性：新的集合可以来自老的集合中并且就像直接设置值那样突变。新集合产生后，旧的集合是可用的。
* 结构化共享：创建的新集合会尽可能多的使用老的集合相同部分的结构，最小程度地减少拷贝操作来提高性能。

所谓不可变，把追踪变化变得简单。每次改变都会导致一个新的对象产生，所以我们只需要检查引用是不是变了就好。举例，常规的js代码中：

```
const x = { foo: 'bar' };
const y = x;
y.foo = 'baz';
x === y; // true

```

即使y被重新编辑了，可因为引用没变，还是和x引用相同的对象，那么比较之后还是true。相似的代码你用immutable.js：

```
const SomeRecord = Immutable.Record({ foo: null });
const x = new SomeRecord({ foo: 'bar' });
const y = x.set('foo', 'baz');
const z = x.set('foo', 'bar');
x === y; // false
x === z; // true

```

此例中，因为改变x的时候返回了一个新的引用，我们用引用判断（x === y）来检查y中的新值是不同于原始在x中的。

另外两个库也能帮助我们实现不可变数据：seamless-immutable和immutability-helper。

不可变数据提供了一种更简单的方式来追踪对象的改变，这正是我们实现 shouldComponentUpdate 所需要的。这将会很好的促进性能提升。












[官网文章 Advanced Guides :Optimizing Performance](https://reactjs.org/docs/optimizing-performance.html)


