---
layout: post
title:  React + antd 页头/登录/登出知识点和逻辑记录_proj_News
date:   2017-12-25
categories: Developing_React
tags: [Developing_React]
---
<big>这篇笔记</big> 是一个React新人--我，对于近期接触到的一些知识点的要点记录。主要就是页头/登录/登出以及pc和移动端响应式的一些要点，antd中Grid布局/Menu/Moadal/Tab/Form/等。记之，聊以自慰。

#### 布局计划

页头部分的布局主要使用了antd的[Grid栅格布局](https://ant.design/components/grid-cn/)

>在多数业务情况下，Ant Design需要在设计区域内解决大量信息收纳的问题，因此在 12 栅格系统的基础上，我们将整个设计建议区域按照 24 等分的原则进行划分。
划分之后的信息区块我们称之为『盒子』。
>建议横向排列的盒子数量最多四个，最少一个。『盒子』在整个屏幕上占比见上图。设计部分基于盒子的单位定制盒子内部的排版规则，以保证视觉层面的舒适感。

计划布局是，将栅格放入"<header>"中，其左侧放入logo和名字，中间放菜单，最右侧是用户操作（登录/注册 和 信息/登出）。中间很大部分都是导航菜单，用antd中Menu组件构建，最右侧部分是登录注册或者用户信息和登出，具体规划就是未登录时显示登录注册，登录后显示登录成功用户名和登出。

假设点击登录注册，则会弹模态对话框，模态框上面由Tab选项卡控制着两个Form表单的切换。

##### **Grid**

antd中Grid的使用简单，就是行中Row嵌套列Col，嵌套的大小span属性控制，总大小24。[具体](https://ant.design/components/grid-cn/)

##### **Menu**

Menu的使用也不难：最外层根部标签使用<Menu>，最基本的是每一项用<Menu.Item>，当然还有Menu.SubMenu等。Menu中selectedKeys可以设置是子项目中key，来表征当前选中的哪个。key是子Menu选项的标识，Menu还有onClick方法。[具体](https://ant.design/components/menu-cn/)

典型的，比方说：

给Menu设置的selectedKeys属性是{this.state.current},也就是说当current属性值变化时，Menu的被选择香就变化了，再设置onClick比如{this.handleMenu.bind(this)},handleMenu中会拿到“事件.key”，这就是点击那个子项目的key，在handle方法中设置state.current这样就完成一个联动操作。

{% highlight ruby %}

//外层Mune
<Menu mode="horizontal" selectedKeys={[this.state.current]}
      onClick={this.handleClick.bind(this)}>
        
        //子选项
    <Menu.Item key="top">
      Main
    </Menu.Item>

//处理函数中拿到key
handleClick(e){
    if(e.key == "top"){
      this.setState({current:'top'});
    }
}

{% endhighlight %}

##### **Modal**

模态框也是简单的使用了下，主要用到了属性有：title，标题；visible，控制显示；onCancel，点击取消时的回调；onOk，点击ok时的回调，okText是ok上按键的文字；[具体](https://ant.design/components/modal-cn/)

另外在<Modal>的内部可以按需嵌套其他逻辑。

{% highlight ruby %}

<Modal title="用户中心" wrapClassName="vertical-center-modal" 
visible={this.state.modalVisiable}
onCancel={()=>this.setModalVisible(false)}
onOk={()=>this.setModalVisible(false)}
okText="关闭">
    //...按需嵌套
</Modal>

//控制显示的方法
setModalVisible(value){
    this.setState({modalVisiable:value});
}

{% endhighlight %}

##### **Tabs**

比较简单，Tabs中嵌套TabPane。Tab属性中，type是样式 onChange是切换事件，例如onChange={this.tabCallback.bind(this)}；TabPane属性中tab="Login"设置标签页， key="1"就类似于Menu的key,所以，就可以想到tabCallback中势必也能拿到key,tab不用设置点击回调onClick。[具体](https://ant.design/components/tabs-cn/)


##### **Form**

Form还是比较复杂点。上代码先。[具体](https://ant.design/components/form-cn/)

{% highlight ruby %}
//最外层
<Form onSubmit={this.handleSubmit.bind(this)}>
    //子元素
    <FormItem label="User Name">
      {getFieldDecorator('userName', {
        rules: [{ required: true, message: 'Please input your username!' }],
      })(
        <Input prefix={<Icon type="user" style={{ color: 'rgba(0,0,0,.25)' }} />} placeholder="Username" />
      )}
    </FormItem>

    //提交按钮
    <Button type="primary" htmlType="submit">Register</Button>

//提交后的事件回调
handleSubmit(e){
    //阻止事件冒泡,这句一定加上
    e.preventDefault();

    this.props.form.validateFields((err, formData) => {
      console.log('Received formData of form: ', formData);

    });
  }
//另外Render方法一开始
//引入Form中会使用的对象
let {getFieldDecorator,getFieldError, isFieldTouched} = this.props.form;

//使用了Form的组件，记得输出组件要在封装下，比如文件的最后一行
export default PCHeader = Form.create({})(PCHeader);

{% endhighlight %}

1.Form是最外层标签，在里面定义onSubmit提交的回调函数；

2.FormItem是内层元素，属性label是元素标题；

3.FormItem中放了一个这个：getFieldDecorator中第一个参数是表单属性索引，提交表单时，值的名字就是这个， rules：规则，比如必填项required: true，要是不填就提示message: 'Please input your username!'。
" <Input prefix={<Icon type="user" style={{ color: 'rgba(0,0,0,.25)' }} />} placeholder="Username" /> "是表单项的主体，Input，prefix属性传递了个对象，是一个Icon标签，就是这个Input的小Icon，placeholder就是hint提示；

{% highlight ruby %}

{getFieldDecorator('userName', {
  rules: [{ required: true, message: 'Please input your username!' }],
})(
  <Input prefix={<Icon type="user" style={{ color: 'rgba(0,0,0,.25)' }} />} placeholder="Username" />
)}

{% endhighlight %}

4.在提交的回调中拿到表单数据是：this.props.form.validateFields；

5.记得将使用了表单的组件在输出模块时再封装下。

#### 响应式

响应式就是基于react-responsive中的MediaQuery针对不同屏幕尺寸和适配方案做不同的布局和逻辑。

{% highlight ruby %}

return (
    <div>
      {/* PC */}
      <MediaQuery query='(min-width: 1224px)'>
        <PCIndex/>
      </MediaQuery>

      {/* Moblie */}
      <MediaQuery query='(max-Width: 1224px)'>
        <MobileIndex/>
      </MediaQuery>
    </div>
)

{% endhighlight %}

#### 网络请求fetch

使用fetch框架完成网络请求。安装就是npm安装。

{% highlight ruby %}

let fetchOptions = {
  method:'GET'
};

fetch("url", fetchOptions)
.then(response=>response.json())
.then(json=>{
    json.NickUserName
});

{% endhighlight %}

#### 登录后的逻辑

登录后除了通过控制组件的state之后，通过localStorage本地存储下服务器返回的用户的信息比如什么userID或者userName之类的。

{% highlight ruby %}

localStorage.userid= json.UserId;
localStorage.userNickName = json.NickUserName;

{% endhighlight %}

还有一个问题，就是假设现在登录成功了，但是刷新了之后登录状态将会刷新掉，这显然不是想要的。那么需要在组件的生命周期方法中结合localStorage做一些校验。举个简单的例子就是:

{% highlight ruby %}

//声明周期方法：刷新之后将会执行
componentWillMount(){
  //实际项目中校验复杂了
  if (localStorage.userid!='') {
    this.setState({hasLogined:true});
    this.setState({userNickName:localStorage.userNickName,userid:localStorage.userid});
  }
}

{% endhighlight %}

#### Necessary To Think More：

* 体会组件化思想

* 下级组件添加事件回调函数，通过设置上级组件状态来控制下级组件显示； 

__路漫漫其修远兮，吾将上下而求索__

