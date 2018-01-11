---
layout: post
title:  Python学习笔记:基础语法速览
date:   2018-1-4
categories: python_learnning
tags: [python]
---
这是关于<big>Python</big>基础的笔记，也方便以后查阅，笔记来自于《Python编程》从入门到实践。
(这本书有2部分:1.基础语法；2.小项目(小游戏/可视化/Web)。我的笔记就将有4篇，基础语法和项目，每个项目有一篇笔记)

### 变量和简单数据类型

#### 变量命名规范

1.只能是字母数字下划线且数字不开头

2.不包含空格

3.不能将关键字和函数名作为变量

4.建议：简短有意义，慎用'l'与'o'

#### 字符串

1.""和''都行，而且""中可以包含''或''中可以包含""而不用转义

2.字符串拼接用'+'

3.介绍了几个方法：

{% highlight ruby %}

# title()方法转换成首字母大写
name = " xiao xin "
print(name.title())

# 删除空格就像trim
# rstrip() 删除头部
# lstrip() 删除尾部
# strip()  头尾皆删
print(name.strip())
print(name.lstrip())
print(name.rstrip())

{% endhighlight %}

#### 数字

数字类型包括整数/浮点数，和其他语言没什么大区别

另外:

{% highlight ruby %}

age = 28
print("I am " + age + "years old")

{% endhighlight %}

这样会报错：must be string

使用str()函数，就像这样："I am " + str(age) + " years old"

### 列表简介

#### 索引

从0开始记，随后一个元素的索引是-1

添加到列表尾部，追加用append()

#### 添加删除列表

插入到某位置用insert(位置,元素)

删除元素用del/按值删除是remove()




{% highlight ruby %}

# 最后一个元素索引是-1

# 添加列表元素，append()添加到末尾
listTest = []
print(listTest)

listTest.append(123)  # 在末尾添加
print(listTest)

listTest.insert(0, 456)  # 在0索引位置添加
print(listTest)

del listTest[-1]  # 删除末尾
print(listTest)

poped = listTest.pop()  # 弹出最后一个
print(poped)
print(listTest)

listTest = [1, 2, 3, 4]
print(listTest.pop(1))  # 弹出任意位置元素

listTest.remove(4)
print(listTest)  # 根据值删除元素

{% endhighlight %}

#### 组织列表元素

sort()和sorted()方法，sort()方法改变原列表，sorted()方法不改变原列表。

{% highlight ruby %}

# 组织列表
# 排序原列表
listTest = ['a', 'c', 'b', 'e', 'd']
listTest.sort()  # 列表排序
print(listTest)
# 反向
listTest.sort(reverse=True)
print(listTest)

# 返回排序列表
listTest = [3, 2, 9, 8, 1]
lisSorted = sorted(listTest)
print("listTest is :" + str(listTest))
print("sorted one is :" + str(lisSorted))

# 翻转列表
listTest = ["b", "o", "r"]
listTest.reverse()
print(listTest)

{% endhighlight %}















### import this

输入import this，会得到python编程之禅
" >>>import this "

>>Beautiful is better than ugly.
Explicit is better than implicit.
Simple is better than complex.
Complex is better than complicated.
Flat is better than nested.
Sparse is better than dense.
Readability counts.
Special cases aren't special enough to break the rules.
Although practicality beats purity.
Errors should never pass silently.
Unless explicitly silenced.
In the face of ambiguity, refuse the temptation to guess.
There should be one-- and preferably only one --obvious way to do it.
Although that way may not be obvious at first unless you're Dutch.
Now is better than never.
Although never is often better than *right* now.
If the implementation is hard to explain, it's a bad idea.
If the implementation is easy to explain, it may be a good idea.
Namespaces are one honking great idea -- let's do more of those!




__那只小羊,听着,如果你觉得很累,那么恭喜你,进步在驱赶着你__
