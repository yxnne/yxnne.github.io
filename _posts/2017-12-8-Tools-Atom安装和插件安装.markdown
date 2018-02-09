---
layout: post
title:  Atom的安装/部分插件安装
date:   2017-12-8
categories: Tools
tags: [Tools]
---
<big>Atom</big> ，号称是21世纪可控制文本编辑器，Github搞出来的东西 [官网](https://atom.io)。Atom是一个使用HTML，JavaScript，CSS和Node.js集成构建的桌面应用程序。它运行在[Electron](https://electronjs.org/)上，这是一个使用Web技术构建跨平台应用程序的框架。

其特点诸如：

* 跨平台：Win/OSX/Linux
* 内置包管理器，直接搜索安装即可（依赖网络状况）
* 自动补全
* 文件浏览
* 多窗口编辑
* 查找和替换

Atom安装很简单，官网下载就好。

近期准备使用Atom写React.js项目。所以搜索了些资料，安装了些比较好用的插件：

#### 插件

##### __emmet__

emmet是插件一个效率化工具，从sublime上就一直追随至今。[github](https://github.com/emmetio/emmet)

##### __atom-ternjs__

JavaScript代码自动补全，代码分析支持是[Tern](https://github.com/ternjs/tern),支持ES5, ES6, ES7, Node.js, jQuery, Angular。[github](https://github.com/tststs/atom-ternjs)

##### __atom-beautify__

顾名思义，美化代码，格式化工具。[github](https://github.com/Glavin001/atom-beautify)

##### __open-in-browser__

添加直接在浏览器打开的功能。

##### __file-icons__

文件树的icon

##### __highlight-line__

行高亮

##### __highlight-selected__

选中高亮

##### __activate-power-mode__

炸裂的编辑特效 [github](https://atom.io/packages/activate-power-mode)

#### 插件安装的问题

本来插件安装时比较简单的,直接搜索安装就好了：
![在Atom中，打开setting](https://raw.githubusercontent.com/yxnne/yxnne.github.io/master/images/artical_using/20171208_atom_install.jpeg)

但是实际安装过程中效果不是很好，主要可能是网络问题吧，比如说atom-beautify这个插件，总是安装不成功，设置翻墙也没成。

后来搜索发现了一种方法是，直接从git上clone下来代码到atom安装在目录下的package目录下，clone成功后使用npm安装：

{% highlight javascript %}

$ cd atom
$ cd pacages
$ git clone "从geihub相关插件主页的url.git"
$ npm install

{% endhighlight %}

在windows下面，最后一步使用cnpm(淘宝镜像)成功了，npm太慢。**但是Mac下这个方法居然没有成功，安装完成之后Atom总是打开后没有响应！！！？？？不知为何啊..**

https://segmentfault.com/a/1190000009600026

http://blog.csdn.net/crper/article/details/52196675
