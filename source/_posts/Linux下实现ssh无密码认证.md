---
title: Linux下实现ssh无密码认证
date: 2015-09-17 23:44:25
tags: [ssh]
categories: Linux的日常
---

> 如果你有登陆远程机器的需要，那么应该对ssh并不陌生。这篇文章属于新手向，自己写写也当做加深印象，万一以后哪天忘了，也可以看看。

## SSH简介

当然既然要用它，先看看ssh是什么?水平有限，我就直接引用wikipedia的介绍了。

### SSH用途

SSH是Secure Shell的缩写，是一项创建在应用层和传输层基础上的安全协议，为计算机上的Shell（壳层）提供安全的传输和使用环境。简而言之，SSH是目前较可靠，专为远程登录会话和其他网络服务提供安全性的协议。

### SSH验证方式

在客户端来看，SSH提供两种级别的安全验证。
+ 第一种级别（基于密码的安全验证），知道帐号和密码，就可以登录到远程主机，并且所有传输的数据都会被加密。但是，可能会有别的服务器在冒充真正的服务器，无法避免被“中间人”攻击。

+ 第二种级别（基于密钥的安全验证），需要依靠密钥，也就是你必须为自己创建一对密钥，并把公有密钥放在需要访问的服务器上。客户端软件会向服务器发出请求，请求用你的密钥进行安全验证。服务器收到请求之后，先在你在该服务器的用户根目录下寻找你的公有密钥，然后把它和你发送过来的公有密钥进行比较。如果两个密钥一致，服务器就用公有密钥加密“质询”（challenge）并把它发送给客户端软件。从而避免被“中间人”攻击。

这里我介绍的是如何使用第二种安全验证方式，即基于密钥的验证。

## SSH实现密钥验证

### 第一步：本地生成公钥

Linux下通过ssh-keygen命令生成公钥，一般直接默认放置家目录的.ssh文件夹下为宜。

	test@liaolushen-ThinkPad-E450:~$ ssh-keygen
	Generating public/private rsa key pair.
	Enter file in which to save the key (/home/test/.ssh/id_rsa):
	Created directory '/home/test/.ssh'.
	Enter passphrase (empty for no passphrase):
	Enter same passphrase again:
	Your identification has been saved in /home/test/.ssh/id_rsa.
	Your public key has been saved in /home/test/.ssh/id_rsa.pub.
	The key fingerprint is:
	f2:ab:1e:c7:00:53:54:13:3a:26:5f:50:35:50:4b:a1 test@liaolushen-ThinkPad-E450
	The key's randomart image is:
	+--[ RSA 2048]----+
	|     .+o*=*.     |
	|     . o + o     |
	|    + + E .      |
	|     * o         |
	|      + S        |
	|       =         |
	|      . +        |
	|       o .       |
	|     .o..        |
	+-----------------+

查看生成的密钥

	test@liaolushen-ThinkPad-E450:~$ ls .ssh/
	id_rsa  id_rsa.pub

这里id_rsa.pub就是你的公钥了，id_rsa则是你的私钥。

### 第二步：远程机器放置公钥

公钥成功生成后，下一步自然就是把它放到你的远程机器上面去了。这里我们把公钥添加到远程机器的authorized_keys文件中，在这个文件中，每个公钥占据一行。所以如果远程机器上并没有其他人的公钥，则直接如下传输即可，但是如果已经有了其他人的公钥，那么就只能手动添加了。

	test@liaolushen-ThinkPad-E450:~$ scp .ssh/id_rsa.pub root@120.24.74.135:~/.ssh/authorized_keys

### 第三步：实现无密码登陆

现在就来试一试

	test@liaolushen-ThinkPad-E450:~$ ssh root@120.24.74.135

果然成功了，这样以后登陆远程机器就不需要密码啦
