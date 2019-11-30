---
title: Linux任务计划crontab
date: 2018-08-27 22:23:14
tags: Linux
categories: "实验楼学习笔记之Linux"

---

时常会有一些定期定时的任务，如周期性的清理一下／tmp，周期性的去备份一次数据库，周期性的分析日志等等。而且有时候因为某些因素的限制，执行该任务的时间会很尴尬。本文将带你很好的利用 Linux 系统的计划工具<!--more-->

crontab 命令常见于 Unix 和类 Unix 的操作系统之中（Linux 就属于类 Unix 操作系统），用于设置周期性被执行的指令。

## Crontab简介

`Crontab`命令从输入设备读取指令，并将其存于`crontab`文件中，以供之后读取和执行。crontab存储的指令在守护进程激活，crond为其守护进程，每一分钟会检查一次是否预定的作业需要执行。

通过 crontab 命令，我们可以在固定的间隔时间执行指定的系统指令或 shell　script 脚本。时间间隔的单位可以是分钟、小时、日、月、周的任意组合。

		# Example of job definition:
		# .---------------- minute (0 - 59)
		# |  .------------- hour (0 - 23)
		# |  |  .---------- day of month (1 - 31)
		# |  |  |  .------- month (1 - 12) OR jan,feb,mar,apr ...
		# |  |  |  |  .---- day of week (0 - 6) (Sunday=0 or 7) OR sun,mon,tue,wed,thu,fri,sat
		# |  |  |  |  |
		# *  *  *  *  * user-name command to be executed

## crontab使用

运行如下命令，添加一个集合任务：

	crontab -e

详细的格式可以使用`man`命令查看：
	
	man crontab

给出一个例子来完成一个任务的添加，该任务是每分钟在/home/zxp目录下创建一个以当前年月日时分秒为名字的空文件：

	*/1 * * * * touch /home/zxp/$(date + /%H/%m/%d/%H/%M/%S)

> 注意 “ % ” 在 crontab 文件中，有结束命令行、换行、重定向的作用，前面加 ” \ ” 符号转义，否则，“ % ” 符号将执行其结束命令行或者换行的作用，并且其后的内容会被做为标准输入发送给前面的命令。

![](https://i.imgur.com/T6DV5Oj.png)

查看添加的任务：

	crontab -l

默认cron是自动启动的，可以通过如下命令查看是否启动:

	ps aux | grep cron

	pgrep cron

启动crontab:

	sudo cron -f &

当不再需要这个任务时，可以使用如下命令：

	crontab -r

## crontab深入

每个用户使用 crontab -e 添加计划任务，都会在 `/var/spool/cron[/crontabs]` 中添加一个该用户自己的任务文档，这样目的是为了隔离。

如果是系统级别的定时任务，应该如何处理？只需要以 sudo 权限编辑 /etc/crontab 文件就可以。

cron 服务监测时间最小单位是分钟，所以 cron 会每分钟去读取一次 /etc/crontab 与 /var/spool/cron/crontabs 里面的內容。

在/etc下，cron相关的文件和目录有：

![](https://i.imgur.com/u9iyxSl.png)


每个目录的作用：

- /etc/cron.daily，目录下的脚本会每天执行一次，在每天的6点25分时运行；
- /etc/cron.hourly，目录下的脚本会每个小时执行一次，在每小时的17分钟时运行；
- /etc/cron.monthly，目录下的脚本会每月执行一次，在每月1号的6点52分时运行；
- /etc/cron.weekly，目录下的脚本会每周执行一次，在每周第七天的6点47分时运行；
系统默认执行时间可以根据需求进行修改。

转自：[实验楼](https://www.shiyanlou.com)