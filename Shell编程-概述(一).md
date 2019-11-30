---

title: Shell编程-概述(一)
date: 2019-03-27 13:44:14
tags: Shell
mathjax: true

---

本系列教程来自：[菜鸟教程](http://www.runoob.com/linux/linux-shell.html)和鸟哥的Linux私房菜基础学习篇。

Shell是一个用C语言编写的程序，它是用户使用Linux的桥梁。Shell既是一种命令语言，又是一种程序设计语言。

Shell是指一种应用程序，这个应用程序提供了一个界面，用户通过这个界面访问操作系统内核的服务。

<!--more-->

[Shell在线工具](http://www.runoob.com/try/runcode.php?filename=helloworld&type=bash)

## 硬件、内核和Shell

三者关系

![](https://i.imgur.com/iKXMIFo.png)

## Shell脚本

Shell 脚本（shell script），是一种为 shell 编写的脚本程序。

业界所说的 shell 通常都是指 shell 脚本，但读者朋友要知道，shell 和 shell script 是两个不同的概念。

由于习惯的原因，简洁起见，本文出现的 "shell编程" 都是指 shell 脚本编程，不是指开发 shell 自身。

## Shell 环境

Shell 编程跟 java、php 编程一样，只要有一个能编写代码的文本编辑器和一个能解释执行的脚本解释器就可以了。

Linux 的 Shell 种类众多，常见的有：

	Bourne Shell（/usr/bin/sh或/bin/sh）
	Bourne Again Shell（/bin/bash）
	C Shell（/usr/bin/csh）
	K Shell（/usr/bin/ksh）
	Shell for Root（/sbin/sh）
	……

本教程关注的是 Bash，也就是 Bourne Again Shell，由于易用和免费，Bash 在日常工作中被广泛使用。同时，Bash 也是大多数Linux 系统默认的 Shell。

在一般情况下，人们并不区分 Bourne Shell 和 Bourne Again Shell，所以，像 #!/bin/sh，它同样也可以改为 #!/bin/bash。

## 第一个shell脚本

打开文编编译器（可以使用vi/vim命令来创建文件），新建一个文件test.sh，扩展名为.sh(sh代表shell)，扩展名并不影响脚本执行，见名知意就好，如果你用php写shell脚本，扩展名就用php就好。

test.sh

	#！ /bin/bash
	echo "Hello world!"


__1. 作为可执行程序__

执行hello_world.sh之前，需要使脚本具有执行的权限：

	chmod +x ./test.sh

然后执行脚本：

	./test.sh

注意，一定要写成 ./test.sh，而不是 test.sh，运行其它二进制的程序也一样，直接写 test.sh，linux 系统会去 PATH 里寻找有没有叫 test.sh 的，而只有 /bin, /sbin, /usr/bin，/usr/sbin 等在 PATH 里，你的当前目录通常不在 PATH 里，所以写成 test.sh 是会找不到命令的，要用 ./test.sh 告诉系统说，就在当前目录找。

__2. 作为解释器参数__

这种运行方式是，直接运行解释器，其参数就是shell脚本的文件名，如：

	/bin/sh test.sh
	/bin/php test.php


