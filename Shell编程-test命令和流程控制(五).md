---

title: Shell编程-test命令和流程控制(五)
date: 2019-03-27 20:18:14
tags: Shell
mathjax: true

---

Shell中的 test 命令用于检查某个条件是否成立，它可以进行数值、字符和文件三个方面的测试。<!--more-->


## test命令

### 数值测试


|参数 | 说明|
|--|--|
|-eq|等于则为真|
|-ne|不等于则为真|
|-gt|大于则为真|
|-ge|大于等于则为真|
|-lt|小于则为真|
|-le|小于等于则为真|

实例演示：

	#!/bin/bash

	num1=100
	num2=100
	
	if test $[num1] -eq $[num2]
	then 
		echo "two number are equal"
	else 
		echo "two number are nequal"
	fi

输出：

	two number are equal


代码中的[]执行基本的算数运算符，如：

	a=5
	b=6
	
	result=$[a+b]
	echo "result is: $result"

输出：

	result is: 11

## 字符串测试

|参数|说明|
|--|--|
|=|等于则为真|
|!=|不相等则为真|
|-z 字符串|字符串的长度为零则为真|
|-n 字符串|字符串的长度不为零则为真|

实例演示：

	num1="zxp"
	num2="zxp2"
	if test $num1 = $num2
	then 
		echo "two string are equal"
	else
		echo "two string are nequal"
	fi

输出：

	two string are nequal


## 文件测试


|参数|说明|
|--|--|
|-e 文件名	|如果文件存在则为真|
|-r 文件名	|如果文件存在且可读则为真|
|-w 文件名	|如果文件存在且可写则为真|
|-x 文件名	|如果文件存在且可执行则为真|
|-s 文件名	|如果文件存在且至少有一个字符则为真|
|-d 文件名	|如果文件存在且为目录则为真|
|-f 文件名	|如果文件存在且为普通文件则为真|
|-c 文件名	|如果文件存在且为字符型特殊文件则为真|
|-b 文件名	|如果文件存在且为块特殊文件则为真|

实例演示：

	cd /bin
	if test -e ./bash
	then 
		echo "file is existing!"
	else
		echo "fiel is not existing!"
	fi


输出：
	
	file is existing!


## 流程控制

和Java、PHP等语言不一样，sh的流程控制不可为空，如PHP流程控制写法：

	<?php
	if (isset($_GET["q"])){
		search(q);
	}
	else
		//不做任何事
	>

在sh/bash里可不能这么写，如果else分支没有语句执行，就不要写这个else。

### if else

if语句语法格式：

	if condition
	then 
		command1
		command2
		...
	fi

写成一行（适用于终端命令提示符）：

	if [$(ps -ef | grep -c "ssh") -gt 1]; then echo "true"; fi

末尾的fi就是if倒过来拼写，后面还会遇到类似的。

if else语法格式：

	if condition
	then 
		command1
		command2
		...
	else
		command
	fi

if else-if else语法格式：

	if condition1
	then 
		command1
	elif condition2
	then
		command2
	else
		commandN
	fi

以下示例判断两个变量是否相等：

	a=10
	b=20
	if [ $a == $b ]
	then 
		echo "a = b"
	elif [ $a -gt $b ]
	then
		echo "a > b"
	elif [ $a -lt $b ]
	then
		echo "a < b"
	else
		echo "no"
	fi

输出：
	
	a < b

if else语句经常与test命令结合使用，如下所示：

	num1=$[2*3]
	num2=$[1+5]
	if test $[num1] -eq $[num2]
	then
		echo "two number are equal"
	else
		echo "two number are nequal"
	fi

输出：

	two number are equal


### for循环

语法：

	for var in item1 item2 ... itemN
	do 
		command1
	done

写成一行：

	for var in item1 item2 ... itemN; do command1; command2 done;

当变量值在列表里，for循环即执行一次所有命令，使用变量名获取列表中的当前取值。命令可为任何有效的shell命令和语句。in列表可以包含替换、字符串和文件名。

in列表是可选的，如果不用它，for循环使用命令行的位置参数。

例如，顺序输出当前列表中的数字：

	for loop in 1 2 3 4 5
	do 
		echo "The value is $loop"
	done


输出：
	
	The value is 1
	The value is 2
	The value is 3
	The value is 4
	The value is 5

