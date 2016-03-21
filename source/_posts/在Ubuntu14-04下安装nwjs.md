---
title: 在Ubuntu14.04下安装nwjs
date: 2015-09-13 23:05:29
tags: [nwjs]
categories: Linux的日常
---

> 原本我是想直接用npm来安装nwjs的，很不幸不知是不是防火墙的原因，最终失败了，只能手动安装。

## 下载安装包

	sudo wget -c http://dl.nwjs.io/v0.12.3/nwjs-v0.12.3-linux-x64.tar.gz

如果失败可能是网站被墙，可以打开代理下载

## 解压到本地

	sudo -xvf nwjs-v0.12.3-linux-x64.tar.gz

## 放到合适目录

我个人喜好将第三方软件放到/opt/文件夹中

	sudo mv nwjs-v0.12.3-linux-x64 /opt/nwjs

## 添加路径

创建一个在/etc/profile.d/下创建新的bash文件

	sudo vim /etc/profile.d/nwjs.sh

在里面添加nwjs的路径

	export PATH=$PATH:/opt/nwjs

## 检测是否成功

这里你可以在命令行输入nw，如果成功打开nwjs那就成功了，有时我发现有的会报如下错误

	bash: /opt/nwjs/nw: Permission denied

这是因为没有nw的执行文件没有添加可执行属性，执行如下命令即可：

	sudo chmod +x /opt/nwjs/nw
