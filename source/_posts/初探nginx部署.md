---
title: 初探nginx部署
date: 2015-12-29 16:17:13
tags: [nginx]
categories: 服务器配置
---

> 其实之前就有多次使用过nginx，但是每次使用配置时都会出现小问题，网上教程又大多已经过时，写一篇nginx基础配置流程给自己备忘用。

## 下载nginx
我目前使用的系统是ubuntu14.04，直接通过`apt-get`安装的nginx过于老旧，不建议使用。目前最新的nginx有stable版和mainline版两种，可以根据自己的需求按照下面的方法安装：
```bash
# 如果要安装mainline版可以把下面一句最后的stable改成development
add-apt-repository ppa:nginx/stable
apt-get update
apt-get install nginx
```
最后你可以试试输入`nginx -v`，如果安装成功了，应该会告诉你`nginx`的版本号

## 配置nginx
安装成功后就是开始配置，这里我只讲简单的配置以及一些我自己遇到的坑，具体的还是去官网看。

### 配置文件的位置
这里是我遇到的第一个坑，就是关于在哪里配置的问题，首先打开`/etc/nginx/`文件夹，网上大多教程都说直接在`nginx.conf`中进行配置，那可能时以前的nginx的配置方法。至少现在我是不推荐的，因为里面放的都是一些全局的配置信息，虽然能把配置写里面，但是如果配置很多，别人看着也很累吧。那么配置信息应该放哪呢？其实在默认的配置文件中你可以找到下面两句话：
```bash
include /etc/nginx/conf.d/*.conf;
include /etc/nginx/sites-enabled/*;
```
如果懂`bash`脚本的人就会知道，他会默认引用`conf.d`和`sites-enabled`两个文件夹里面的配置文件信息，但是为什么要有两个文件夹呢？如果你安装的是`mainline`版的`nginx`，会发现就只包含了`conf.d`文件夹，而并没有下面那一句，甚至都已经没有了`site-enabled`文件夹。其实这是`nginx`的一个变化吧，以前的`nginx`也是只有`sites-enabled`文件夹，而没有`conf.d`。反正说这么多，就一句话——**在`conf.d`文件夹里放私人配置文件，且配置文件以`.conf`为后缀！**

### 配置出错的处理
配置时自然避免不了出错，而且经常发现出错后你可能就是单纯的打不开网页，而完全不知道是哪里错了，出错时该怎么办呢？其实答案很简单——看log日志。反正我的一些低级错误都是通过log日志来定位的。下面是我的日志打印的示例
`error_log  /var/log/nginx/error.log warn;`
`error_log`有两个参数，第一个参数是你的日志的位置，第二个参数是日志输出的等级，日志等级有`debug | info | notice | warn | error | crit | alert | emerg`，具体的信息去官网看吧。

### 新配置启用
`sudo service nginx reload`

## 配置总结
其实我写得很简单，但是我感觉已经够用了。知道了配置在哪写，配置写错了怎么查错，剩下的应该都能自己解决了。**官方文档才是王道！**
