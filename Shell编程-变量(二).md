---

title: Shell编程-变量(二)
date: 2019-03-27 18:43:14
tags: Shell
mathjax: true

---

在Linux部分介绍了[变量的概念](https://www.zxpblog.cn/2018/08/16/%E7%8E%AF%E5%A2%83%E5%8F%98%E9%87%8F%E4%B8%8E%E6%96%87%E4%BB%B6%E6%9F%A5%E6%89%BE/)，以及初略的介绍了shell变量和环境变量，这里将深入讨论。
<!--more-->

定义变量时，变量名不加美元符号$(PHP语言中变量需要)，如：

	your_name="zxp";

注意，变量名和等号之间不能有空格，这可能和熟悉的所有编程语言都不一样。同时，变量名的命名虚遵循如下规则：

- 命名只能使用英文字面、数字和下划线，首个字符不能以数字开头；
- 中间不能有空格，可以使用下划线；
- 不能使用标点符号；
- 不能使用bash里的关键字。

有效的shell变量名示例如下：

	zxp
	_zxp
	var1
	_var2

无效的变量名：

	3var
	zxp=
	user*name
	

除了显式地直接赋值外，还可以用语句给变量赋值，如：

	for file in 'ls /etc'
	for file in $(ls /etc)

以上语句将/etc下目录的文件名循环出来

## 使用变量

使用一个定义过的变量，只要在变量名前面加上美元符号即可，如：

	your_name="zxp"
	echo $your_name
	echo ${your_name}

变量名外面的花括号是可选的，加不加都行，加花括号是为了帮助解释器识别变量的边界，比如下面这种操作:

	for skill in Ada Coffe Action Java; do
		echo "I am good at $(skill)Script"
	done

如果不给变量加花括号，写成了`echo "I am good at $skillScript"`，解释器就会把$skillScript当成一个变量（其值为空），代码执行结果就不是我们期望的样子了。

推荐给所有变量加上花括号，这是个好的编程习惯。

已定义的变量，可以被重新定义，如：

	your_name="tom"
	echo $your_name
	your_name="alibaba"
	echo $your_name

这样的写法是合法的，但注意，第二次赋值的时候不能写`$your_name="alibaba"`，使用变量的时候才加美元符

## 只读变量

使用readonle命令可以将变量定义为只读变量，只读变量的值不能被改变。

下面的例子尝试更改只读变量，结果报错：

	#! /bin/bash
	myUrl="https://www.zxpblog.cn"
	readonly myUrl
	myUrl="https://zxpblog.cn"

运行脚本，结果如下：

	./test.sh:行4: myUrl: 只读变量

## 删除变量

使用unset命令可以删除变量，语法：

	unset variable_name

变量被删除后不能再次使用，__unset命令不能删除只读变量__。

实例

	#! /bin/bash
	myUrl="https://www.zxpblog.cn"
	unset myUrl
	echo $myUrl


以上实例执行将没有任何输出。

## 变量类型

运行shell时，会同时存在三种变量：

- 局部变量： 局部变量在脚本或命令中定义，仅在当前shell实例中有效，其他shell启动的程序不能访问局部变量。
- 环境变量： 所有程序，包括shell启动的程序，都能访问环境变量，有些程序需要环境变量来保证其正常运行。必要的时候shell脚本也可以定义环境变量。
- shell变量： shell变量是由shell程序设置的特殊变量，shell变量中有一部分是环境变量，有一部分局部变量，这些变量保证了shell的正常运行。

## shell字符串

字符串是shell编程中最常用最有用的数据类型（除了数字和字符串，也没有啥其他类型好用了），字符串可以用单引号，也可以使用双引号，也可以不用引号，单双引号的区别跟PHP类似。

### 单引号

	str='this is a string'

单引号字符串的限制：

- 单引号里的任何字符都会原样输出，单引号字符串中的变量是无效的；
- 单引号字符串中不能出现单独一个的单引号（对单引号使用转义字符也不行），但可成对出现，作为字符串拼接使用。

### 双引号

	your_name="zxp"
	str="Hello, I know you are \"$your_name\"! \n"
	echo -e $str

输出结果

	Hello, I know you are "zxp"!

双引号的优点：

- 双引号里可以有变量
- 双引号里可以出现转义字符


### 拼接字符串

	your_name="zxp"
	#使用双引号拼接
	greeting="hello, "$your_name"!"
	greeting_1="hello, ${your_name}!"
	echo $greeting $greeting_1
	#使用单引号拼接
	greeting_2='hello, '$your_name' !'
	greeting_3='hello, ${your_name} !'
	echo $greeting_2 $greeting_3


输出：

	hello, zxp! hello, zxp!
	hello, zxp ! hello, ${your_name} !


### 获取字符串长度

	string="abcd"
	echo ${#string}
	#输出4

### 提出子字符串

以下实例从字符串第2个字符开始截取4个字符：

	#extract substring
	string="zxpblog is a gread site"
	echo ${string:1:4}
	#输出xebl


### 查找子字符串

查找字符i或o的位置（哪个字符先出现就计算哪个）：

	#find substring
	string="zxpblog is a gread siet"
	echo `expr index "$string" io`
	#输出6

注意：以上脚本中的`是反引号，而不是单引号'，不要看错了

## shell数组

bash支持一维数组（不支持多维数组），并且没有限定数组的大小。

类似于C语言，数组元素的下表由0开始编号，获取数组中的元素利用下表，下表可以是整数或算术表达式，其值应大于或等于0。

### 定义数组

在shell中，用括号来表示数组，数组元素用空格符号隔开。定义数组的一般形式如下：

	数组名=(值1 值2 ... 值n)

例如，

	array_name=(value0 value1 value2 value3)

或者

	array_name=(
	value0
	value1
	value2
	value3
	)

还可以单独数组的各个分量：

	array_name[0]=value0
	array_name[1]=value1

可是不使用连续的下标，而且下标的范围没有限制。

### 读取数组

读取数组元素值得一般格式是：

	${数组名[下标]}

例如：
	
	value=${array_name[n]}

使用@符号可以获取数组中得所有元素，例如：

	echo ${array_name[@]}

### 获取数组得长度

获取数组长度得方法与获取字符串长度得方法相同，例如：

	array_name=(1 2 3 4)
	echo ${array_name[@]} ${array_name[1]}
	#取得数组元素的个数
	length=${#array_name[@]}
	echo $length
	#取得数组元素的个数
	length=${#array_name[*]}
	echo $length
	#获取数组单个元素的长度
	lengthn=${array_name[n]}
	echo $lengthn


输出：

	1 2 3 4 2
	4
	4
	1


## shell注释

以#开头的行就是注释，会被解释器忽略

通过每一行加上一个#设置多行注释，如下：


	#--------------------------------------------
	# 这是一个注释
	# author：zxpblog
	# site：www.zxpblog.cn
	# slogan：学的不仅是技术，更是梦想！
	#--------------------------------------------
	##### 用户配置区 开始 #####
	#
	#
	# 这里可以添加脚本描述信息
	# 
	#
	##### 用户配置区 结束  #####


每一行加个#符号太费力了，可以把这一段要注释的代码用一对花括号括起来，定义成一个函数，没有地方调用这个函数，这块代码就不会执行，达到了和注释一样的效果。

### 多行注释

多行注释还可以使用以下格式：

	:<<EOF
	注释内容...
	注释内容...
	注释内容...
	EOF

EOF也可以使用其他符号：

	:<<'
	注释内容...
	注释内容...
	注释内容...
	'
	

	:<<!
	注释内容...
	注释内容...
	注释内容...
	!







