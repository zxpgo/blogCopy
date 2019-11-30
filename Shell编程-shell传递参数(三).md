---

title: Shell编程-shell传递参数(三)
date: 2019-03-27 19:59:14
tags: Shell
mathjax: true

---

我们可以在执行Shell脚本时，向脚本传递参数，脚本内获取参数的格式为： $n， n代表一个数字，1为执行脚本的第一个参数，2为执行脚本的第二个参数，以此类推...<!--more-->

### 实例

以下实例我们向脚本传递三个参数，并分别输出，其中$0为可执行的文件名：
	
	#! /bin/bash
	#author:zxp
	#url: www.zxpblog.cn
	
	echo "example of shell passing parameter";
	echo "name of file: $0";
	echo "First parameter: $1";
	echo "Second parameter: $2";
	echo "Third parameter: $3";



输出：

	example of shell passing parameter
	name of file: ./test.sh
	First parameter: 1
	Second parameter: 2
	Third parameter: 3

另外，还有几个特殊字符用来处理参数：

| 参数处理 | 说明 |
|--|--|
|$#| 传递到脚本的参数个数|
|$*|以一个单字符显式所有向脚本传递的参数|
|$$|脚本运行的当前进程ID|
|$!|后台运行的最后一个进程的ID号|
|$@|与$*相同，但是使用时加引号，并在引号中返回每个参数。以"$1" "$2" … "$n" 的形式输出所有参数。|
|$-|显示shell使用的当前选项，与set命令功能相同|
|$?|显示最后命令的退出状态，0表示没有错误，其他任何表明有错误|


	echo "Number of parameter: $#"
	echo "The first charater is passing parameter: $*"
	echo "$$"


输出：
	./test.sh 1 2 3

	Number of parameter: 3
	The first charater is passing parameter: 1 2 3
	25790