---

title: Centos7设置默认进入图形界面和命令行界面
date: 2018-09-01 15:57:50
tags: Linux

---

相比7之前的版本，在centos7版本中，设置OS启动默认进入图形界面还是文本界面有了点变化。<!--more-->


查看当前默认设置

	systemctl get-default
	graphical.target #默认图形界面


设置开始进入命令行界面：

	systemctl set-default multi-user.target
	#返回
	Removed symlink /etc/systemd/system/default.target.
	Created symlink from /etc/systemd/system/default.target to /usr/lib/systemd/system/multi-user.target.


设置开始进入图形界面：

	systemctl set-default grphical.target

阅读/etc/inittab，也可以发现如下内容


	[root@rems2 ~]# cat /etc/inittab
	# inittab is no longer used when using systemd.
	#
	# ADDING CONFIGURATION HERE WILL HAVE NO EFFECT ON YOUR SYSTEM.
	#
	# Ctrl-Alt-Delete is handled by /usr/lib/systemd/system/ctrl-alt-del.target
	#
	# systemd uses 'targets' instead of runlevels. By default, there are two main targets:
	#
	# multi-user.target: analogous to runlevel 3 --->告诉我们multi-user.target相当于以前的runlevel 3
	# graphical.target: analogous to runlevel 5 --->告诉我们graphical.target相当于以前的runlevel 5
	#
	# To view current default target, run:
	# systemctl get-default
	#
	# To set a default target, run:
	# systemctl set-default TARGET.target
	#

转自:[https://blog.csdn.net/liglewang/article/details/76222642](https://blog.csdn.net/liglewang/article/details/76222642)


命令行输入：
 
	init 3

进入命令行界面

	init 5

进入图形界面


图形界面安装教程：

[https://www.linuxidc.com/Linux/2018-04/152000.htm](https://www.linuxidc.com/Linux/2018-04/152000.htm)

