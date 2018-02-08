---
layout: post
title:  Express:一个Http Server框架
date:   2017-11-27
categories: node.js_learnning
tags: [node.js]
comment: true
---
<big>Express</big> ,是一个简洁而灵活的 node.js Web应用框架, 提供了一系列强大特性帮助你创建各种 Web 应用，和丰富的 HTTP 工具。

使用 Express 可以快速地搭建一个完整功能的网站。

Express 框架核心特性：

* 可以设置中间件来响应 HTTP 请求。

* 定义了路由表用于执行不同的 HTTP 请求动作。

* 可以通过向模板传递参数来动态渲染 HTML 页面。

以上学习自菜鸟教程。官方自称的是：基于 Node.js 平台，快速、开放、极简的 web 开发框架。

#### 最基本的几行使用

安装没什么好说的，就是npm安装到项目中(cnpm i --save express) , 接着就可以使用了(项目目录下新建server.js文件):


{% highlight javascript %}

// 引入库
const express = require('express');

// 新建app
const app = express();
// 监听,就是
app.listen(9090, function(){
  console.log("node js express has been started");
});

// 根目录，直接发送html
app.get('/', (require, response) =>{
  // 用response对象
  response.send('<h1>Hello World ~~ in Express</h1>');
});

// '/json'
app.get('/json', (require, response) =>{
  // 用response对象
  response.json({
    type:"test",
    name:"express"
  });
});

{% endhighlight %}

([Express官网](http://www.expressjs.com.cn/))上有很详细的指导，鉴于此，我这里文章的最大意义就是当前这句话。
运行这段代码就是 $ node server.js或者使用nodemon工具(也是需要安装的 : npm全局安装)来实现热加载($ nodemon server.js)

#### Express通过mongoose配合操作mongodb

mongoose是基于nodejs平台来操作数据库的工具，引入的方法依然是是基于npm($ cnpm i --save mongoose),那么在server.js中继续：

{% highlight javascript %}

// 引入
const mongoose = require('mongoose');
// 设置URL （ test要是没有会新建,test是集合 ）
const DB_URL = 'mongodb://127.0.0.1:27017/test'; 
// 连接
mongoose.connection.on('connected', function(){
  console.log("mongodb has connected and I noticed it ~~");
  console.log("mongoose is helpful ~~~~");
});

{% endhighlight %}

mongodb不是关系型数据库，所以他并没有表等概念，对应的概念是文档模型：

{% highlight javascript %}

// 类似于建立 MySQL 的表,文档模型
const User = mongoose.model('user', new mongoose.Schema({
  name:{type:String, require:true},
  age:{type:Number,require:true}
}));

{% endhighlight %}

使用mongoose进行增删改查：

* 增--新建数据：

{% highlight javascript %}

// 新建数据
User.create({
  name:"ppp",
  age:5
},(err, doc)=>{
  if (!err){
    console.log(doc);
  }else{
    console.log(err);
  }
});

{% endhighlight %}

* 删--删除按条件：

{% highlight javascript %}

// 删除age是18的对象
User.remove({age:18},(e, d)=>{
  console.log(d);
});

{% endhighlight %}

* 改--按条件修改：

{% highlight javascript %}

// 更新名字是jerk的
User.update({name:"jerk"}, {'$set':{age:77}}, (err, doc)=>{
  console.log(doc);
});

{% endhighlight %}

* 查--按条件查询：

{% highlight javascript %}

	//不提供条件过滤就是全部
  User.find({},(err, doc)=>{
    response.json(doc);
  });

{% endhighlight %}

可以看到，最后一个参数总是一个回调函数(箭头函数),err是操作中的错误，而doc是数据库的返回结果。

#### Mac(OSX)安装mongodb 以及 遇到的一个问题

OSX中用Terminal安装mongodb使用brew工具：

{% highlight javascript %}

$ brew install monogoab

{% endhighlight %}

经过了漫长的等待后，总算安装完了。

{% highlight javascript %}

To have launchd start mongodb now and restart at login:
  brew services start mongodb
Or, if you don't want/need a background service you can just run:
  mongod --config /usr/local/etc/mongod.conf

{% endhighlight %}

这时候我的电脑运行 $ mongod --config /usr/local/etc/mongod.conf并没有反应，卡主了。

baidu后 someone said，可能是没有/data/db目录 新建这个目录试试，然后再启动服务 $ brew services start mongodb

然后接着运行：
$ mongod --config /usr/local/etc/mongod.conf 瞬间就完成了，没有卡主
运行：
$ mongo 一串提示后看见提示符'>' ,提示如下，其中重要的有版本信息和url

{% highlight javascript %}

MongoDB shell version v3.6.2
connecting to: mongodb://127.0.0.1:27017
MongoDB server version: 3.6.2
Server has startup warnings: 
2018-02-02T14:46:24.526+0800 I CONTROL  [initandlisten] 
2018-02-02T14:46:24.526+0800 I CONTROL  [initandlisten] ** WARNING: Access control is not enabled for the database.
2018-02-02T14:46:24.526+0800 I CONTROL  [initandlisten] **          Read and write access to data and configuration is unrestricted.
2018-02-02T14:46:24.526+0800 I CONTROL  [initandlisten]

{% endhighlight %}

最后：

这是一个新的开始，我会真正成为 Real Full Stack 

参考:

[解决安装mongodb问题的文章](https://www.cnblogs.com/timtike/p/6629764.html)

[菜鸟教程：mongodb](http://www.runoob.com/mongodb/mongodb-osx-install.html)

[菜鸟教程：nodejs express](http://www.runoob.com/nodejs/nodejs-express-framework.html)

[express官网](http://www.expressjs.com.cn/)

[mongodb官网](https://www.mongodb.com)

[GitHub:nodemon](https://github.com/remy/nodemon)

[GitHub:mongoose](https://github.com/Automattic/mongoose)
