---

title: Linux 日志系统
date: 2018-09-05 11:07:50
tags: Linux	
categories: "实验楼学习笔记之Linux"

---

日志数据可以是有价值的信息宝库，也可以是毫无价值的数据泥潭。它可以记录下系统产生的所有行为，并按照某种规范表达出来。可以使用日志系统所记录的信息为系统进行排错，优化系统的性能，或者根据这些信息调整系统的行为。收集你想要的数据，分析出有价值的信息，可以提高系统、产品的安全性，还可以帮助开发完善代码，优化产品。日志会成为在事故发生后查明“发生了什么”的一个很好的“取证”信息来源。日志可以为审计进行审计跟踪。<!--more-->

## 一、常见的日志

日志是一个系统管理员，一个运维人员，甚至开发人员不可或缺的东西，系统永久了偶尔会出现一些错误，需要日志来给系统排错，在一些网络应用服务不能正常工作的时候，需要日志来做问题定位，日志还是过往时间的记录表，可以通过它知道是否被不明用户登陆过等等。

在linux中大部分的发行版都内置使用syslog系统日志，常见的日志一般存放在`var/log`中，来看看其他有哪些日志：

	ll /var/log


根据图中所显示的日志，可以根据服务对象粗略的将日志分为两类：

- 系统日志：主要存放系统内置程序或系统内核之类的日志信息如alternative.log,btmp等等；
- 应用日志：主要是安装第三方应用所产生的日志，如：tomact7,apache3等等

系统日志具有有哪些，都记录了怎样的信息：

| 日志名称	| 记录信息 |
|---|
| alternatives.log	| 系统的一些更新替代信息记录 |
| apport.log	| 应用程序崩溃信息记录 |
| apt/history.log	| 使用 apt-get 安装卸载软件的信息记录 |
| apt/term.log	| 使用 apt-get 时的具体操作，如 package 的下载、打开等 |
| auth.log	| 登录认证的信息记录 |
| boot.log	| 系统启动时的程序服务的日志信息 |
| btmp	| 错误的信息记录 |
| Consolekit/history	| 控制台的信息记录 |
| dist-upgrade	| dist-upgrade 这种更新方式的信息记录 |
| dmesg	| 启动时，显示屏幕上内核缓冲信息,与硬件有关的信息 |
| dpkg.log	dpkg | 命令管理包的日志。 |
| faillog	| 用户登录失败详细信息记录 |
| fontconfig.log	| 与字体配置有关的信息记录 |
| kern.log	| 内核产生的信息记录，在自己修改内核时有很大帮助 |
| lastlog	| 用户的最近信息记录 |
| wtmp	| 登录信息的记录。wtmp可以找出谁正在进入系统，谁使用命令显示这个文件或信息等 |
| syslog	| 系统信息记录 |

只闻其名，不见其人，并不能明白这些日志记录的内容。首先我们来看 alternatives.log 中的信息：

