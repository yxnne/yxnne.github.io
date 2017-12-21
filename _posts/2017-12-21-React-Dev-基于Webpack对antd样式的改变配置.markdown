---
layout: post
title:  React + Webpack 环境搭建 简记
date:   2017-12-9
categories: Developing_React
tags: [Developing_React,antd]
---
<big>antd</big>是我最开始接触react开发时候的入门组件库。也是蚂蚁金服的设计规范，总体来说包含了日常使用的大多数场景所以显得格外好用。

现在，需要改变antd的theme，比如，我不喜欢支付宝蓝。

antd官方网址给的介绍有点简练，对于我一个非专业前端新人来讲，门槛又高了。

[antd官方网址给的关于theme的介绍](https://ant.design/docs/react/customize-theme-cn)

不过大致就是说，看上去只要在package.json中加一个theme属性这么简单的方法，只有在使用antd的脚手架的时候才奏效：antd-init 和 dva-cli 。如果用的正式这样的脚手架，那么的确非常简单。

那么问题在于，假如我没有用这个脚手架呢？

antd的官网上也有提供思路：如果你使用的是其他脚手架，可以参考 [atool-build 中 less-loader 的 webpack 相关配置](https://github.com/ant-tool/atool-build/blob/a4b3e3eec4ffc09b0e2352d7f9d279c4c28fdb99/src/getWebpackCommonConfig.js#L131-L138) ，**利用 less-loader 的 modifyVars 配置来覆盖原来的样式变量**。

于我来说真可谓言简意赅，惜字如金，遂带着问题我咨询了一位阿里系的前辈，他给我推荐了这篇文章：[一篇讲的不错的墙外文章](https://medium.com/@GeoffMiller/how-to-customize-ant-design-with-react-webpack-the-missing-guide-c6430f2db10f)

以下我的笔记也几乎来自于此文章。

#### 第一步：依从安装

* antd这就不说了

{% highlight ruby %}

$ npm install antd --save

{% endhighlight %}

* babel-plugin-import

[作用](https://www.cnblogs.com/yswz/p/7165031.html):按需加载组件

{% highlight ruby %}

$ npm install babel-plugin-import --save-dev

{% endhighlight %}

* less-vars-to-js

less-vars-to-js将less文件视作String，以对象形式返回其中的变量。

{% highlight ruby %}

$ npm install babel-plugin-import --save-dev

{% endhighlight %}

* 安装less / less loader / css-loader / style-loader 等支持，缺啥就安啥

{% highlight ruby %}

$ npm install less less-loader css-loader style-loader --save-dev

{% endhighlight %}

#### 配置webpack.config.js > babel-loader中装插件

{% highlight ruby %}

      {
        test: /.js?$/,
        exclude: /(node_modules)/,
        loader: 'babel-loader',
        query: {
          presets: ['react', 'babel-preset-react'],
          // this add here 
          plugins: [
            ['import', {
              libraryName: "antd",
              style: true
            }]
          ]
          // this add here end

        }
      },

{% endhighlight %}

#### 新建一个.less文件配置样式

在根目录下新建一个.js文件来配置主题，比如theme_config.less,在里面改变颜色，很low的红色：

可用的theme设置在这里：[antd theme官方](https://github.com/ant-design/ant-design/blob/master/components/style/themes/default.less)

{% highlight ruby %}

@primary-color: #ff0000; 

{% endhighlight %}

#### 配置webpack.config.js > less-loader配置

* 把刚才配的theme引入过来

{% highlight ruby %}

const path = require('path');

const fs  = require('fs');

const lessToJs = require('less-vars-to-js');

const themeVariables = lessToJs(fs.readFileSync(path.join(__dirname, './theme_config.less'), 'utf8'));

{% endhighlight %}

* 配置各个样式loader

{% highlight ruby %}
      
      {
        test: /\.css$/,
        loader: 'style-loader!css-loader'
      },

      {
        //css模块化设置
        test: /\.less$/,
        //ant-design样式需要这样
        use: [{
            loader: "style-loader"
          },
          {
            loader: "css-loader"
          },
          {
            loader: "less-loader",
            options: {

              modifyVars: themeVariables
            }
          }
        ]
      },

{% endhighlight %}

这几步下来基本就ok啦。

**总结下**：

基本就是：

1.给babel-loader装插件{libraryName: "antd",style: true};

2.新建一个less文件配置样式并引入到webpack.config.jsz中。

3.配置各种less-loader style-loader css-loader等。然后用之前引入的样式，通过less-loader覆盖原有样式。

#### PS : 可用的一个webpack.config.js参考下，以免日后忘记

{% highlight ruby %}

const webpack = require('webpack');
const path = require('path');

const fs = require('fs');

const lessToJs = require('less-vars-to-js');
const themeVariables = lessToJs(fs.readFileSync(path.join(__dirname, './theme.less'), 'utf8'));
module.exports = {
  context: __dirname + '/src',
  entry: "./js/root.js",
  module: {

    loaders: [{
        test: /.js?$/,
        exclude: /(node_modules)/,
        loader: 'babel-loader',
        query: {
          presets: ['react', 'babel-preset-react'],
          plugins: [
            ['import', {
              libraryName: "antd",
              style: true
            }]
          ]
        }
      },
      {
        test: /\.css$/,
        loader: 'style-loader!css-loader'
      },

      {
        //css模块化设置
        test: /\.less$/,
        //ant-design样式需要这样
        use: [{
            loader: "style-loader"
          },
          {
            loader: "css-loader"
          },
          {
            loader: "less-loader",
            options: {
              modifyVars: themeVariables
            }
          }
        ]
      },
    ]
  },
  output: {
    path: __dirname + "/src/",
    filename: "bundle.js"
  },
  devServer: {
    //我们在这里对webpack-dev-server进行配置
    port: 18080,
    historyApiFallback: true
  }
};


{% endhighlight %}

__路漫漫其修远兮，吾将上下而求索__

