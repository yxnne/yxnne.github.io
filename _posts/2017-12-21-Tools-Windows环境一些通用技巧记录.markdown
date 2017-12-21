---
layout: post
title:  Window开发环境下通用技巧记录
date:   2017-7-1
categories: Tools
tags: [Tools,Windows]
---
<big>本文将持续记录</big> 在开发过程中与到的需要记录的东西，原则是，只要是自己以前不会的，有价值的都要记下。

#### 将程序作为服务

假设某程序是:D:\dev\finderX\xyz.cmd

**添加**cmd中使用命令sc create:

>sc create 新名字 binPath= D:\dev\finderX\xyz.cmd start=auto

**删除**cmd中使用命令sc delete:

>sc delete 名字 