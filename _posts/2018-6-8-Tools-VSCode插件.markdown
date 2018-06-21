---
layout: post
title:  VSCode的插件(前端)
date:   2018-6-8
categories: Tools
tags: [Tools]
---
<big>VSCode</big>看上去也挺好用的，现在自己写React和Vue，看着大家的推荐又是一堆插件需要配起来了： 


##### Auto Close Tag

自动补全标签结束
##### Auto Close Tag
自动添加HTML / XML关闭标签（必备）


##### Auto Rename Tag
自动重命名配对的HTML / XML标签(必备)


##### Beautify
格式化javascript，JSON，CSS，Sass，和HTML

##### Bootstrap 4 & Font awesome snippets
包含Bootstrap 4＆Font awesome 的代码片段

##### Bracket Pair Colorizer
颜色识别匹配括号


##### Class autocomplete for HTML
智能提示HTML class = ""属性 

##### Code Runner
非常强大的一款插件，能够运行多种语言的代码片段或代码文件：C，C ++，Java，JavaScript，PHP，Python，Perl，Ruby，Go等等，安装完成后，右上角出现。

##### css peek
能够查看CSS ID和类的字符串作为HTML文件中相应的CSS定义


##### Document This
添加注释块

>设置：
"docthis.includeAuthorTag": true,//出现@Author
"docthis.includeDescriptionTag": true,//出现@Description
"docthis.authorName": "shenzekun",//作者名字
快捷键： 按两次Ctrl+alt+d

##### ESLint
EsLint可以帮助我们检查Javascript编程时的语法错误。比如：在Javascript应用中，你很难找到你漏泄的变量或者方法。EsLint能够帮助我们分析JS代码，找到bug并确保一定程度的JS语法书写的正确性。


##### Font-awesome codes for html
用于 html 的Font-awesome代码片段

##### filesize
在底部状态栏显示当前文件大小，点击后还可以看到详细创建、修改时间



##### Git History
以图表的形式查看git日志

使用 command+shift+p（Ctrl+shift+p） 输入git log就可以看到了

##### Git Lens
git 日志插件

##### HTML CSS Support
在 html 标签上写class 智能提示当前项目所支持的样式 

##### HTML Snippets
html 代码片段 

##### htmlhint
html代码检测

##### htmltagwrap
可以在选中HTML标签中外面套一层标签


##### Indenticator
突出目前的缩进深度


##### IntelliSense for CSS class names
智能提示 css 的 class 名

##### Image Preview
鼠标移到路径里显示图像预览

##### JavaScript (ES6) code snippets
es6代码片段（必备）

##### JavaScript Snippet Pack
js代码片段（必备）

##### jQuery Code Snippets
jQuery 代码片段

##### markdownlint
markdown 语法检查

##### Node.js Modules Intellisense
可以在导入语句中自动完成JavaScript / TypeScript模块。

##### open in browser
当前的 html 文件用浏览器打开，类似 webstorm 的那四个小浏览器图标功能，前提条件html 文件必须保存

快捷键alt+b

##### Output Colorizer
输出提示的文字颜色有一些变化，方便获取关键信息


##### Path Intellisense
路径自动补全 

##### Prettier
格式化JavaScript / TypeScript / CSS 。

##### Sass
写 sass 必备

##### vscode-faker
生成假数据，地址，电话，图片等等

打开方式shift+cmd+p(shift+ctrl+p)) 然后输入faker 就可以选择了

##### Quokka.js
实时观看 javascript 的变量的变化

使用：先shift+cmd+p （ctrl+shift+p）输入 quokka 选择 new javascript 就行了

#####Regex Previewer
测试正则的插件

##### vscode-icons
目录树图标

#### react
##### React-Native/React/Redux snippets for es6/es7
react代码片段，下载人数超多?

#####react-beautify
格式化 javascript, JSX, typescript, TSX 文件

#### vue
##### vetur
语法高亮、智能感知

##### VueHelper
vue代码片段


##### Vue 2 Snippets


#### PS：vsc常用快捷键：
同时打开多个窗口（查看多个项目）

* 打开一个新窗口： Ctrl+Shift+N
* 关闭窗口： Ctrl+Shift+W
同时打开多个编辑器（查看多个文件）

##### 新建文件 Ctrl+N
* 历史打开文件之间切换 Ctrl+Tab，Alt+Left，Alt+Right
* 切出一个新的编辑器（最多3个）Ctrl+\，也可以按住Ctrl鼠标点击Explorer里的文件名
* 左中右3个编辑器的快捷键Ctrl+1 Ctrl+2 Ctrl+3
* 3个编辑器之间循环切换 Ctrl+反引号`
* 编辑器换位置，Ctrl+k然后按Left或Right

##### 格式调整

* 代码行缩进Ctrl+[， Ctrl+]
* 折叠打开代码块 Ctrl+Shift+[， Ctrl+Shift+]
* Ctrl+C Ctrl+V如果不选中，默认复制或剪切一整行
* 代码格式化：Shift+Alt+F，或Ctrl+Shift+P后输入format code
* 修剪空格Ctrl+Shift+X
* 上下移动一行： Alt+Up 或 Alt+Down
* 向上向下复制一行： Shift+Alt+Up或Shift+Alt+Down
* 在当前行下边插入一行Ctrl+Enter
* 在当前行上方插入一行Ctrl+Shift+Enter

##### 光标相关

* 移动到行首：Home
* 移动到行尾：End
* 移动到文件结尾：Ctrl+End
* 移动到文件开头：Ctrl+Home
* 移动到后半个括号 Ctrl+Shift+]
* 选中当前行Ctrl+i（双击）
* 选择从光标到行尾Shift+End
* 选择从行首到光标处Shift+Home
* 删除光标右侧的所有字Ctrl+Delete
* Shrink/expand selection： Shift+Alt+Left和Shift+Alt+Right
* Multi-Cursor：可以连续选择多处，然后一起修改，Alt+Click添加cursor或者Ctrl+Alt+Down 或 Ctrl+Alt+Up
* 同时选中所有匹配的Ctrl+Shift+L
* Ctrl+D下一个匹配的也被选中(被我自定义成删除当前行了，见下边Ctrl+Shift+K)
回退上一个光标操作Ctrl+U

##### 重构代码

* 跳转到定义处：F12
* 定义处缩略图：只看一眼而不跳转过去Alt+F12
* 列出所有的引用：Shift+F12
* 同时修改本文件中所有匹配的：Ctrl+F12
* 重命名：比如要修改一个方法名，可以选中后按F2，输入新的名字，回车，会发现所有的文件都修改过了。
* 跳转到下一个Error或Warning：当有多个错误时可以按F8逐个跳转
* 查看diff 在explorer里选择文件右键 Set file to compare，然后需要对比的文件上右键选择Compare with 'file_name_you_chose'.

###### 查找替换

* 查找 Ctrl+F
* 查找替换 Ctrl+H
* 整个文件夹中查找 Ctrl+Shift+F

##### 显示相关

* 全屏：F11
* zoomIn/zoomOut：Ctrl + =/Ctrl + -
* 侧边栏显/隐：Ctrl+B
* 预览markdown Ctrl+Shift+V

##### 其他

* 自动保存：File -> AutoSave ，或者Ctrl+Shift+P，输入 auto

[参考文章1](https://segmentfault.com/a/1190000011779959)
[参考文章2](https://blog.csdn.net/amyloverice/article/details/79388270)