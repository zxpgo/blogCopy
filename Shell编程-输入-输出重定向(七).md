---

title: Shell编程-输入/输出重定向(七)
date: 2019-03-28 15:18:14
tags: Shell
mathjax: true

---

大多数 UNIX 系统命令从你的终端接受输入并将所产生的输出发送回​​到您的终端。一个命令通常从一个叫标准输入的地方读取输入，默认情况下，这恰好是你的终端。同样，一个命令通常将其输出写入到标准输出，默认情况下，这也是你的终端。<!--more-->

重定向命令列表如下：

|命令|说明|
|--|--|
|command>file|将输出重定向到file|
|command<file|将输入重定向到file|
|command>>file|将输出以追加的方式重定向到file|
|n>file|将文件描述符为n的文件重定向到file|
|n>>file|将文件描述符为n的文件以追加的方式重定向到file|
|n>& m|将输出文件m和n合并|
|n<& m|将输入文件m和n合并|
|<<tag|将开始标记tag和结束标记tag之间的内容作为输入|

注意：文件描述符0通常是标准输入(STDIN)，1是标准输出(STDOUT)，2是标准错误输出(STDERR)。

## 输出重定向

重定向一般通过再命令将插入特定的符号来实现。特别的，这些符号的语法如下所示：

	command > file1

上述这个命令执行command然后将输出的内容存入file1。

注意任何file1内的已经存在的内容将被新内容替代。如果要将新内容添加到文件末尾，请使用>>操作符。

实例：

执行下面的who命令，它将命令的完整的输出重定向再用户文件中（users）:

	[root@localhost bash]# who > users
	[root@localhost bash]# cat users
	roo      :0           2019-03-20 09:27 (:0)
	roo      pts/0        2019-03-20 09:28 (:0)
	[root@localhost bash]# who >> users
	[root@localhost bash]# cat users
	roo      :0           2019-03-20 09:27 (:0)
	roo      pts/0        2019-03-20 09:28 (:0)
	roo      :0           2019-03-20 09:27 (:0)
	roo      pts/0        2019-03-20 09:28 (:0)


### 输入重定向

和输出重定向一样，Unix命令也可以从文件获取输入，语法为：

	command < file1

这样，本来需要从键盘获取输入的命令会转移到文件读取内容。

注意：输出重定向是大于号(>)，输入重定向是小于号(<)。

实例：

接着以上实例，我们需要统计users文件的行数，执行以下命令：

	[root@localhost bash]# wc -l  users
	4 users

也可以将输入重定向到users文件：

	[root@localhost bash]# wc -l < users
	4

注意：上面两个例子的结果不同：第一个例子，会输出文件名；而第二个不会，因为它仅仅知道从标准输入读取内容。

	command1 < infile > outfile

同时替换输入和输出，执行command1，从文件infile读取内容，然后将输出写入到outfile中。

### 重定向深入理解

一般情况下，每个Unix/Linux命令运行都会打开三个文件：

- 标准输入文件(stdin)：stdin的文件描述符为0，Unix程序默认从stdin读取数据。
- 标准输出文件(stdout)：stdout的文件描述符为1，Unix程序默认向stdout输出数据。
- 标准错误文件(stderr)：stderr的文件描述符为2，Unix程序回想stderr流中写入错误信息。

默认情况下，command > file将stdout重定向到file，command < file将stdin重定向到file。

如果希望stderr重定向到file，可以这样写：

	command 2 > file

如果希望stderr追加到file文件末尾，可以这样写：

	command 2 >> file

如果希望将stdout和stderr合并后重定向到file，可以这样写：

	command > file  2>&1

	或者

	command >> file 2>&1


如果希望对stdin和stdout都重定向，可以这样写：

	command < file1 > file2 

command 命令将 stdin 重定向到 file1，将 stdout 重定向到 file2。


### Here Document

Here Document是shell中的一种特殊的重定向方式，用来将输入重定向到一个交互式shell脚本或程序。

它的基本形式如下：

	command << delimiter
		document
	delimiter

它的作用是将两个delimiter之间的内容（document）作为输入传递给command。

注意：

- 结尾的delimiter一定要顶格写，前面不能有任何字符，后面也不能有任何字符，包括空格和tab缩进。
- 开始的delimiter前后的空格会被忽略掉。

实例：

在命令行中通过 wc -l命令计算Here Document的行数：

	[root@localhost bash]# wc -l << EOF
	> Hello 
	> World!
	> zxpblog
	> EOF
	3

也快车将Here Document用在脚本中，例如：

	#! /bin/bash
	
	cat << EOF
	Hello World!
	zxpblog
	EOF
	
	wc -l << EOF
	Hello world
	zxpblo
	EOF


输出：

	Hello World!
	zxpblog
	2

### /dev/null文件

如果希望执行某个命令，但又不希望在屏幕上显示输出结果，那么可以将输出重定向到/dev/null:

	command > /dev/null

/dev/null是一个特殊的文件，写入到它的内容都会被丢弃；如果尝试从该文件读取内容，那么什么也读不到。但是/dev/null文件非常有用，将命令的输出重定向到它，会起到禁止输出的效果。

如果希望屏幕屏蔽stdout和stderr，可以这样写：

	command > /dev/null 2>&1

注意： 0 是标准输入（STDIN），1 是标准输出（STDOUT），2 是标准错误输出（STDERR）。


	

	