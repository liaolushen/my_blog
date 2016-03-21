---
title: Sublime Text3 报错 There are no packages available for installation
date: 2015-09-07 01:18:36
tags: Sublime
categories: 工具使用
---

>今天想用sublime安装插件时，发现了这个报错，于是开始网上查找问题解决方法，最后总算解决。

## 问题原因
其实原因非常简单，查看了sublime作者的github上的issue之后，我试了下登陆sublime的package资源网站<https://packagecontrol.io/>，发现果然是无法进入，开启代理之后成功打开，最终确定罪魁祸首是[GFW](https://zh.wikipedia.org/wiki/%E9%98%B2%E7%81%AB%E9%95%BF%E5%9F%8E)。

## 问题解决
确定问题后，解决方法就有很多了。你也改代理之类的，网上教程很多。我的方法是设置自己的代理上网。
在preferences->Setting-User加上如下语句

	"http_proxy": "http://127.0.0.1:1080"

这里改成你自己的上网代理端口即可。
