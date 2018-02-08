---
layout: post
title:  体验脚手架 create-react-app 
date:   2018-2-1
categories: Developing_React
tags: [Developing_React]
---
<big>create-react-app</big> 是facebook官方开发的脚手架，自己配置webpack太麻烦了还容易出差错，所以，还是有必要学习下这个脚手架来提高下效率的。

#### 新建和启动

首先确认已经全局安装了create-react-app(命令是:cnpm i -g create-react-app)。

**相关终端命令**

{% highlight javascript %}

$ create-react-app 项目名

$ cd 项目名

$ cnpm start

{% endhighlight %}

脚手架启动项目的默认端口是3000.so，访问应该是http://localhost:3000。另外，脚手架还有热加载功能。

#### 简单看看

{% highlight javascript %}
 
 my-app
│
├── README.md
├── node_modules
├── package.json
├── .gitignore
│
├── public
│   └── favicon.ico
│   └── index.html
│   └── manifest.json
│
└── src
    └── App.css
    └── App.js
    └── App.test.js
    └── index.css
    └── index.js
    └── logo.svg
    └── registerServiceWorker.js

{% endhighlight %}

create-react-app完成后，项目的结构就是上面这样，/src/下面放源文件，public下面放置公共资源，node_modules是第三方库。

另外，在**package.json**中，会发现有一个叫'react-script'的东西，比方说，'npm start'命令其实对应着'react-script start'的命令。这个react-script其实就是facebook对常用工具的封装，封装几乎所有常用的配置，那么要是想修改这些配置怎么办呢？

>The tradeoff is that these tools are preconfigured to work in a specific way. If your project needs more customization, you can "eject" and customize it, but then you will need to maintain this configuration.

使用eject命令(弹出封装配置)($ cnpm run eject)

运行并确认弹出之后这下发现，果然package.json从几行配置变成了一大堆，领完还多了两个目录：config和scripts，这里面就包括了webpack.config.js,react-script。这里面的配置很复杂，通常不用更改。

[create-react-app在GitHub上的官方地址](https://github.com/facebook/create-react-app#creating-an-app)


__路漫漫其修远兮，吾将上下而求索__

