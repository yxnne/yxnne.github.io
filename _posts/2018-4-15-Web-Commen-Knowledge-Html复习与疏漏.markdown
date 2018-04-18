---
layout: post
title:  浏览器同源策源以及相关跨域请求的处理
date:   2018-4-15
categories: Common_Knowledge_Fragments
tags: [Developing_React, Web_Common_Knowledge]
comment: true
---
对于html，之前都是现用现baidu，但是从来没有好好学习和记忆过，所以，总是查了忘，忘了查，而且知识点也是支离玻碎的。
对于css也是一样，所以，计划抽出几天好好的复习下最基础的html \ html5\ css \ css3 \ js \ es6。

这是关于html的复习，记录了在复习过程中之前没注意过的。

#### 疏漏
##### 标题很重要
* 请确保将 HTML heading 标签只用于标题。不要仅仅是为了产生粗体或大号的文本而使用标题。

* 搜索引擎使用标题为您的网页的结构和内容编制索引。

* 因为用户可以通过标题来快速浏览您的网页，所以用标题来呈现文档结构是很重要的。

* 应该将 h1 用作主标题（最重要的），其后是 h2（次重要的），再其次是 h3，以此类推。

##### 避免使用这些标签，应该用样式
这些都是HTML 4 中，被废弃（Deprecated），所以需要尽量避免使用：
标签类：
1. center标签    定义居中的内容。
2. font 和 basefont标签 定义 HTML 字体。
3. s 和 strike标签  定义删除线文本
4. u标签 定义下划线文本

避免使用这些属性：
1. align   定义文本的对齐方式
2. bgcolor 定义背景颜色
3. color   定义文本颜色


##### 格式化相关
* 预格式文本，pre标签
预格式文本就是，pre标签内的格式将会被显示，比如，在未被pre标签包裹的HTML中，多个连续空格会被显示成一个，回车符号将不会被显示成换行，但是使用pre包裹后，会按照文本格式显示。
这个元素在显示代码的时候是有用的，因为code标签居然不保留空格和换行，所以，可以用pre将code包裹就好。

* 引用：blockquote & q
浏览器通常会对 blockquote 元素进行缩进处理。而对 q 元素显示上用引号包裹。q 是行内元素，blockquote如其名，块级的引用。

##### img标签我没注意过的：
* img标签的align属性
```
<p>图像 <img src="/i/eg_cute.gif" align="bottom"> 在文本中</p>

```
上述代码中，文字和图片的底部在一条水平线

同理 align中也可以设置成：middle \ top ，分别是竖直方向上的位置；
align属性设置为left和right是左、右浮动,和float样式差不多；

