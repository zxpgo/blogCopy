---
title: Python2.x和Python3.x的版本区别
date: 2018-08-22 22:28:50
tags:  Python

---

Python 3.0在设计的时候没有考虑向下相容。许多针对早期Python版本设计的程式都无法在Python 3.0上正常执行。<!--more-->

[pyython3.x和python2.x的版本区别](https://www.zxpblog.cn/2019/08/22/python2.x和python3.x版本区别/)

为了照顾现有程式，Python 2.6作为一个过渡版本，基本使用了Python 2.x的语法和库，同时考虑了向Python 3.0的迁移，允许使用部分Python 3.0的语法与函数。

目前不支援Python 3.0的第三方库有Twisted, py2exe, PIL等。大多数第三方库都正在努力地相容Python 3.0版本。即使无法立即使用Python 3.0，也建议编写相容Python 3.0版本的程式，然后使用Python 2.6, Python 2.7来执行。


## print

python 3.0 中使用print()函数来输出，而不再使用print语句。
	
	print("zxp") #python3.0
	print "zxp" #python3.0不再支持

在python2.6和python2.7中，三种形式等价：

	print("zxp")# print不能带任何参数
	print ("zxp") #print后有空格
	print "zxp"

python2.6实际已经支持print()语法：

	seq = 'hello'
	print('zxp','panda',seq)

## Unicode

python2中有ASCII str()类型，unicode()是单独的，不是byte类型。<!--more-->
python3中有Unicode(utf-8)字符串，以及一个字节类：byte和bytearrays。

python3.x源码文件默认使用utf-8:

	>>>中国 = “china”
	>>>print(中国)
	china

而在python2.x中是语法错误</br>

python2.x

	>>> 中国 = "china"
	SyntaxError: invalid syntax
	>>> str = "我是霸王"
	>>> str
	'\xce\xd2\xca\xc7\xb0\xd4\xcd\xf5'
	>>> print str
	我是霸王
	>>> str = u'我是霸王'
	>>> str
	u'\u6211\u662f\u9738\u738b'

python3.x

	>>> str = "我是霸王"
	>>> str
	'我是霸王'
	>>> print(str)
	我是霸王

## 除法运算

python中除法包括两个运算符，`/`和`//`

python2.x中的`/`除法，跟java、c相同，整数相除结果是一个整数，把小数忽略掉。浮点数的除法会保留小数部分，得到一个浮点数结果。

python3.x中`/`除法对于整数之间相除，结果也会是浮点数

python2.x

	>>> 3/2
	1

python3.x

	>>> 3/2
	1.5


而对于`//`除法，被称为floor除法(python3.x中的floor)，会对除法的结果自动进行一个floor操作，在python2.x和python3.x中是一致的

	>>> 5//3
	1

python3.x中floor操作：向下取整，小数不存在

	>>> import math
	>>> math.floor(2.2)
	2

python2.x中floor操作：向下取整，小数存在，仍然是浮点数

	>>> math.floor(3.2)
	3.0

如果需要取整，需要`trunc`函数

	>>> import math
	>>> math.trunc(5/3)
	1

## 异常

在 Python 3 中我们现在使用 as 作为关键词。捕获异常的语法由 except exc, var 改为 except exc as var。

使用语法except (exc1, exc2) as var可以同时捕获多种类别的异常。 Python 2.6已经支持这两种语法。

- 1在2.x时代，所有类型的对象都是可以被直接抛出的，在3.x时代，只有继承自BaseException的对象才可以被抛出。
- 2.x raise语句使用逗号将抛出对象类型和参数分开，3.x取消了这种奇葩的写法，直接调用构造函数抛出对象即可。
 
## 创建迭代对象

在Python 2 中 xrange() 创建迭代对象的用法是非常流行的。比如： for 循环或者是列表/集合/字典推导式。这个表现十分像生成器（比如。"惰性求值"）。但是这个 xrange-iterable 是无穷的，意味着你可以无限遍历。由于它的惰性求值，如果你不得仅仅不遍历它一次，xrange() 函数 比 range() 更快（比如 for 循环）。尽管如此，对比迭代一次，不建议你重复迭代多次，因为生成器每次都从头开始。

在 Python 3 中，range() 是像 xrange() 那样实现以至于一个专门的 xrange() 函数都不再存在（在 Python 3 中 xrange() 会抛出命名异常）。

python2.x中使用xrange()和range()

	>>> range(1,10,2)
	[1, 3, 5, 7, 9]
	>>> xrange(1,10,2)
	xrange(1, 11, 2)
	>>> list(xrange(1,10,2))
	[1, 3, 5, 7, 9]

python3.x中使用range(),内容包含了xrange()

	>>> xrange(10)
	Traceback (most recent call last):
	  File "<pyshell#0>", line 1, in <module>
	    xrange(10)
	NameError: name 'xrange' is not defined
	>>> range(10)
	range(0, 10)
	>>> list(range(1,10))
	[1, 2, 3, 4, 5, 6, 7, 8, 9]

## 八进制字面量表示

八进制数必须写成0o777，原来的形式0777不能用了；二进制必须写成0b111。

新增了一个bin()函数用于将一个整数转换成二进制字串。 Python 2.6已经支持这两种语法。

在Python 3.x中，表示八进制字面量的方式只有一种，就是0o1000。

	>>> 0777
	SyntaxError: invalid token
	>>> 0o777
	511

python2.x

	>>> 0777
	511
	>>> 0o777
	511

## 不等于运算符

Python 2.x中不等于有两种写法 != 和 <>

Python 3.x中去掉了<>, 只有!=一种写法，还好，我从来没有使用<>的习惯

## 数据类型

python3.x中不再有long类型，只有一种整性int,当它的行为跟python2.x的long相同

python3.x中，字符串和bytes类型彻底分开了。字符串是以字符为单位进行处理的，bytes类型是以字节为单位处理的。

### bytes
bytes数据类型在所有的操作和使用甚至内置方法上和字符串数据类型基本一样，也是不可变的序列对象。

bytes对象只负责以二进制字节序列的形式记录所需记录的对象，至于该对象到底表示什么（比如到底是什么字符）则由相应的编码格式解码所决定。Python3中，bytes通常用于网络数据传输、二进制图片和文件的保存等等。可以通过调用bytes()生成bytes实例，其值形式为 b'xxxxx'，其中 'xxxxx' 为一至多个转义的十六进制字符串（单个 x 的形式为：\x12，其中\x为小写的十六进制转义字符，12为二位十六进制数）组成的序列，每个十六进制数代表一个字节（八位二进制数，取值范围0-255），对于同一个字符串如果采用不同的编码方式生成bytes对象，就会形成不同的值.

	b = b''         # 创建一个空的bytes
	b = byte()      # 创建一个空的bytes
	b = b'hello'    #  直接指定这个hello是bytes类型
	b = bytes('string',encoding='编码类型')  #利用内置bytes方法，将字符串转换为指定编码的bytes
	b = str.encode('编码类型')   # 利用字符串的encode方法编码成bytes，默认为utf-8类型
	
	bytes.decode('编码类型')：将bytes对象解码成字符串，默认使用utf-8进行解码。

对于bytes，我们只要知道在Python3中某些场合下强制使用，以及它和字符串类型之间的互相转换，其它的基本照抄字符串。

简单的省事模式：

`string = b'xxxxxx'.decode()` 直接以默认的utf-8编码解码bytes成string

`b = string.encode()` 直接以默认的utf-8编码string为bytes



转自：[http://www.runoob.com/python/python-2x-3x.html](http://www.runoob.com/python/python-2x-3x.html)
[https://www.cnblogs.com/R-bear/p/7744454.html](https://www.cnblogs.com/R-bear/p/7744454.html)