![](https://i.imgur.com/5z01zYQ.png)

从中得到的信息有程序作用，日期，命令，成功与否的返回码

对于Debain系统，使用apt-get安装软件，因在 apt 文件夹中的日志信息，其中有两个日志文件 history.log 与 term.log，两个日志文件的区别在于 history.log 主要记录了进行了哪个操作，相关的依赖有哪些，而 term.log 则是较为具体的一些操作，主要就是下载包，打开包，安装包等等的细节操作。


其他的日志格式也都类似于之前所查看的日志，主要便是时间，操作。而这其中有两个比较特殊的日志，其查看的方式比较与众不同，因为这两个日志并不是 ASCII 文件而是被编码成了二进制文件，所以我们并不能直接使用 less、cat、more 这样的工具来查看，这两个日志文件是 wtmp，lastlog。

查看的方法是使用 last 与 lastlog 工具来提取其中的信息

![](https://i.imgur.com/xaCBhpO.png)

## 二、配置的日志

这些日志是如何产生的？通过上面的例子我们可以看出大部分的日志信息似乎格式都很类似，并且都出现在这个文件夹中。

这样的实现可以通过两种方式：

一种是由软件开发商自己来自定义日志格式然后指定输出日志位置；
一种方式就是 Linux 提供的日志服务程序，而我们这里系统日志是通过 syslog 来实现，提供日志管理服务。
syslog 是一个系统日志记录程序，在早期的大部分 Linux 发行版都是内置 syslog，让其作为系统的默认日志收集工具，虽然时代的进步与发展，syslog 已经年老体衰跟不上时代的需求，所以他被 rsyslog 所代替了，较新的 Ubuntu、Fedora 等等都是默认使用 rsyslog 作为系统的日志收集工具

rsyslog的全称是 rocket-fast system for log，它提供了高性能，高安全功能和模块化设计。rsyslog 能够接受各种各样的来源，将其输入，输出的结果到不同的目的地。rsyslog 可以提供超过每秒一百万条消息给目标文件。

这样能实时收集日志信息的程序是有其守护进程的，如 rsyslog 的守护进程便是 rsyslogd

因为一些原因本实验环境中默认并没有打开这个服务，我们可以手动开启这项服务，然后来查看

	sudo apt-get update
	sudo apt-get install -y rsyslog
	sudo service rsyslog start
	ps aux | grep syslog


既然它是一个服务，那么它便是可以配置，为我们提供一些我们自定义的服务

首先我们来看 rsyslog 的配置文件是什么样子的，而 rsyslog 的配置文件有两个，

- 一个是 /etc/rsyslog.conf：主要是配置的环境，即rsyslog加载什么模块
- 一个是 /etc/rsyslog.d/50-default.conf：主要是配置的Filter Conditions。

/etc/rsyslog.conf
![](https://i.imgur.com/1lDH2Ei.png)

/etc/rsyslog.d/50-default.conf
![](https://i.imgur.com/BqEWyDg.png)

看看 rsyslog 的结构框架，数据流的走向，如下图：

![](https://i.imgur.com/YoF835G.png)

通过这个简单的流程图我们可以知道 rsyslog 主要是由 Input、Output、Parser 这样三个模块构成的，并且了解到数据的简单走向，首先通过 Input module 来收集消息，然后将得到的消息传给 Parser module，通过分析模块的层层处理，将真正需要的消息传给 Output module，然后便输出至日志文件中。

![](https://i.imgur.com/LXDCZe2.png)

Rsyslog 架构如图中所示，从图中我们可以很清楚的看见，rsyslog 还有一个核心的功能模块便是 Queue，也正是因为它才能做到如此高的并发。

第一个模块便是 Input，该模块的主要功能就是从各种各样的来源收集 messages。而Output 中也有许多可用的接口，可以通过 man 或者官方的文档查看。

配置中 rsyslog 支持三种配置语法格式：

sysklogd
legacy rsyslog
RainerScript
sysklogd 是老的简单格式，一些新的语法特性不支持。而 legacy rsyslog 是以 dollar 符($)开头的语法，在 v6 及以上的版本还在支持，就如上文所说的 $ModLoad 还有一些插件和特性只在此语法下支持。而以 $ 开头的指令是全局指令，全局指令是 rsyslogd 守护进程的配置指令，每行只能有一个指令。 RainnerScript 是最新的语法。在官网上 rsyslog 大多推荐这个语法格式来配置

老的语法格式（sysklogd & legacy rsyslog）是以行为单位。新的语法格式（RainnerScript）可以分割多行。

注释有两种语法:

- 井号 #
- C-style /* .. */

执行顺序: 指令在 rsyslog.conf 文件中是从上到下的顺序执行的。

模板是 rsyslog 一个重要的属性，它可以控制日志的格式，支持类似 template() 语句的基于 string 或 plugin 的模板，通过它我们可以自定义日志格式。

legacy 格式使用 $template 的语法，不过这个在以后要移除，所以最好使用新格式 template():，以免未来突然不工作了也不知道为什么

模板定义的形式有四种，适用于不同的输出模块，一般简单的格式，可以使用 string 的形式，复杂的格式，建议使用 list 的形式，使用 list 的形式，可以使用一些额外的属性字段（property statement）

如果不指定输出模板，rsyslog 会默认使用 RSYSLOG_DEFAULT。

了解了 rsyslog 环境的配置文件之后，再看向 /etc/rsyslog.d/50-default.conf 这个配置文件，这个文件中主要是配置的 Filter Conditions，也就是我们在流程图中所看见的 Parser & Filter Engine,它的名字叫 Selectors 是过滤 syslog 的传统方法，他主要由两部分组成，facility 与 priority，其配置格式如下:

	facility.priority　　　　　log_location

其中一个 priority 可以指定多个 facility，多个 facility 之间使用逗号 , 分割开,具体可见上图


rsyslog 通过 Facility 的概念来定义日志消息的来源，以便对日志进行分类，Facility 的种类有：


| kern	| 内核消息 | 
| user	| 用户信息	| 
| mail	| 	邮件系统消息	| 
| daemon	| 系统服务消息	| 
| auth	| 认证系统	| 
| authpriv	| 权限系统	| 
| syslog	| 日志系统自身消息	| 
| cron	| 计划安排	| 
| news	| 新闻信息	| 
| local0~7	| 由自定义程序使用	| 

而另外一部分 priority 也称之为 serverity level，除了日志的来源以外，对统一源产生日志消息还需要进行优先级的划分，而优先级的类别有一下几种：

| 类别	| 解释 | 
| --- | 
| emergency	| 系统已经无法使用了 |  
| alert	| 必须立即处理的问题 | 
| critical	| 很严重了 | 
| error	| 错误 | 
| warning	| 警告信息 | 
| notice	| 系统正常，但是比较重要 | 
| informational	| 正常 |  
| debug	| debug的调试信息 | 
| panic	| 很严重但是已淘汰不常用 |  
| none	| 没有优先级，不记录任何日志消息 | 

![](https://i.imgur.com/BqEWyDg.png)


	auth,authpriv.*       /var/log/auth.log

这里的意思是 auth 与 authpriv 的所有优先级的信息全都输出于 /var/log/auth.log 日志中

而其中有类似于这样的配置信息意思有细微的差别

	kern.*      -/var/log/kern.log

`- `代表异步写入，也就是日志写入时不需要等待系统缓存的同步，也就是日志还在内存中缓存也可以继续写入无需等待完全写入硬盘后再写入。通常用于写入数据比较大时使用。


与日志相关的还有一个还有常用的命令 logger,logger 是一个 shell 命令接口，可以通过该接口使用 Syslog 的系统日志模块，还可以从命令行直接向系统日志文件写入信息。

	#首先将syslog启动起来
	sudo service rsyslog start
	
	#向 syslog 写入数据
	ping 127.0.0.1 | logger -it logger_test -p local3.notice &
	
	#查看是否有数据写入
	sudo tail -f /var/log/syslog


![](https://i.imgur.com/Tba1rR8.png)


从图中我们可以看到我们成功的将 ping 的信息写入了 syslog 中，格式也就是使用的 rsyslog 的默认模板

我们可以通过 man 来查看 logger 的其他用法，

| 参数	| 内容 | 
| --- |
| -i	| 在每行都记录进程 ID | 
| -t	| 添加 tag 标签 | 
| -p	| 设置日志的 facility 与 priority | 


## 三、转储的日志

在本地的机器中每天都有成百上千条日志被写入文件中，更别说是我们的服务器，每天都会有数十兆甚至更多的日志信息被写入文件中，如果是这样的话，每天看着我们的日志文件不断的膨胀，那岂不是要占用许多的空间，所以有个叫 logrotate 的东西诞生了。

logrotate 程序是一个日志文件管理工具。用来把旧的日志文件删除，并创建新的日志文件。我们可以根据日志文件的大小，也可以根据其天数来切割日志、管理日志，这个过程又叫做“转储”。

大多数 Linux 发行版使用 logrotate 或 newsyslog 对日志进行管理。logrotate 程序不但可以压缩日志文件，减少存储空间，还可以将日志发送到指定 E-mail，方便管理员及时查看日志。

显而易见，logrotate 是基于 CRON 来运行的，其脚本是 /etc/cron.daily/logrotate；同时我们可以在 /etc/logrotate 中找到其配置文件

	cat /etc/logrotate.conf

![](https://i.imgur.com/fkmBH7L.png)


	# see "man logrotate" for details  //可以查看帮助文档
	# rotate log files weekly
	weekly                             //设置每周转储一次(daily、weekly、monthly当然可以使用这些参数每天、星期，月 )
	# keep 4 weeks worth of backlogs
	rotate 4                           //最多转储4次
	# create new (empty) log files after rotating old ones
	create                             //当转储后文件不存在时创建它
	# uncomment this if you want your log files compressed
	compress                          //通过gzip压缩方式转储（nocompress可以不压缩）
	# RPM packages drop log rotation information into this directory
	include /etc/logrotate.d           //其他日志文件的转储方式配置文件，包含在该目录下
	# no packages own wtmp -- we'll rotate them here
	/var/log/wtmp {                    //设置/var/log/wtmp日志文件的转储参数
	    monthly                        //每月转储
	    create 0664 root utmp          //转储后文件不存在时创建它，文件所有者为root，所属组为utmp，对应的权限为0664
	    rotate 1                       //最多转储一次
	}


当然在 /etc/logrotate.d/ 中有各项应用的 logrotate 配置，还有更多的配置参数，大家可以使用 man 查看，如按文件大小转储，按当前时间格式命名等等参数配置。