顺序输出字符串：

	for str in 'This is a string'
	do 
		echo $str
	done

输出：

	This is a string


### while语句

语法：

	while condition
	do 
		command
	done

以下是一个基本的while循环，测试条件是：如果int小于等于5，那么条件返回真。int从0开始，int加1.运行上述脚本，返回数字1到5，然后终止：

	#! /bin/bash
	int=1
	while(($int<=5))
	do 
		echo $int
		let "int++"
	done


输出
	
	1
	2
	3
	4
	5
使用中使用了 Bash let 命令，它用于执行一个或多个表达式，变量计算中不需要加上 $ 来表示变量

while循环可用于读取键盘信息。下面的例子中，输入信息被设置为变量FILM，按<Ctrl-D>结束循环。


echo 'input <CTRL-D> to quit:'
echo -n 'input your favorite websiet: '
while read FILM
do 
	echo "Yes, $FILM is good website"
done

输出：

input <CTRL-D> to quit:
input your favorite websiet: www.zxpblog.cn
Yes, www.zxpblog.cn is good website


### 无限循环

无限循环语法格式：

	while:
	do
		command
	done

或者：
	
	while true
	do
		command
	done

或者

	for ((;;))
	

### until循环

until循环执行一系列命令直到条件为true时停止。

until循环与while循环在处理方式上刚好相反。

一般while循环优于until循环，但在某些时候——也只是极少数情况下，until循环更加有同。

until语法格式：

	until condition
	do
		command
	done

condition一般为条件表达式，如果返回值为true，则继续机型，否则跳出循环。

以下实例我们使用until名来来输出0~9的数字：

		
	a=0
	
	until [ ! $a -lt 10 ]
	do 
		echo $a
		a=`expr $a + 1`
	done

输出：

	0
	1
	2
	3
	4
	5
	6
	7
	8
	9


### case

Shell case语句为多选择语句，可以用case语句匹配一个值与一个模式，如果匹配成功，执行相匹配的命令。case语句格式如下：

	case 值 in
		模式1)
			command1
		;;
		模式2)
			command2
		;;
		*)
			command3
		;;
	esac

case工作方式如上所示，取值后面必须为单词in，每一个模式必须以右括号结束。取值可以为变量或常量。匹配发现取值符合某一模式后，其间所有命令开始执行直至;;。

取值将检测匹配的每一个模式。一旦模式匹配，则执行匹配模式相应命令后不再继续其他模式。如果无一匹配模式，使用星号捕获该值，再执行后面的命令。

下面的脚本提示输入1到4，与每一种模式进行匹配：


	echo "input number(1-4):"
	echo "The number that you input is: "
	read aNum
	case $aNum in
		1) echo "1"
		;;
		2) echo "2"
		;;
		3) echo "3"
		;;
		4) echo "4"
		;;
		*) echo "other"
		;;
	esac

输出：

	ut number(1-4):
	The number that you input is: 
	3
	3


### 跳出循环

在循环过程中，有时候需要在未达到循环结束条件时强制跳出循环，Shell使用两个命令来实现该功能：break和continue。

#### break命令

break命令允许跳出所有循环（终止执行后面的所有循环）。

下面的例子中，脚本进入死循环直至用户输入数字大于5。要跳出这个循环，返回到shell提示符下，需要使用break命令。

	while :
	do
		echo -n "input number(1~5): "
		read aNum
		case $aNum in
			1|2|3|4|5) echo "The number is $aNum !"
			;;
			*) echo "other, game is over!"
				break
			;;
		esac
	done

输出：

	input number(1~5): 3
	The number is 3 !
	input number(1~5): 5
	The number is 5 !
	input number(1~5): 7
	other, game is over!


#### continue

continue命令与break命令类似，只有一点差别，它不会跳出所有循环，仅仅跳出当前循环。

对上面的例子进行修改：

	while true
	do 
		echo -n "input number(1-5): "
		read aNum
		case $aNum in
			1|2|3|4|5) echo "The number is $aNum !"
			;;
			*) echo "other"
				continue
				echo "Game is over!"
			;;
		esac
	done


### esac


case的语法和C family语言差别很大，它需要一个esac（就是case反过来）作为结束标记，每个case分支用右圆括号，用两个分号表示break。





