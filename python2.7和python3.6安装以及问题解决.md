---
title: Python2.7和Python3.6安装以及文件解决
date: 2018-08-22 22:28:50
tags:  Python
---
在实际中，可以需要同时安装python2.7和python3.6版本，在此过程中可以遇到一些问题，在此将一一陈述。<!--more-->
## Python2.7和Python3.6并存

在环境变量中添加Python两个版本的值即可，具体如下：

右键我的电脑，按下图操作：
![](https://i.imgur.com/XR3VI7D.png)

添加值：

![](https://i.imgur.com/85ex4wA.png)

在环境变量中添加：

	C:\Python27
	C:\Python27\Scripts	

	C:\Python36
	C:\Python36\Scripts

打开CMD,切换Python2.7和Python3.6

	py -2 切换至python2.7
	py -3 

![](https://i.imgur.com/LtX3Wxy.png)

Ctrl+C或 输入`exit()`退出Python环境

在具体py文件中，使用

	#! python2
	# coding: utf-8

可以指定解释器版本，这时只需py test.py，无需带-2或-3这种参数



## IDLE打开方式

可以尝试在搜索栏中输入IDLE，然后双击，如果找不到，尝试如下方法：
找到`C:\Python27\Lib\idlelib`下的`idle.bat`文件，双击即可打开IDLE


## Python2.7的IDLE无法打开问题解决

打开运行，输入如下代码：

	%USERPROFILE%\.idlerc

会自动大量如下文件夹：

![](https://i.imgur.com/9YIypTY.png)


删除文件夹中的三个文件


## pip安装第三方库

	pip2.7 install numpy
	#仅为python2.7安装numpy库
	
	pip3.6 install numpy
	#仅为python3.6安装numpy库

![](https://i.imgur.com/I9XDzyO.png)


第三方库安装目录：`C:\Python27\Lib\site-packages`