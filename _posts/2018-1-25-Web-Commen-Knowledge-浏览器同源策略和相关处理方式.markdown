---
layout: post
title:  浏览器同源策源以及相关跨域请求的处理
date:   2018-1-15
categories: Web_Common_Knowledge
tags: [Developing_React, Web_Common_Knowledge]
comment: true
---
跨域问题是我以前做APP开发时从来没有遇到的问题，原来这个问题是浏览器端的特殊问题，现在开发Web前端了，自然而然的遇到了。（这里先简单记下，后续有时间再扩展）

#### 同源策略

是浏览器因为安全性而做出的一种限制，是为了保证用户信息的安全，防止恶意的网站窃取数据。在请求方面：同源政策规定，AJAX请求只能发给同源的网址，否则就报错。

所谓同源是说，**协议**， **端口号**， **域名** 这三者必须相同。 

>举例来说，http://www.example.com/dir/page.html这个网址，协议是http://，域名是www.example.com，端口是80（默认端口可以省略）。它的同源情况如下。

http://www.example.com/dir2/other.html：同源
http://example.com/dir/other.html：不同源（域名不同）
http://v2.www.example.com/dir/other.html：不同源（域名不同）
http://www.example.com:81/dir/other.html：不同源（端口不同）

(例子来自[阮一峰老师](http://www.ruanyifeng.com/blog/2016/04/same-origin-policy.html))

#### 典型的解决

这里我重点了解了两个办法：**JSONP**， **CORS**。

1.JSONP（JSON with Padding）:这是不完全绕开同源限制的一种技术手段。

总体思想就是：浏览器如果对其他非同源服务发起ajax请求是受到同源策略约束的。但是如果去非同源站点请求JS代码，这是没有限制的，那么，将请求行为伪装成请求JS脚本，将接口本应返回的数据“伪装”成请求回来的JS脚本，这是一种思路。

这种思路后来就是用所谓JSONP技术实现的，看下面代码来自（[菜鸟教程](http://www.runoob.com/json/json-jsonp.html)）

客户端:

{% highlight ruby %}

<!DOCTYPE html>
<html>
<head>
<meta charset="utf-8">
<title>JSONP 实例</title>
</head>
<body>
    <div id="divCustomers"></div>
    <script type="text/javascript">
function callbackFunction(result, methodName)
        {
            var html = '<ul>';
            for(var i = 0; i < result.length; i++)
            {
                html += '<li>' + result[i] + '</li>';
            }
            html += '</ul>';
            document.getElementById('divCustomers').innerHTML = html;
        }
</script>
<script type="text/javascript" src="http://www.runoob.com/try/ajax/jsonp.php?jsoncallback=callbackFunction"></script>
</body>
</html>


{% endhighlight %}

服务端:

{% highlight ruby %}

<?php
header('Content-type: application/json');
//获取回调函数名
$jsoncallback = htmlspecialchars($_REQUEST ['jsoncallback']);
//json数据
$json_data = '["customername1","customername2"]';
//输出jsonp格式的数据
echo $jsoncallback . "(" . $json_data . ")";
?>

{% endhighlight %}

简单的说下，假设客户期望返回JSON数据：["customername1","customername2"]。那么，真正返回到客户端的数据显示为: callbackFunction(["customername1","customername2"])。然后，脚本到了客户端后会执行，执行就是调用在客户端中已经写好的的处理函数，期待数据已经参数中了。

把这种思路可以封装下，可以更简单实现JSONP，比如前后端约定回调函数名的过程可以被封装起来。

#### 参考文献

{% highlight ruby %}
def print_hi(name)
  puts "Hi, #{name}"
end
print_hi('Tom')
#=> prints 'Hi, Tom' to STDOUT.
{% endhighlight %}

Check out the [Jekyll docs][jekyll-docs] for more info on how to get the most out of Jekyll. File all bugs/feature requests at [Jekyll’s GitHub repo][jekyll-gh]. If you have questions, you can ask them on [Jekyll Talk][jekyll-talk].


__操千曲尔后晓声，观千剑尔后识器__