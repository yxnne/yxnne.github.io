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

马上，狗年到啦~~~~Let's GOGOGGO~~~~~~
#### 登录逻辑
UI和注册差不多，控件更少
逻辑：
前端:输入校验之后，发送请求。
后台:收到请求，通过用户名密码从mongo中查询，匹配则返回ok。

#### Cookie
这里，应用还需要引入Cookie校验机制，后台读写cookie的使用cookie-parser，已经安装。
用户登录/注册成功之后，在返回结果之前，应该写入cookie：

{% highlight javascript %}

// 登录成功设置cookie userid
rsp.cookie('userid', doc._id);

return rsp.json({code:0, data:doc});

{% endhighlight %}
这时可以从浏览器的调试窗口network中看到response中有cookie信息。

#### 前端AuthRoute的修改
前端之前顶层有一个控件叫AuthRouter，不参与绘制UI，但功能有点像AOP，就是每每跳转页面的时候都会加载AuthRoute，干什么呢，比如说这里至少有个逻辑是：
获取当前页面的路由地址，如果是/login 或者 /register中的一个, 不进行处理，否则先去后台验证用户是否登录
{% highlight javascript %}

componentDidMount(){
		// 获取当前页面的路由地址
		// 如果是/login 或者 /register中的一个, 不进行处理
		// 否则先去后台验证用户是否登录
		const publicList = ['/login', '/register'];
		const pathname = this.props.location.pathname;
		if (publicList.indexOf(pathname) > -1 ) {
			return null;
		}

		// 获取登录信息
		axios.get('user/info')
		.then(res =>{
			if (res.status === 200){

				if (res.data.code == 0 ) {
					// 有登录信息
					// 设置信息到redux中
					this.props.loadData(res.data.data);

				} else {
					this.props.history.push('/login');
				}
			}
		});

	}

{% endhighlight %}

上述代码中，获取登录信息调用接口/user/info,这个接口就是根据cookie中的userid从mongo中查询用户数据的，若查无此人，或根本就没有cookie，那么就是代码中else分支了，去吧，登录先~~~~

这里 'this.props.loadData(res.data.data)'就是写在redux中的处理逻辑了。当然需要在AuthRoute前connect引入。
{% highlight javascript %}

@withRouter
@connect(
	null,
	{ loadData }
)

{% endhighlight %}

loadData是对外暴露了一个action creator 它分发LOAD_DATA这个action,这里的目的很单纯，功能很简单，就是把后台返回的用户数据写入到redux维护的state中。

这样解决了一个什么问题，就是不管我在那一页，只要浏览器中存在cookie，那么我在AuthRoute中就查到了用户信息，那么就不会被迫跳转到login页面了。

服务端的user/info接口代码:

{% highlight javascript %}

// '/info'的路由
Router.get('/info', (req, rsp) =>{
	// 得到cookie
	// 用户请求的时候是不是携带cookie
	const { userid } = req.cookies;
	if (!userid) {//没有携带cookie，返回失败
		return rsp.json({code:1});
	}
	User.findOne({_id:userid}, _filter, function(err, doc){
		if (err) {
			return rsp.json({code:1, msg:'后台错误'});
		}
		if (doc) {
			return rsp.json({code:0, data:doc});
		}
	});

});

{% endhighlight %}


### 正月初一

登录注册先告一段落，接着要做的是完善信息，用户在刚注册完了之后，除了用户名密码至少还应该有，简介，组织，以及头像。

