---

title: Linux数据流重定向
date: 2018-09-01 15:57:50
tags: Linux
categories: "实验楼学习笔记之Linux"

---

你可能对重定向这个概念感到些许陌生，但你应该在前面的课程中多次见过>或>>操作了，并知道他们分别是将标准输出导向一个文件或追加到一个文件中。这其实就是重定向，将原本输出到标准输出的数据重定向到一个文件中，因为标准输出(/dev/stdout)本身也是一个文件，我们将命令输出导向另一个文件自然也是没有任何问题的。<!--more-->

常用重定向操作：

	echo 'hello shiyanlou' > redirect 
	echo 'www.shiyanlou.com' >> redirect
	cat redirect

![](https://i.imgur.com/llngSgM.png)


## 简单的重定向

Linux 默认提供了三个特殊设备，用于终端的显示和输出，分别为stdin（标准输入,对应于你在终端的输入），stdout（标准输出，对应于终端的输出），stderr（标准错误输出，对应于终端的输出）。

| 文件描述符	| 设备文件 | 说明 | 
|---|
| 0	| /dev/stdin	| 标准输入 | 
| 1	| /dev/stdout	| 标准输出 | 
| 2	| /dev/stderr	| 标准错误 |


>文件描述符：文件描述符在形式上是一个非负整数。实际上，它是一个索引值，指向内核为每一个进程所维护的该进程打开文件的记录表。当程序打开一个现有文件或者创建一个新文件时，内核向进程返回一个文件描述符。在程序设计中，一些涉及底层的程序编写往往会围绕着文件描述符展开。但是文件描述符这一概念往往只适用于 UNIX、Linux 这样的操作系统。 


将cat的连续输出（heredoc方式）重定向到一个文件：

		cat > test.cpp << EOF

输入需要存入test.cpp文件的内容，输入EOF结束

![](https://i.imgur.com/qXfT6xI.png)

将一个文件作为命令的输入，标准输出作为命令的输出：

	cat test.cpp

![](https://i.imgur.com/z7peKkU.png)

将echo命令通过管道传过来的数据作为cat命令的输入，将标准输出作为命令的输出：

	echo 'hi' | cat

![](https://i.imgur.com/pSNOE2h.png)

将echo命令的输出从默认的标准输出重定向到一个普通文件：

	echo "hello world" > redirect
	cat redirect

![](https://i.imgur.com/7aDwgCP.png)
 
## 标准错误重定向


重定向标准输出到文件，这是一个很实用的操作，另一个很实用的操作是将标准错误重定向，标准输出和标准错误都被指向伪终端的屏幕显示，所以我们经常看到的一个命令的输出通常是同时包含了标准输出和标准错误的结果的。比如下面的操作：

	#使用cat同时读取两个文件，其中一个存在，一个不存在
	cat test.c hello.c
	#可以看到上述命令，输出第一个文件的内容，还在末尾处出现了一条错误信息
	#下面将输出重定向到一个文件
	cat test.cpp hello.cpp > somefile

![](https://i.imgur.com/62lQw3N.png)

遗憾的是，这里依然出现了那条错误信息，这正是因为如我上面说的那样，标准输出和标准错误虽然都指向终端屏幕，实际它们并不一样。那有的时候我们就是要隐藏某些错误或者警告，那又该怎么做呢。这就需要用到我们前面讲的文件描述符了：

	#将标准错误重定向到标准输出(2>&1)，再将标准输出重定向到文件，注意要将重定向文件写到前面,2表示标准错误，1表示标准输出，
	cat test.cpp hello.cpp > somefile 2>&1
	#或者只用bash提供的特殊的重定向符号`&`将标准错误和标准输出同时重定向到文件
	cat test.cpp hello.cpp &>somefilehell

**注意**你应该在输出重定向文件描述符前加上&,否则shell会当做重定向到一个文件名为1的文件中

![](https://i.imgur.com/gNJmXCQ.png)

## 使用tee命令同时重定向到多个文件

除了需要将输出重定向到文件,也需要将信息打印在终端。那么你可以使用tee命令来实现：

	echo "hello world;" | tee hello

![](https://i.imgur.com/UxYABqL.png)


## 永久重定向

前面的重定向操作都只是临时性的，即对当前命令有效，如何做到重定向对所有命令都有效呢？比如在一个脚本中，仅需要某一部分的命令全部进行重定向，难道要在每个命令上面加上临时重定向的操作吗？当然不需要，可以使用`exec`命令实现“永久”重定向。`exec`命令的作用是使用指定的命令替换当前的shell，即使用一个进程替换当前进程，或者指定新的重定向。

	#先开启一个子shell
	zsh
	#使用exec替换当前进程的重定向，将标准输出重定向到一个文件
	exec 1>somefile
	#下面执行的命令的输出都将重定向到文件中，直到你退出当前shell，或取消exec的重定向
	ls
	ll
	exit
	cat somefile

![](https://i.imgur.com/ewhmvXI.png)

## 创建输出文件描述符

在shell中有9个文件描述符，前面介绍和使用了默认提供的0、1、2文件描述符。另外还可以使用3-8的文件描述符，只是它们默认没有打开而已。


使用如下命令查看当前shell进程中打开的文件描述符：

	cd /dev/fd/; ls -Al

![](https://i.imgur.com/AjSlCTY.png)

同样，可以使用`exec`命令创建新的文件描述符：

	zsh
	exec 3>somefile
	#先进入目录，在查看，否则可能不能得到正确的结果，然后再回到上一次的目录
	cd /dev/fd/;ls -Al; cd -
	#注意下面的命令>和&之间不应该有空格，如果有空格则会报错
	echo "this a test" >&3
	cat somefile
	exit

![](https://i.imgur.com/6ylRo5r.png)

## 关闭文件描述符

上面打开了3号文件描述符，可以使用如下操作将其关闭：

	exec 3>&-
	cd /dev/fd;ls -Al;cd -

![](https://i.imgur.com/LVQ1iRr.png)

## 完全屏蔽命令的输出

在Linux中有一个被称为“黑洞”的设备文件，所以导入它的数据都将被“吞噬”。

在类 UNIX 系统中，/dev/null，或称空设备，是一个特殊的设备文件，它通常被用于丢弃不需要的输出流，或作为用于输入流的空文件，这些操作通常由重定向完成。读取它则会立即得到一个EOF。

可以实利用`/dev/null`屏蔽命令的输出：

	cat test.cpp hello.cpp 1>/dev/null 2>&1

上面这样的操作将使你得不到任何输出结果。

## 使用xargs分割参数列表

xargs 是一条 UNIX 和类 UNIX 操作系统的常用命令。它的作用是将参数列表转换成小块分段传递给其他命令，以避免参数列表过长的问题

这个命令在有些时候十分有用，特别是当用来处理产生大量输出结果的命令如 find，locate 和 grep 的结果，详细用法请参看 man 文档。

	cut -d: -f1 < /etc/passwd | sprt | xargs echo

上面这个命令用于将/etc/passwd文件按:分割取第一个字段排序后，使用echo命令生成一个列表。

![](https://i.imgur.com/NXaTkqH.png)

