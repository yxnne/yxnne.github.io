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

__宝剑锋从磨砺出,梅花香自苦寒来__
