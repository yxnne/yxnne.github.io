---
layout: post
title:  node.js——初见
date:   2017-11-27
categories: node.js_learnning
tags: [node.js]
---
<big>Node.js</big>是基于Chrome JavaScript运行时建立的一个平台，实际上它是对Google Chrome V8引擎进行了封装，它主要用于创建快速的、可扩展的网络应用。Node.js采用事件驱动和非阻塞IO模型，使其变得轻微和高效，非常适合构建运行在分布式设备的数据密集型实时应用。

JS解析器只是JS代码运行的一种环境，浏览器是JS运行的一种环境，浏览器为JS提供了操作DOM对象和window对象等接口。Node.js也是JS的一种运行环境，node.js为JS提供操作文件、创建http服务、创建TCP、UDP服务等接口，所以Node.js可以完成其他后台语言能完成的工作。

#### 简单点：
* Node.js 就是运行在服务端的 JavaScript。

既然是node.js是服务器端运行的js代码，那么初窥node.js最好就是写一个服务端小程序了：

{% highlight ruby %}

//import module using require() 
//instance 'http object'
var http = require("http");
//to create a server and listen port:8888
http.createServer(function(request ,response){

	//send Http Head : State :200 content type :text/plain 
	response.writeHead(200, {'Content-Type' : 'text/plain'});
	//send response 
	response.end('Hello World\n');


}).listen(8888);

// out put in the console 
console.log("the server has started");

{% endhighlight %}


* require( )是node.js自带的函数，用来载入模块，这里载入了这个模块，把它实例化给了引用http.

* createServer( )是http对象的函数(方法)，顾名思义，创造一个服务。该函数的参数是一个函数，表示服务的行为；listen( )是http对象的另一个方法，用来监听端口。

最后：

[感谢菜鸟教程](http://www.runoob.com/nodejs/nodejs-http-server.html)
