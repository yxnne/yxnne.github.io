---
layout: post
title:  2018狗年过年写个小项目yebChat
date:   2018-2-10
categories: Personal
comment: true
---
### 腊月廿四
项目计划采用React技术栈，前端大约是React + ReactRouter4 + Redux + antd-mobile + axios。
后台计划使用nodejs的Express + mongoDB。
开发环境: Atom / node8 / OSX  

#### 创建项目和安装必要依赖

{% highlight javascript %}

// 创建项目
$ create-react-app ./

// cnpm run eject

// 前端安装
$ cnpm i --save react-router-dom
$ cnpm i --save redux
$ cnpm i --save react-redux
$ cnpm i --save redux-thunk
$ cnpm i --save babel-plugin-transform-decorators-legacy
$ cnpm i --save axios
$ cnpm i --save antd-mobile

// 后端安装
$ cnpm i --save express
$ cnpm i --save mongoose
$ cnpm i --save cookie-parser

{% endhighlight %}

#### 更改配置

{% highlight javascript %}

// package.json中增加

"babel": {
  // 添加以下支持redux装饰器依赖注入
  "plugins": [
    "transform-decorators-legacy"
  ]
},

// 添加以下使axios可跨域
"proxy":"http://localhost:9090"

{% endhighlight %}





临时备用线----------------------------------------------

{% highlight javascript %}

{% endhighlight %}

