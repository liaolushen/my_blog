---
title: Ubunutu下Apache配置私人目录
date: 2015-09-07 12:00:34
tags: [Apache]
categories: 服务器配置
---

> 在Ubuntu中apache的默认目录为/var/www/html/,而该目录的操作是需要root权限的。能不能额外增加端口，然后工作目录设置为自己的私人目录呢？当然是可以的！

## 添加端口

打开/etc/apache2/ports.conf

	sudo vi /etc/apache2/ports.conf

添加一个端口，如下所示：

	Listen 80
	Listen 8080

	<IfModule ssl_module>
		Listen 443
	</IfModule>

	<IfModule mod_gnutls.c>
		Listen 443
	</IfModule>


## 添加配置文件

在/etc/apache2/sites-avavilble目录下添加一个配置文件。可以直接复制该目录下的000-default.conf文件：

	sudo cp /etc/apache2/sites-available/000-default.conf /etc/apache2/sites-available/my.conf

VirtualHost *:80改为VirtualHost *:8080

DocumentRoot /var/www/html 改为自己的目录

## 使配置文件生效

将my.conf 软链到sites-enabled

	sudo ln -s /etc/apache2/sites-available/my.conf /etc/apache2/sites-enabled/my.conf

重启apache

	sudo service apache2 restart
