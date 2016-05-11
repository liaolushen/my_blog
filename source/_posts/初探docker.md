---
title: 初探docker
date: 2016-05-06 22:49:00
tags: [docker]
categories: 服务器配置
---

> 之前对docker的大名早有耳闻，但一直没有接触，这次趁着课程作业的机会了解了一下docker的工作方式，感觉docker确实非常强大，能够极大的方便生产开发环境的统一部署。这篇文章内容都是出自官方文档，我是勤劳的搬运工。

## docker的安装
1. 更新包信息，确保`apt`能够使用`https`方法以及安装了CA证书
{% codeblock line_number:false lang:bash %}
$ sudo apt-get update
$ sudo apt-get install apt-transport-https ca-certificates
{% endcodeblock %}

2. 添加新的`GPG`密钥
{% codeblock line_number:false lang:bash %}
$ sudo apt-key adv --keyserver hkp://p80.pool.sks-keyservers.net:80 --recv-keys 58118E89F3A912897C070ADBF76221572C52609D
{% endcodeblock %}

3. 添加第三方库到源中
{% codeblock line_number:false lang:bash %}
$ sudo add-apt-repository "deb https://apt.dockerproject.org/repo $(lsb_release -s -c) main"
{% endcodeblock %}


4. 更新包信息，如果必要的话，还有清理旧仓库
{% codeblock line_number:false lang:bash %}
$ sudo apt-get update
$ sudo apt-get purge lxc-docker
{% endcodeblock %}

5. 官网还推荐在安装前安装一些推荐的包（我没去研究他们是干啥的）
{% codeblock line_number:false lang:bash %}
$ sudo apt-get install linux-image-extra-$(uname -r)
{% endcodeblock %}

6. 终于开始真正的安装
{% codeblock line_number:false lang:bash %}
$ sudo apt-get install docker-engine
{% endcodeblock %}

7. 开启docker服务
{% codeblock line_number:false lang:bash %}
$ sudo service docker start
{% endcodeblock %}

## 基本使用
在定制自己的镜像之前，还是先了解下docker的最基本用法。
基本用法如下：
{% codeblock line_number:false lang:bash %}
# Usage:  [sudo] docker [subcommand] [flags] [arguments] ..
# Example:
$ docker run -i -t ubuntu /bin/bash
{% endcodeblock %}
+ `docker run`启动了一个容器
+ `ubuntu`是你运行的镜像，这里就是ubuntu系统镜像。你定义了一个镜像，docker会现在本地进行寻找，如果没有会再到仓库中进行查找
+ `/bin/bash`是运行的命令，这里就是打开一个`bash`命令行
+ `-i`表示交互式运行
+ `-t`表示使用超级权限运行

基本就是这样，我还省去了很多细节，官网讲的详细得多，如果还有疑惑可以去官网看看。

## 镜像定制
基本用法搞定了，接下来就是自己定制镜像了。在基本用法中已经有使用了镜像，但是是直接使用仓库中提供了，有时根据应用的不同会有不同的需求，这时就需要自己定制镜像了。
自己定制镜像首先需要创建一个`Dockerfile`文件，里面记录了该镜像的所有信息。

`Dockerfile`的基本语法是
+ 使用#来注释
+ FROM 指令告诉 Docker 使用哪个镜像作为基础
+ 接着是维护者的信息
+ RUN开头的指令会在创建中运行，比如安装一个软件包，在这里使用 apt-get 来安装了一些软件

先直接上一个我自己的示例：
{% codeblock line_number:false lang:bash %}
FROM ubuntu
MAINTAINER Luwin
RUN apt-get update && apt-get install -y git python-pip
RUN git clone https://github.com/liaolushen/quick-share.git
RUN cd quick-share && pip install -r requirement.txt && cp config.example.py config.py
{% endcodeblock %}

编写完`Dockerfile`之后，使用`docker build`来生成镜像
{% codeblock line_number:false lang:bash %}
$ docker build -t luwin/webapp:0.1 .
{% endcodeblock %}

生成后就可以运行了
{% codeblock line_number:false lang:bash %}
$ docker run -d -p 8888:8888 luwin/webapp:0.1 python quick-share/run.py
{% endcodeblock %}
