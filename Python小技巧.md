---

title:   Python小技巧
date: 2018-10-25 19:08:14
tags: python

---

给初学者的Python小技巧，非常实用，可以用在你的程序中，代码瞬间变得高大上，看起来还有Pythonic（很Python）的感觉。<!--more-->

### 交换两个值

	a, b =  4, 10
	print(a, b)
	a, b = b, a
	print(a, b)

在很多语言中，交换两个变量不得不引入一个临时变量，而Python可以直接交换，无需引入第三者。

### 评价列表中的字符串

	>>> a = ["python", "is", "awesome"]
	>>> print(" ".join(a))
	python is awesome

虽然你可以用“+”操作来合并多个字符串，但是使用join方法效率会更高哦。


### 查找列表中出现最频繁的元素

	>>> a = [1, 2, 4, 3, 1, 2, 1, 3, 1]
	#方法一
	>>> print(max(set(a), key = a.count))
	1
	#方法二
	>>> from collections import Counter
	>>> cnt = Counter(a)
	>>> print(cnt.most_common(3))
	[(1, 4), (2, 2), (3, 2)]

set() 函数创建一个无序不重复元素集，可进行关系测试，删除重复数据，还可以计算交集、差集、并集等。

Counter在统计计算的时候给了我们很大的便利，无需自己再去造个轮子。

### 检查两个字符是否有相同的字符构成

	>>> str1 = "abc"
	>>> str2 = "bcd"
	>>> Counter(str1) == Counter(str2)
	False
	>>> str1 = "abcd"
	>>> str2 = "abcd"
	>>> Counter(str1) == Counter(str2)
	True

### 列表反转

	>>> a = [5, 4, 3, 2, 1]
	>>> print(a[::-1])
	[1, 2, 3, 4, 5]
	>>> for ele in reversed(a):
		print(ele)
	
		
	1
	2
	3
	4
	5
	>>> reversed(a)
	<list_reverseiterator object at 0x0000022DB7E244A8>
	>>> b = reversed(a)
	>>> print(list(b))
	[1, 2, 3, 4, 5]

列表有个reverse方法，也是反转列表，但是它是对原列表进行反转，而不是返回一个新的列表

### 装置二维数组

	>>> original = [['a','b'],['c','d'],['e','f']]
	>>> transposed = zip(*original)
	>>> print(list(transposed))
	[('a', 'c', 'e'), ('b', 'd', 'f')]

### 链式比较

	>>> print(4 < 6 < 7)
	True
	>>> print(1 == 6 < 7)
	False
	>>> print(1 < 6 == 7)
	False

### 链式函数调用

	>>> def product(a, b):
		return a * b
	
	>>> def add(a, b):
		return a + b
	
	>>> b = True
	>>> print((product if b else add)(5, 7))
	35

### 拷贝列表

	>>> a = [1, 2, 3, 4, 5]
	>>> b = a
	>>> b[0] = 10
	>>> a
	[10, 2, 3, 4, 5]

	>>> b = a[:]
	>>> b[0] = 11
	>>> b
	[11, 2, 3, 4, 5]

	>>> a = [1, 2, 3, 4, 5]
	>>> print(list(a))
	[1, 2, 3, 4, 5]

	>>> print(a.copy())
	[1, 2, 3, 4, 5]

	>>> from copy import deepcopy
	>>> l = [[1, 2], [3, 4]]
	>>> l2 = deepcopy(l)
	>>> print(l2)
	[[1, 2], [3, 4]]


### 字典get

	>>> d = {'a': 1, 'b': 2}
	>>> print(d.get('c', 3))
	3
	>>> d
	{'a': 1, 'b': 2}
	>>> print(d.get('a', 3))
	1

找不到时，返回默认值

### 按字典的值排序

	>>> d = {'a': 1, 'b': 3, 'c': 2}
	>>> print(sorted(d.items(), key = lambda x: x[1]))
	[('a', 1), ('c', 2), ('b', 3)]
	
	>>> from operator import itemgetter
	>>> print(sorted(d.items(), key=itemgetter(1)))
	[('a', 1), ('c', 2), ('b', 3)]
	
	>>> print(sorted(d, key=d.get))
	['a', 'c', 'b']

### for else

	a = [1, 2, 3, 4, 5]
	for el in a:
	    if el == 0:
	        break
	else:
	    print("did not brek out of for loop")
	==================== RESTART: C:/Users/zxp/Desktop/11.py ====================
	did not brek out of for loop

### 将列表转成用逗号分割的字符串

	>>> items = ['foo', 'bar', 'xyz']
	>>> print(','.join(items))
	foo,bar,xyz

	>>> numbers = [2, 3, 4, 5]
	>>> print(','.join(map(str, numbers)))
	2,3,4,5

	>>> data = [2, 'hello', 3, 4.4]
	>>> print(','.join(map(str, data)))
	2,hello,3,4.4

### 合并字典

	>>> d1 = {'a': 1}
	>>> d2 = {'b': 2}
	>>> print({**d1, **d2})
	{'a': 1, 'b': 2}
	
	>>> print(dict(d1.items() | d2.items()))
	{'a': 1, 'b': 2}

	>>> d1.update(d2)
	>>> d1
	{'a': 1, 'b': 2}

### 获取列表中最大值与最小值的位置

	>>> lst = [3, 1, 2, 4]
	>>> def minIndex(lst):
		return min(range(len(lst)), key=lst.__getitem__)
	
	>>> def maxIndex(lst):
		return max(range(len(lst)), key=lst.__getitem__)
	
	>>> print(minIndex(lst))
	1
	>>> print(maxIndex(lst))
	3

### 移除列表中重复的元素

	>>> items = [2, 2, 3, 3,1]
	>>> newitems = list(set(items))
	>>> print(newitems)
	[1, 2, 3]
	
	>>> from collections import OrderedDict
	>>> items = ['foo', 'bar', 'bar', 'foo']
	>>> print(list(OrderedDict.fromkeys(items).keys()))
	['foo', 'bar']




转自:[https://mp.weixin.qq.com/s/k_coGMYUd8aBpqX67yWQbw](https://mp.weixin.qq.com/s/k_coGMYUd8aBpqX67yWQbw)