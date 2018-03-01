---
layout: post
title:  MySQL阿里云的1130错误解决方法
date:   2018-2-26
categories: Common_Knowledge_Fragments
tags: [MySQL]
comment: true
---
阿里云安装好mysql后，本地登录ok，远程不能登录mysql，返回1130错误。百度查询mysql的1130错误是远程连接的用户无远程权限问题导致。

[参考这篇文章，这里记录要点](https://yq.aliyun.com/articles/47168)

解决方法：
解决1：命令行
1）首先以 root 帐户登陆 MySQL
在 Windows 主机中点击开始菜单，运行，输入“cmd”，进入控制台，MySQL 的 bin 目录下，然后输入下面的命令。

{% highlight javascript %}
MySQL -uroot -p123456
123456 为 root 用户的密码。
{% endhighlight %}

2）创建远程登陆用户并授权

{% highlight javascript %}

grant all PRIVILEGES on discuz.* to ted@'123.123.123.123' identified by '123456';

{% endhighlight %}
上面的语句表示将 discuz 数据库的所有权限授权给 ted 这个用户，允许 ted 用户在 123.123.123.123 这个 IP 进行远程登陆，并设置 ted 用户的密码为 123456 。

下面逐一分析所有的参数：
all PRIVILEGES 表示赋予所有的权限给指定用户，这里也可以替换为赋予某一具体的权限，例如：select,insert,update,delete,create,drop 等，具体权限间用“,”半角逗号分隔。

discuz. 表示上面的权限是针对于哪个表的，discuz 指的是数据库，后面的 * 表示对于所有的表，由此可以推理出：对于全部数据库的全部表授权为“.”，对于某一数据库的全部表授权为“数据库名.”，对于某一数据库的某一表授权为“数据库名.表名”。

ted 表示你要给哪个用户授权，这个用户可以是存在的用户，也可以是不存在的用户。

123.123.123.123 表示允许远程连接的 IP 地址，如果想不限制链接的 IP 则设置为“%”即可。

123456 为用户的密码。

执行了上面的语句后，再执行下面的语句，方可立即生效。

{% highlight javascript %}
flush privileges;
{% endhighlight %}
__操千曲尔后晓声，观千剑尔后识器__