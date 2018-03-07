---
layout: post
title:  2018狗年过年写个小项目yebChat
date:   2018-2-10
categories: Developing_React
tags: [Developing_React]
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
$ cnpm i --save babel-plugin-import

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

### 腊月廿五
大吉大利，今晚回家，一会去机场！
简单测试了下antd-mobile,和antd差不多，都挺不错的。
* <a href="#my_antd_mobile">antd-mobile→</a>
* <a href="#my_project_structure">项目的架构是这样→</a>

另外开始处理登录/注册页面，今天只写了UI部分，估计下次写到后天了~~~</br>
回家还是最开心的~

### 腊月廿八
果然，回家就只能挤时间了，前两天拜访了媳妇家，今天下去坐在家里没事，赶紧走一波:
今天需要把登录注册页面做完。后台暂时用写死数据做。
#### 验证逻辑
设计一个不参与ui显示的组件，放置在路由的顶层，也就是说，一定会加载到这个组件，用这个组件向后端发请求来看用户信息(登录信息，角色)从而决定跳转页面。没有登录就跳转到登录页面，本来就是在登录注册页面除外。

#### 注册页面
注册页面将控件添加了事件响应 ，用onChange属性

{% highlight javascript %}

	<InputItem type="password" onChange={v => this.handleChange('pwd',v)}>密码</InputItem>
	
	<WhiteSpace />

	<InputItem type="password" onChange={v => this.handleChange('repeatPwd',v)}>确认密码</InputItem>
	<WhiteSpace />

	// 处理函数
	handleChange(key, value){
		this.setState({
			// 注意这个中括号
			[key]:value
		});
	}

{% endhighlight %}

这里setState这里注意[key]写法，就是按照key变量的值。如果不加中括号，那么state的键就是‘key’，加了中括号，key变量是什么值，这里键就是什么值。






























### 腊月廿九

























临时备用线----------------------------------------------

{% highlight javascript %}

	<InputItem type="password" onChange={v => this.handleChange('pwd',v)}>密码</InputItem>
	<WhiteSpace />
	<InputItem type="password" onChange={v => this.handleChange('repeatPwd',v)}>确认密码</InputItem>
	<WhiteSpace />

{% endhighlight %}


{% highlight javascript %}

{% endhighlight %}

### 项目架构
<a name="my_project_structure"></a>


### amtd-mobile总结
<a name="my_antd_mobile"></a>
[antd-mobile官网在此](https://mobile.ant.design/docs/react/introduce-cn)
#### 布局组件

* WhiteSpace 上下留白
* WingBlank 两翼留白

#### 表单组件
* List 列表容器
* Radio 单选
* InputItem 输入框
* Button 按钮