#### 关于头像的处理
抽象出一个avatar选择器组件，使用antd-mobile的Grid来显示项目中预设的可供选择的头像。[官方API:Grid,宫格](https://mobile.ant.design/components/grid-cn/)

#### 重构user.redux.js
这里因为补充信息页面就是补充个人信息，补充信息应该有一个action creator是update，但是Action吧，这里发现update，register，login之间有相同的可以抽出的地方，所以，将三者的Action可以变成一个。

### 正月初三



#### 登录后路由和目录结构

当用户不存在没有完成信息的时候，实际上路由匹配的是Dashboard，那么就会进入Dashboard组件。
{% highlight javascript %}

// 在index.js中
<Switch>
  <Route path='/login' component={Login}></Route>
  <Route path='/register' component={Register}></Route>
	<Route path='/binfo' component={BInfo}></Route>
	<Route path='/ginfo' component={GInfo}></Route>

	{/* Switch 中没有命中则显示DashBoard */}
  <Route component={Dashboard}></Route>
</Switch>

{% endhighlight %}

#### Dashboard的结构

Dashboard分为三个部分，Header，中间内容，和类似iOS的底部导航tab。

{% highlight javascript %}
<div>
<NavBar className='fixed-header' mode='dark'>{navList.find(v => v.path === pathname).title}</NavBar>
<div style={{marginTop:8}}>
  <Switch>
    {
      navList.map(v=>(
        <Route key={v.path} path={v.path} component={v.component} />
      ))
    }
  </Switch>
</div>
{/* 底部Tab */}
<NavLinkBar data={navList}></NavLinkBar>
</div>
{% endhighlight %}

以上中，navList是一个对象数组，数组中对象的结构是这样：


{% highlight javascript %}

{
  path:'/me', // 中间页的url
  text:'Me',  // 底部的tab的标签名
  icon:'user',// icon名字 
  title:'Profile',// Header的名字
  component:Me    // 加载的组件

},

{% endhighlight %}

所以在中间Switch中,还放的有路由。NavLinkBar和 navList做关联，通过关联关系，来改变路由从而使得中间部分的内容得以切换。


{% highlight javascript %}
// 在NavLinkBar中
<TabBar>
{
  navList.map(v=>(
    <TabBar.Item title={v.text} key={v.path}
      icon={{uri:require(`./img/${v.icon}.png`)}}
      selectedIcon={{uri:require(`./img/${v.icon}-active.png`)}}
      selected={pathname === v.path}
      // 控制路由的一个关键一句
      // 即，点击某一个tab，将路由变成v.path
      // 从而整个页面主题部分改变
      onPress={()=>{
        this.props.history.push(v.path)
      }}>
    </TabBar.Item>
  ))
}
</TabBar>
{% endhighlight %}

#### 用户列表

##### 前端结构和redux状态

用户列表部分通过请求后台数据后，得到用户对象的数组，map遍历这个数组将每一个对象封装成卡片Card样式，将这些卡片抽象成一个独立组件UserCards：

{% highlight javascript %}
class UserCards extends React.Component{

  // 属性验证
  static propTypes = {
    userList:PropTypes.array.isRequired
  };

  render(){

    return (
      <div>
        <WingBlank>
          {this.props.userList.map(v=>(
            v.avatar?
            <Card key={v._id}>
              <Card.Header title={v.user}
                thumb={<img style={{width:58, height:58}} src={require(`../img/${v.avatar}.svg`)}/>}
                extra={<span>{v.title}</span>}>
              </Card.Header>
              <Card.Body>
                {v.type=='b'?<div>{v.company}</div>:null}
                {v.desc.split('\n').map(i=>(
                    <div>{i}</div>
                ))}
                {v.type=='b'?<div>{v.money}</div>:null}
              </Card.Body>
            </Card>
            :null
          ))}
        </WingBlank>
      </div>
    );
  }
}
{% endhighlight %}
那么使用就是<UserCards userList={this.props.userList} />

关于这里的Redux设计下(一个新的redux文件):
Action暂时只涉及USER_LIST，所以action creator中也产生相关type是USER_LIST的ACTION ；
对外暴露getUserList的方法，这里异步获得后台数据，并分发action creator,从而使得reducer可以处理并改变状态。
组件可以通过@connect关联redux，从而通过this.props来获得redux暴露的方法和状态。

{% highlight javascript %}
import axios from 'axios';

// chatuser 相关的 ACTION

const USER_LIST = 'USER_LIST';


// 用户的初始状态
const initState = {
  userList:[]
};

// reducer
export function chatuser(state=initState, action){
  switch (action.type) {
    case USER_LIST:
      return {...state, userList:action.payload};

    default:
      return state;
  }
}

// action creator UserList
function userList(data){
  return {type:USER_LIST, payload:data}
}

// 对外暴露 getUserList 获得列表
export function getUserList(type){
  return dispatch=>{
    axios.get('/user/list?type=' + type)
    .then(res=>{
      if (res.data.code==0) {
        // this.setState({data:res.data.data})
        dispatch(userList(res.data.data));
      }
    });
  };
}

{% endhighlight %}

不同的redux文件需要合并：

{% highlight javascript %}
import { combineReducers } from 'redux';
import { user } from './redux/user.redux';
import { chatuser } from './redux/chatuser.redux';

export default combineReducers({ user, chatuser });

{% endhighlight %}

##### 后台实现

主要是从mongo中查询用户列表：

{% highlight javascript %}

Router.get('/list', function(req, rsp){
	// GET 参数使用 req.query来获得,POST 蚕食是req.body获取
	const type = req.query.type;
	User.find({type}, function(err, doc){
		return rsp.json({code:0, data:doc});
	});
});

{% endhighlight %}




















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
* Grid 宫格
* Card 卡片
* TabBar 底部导航 
* NavBar 头部状态栏