* 图像映射
创建带有可供点击区域的图像地图。其中的每个区域都是一个超级链接。
就是一副图片上显示不同的热点，对应不同的点击响应，相关的标签有img, map, area, 不深究这个。
[图像映射的例子](http://www.w3school.com.cn/tiy/t.asp?f=html_areamap)

##### 关于表格
表格我之前一直不太熟悉，今天敲好整体过一遍，记录下：
* 最基本的表格标签：table \ tr (一行) \ td（行中一列）；
* table标签的border属性用来设置边框粗细大小，不想要边框，设置成0；
* 表格的td里面可以嵌套其他标签，比如div \ ul \ p等；
* 跨行或者跨列的表格，[示例](http://www.w3school.com.cn/tiy/t.asp?f=html_table_span)
```
  <td colspan="2">电话</th> <!-- 这意思是长度是两列的长度 -->

  <td rowspan="2">电话</th> <!-- 这意思是高度是两行的高度 -->
```
* align属性可以排列表格内容的位置；
* frame属性可以设置表格的边框，设置边框线只有上下还是左右，或者其他；
* 表格的组成还包括其他标签：
```
<table>
    <caption>这是表的标题标签</caption>
    <!-- colgroup 标签配合col标签， 可以用来给表格列分配位置空间 -->
    <colgroup>
        <col style="width: 15%;"  >
        <col style="width: 15%;">
        <col style="width: 15%;">
        <col style="width: 15%;">
        <col style="width: 15%;">
        <col>
    </colgroup>
    <!-- thead 表头标签，一般定义列头 -->
    <thead>
        <tr>
            <th >姓名</th>
            <th >姓名</th>
            <th >姓名</th>
            <th >姓名</th>
            <th >姓名</th>
        </tr>
    </thead>
    <!-- tbody 表主体标签 -->
    <tbody>
        <tr>
            <td ></td>
            <td ></td>
            <td ></td>
            <td ></td>
            <td ></td>
        </tr>
    </tbody>
    <!-- tbody 表脚 -->
    <tfoot>
        <tr>
            <td colspan="6">
                
            </td>
        </tr>
    </tfoot>
</table>

```

##### 关于列表的补缺
* 列表大致分为ul（无序）\ ol(有序) \ 以及dl(自定义)
* 属性type可以定义列表项的样式，比如无序列表中设置属性， type="disc"： 与默认一致，是个实心圆；type="circle"，空心圆；type="square"这是方块；有序列表也是可以设置type的；
* dl, dt, dd,三个标签来完成自定义列表,[自定义列表例子](http://www.w3school.com.cn/tiy/t.asp?f=html_list_definition)

##### 框架和内联框架
* 先说frame
```
<html>

<frameset cols="120,*">

  <frame src="/example/html/html_contents.html">
  <frame src="/example/html/frame_a.html" name="showframe">

</frameset>

</html>

```
上面的代码是frame的典型用法，就是把页面分成两部分frame，放在frameset中，左边宽120，剩下的是右边；
左边frame中放置一个html页面，右边的frame中也放置一个；
这样的结构可以是一个典型的导航结构，假设左边的frame中放置几个链接像这样：
```
<html>
<body>
    <a href="/example/html/frame_a.html" target="showframe"/>

    <!-- 其他省略 -->
</body>
</html>
```
这里target属性就是frame的name属性，也就是说，这个链接的作用是，让name等于target的frame中呈现我href的页面。

* 再看iframe
iframe和frame从功能上来讲颇为类似，也是提供一个页面容器，在里面放置html呈现。
```
在body中的代码片段
<iframe src="demo_iframe.htm" name="iframe_a"></iframe>
<p><a href="http://www.w3school.com.cn" target="iframe_a">W3School.com.cn</a></p>
```

* frame 和 iframe的区别
1. frame不能脱离frameSet单独使用，iframe可以；
2. frame不能放在body中；
3. 嵌套在frameset中的iframe必须放在body中，而不嵌套在frameset的iframe则随意（可以不放body中）；
4. frame必须在frameset里 
而frameset不能与body元素共存；
5. frame的高度只能通过frameSet控制；iframe可以自己控制，不能通过frameSet控制；

##### html的 head标签中的元素
* title 标题，这个标题决定了：浏览器工具栏中的标题；提供页面被添加到收藏夹时显示的标题；在搜索引擎结果中的页面标题；
* base, 标签为页面上的所有链接规定默认地址或默认目标（target）
```
<head>
<base href="http://www.w3school.com.cn/images/" />
<base target="_blank" />
</head>
```
* link,常用外部样式引入；
* style，文档内样式；
* script, 里面放脚本；
* meta，元数据（metadata）是关于数据的信息。

meta 标签提供关于 HTML 文档的元数据。元数据不会显示在页面上，但是对于机器是可读的。

典型的情况是，meta 元素被用于规定页面的描述、关键词、文档的作者、最后修改时间以及其他元数据。

*一些搜索引擎会利用 meta 元素的 name 和 content 属性来索引您的页面。*

```
<meta name="description" content="Free Web tutorials on HTML, CSS, XML" />

<meta name="keywords" content="HTML, CSS, XML" />
```
[关于meta，其实东西有很多](http://www.w3school.com.cn/tags/tag_meta.asp)
#### 参考文献

[W3 School HTML](http://www.w3school.com.cn/html/index.asp)


__操千曲尔后晓声，观千剑尔后识器__