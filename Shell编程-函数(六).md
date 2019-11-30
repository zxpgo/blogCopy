---

title: Shell编程-函数(六)
date: 2019-03-28 12:18:14
tags: Shell
mathjax: true

---

Linux shell可以用户定义函数，然后再shell脚本中可以随意调用。<!--more-->

shell中函数的定义格式如下：

	[ function ] funname [()]
	{
		action;
		[ return int;]
	}

说明：

- 可以带function fun()定义，也可以直接fun()定义不带任何参数。
- 参数返回，可以显示加：return 返回，如果不加，将以最后一条命令运行结果，作为返回值。return后跟数值n(0~255)。

下面的例子定义了一个函数并进行调用：


	#! /bin/bash
	
	demoFun(){
		echo "This is a shell function!"
	}
	
	echo "----Begin act function----"
	demoFun
	echo "----End of function----"

输出：

	----Begin act function----
	This is a shell function!
	----End of function----

下面定义一个带有return语句的函数：

	funWithReturn(){
		echo "The function will add two number that you input: "
		echo "Input first number: "
		read aNum
		echo "Input second number: "
		read anotherNum
		echo "Two numbers are $aNum and $anotherNum !"
		return $(($aNum+$anotherNum))
	}
	funWithReturn
	echo "The result is $? !"


输出：

	The function will add two number that you input: 
	Input first number: 
	4
	Input second number: 
	3
	Two numbers are 4 and 3 !
	The result is 7 !

### 函数参数

在Shell中，调用函数时可以向其传递参数。在函数体内部，通过 $n 的形式来获取参数的值，例如，$1表示第一个参数，$2表示第二个参数...

带参数的函数示例：


	funWithParam(){
		echo "First parameter: $1 !"
		echo "Second parameter: $2 !"
		echo "Tenth parameter: $10 !"
		echo "Tenth parameter: ${10} !"
		echo "The number of parameter: $# !"
		echo "Outpur all paramter: $* !"
	}
	
	funWithParam 1 2 3 3 4 5 6 7 8 9 10 11


输出：

	First parameter: 1 !
	Second parameter: 2 !
	Tenth parameter: 10 !
	Tenth parameter: 9 !
	The number of parameter: 12 !
	Outpur all paramter: 1 2 3 3 4 5 6 7 8 9 10 11 !

注意，$10 不能获取第十个参数，获取第十个参数需要${10}。当n>=10时，需要使用${n}来获取参数。

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

