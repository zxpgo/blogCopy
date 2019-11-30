---

title: Shell编程-输出命令echo和printf(四)
date: 2019-03-27 20:18:14
tags: Shell
mathjax: true

---

Shell的echo指令与PHP的echo指令类似，都是用于字符串的输出，命令格式：

	echo string

您可以使用echo实现复杂的输出格式控制。<!--more-->


## echo

### 显示普通字符串

	echo "It is a test"

这里的双引号完全可以省略，以下命令与上面实例效果一致：

	echo It is a test

### 显示转义字符

	echo "\"It is a test\""

通常，双引号也可以省略

	echo "\It is a test\"

### 显示变量

read命令从标准输入中读取一行，并把输入行的每个字段的值都指定给shell变量：

	#! /bin/bash
	echo It is a test
	echo "\It is a test\"
	read name
	echo $name It is a test

结果：

	It is a text
	"It is a test"
	OK   #输入内容
	OK It is a text


### 显示换行

	-e 开启转义
	echo -e "OK! \n It is a test"
	
输出：

	OK! 
	 It is a test

### 显示不换行

	#！ /bin/bash
	echo -e "OK! \c"
	echo "It is a test"

输出：

	OK! It is a test

## 显示结果定向至文件

	echo "It is a test" > profile

## 原样输出字符串，不进行转义或区变量(用单引号)

	echo '$name\"'

输出：
	$name\"

## 显示命令执行时间
	
	echo `date`

注意，这里的是反引号`，而不是单引号'

输出：

	2019年 03月 27日 星期三 20:29:13 CST

## printf

printf 命令模仿 C 程序库（library）里的 printf() 程序。

printf 由 POSIX 标准所定义，因此使用 printf 的脚本比使用 echo 移植性好。

printf 使用引用文本或空格分隔的参数，外面可以在 printf 中使用格式化字符串，还可以制定字符串的宽度、左右对齐方式等。默认 printf 不会像 echo 自动添加换行符，我们可以手动添加 \n。

printf 命令的语法：

	printf format-string [arguments...]

参数说明：

- format-string： 为格式控制字符串
- arguments：为参数列表

实例如下：

	#! /bin/bash
	printf "%-10s %-8s %-4s\n" name sex weight/kg
	printf "%-10s %-8s %-4s\n" Alice Male 66.124
	printf "%-10s %-8s %-4s\n" Bob Female 48.555


输出：

	name       sex      weight/kg
	Alice      Male     66.124
	Bob        Female   48.555

%s %c %d %f都是格式替代符

%-10s 指一个宽度为10个字符（-表示左对齐，没有则表示右对齐），任何字符都会被显示在10个字符宽的字符内，如果不足则自动以空格填充，超过也会将内容全部显示出来。

%-4.2f 指格式化为小数，其中.2指保留2位小数。


更多实例：
	
	printf "%d %s \n" 1 "abc"
	
	printf '%d %s \n' 1 "abc"
	
	printf %s abcdef
	
	printf %s abc def
	
	printf "%s \n" abc def
	
	printf "%s %s %s\n" a b c d e f
	
	printf "%s and %d\n"


输出：


	1 abc 
	1 abc 
	abcdefabcdefabc 
	def 
	a b c
	d e f
	 and 0


### printf的转义序列

![](https://i.imgur.com/t2ZfwbY.png)

