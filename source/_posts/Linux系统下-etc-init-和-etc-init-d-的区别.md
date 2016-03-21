title: Linux系统下/etc/init/和/etc/init.d/的区别
date: 2015-09-11 01:37:28
tags: 
categories: Linux的日常
---

## 问题发现

今天在给ubuntu系统安装ftp服务时，一件奇怪的事引起了我的注意。当我服务安装完成后，想要测试一下是否能控制服务，便输入如下命令：

	service vsftpd restart

它返回的信息是：

	stop: Unknown job: vsftpd
	start: Unknown job: vsftpd

另外甚至在<b>/etc/init.d/文件夹</b>中都没有发现vsftpd的启动脚本。

之后我google后在service命令前加了sudo，命令如下:

	sudo service vsftpd restart

结果成功运行。

现在虽然ftp能够正常的启动和终止，但是另一个疑问没有解决，service不是调用/etc/init.d/文件夹里的启动脚本的吗，然而/etc/init.d/中并没有vsftpd这个脚本，为什么还能够运行？我通过man命令查看了一下service指令的信息，果然发现了一些端倪，里面是这样讲的

> The  SCRIPT  parameter specifies a System V init script, located in /etc/init.d/SCRIPT, or the name of an upstart job in /etc/init.

于是我在/etc/init文件夹中果然发现了vsftpd.conf这个文件。但是又有奇怪的事情出现了，查看/etc/init/文件夹中许多文件是和/etc/init.d/文件夹中重复的，比如mysql。那么这两个文件夹有什么区别吗？

## 寻求答案

这种东西当然是寻求google的帮助最快了。果然很快就找到了一位外国大神的答案。他是这么说的：

> /etc/init.d contains scripts used by the System V init tools (SysVinit). This is the traditional service management package for Linux, containing the init program (the first process that is run when the kernel has finished initializing¹) as well as some infrastructure to start and stop services and configure them. Specifically, files in /etc/init.d are shell scripts that respond to start, stop, restart, and (when supported) reload commands to manage a particular service. These scripts can be invoked directly or (most commonly) via some other trigger (typically the presence of a symbolic link in /etc/rc?.d/).

> /etc/init contains configuration files used by Upstart. Upstart is a young service management package championed by Ubuntu. Files in /etc/init are configuration files telling Upstart how and when to start, stop, reload the configuration, or query the status of a service. As of lucid, Ubuntu is transitioning from SysVinit to Upstart, which explains why many services come with SysVinit scripts even though Upstart configuration files are preferred. In fact, the SysVinit scripts are processed by a compatibility layer in Upstart.

简而言之，/etc/init.d/就是旧时代liunx的用法，/etc/init/是现在Ubuntu的提倡并一步步转型的用法。为了平缓过渡，便让service命令可以同时寻找到两个文件夹。

## 问题思考
问题到这貌似解决了，但是感觉还差些什么。

在查看service命令时，是这么介绍的：

> service - run a System V init script

没错，service这个命令一开始就是用在SysVinit上的，为了支持Upstart才额外增加了启动/etc/init/文件夹，那么如何使用Upstart启动服务呢。最后查看Upstart的文档，当我要重启mysql时。我只需要输入如下命令：

	sudo restart mysql

而且Upstart的启动配置文件确实简单很多。

## 问题总结

其实这两个文件夹的区别也就是服务启动方式的区别，目前有三种启动方式。(例如我启动mysql服务)

1.只从/etc/init.d/文件夹启动：

	/etc/init.d/mysql start

2.只从/etc/init/文件夹启动(Ubuntu提倡)：

	sudo start mysql

3.从两个文件夹中启动：

	service start mysql
