---
layout: post
title:  React + Webpack 环境搭建 简记
date:   2017-12-9
categories: Developing_React
tags: [Developing_React]
---
<big>Webpack</big> 可以对React项目打包编译并热加载（就是开发中，每逢保存就自动编译和部署），所以基本开发比配了。
(PS:想开发React，门槛好像还是有点高，作为一个不是搞前端的非专业人员，面对一大堆名词还真一些字无从下手了)

#### 安装速查 (Windows和OSX一样)

**相关终端命令**

{% highlight ruby %}

$ cd 项目根目录

//1.初始化项目，生成package.json
$ npm init

//React部分

//2.安装 react 和 react-dom
//'--save'是安装到本项目，-g是全局安装
//可以不用全局安装
$ npm install --g react react-dom
$ npm install --save react react-dom

//3.安装js环境语法支持(es6和JSX支持等）
//babel-preset-react (cli:command-line interface)
$ npm install --save-dev babel-cli babel-preset-react
//babel-preset-env
npm install babel-preset-env --save-dev
$ npm install
//babel-loader
$ npm install babel-loader

//webpack部分

//4.webpack
$ npm install --save webpack

//5.webpack-dev-server
$ npm install --save webpack-dev-server

{% endhighlight %}

**一个可用的webpack.config.js**

{% highlight ruby %}

const webpack = require('webpack');
const path = require('path');

module.exports = {
  context : __dirname + '/src',
  entry   : "./js/index.js",
  module  : {
    loaders   :[{
      test : /.js?$/,
      exclude:/(node_modules)/,
      loader: 'babel-loader',
      query :{
        presets:['react' , 'babel-preset-react']
      }
    }]
  },
  output:{
    path:__dirname + "/src/",
    filename:"bundle.js"
  },
  devServer:{
    //我们在这里对webpack-dev-server进行配置
    port:18080
  }
};

{% endhighlight %}

**webpack命令**

{% highlight ruby %}

//普通打包
$ webpack

//每逢保存时自动打包，还是要刷新页面
$ webpack --watch

//热加载+服务器 指定根目录
$ webpack-dev-server --content-base src --hot

{% endhighlight %}

#### 简笔记

* React和相关的支持是根据[官网：Installation](https://reactjs.org/docs/installation.html)所指导的操作的。

* webpack安装好了之后需要在项目的根目录中新建一个文件:Webpack.config.js

* 在webpack.config.js中：'entry'指定了入口;'output'是说把打包好的文件输出的位置(上面的例子就是src/bundle.js。所以.html需要引用js的话要引用src/bundle.js);'devServer'是设置服务器的。

#### 环境搭建好，HelloWord？

新建src/index.html

{% highlight ruby %}

<div id="example">123</div>
<script type="text/javascript" src="bundle.js">
</script>

{% endhighlight %}

新建src/js/index.js,HelloWord Demo

{% highlight ruby %}

import React from 'react';
import ReactDOM from 'react-dom';

ReactDOM.render(
   <h1>Hello, world hi my cat ~~~~~!!!!!</h1>,
   document.getElementById('example')
 );

{% endhighlight %}



__路漫漫其修远兮，吾将上下而求索__

