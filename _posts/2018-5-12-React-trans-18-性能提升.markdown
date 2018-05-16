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

#### 可视的长列表















```

```


#### PropTypes



[官网文章 Advanced Guides :Optimizing Performance](https://reactjs.org/docs/optimizing-performance.html)


