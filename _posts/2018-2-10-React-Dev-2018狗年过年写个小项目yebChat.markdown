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
$ cnpm i --save react-router-dom		// react 路由
$ cnpm i --save redux								// redux	
$ cnpm i --save react-redux					// redux的react支持版
$ cnpm i --save redux-thunk       	// redux异步分发支持
$ cnpm i --save babel-plugin-transform-decorators-legacy		// 装饰器写法支持
$ cnpm i --save axios								// 网络请求
$ cnpm i --save antd-mobile					// antd mobile 样式
$ cnpm i --save babel-plugin-import	// 按需加载

// 后端安装
$ cnpm i --save express							// server框架
$ cnpm i --save mongoose						// mongo的链接库
$ cnpm i --save cookie-parser				// 解析cookie
$ cnpm i --save body-parser					// 解析body
$ cnpm i --save utility							// 工具集，例如md5

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
设计一个不参与ui显示的组件AuthRouter，放置在路由的顶层，也就是说，一定会加载到这个组件，用这个组件向后端发请求来看用户信息(登录信息，角色)从而决定跳转页面。没有登录就跳转到登录页面，本来就是在登录注册页面除外。

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

#### 关于user的redux添加
在src/redux/下面添加user.redux.js:
回顾下redux先：
一个相关.redux.js文件中应该有这么几个东西：
1.action creator:产生action；
2.reducer:根据action来改变state的；
3.对外暴露的其他方法或逻辑；
另外，项目引用react-redux，提供了Provider组件和connect来简化开发。

具体这里设计如下：

{% highlight javascript %}

// 用户的初始状态
const initState = {
  isAuth:false, // 是否登录过
  msg:'',				// 提示信息
  user:'',			// 用户
  pwd:'',				// 密码
  type:''       // l类别
};
// reducer
export function user(state=initState, action){
  switch (action.type) {
    case REGISTER_SUCCESS:
      return {...state, msg:'', isAuth:true, ...action.payload};
    case ERROR_MSG:
      return {...state, isAuth:false, msg:action.msg}
    default:
      return state;
  }
}

// Action Creator
function registerSuccess(data){
  return {type:REGISTER_SUCCESS, payload:data}
}

function errorMsg(msg){
  return {type:ERROR_MSG, msg:msg}
}

// 对外暴露
export function register({user, pwd, repeatPwd, type}, action){

  // 校验不通过情况
  if(!user || !pwd || !repeatPwd ){
    return errorMsg("信息输入不完整");
  }
  if(pwd !== repeatPwd){
    return errorMsg("密码不同于确认密码");
  }
  // 校验通过
  return dispatch => {
    axios.post('/user/register', { user, pwd, type })
    .then(res => {
      if (res.status == 200 && res.data.code === 0) {
        dispatch(registerSuccess({ user, pwd, type }));
      } else {
        dispatch(errorMsg(res.data.msg));
      }
    });
  }
}

{% endhighlight %}

上述register注入到组件被调用后，假设不成功，那么msg就不为空，那么下面就是现实msg的方法

{% highlight javascript %}

//render return中添加
{ this.props.msg ? <p className='error-msg'>{this.props.msg}</p> : null }

{% endhighlight %}

注意：这里是this.props.msg,不是this.state.msg是因为：connect就是将store中的必要数据作为props传递给React组件来render

#### 服务端注册功能

服务端的注册功能主要写在/server/user.js中，数据库模型的定义，建立等在/server/model.js中

注册这里是post请求上来数据，这里解析post的数据需要body-parser的中间件，当然以后涉及cookie也需要cookie-parser的中间件，都先给他cnpm install 了。

{% highlight javascript %}

Router.post('/register', function(req, rsp){
	console.log(req.body);
	
	// req.body 就是使用bodypaser解析出来的数据，上传时就是以json形式
	const { user, pwd, type } = req.body;
	
	// 拿到post上传信息后，先查询下，因为用户名需要唯一的
	User.findOne({user:user}, function(err, doc){
		// doc不为空那么就是说存在这个用户名
		if (doc) {
			return rsp.json({code:1, msg:'用户已经存在'});
		}
		// 查不到就是合法的
		User.create({user:user, pwd:pwd, type:type}, function(err, doc){
			if (err) {
				return rsp.json({code:1, msg:'后台错误'});
			}

			return rsp.json({code:0});
		});
	});
});

{% endhighlight %}
User.findOne()这里的逻辑是，从mongo中根据用户名，先查询下，如果查询结果不为空，那么本次注册不合法，因为不能用户名重复，返回错误信息。反之，则返回ok。

#### 密码入库前加密
常规策略就是：MD5，但是假设用户本来输入就很简单，加密后是可以暴力破解的（彩虹表），那么使用两层MD5再加盐的方法，可以有效防止彩虹表：

{% highlight javascript %}
const utility = require('utility');

// 工具方法，密文增加复杂度，放置彩虹表暴力破解
// 两层MD5加加盐
function md5Pwd(pwd){
	const salt = 'ybchat_is_good_#@~~6868!';
	return utility.md5(utility.md5(pwd + salt));
}

{% endhighlight %}

这里utility是第三方库，需要npm install。


































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

#### /server ---- 放的是服务端的代码

#### /src    ---- 前端
* /src/component 	: 这里是公用组件
* /src/container 	: 页面逻辑
* /src/redux			: redux相关的代码，另外，redux合成在/src/reducers.js中
* /src/config.js 	: 这个是配置axios拦截器

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




