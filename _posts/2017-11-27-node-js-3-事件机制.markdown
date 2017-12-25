---
layout: post
title:  node.js——事件机制
date:   2017-11-27
categories: node.js_learnning
tags: [node.js]
---
<big>非阻塞IO</big> , Node.js也是基于轮询队列和回调完成事件处理的。很显然，这是观察者模式的应用。

关于这部分功能的核心对象是eventEmitter（事件发生器）。

#### 基本用法

* 引入模块
{% highlight ruby %}

//add events module  
var events = require('events');

{% endhighlight %}

* 绑定事件（监听某个事件）
{% highlight ruby %}

//listen somewhat event and ,
//add the callback function when this event is triggered 
eventEmitter.on('eventName', eventHandler);

{% endhighlight %}

* 触发事件
{% highlight ruby %}

eventEmitter.emit('eventName');

{% endhighlight %}

#### 继续，简单探索

{% highlight ruby %}

//add events
var events = require('events');
//create a eventEmitter object
var eventEmitter = new events.EventEmitter();

//set listen and callback
eventEmitter.on('yxnne_event',function(){
	console.log('my own event triggered');
});
//trigger the events after three sec.
setTimeout(function(){
	eventEmitter.emit('yxnne_event');
},3000);

{% endhighlight %}

* 一种事件可以注册多个监听
在上面代码的基础上

{% highlight ruby %}

//another register to listen 
eventEmitter.on('yxnne_event',function(){
	console.log('my own event triggered in another function');
});

{% endhighlight %}
这样因为在触发“yxnne_event”之前就注册了对这个事件的两个监听，所以当事件触发的时候，按注册监听的先后顺序进行回调

* 带参数的触发
在上面代码的基础上

{% highlight ruby %}

setTimeout(function(){
	eventEmitter.emit('yxnne_event',10,20);
},3000);

{% endhighlight %}

我本来以为，注册事件“yxnne_event”的时候给他的回调函数里面没有参数列表，所以这个触发可能不会执行，但是我错了。这里还是会执行的回调的。参数列表这个东西，有，则用，无，则不用（对js可能还是不够熟悉）。
那么，再验证下？

{% highlight ruby %}

//listen event :yxnne_event_param and add callback
eventEmitter.on('yxnne_event_param',function(num1,num2){
	var res = num1 + num2;
	if(res){

		var info = '--> ' + num1 + ' and ' + num2 + ' is ' + res  ;
		console.log(info);
	}else{
		console.log('*** yxnne error info : maybe the param is not enough ???');
		//this is  the eventEmitter obj who emitter this event 
		console.log(this);
	}
});
//I think it's right
setTimeout(function(){
	eventEmitter.emit('yxnne_event_param',10,20);
},5000);
//forecast : may not ok
setTimeout(function(){
	eventEmitter.emit('yxnne_event_param','jerk',20);
},5000);
//forecast : unnormal
setTimeout(function(){
	eventEmitter.emit('yxnne_event_param',20);
},5000);


{% endhighlight %}

结果是：

>--> 10 and 20 is 30 <br>
--> jerk and 20 is jerk20 <br>
*** yxnne error info : maybe the param is not enough ???

* 一个eventEmitter监听的对象用另一个eventEmitter触发？

{% highlight ruby %}

setTimeout(function(){
	//also trigger yxnne_event_param event
	var e = new events.EventEmitter();
	e.emit('yxnne_event_param',100,20);
},6000);

{% endhighlight %}

结果是，某一个eventEmitter对象只能触发他自己监听的事件，这个结论吧，也算合理。

* 一个静态方法listenerCount()

{% highlight ruby %}

var counts = events.EventEmitter.listenerCount(eventEmitter,'yxnne_event_param');
console.log('the yxnne_event_param event listeners counts is',counts);

{% endhighlight %}

这个静态方法是统计某一个emitter对象监听某一个事件的数量。这很明显，暗示这监听和触发是和对象相关的，我要是早点知道这个方法，也行上一条就不用所谓“探索”了。
不过自己探索过了也不是个坏事，对吧~

最后：

[我要继续感谢菜鸟教程](http://www.runoob.com/nodejs/nodejs-event-loop.html)
