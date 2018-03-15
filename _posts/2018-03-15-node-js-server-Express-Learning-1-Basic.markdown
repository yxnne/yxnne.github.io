---
layout: post
title:  Express系统学习1：入门笔记
date:   2018-03-15
categories: node.js_learnning
tags: [node.js]
comment: true
---
<big>Express</big> ,是一个简洁而灵活的 node.js Web应用框架, 提供了一系列强大特性帮助你创建各种 Web 应用，和丰富的 HTTP 工具。

#### Express中路由和中间件的顺序很重要

下面这个例子中，访问localhost：3000后就是404的的文本，这是按照顺序来的，它一上来就遇到了404，'匹配'成功...so,404和500应该放在路由的后面。PS：text/plain是纯文本。

```
var express = require('express');

var app = express();

// 设置端口
app.set('port', process.env.PORT || 3000);

// 定制404页面
app.use(function(req, res){
  res.type('text/plain');
  res.status(404);
  res.send('404 - Not Found');
});

// 定制500页面
app.use(function(err, req, res, next){
  console.error(err.stack);
  res.type('text/plain');
  res.status(500);
  res.send('500 - Server Error');
});

app.listen(app.get('port'), function(){
  console.log('jurneyNode Server has been started on Port:' + app.get('port')); 
});

```
那么上述代码中添加两个路由'/'和'/about'.
```
app.get('/',function(req, res){
  res.type('text/plain');
  res.send('OK,This is Root');
});

app.get('/about',function(req, res){
  res.type('text/plain');
  res.send('About Me');
});

```
如果想按照期待的那样对'/'和'/about'进行拦截处理，那么就一定要把新加上的代码放在404和500之前。

#### 了解下handlebars,express的视图渲染引擎
简单的说，视图渲染就是，在服务端，通过拼装视图层（HTML）模板，向浏览器端直接发送完整的HTML代码。这和现在流行的WebApp可不一样，WebApp是将除去数据在内的所有东西都下载到浏览器端，再由浏览器端向后台请求数据，在前端渲染数据。

npm install --save express-handlebars之后，在所有路由前面先添加express-handlebars，如下面代码：

```
// 添加 handlebars 视图引擎
var handlebars = require('express-handlebars').create({defaultLayout:'main'});
app.engine('handlebars', handlebars.engine);
app.set('view engine', 'handlebars');

```

新建项目根目录/views/layouts/main.handlebars文件作为视图模板：
```
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>Jurney Node</title>
</head>
<body>

  三个大括号body三个大括号
</body>
</html>

```
这里的body就是被替换渲染的地方，假设有/views/home.handlebars文件，里面放置任意的HTML代码，比如一个h1标签，那么，
修改app.get中代码：

```
app.get('/',function(req, res){
  // res.type('text/plain');
  // res.send('OK,This is Root');
  res.render('home');
});

```
这里res不用设置type,handlebar会默认将type设置为text/html，res.render('home')，就是将home.handlebars的内容渲染到main.handlebars中的三个大括号包裹的body中。

上述算是静态渲染。<br>

另外，模板中两个大括号包裹起来的变量可以动态渲染。就是在server中动态给它设置。假设模板中有一个待渲染的值叫randomName，那么在server端渲染，调用render函数时多传一个对象：
```
app.get('/about',function(req, res){

  // 测试handlebars渲染动态值
  const names = ['yxnne', 'ax', 'jerkins', 'yellowman', 'yaplob'];
  var name = names[Math.floor( Math.random() * names.length)];
  res.render('about', {randomName:name});
});

```

#### Express中静态资源的中间件

```
// 添加静态文件资源处理中间件
app.use(express.static(__dirname + '/public' ));

```
这是将根目录下的/public文件夹视作静态资源文件目录 ，比如模板中有img标签，他的 src="/img/logo.png" ，这里的实际路径应该是/public/img/logo.png，但是，src引用没有/public,这里要注意了~~~

__宝剑锋从磨砺出,梅花香自苦寒来__
