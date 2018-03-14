---
layout: post
title:  MongoDB-Mac-Windows下安装
date:   2018-2-17
categories: node.js_learnning
tags: [node.js]
comment: true
---

之前在Mac上安装mongodb总体来说问题不大，就是遇到了这么一个小问题
[解决安装mongodb问题的文章](https://www.cnblogs.com/timtike/p/6629764.html)
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

#### windows 下安装mongodb

1. 下载安装包

2. 自定义安装选择安装位置，这里有个选择是mongo compass，这是一个可视化的工具，可装可不装，如果选择安装了就比较慢，30分钟等待额正常，选择不安装的话很快

3. 创建配置文件
在某路径下建立mongo.conf
```
dbpath=D:\Program Files\MongoDB\Server\3.2\data #数据库路径  
logpath=D:\Program Files\MongoDB\Server\3.2\logs\mongo.log #日志输出文件路径  
logappend=true #错误日志采用追加模式  
journal=true #启用日志文件，默认启用  
quiet=true #这个选项可以过滤掉一些无用的日志信息，若需要调试使用请设置为false  
port=27017 #端口号 默认为27017  

```
运行> mongod --config D:\Program Files\MongoDB\Server\3.2\mongo.conf

后在浏览器中http://localhost:27017，表示已经启动

4. 变成系统服务

进入mongo安装根目录下\Server\3.2\bin（依安装目录而定）目录下：
```
mongod --config D:\software\Mongodb\mongo.config --install --serviceName "MongoDB"

```
接着，cmd中输入：services.msc可查看安装的服务MongoDB的状态。

参考：
[Windows下安装MongoDB](http://blog.csdn.net/heshushun/article/details/77776706)
[解决安装mongodb问题的文章](https://www.cnblogs.com/timtike/p/6629764.html)
#### Mac(OSX)安装mongodb 以及 遇到的一个问题

__宝剑锋从磨砺出,梅花香自苦寒来__
