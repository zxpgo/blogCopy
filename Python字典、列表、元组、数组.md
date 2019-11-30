---
title: Python字典、列表、元组、数组
date: 2018-09-27 20:51:50
tags:  Python

---

介绍Python字典、列表、元组、数组的函数和方法。<!--more-->

## 一、字典

### 1 、内置函数

	1、cmp(dict1, dict2)
	比较两个字典元素。
	
	2、len(dict)
	计算字典元素个数，即键的总数。
	
	3、str(dict)
	输出字典可打印的字符串表示。
	
	4、type(variable)
	返回输入的变量类型，如果变量是字典就返回字典类型。

### 2、内置方法

	1、dict.clear()
	删除字典内所有元素
	
	2、dict.copy()
	返回一个字典的浅复制
	
	3、dict.fromkeys(seq[, val])
	创建一个新字典，以序列 seq 中元素做字典的键，val 为字典所有键对应的初始值
	
	4、dict.get(key, default=None)
	返回指定键的值，如果值不在字典中返回default值
	
	5、dict.has_key(key)
	如果键在字典dict里返回true，否则返回false
	
	6、dict.items()
	以列表返回可遍历的(键, 值) 元组数组
	
	7、dict.keys()
	以列表返回一个字典所有的键
	8、dict.setdefault(key, default=None)
	和get()类似, 但如果键不存在于字典中，将会添加键并将值设为default
	
	9、dict.update(dict2)
	把字典dict2的键/值对更新到dict里
	
	10、dict.values()
	以列表返回字典中的所有值
	
	11、pop(key[,default])
	删除字典给定键 key 所对应的值，返回值为被删除的值。key值必须给出。 否则，返回default值。
	
	12、popitem()
	随机返回并删除字典中的一对键和值

**数组转换为字典：**

1、现在有两个列表，list1 = ['key1','key2','key3']和list2 = ['1','2','3']，把他们转为这样的字典：{'key1':'1','key2':'2','key3':'3'}

	list1 = ['key1','key2','key3']
	
	list2 = ['1','2','3']
	
	list2 = range(len(list1))
	
	dict(zip(list1,list2))
	
	{'key1':'1','key2':'2','key3':'3'}

2、将嵌套列表转为字典，有两种方法，

	new_list= [['key1','value1'],['key2','value2'],['key3','value3']]
	
	dict(list)
	
	{'key3': 'value3', 'key2': 'value2', 'key1': 'value1'}


## 二、列表

**列表初始化**

1.初始化递增的list：

	list1 = range(10)
	print(list1)
		[0,1,2,...,9]

2.初始化每项为0的一维数组：

	list2 = [0] * 5
	print(list2)
		[0,0,0,0,0]


3.初始化固定值的一维数组：

	initVal = 1
	listLen = 5
	list3 = [ initVal for i in range(5)]
	print(list3)
		[1,1,1,1,1]
	list4 = [initVal] * listLen
	print(list4)
		[1,1,1,1,1]

4.初始化一个5x6每项为0（固定值）的数组（推荐使用）：

	multilist = [[0 for col in range(5)] for row in range(6)]

5.初始化一个5x6每项为0（固定值）的数组

	multilist = [[0] * 5 for row in range(3)]

 

看到了以上的方法，那初始化一个二维数组时，是否可以这样做呢：

	multi = [[0] * 5] * 3

其实，这样做是不对的，因为`[0] * 5`是一个一维数组的对象，`* 3`的话只是把对象的引用复制了3次，比如，我修改multi[0][0]：

	multi = [[0] * 5] * 3
	multi[0][0] = 'Hello'
	print(multi)

输出的结果将是：

	[['Hello', 0, 0, 0, 0], ['Hello', 0, 0, 0, 0], ['Hello', 0, 0,0, 0]]

我们修改了multi[0][0]，却把我们的multi[1][0]，multi[2][0]也修改了。这不是我们想要的结果。

但是如下写法是对的：

	multilist = [[0] * 5 for row in range(3)]
	multilist[0][0] = 'Hello'
	print(multilist)

我们看输出结果：

	[['Hello', 0, 0, 0, 0], [0, 0, 0, 0, 0], [0, 0, 0, 0, 0]]

恩，没问题。但是，由于使用 * 的方法比较容易引起混淆导致Bug，所以还是推荐使用上面方法4，即：

	multilist = [[0 for col in range(5)] for row in range(6)]

## 三、元组

### 初始化

	L = 3, 4, 5
	或 L = (3,4,5)
 
	del(L) //删除元组
	(1,2) + (3,4) //得到一个新的元组

### 元组索引，截取

因为元组也是一个序列，所以我们可以访问元组中的指定位置的元素，也可以截取索引中的一段元素，如下所示：

元组：

	L = ('spam', 'Spam', 'SPAM!')

| Python 表达式 |	结果	 | 描述 |
|---|
| L[2]	| 'SPAM!'	| 读取第三个元素 |
| L[-2]	| 'Spam'	| 反向读取，读取倒数第二个元素 |
| L[1:]	| ('Spam', 'SPAM!')	| 截取元素 |

### 内置函数
 
	cmp(tuple1, tuple2)
	比较两个元组元素。

	len(tuple)
	计算元组元素个数。

	max(tuple)
	返回元组中元素最大值。

	min(tuple)
	返回元组中元素最小值。

	tuple(seq)
	将列表转换为元组。

## 数组

### 数组创建

	import numpy as np
	
	a = np.array([2,3,4])
	b = np.array([2.0,3.0,4.0])
	c = np.array([[1.0,2.0],[3.0,4.0]])
	d = np.array([[1,2],[3,4]],dtype=complex) # 指定数据类型
	
	print(a) #打印数组
	

### 创建数组的常用函数

	np.arange(0,7,1,dtype=np.int16) # 0为起点，间隔为1时可缺省(引起歧义下不可缺省)
	np.ones((2,3,4),dtype=np.int16) # 三维数组，2页，3行，4列，全1，指定数据类型
	np.zeros((2,3,4)) #三维数组， 2页，3行，4列，全0
	np.empty(3) #值取决于内存
	np.arange(0,10,2) # 起点为0，不超过10，步长为2
	np.linspace(-1,2,5) # 起点为-1，终点为2，取5个点 
	np.random.randint(0,3,(2,3)) # 大于等于0，小于3，2行3列的随机整数




	b = np.ones((2,3,4),dtype=np.int16)
	print(b.shape) #输出a的形状
	print(b.shape[0]) #页数 ， 1行数，2列数
	print(b.ravel()) # 输出平坦化后的a（a本身不改变）
	b.shape = (6,2); print(b)# 改变a的形状
	print(a.transpose()) # 输出a的转置

