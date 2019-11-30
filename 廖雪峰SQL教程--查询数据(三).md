---

title: 廖雪峰SQL教程--查询数据(三)
date: 2019-2-26 21:54:50
tags:  SQL
categories: "SQL"
mathjax: true

---


在关系数据库中，最常用的操作就是查询！结合DateWhale-MySQL集训进行学习，文末有DateWhale-MySQL集训给出的练习项目。<!--more-->

## 准备数据

为了便于讲解和练习，我们先准备好了一个students表和一个classes表，它们的结构和数据如下：

students表存储了学生信息：

	id	class_id	name	gender	score
	1	1	 小明	M	90
	2	1	 小红	F	95
	3	1	 小军	M	88
	4	1	 小米	F	73
	5	2	 小白	F	81
	6	2	 小兵	M	55
	7	2	 小林	M	85
	8	3	 小新	F	91
	9	3	 小王	M	89
	10	3	 小丽	F	85

classes表存储了班级信息：

	id	name
	1	一班
	2	二班
	3	三班
	4	四班

	
如果想用MySQL练习，可以下载[这个SQL脚本](https://github.com/michaelliao/learn-sql/blob/master/mysql/init-test-data.sql)，然后在命令行运行：

	mysql -u root -p < init-test-data.sql

就可以自动创建test数据库，并且在test数据库下创建students表和classes表，以及必要的初始化数据。

![](https://i.imgur.com/H3qdf0o.png)

和内存数据库不同的是，对MySQL数据库做的所有修改，都会保存下来。如果你希望恢复到初始状态，可以再次运行该脚本。

## 基本查询

查询数据库中所有数据：

	SELECT * FROM <表名>;



查询students表中所有数据：

	SELECT * FROM students;

使用`SELECT * FROM students`时，`SELECT`是关键字，表示将要执行一个查询，`*`表示“所有列”，`FROM`表示将要从哪个表查询，本例中是students表。

该SQL将查询出students表的所有数据。注意：查询结果也是一个二维表，它包含列名和每一行的数据。

![](https://i.imgur.com/et6K5r9.png)

`SELECT`语句其实并不要求一定要有FROM子句。我们来试试下面的`SELECT`语句：

	SELECT 100+200; //计算100+100

![](https://i.imgur.com/a7zjZqc.png)

上述查询会直接计算出表达式的结果。虽然`SELECT`可以用作计算，但它并不是SQL的强项。但是，不带`FROM`子句的`SELECT`语句有一个有用的用途，就是用来判断当前到数据库的连接是否有效。许多检测工具会执行一条`SELECT 1`;来测试数据库连接。


## 条件查询

使用`SELECT * FROM <表名>`可以查询一张表中的所有记录。但是有时候，我们并不是希望获得所有记录，而是根据条件选择性地获取指定条件的记录。例如，查询分数在80分以上的学习记录。


`SELECT`语句可以通过`WHERE`条件来设定查询条件，查询结果是满足查询条件的记录。例如，要指定条件“分数在80分或以上的学生”，写成WHERE条件就是:

	SELECT * FROM students WHERE score >= 80;

其中，`WHERE`关键字后面的`score >= 80`就是条件。`score`是列名，该列存储了学生的成绩，因此，`score >= 80`就筛选出了指定条件的记录：

![](https://i.imgur.com/zFmE0BK.png)

因此，条件查询的语法就是：

	SELECT * FROM <表名> WHERE <条件表达式>;

#### AND

	SELECT * FROM students WHERE score >= 80 AND gender = 'M';

![](https://i.imgur.com/QN1q0ym.png)

### OR

	SELECT * FROM students WHERE score >= 80 OR gender = 'M';

![](https://i.imgur.com/Cx2I1p6.png)

### NOT

	SELECT * FROM students WHERE NOT class_id = 2;


![](https://i.imgur.com/oNpcz3y.png)

如果不加括号，条件运算按照`NOT、AND、OR`的优先级进行，即`NOT`优先级最高，其次是`AND`，最后是`OR`。加上括号可以改变优先级。


### 常用的条件表达式

| 条件	| 表达式举例1 |	表达式举例2	| 说明 |
|---|
|使用=判断相等	|score = 80|	name = 'abc'	|字符串需要用单引号括起来|
|使用>判断大于	|score > 80	|name > 'abc'	|字符串比较根据ASCII码，中文字符比较根据数据库设置|
|使用>=判断大于或相等|	score >= 80|	name >= 'abc'	||
|使用<判断小于|	score < 80|	name <= 'abc'	||
|使用<=判断小于或相等	|score <= 80	|name <= 'abc'	||
|使用<>判断不相等	|score <> 80	|name <> 'abc'	||
|使用LIKE判断相似	|name LIKE 'ab%'	|name LIKE '%bc%'|	%表示任意字符，例如'ab%'将匹配'ab'，'abc'，'abcd'|

###  查询分数在60分(含)～90分(含)之间的学生可以使用的WHERE语句
 
	WHERE score BETWEEN 60 AND 90
	
	WHERE score >= 60 AND score <= 90

### 查询前N条数据

	LIMIT N

	SELECT * FROM students LIMIT 5;

![](https://i.imgur.com/oiMzZYR.png)

## 投影查询

如果我们只希望返回某些列的数据，而不是所有列的数据，我们可以用`SELECT 列1, 列2, 列3 FROM ...`，让结果集仅包含指定列。这种操作称为__投影查询__。

例如，从`students`表中返回`id、score`和`name`这三列：

	SELECT id, score, name FROM students;

![](https://i.imgur.com/Rfb3Soe.png)

使用`SELECT 列1, 列2, 列3 FROM ...`时，还可以给每一列起个别名，这样，结果集的列名就可以与原表的列名不同。它的语法是:

	SELECT 列1 别名1, 列2 别名2, 列3 别名3 FROM ...;

例如，以下`SELECT`语句将列名`score`重命名为`points`，而`id`和`name`列名保持不变：

	SELECT id, score points, name FROM students;

![](https://i.imgur.com/Ew6dPfz.png)


## 排序 ORDER BY

默认情况是按主键进行排序的。

按`score`分数从低到高排序：

	SELECT id, name, gender, score FROM students ORDER BY score;

![](https://i.imgur.com/rLtVwjM.png)


如果反过来，按成绩__从高到低(DESC)__排序：

	SELECT id, name, gender, score FROM students ORDER BY score DESC;

![](https://i.imgur.com/IPPggRQ.png)

如果`score`列有相同的数据，要进一步排序，可以继续添加列名。例如，使用`ORDER BY score DESC, gender`表示先按`score`列倒序，如果有相同分数的，再按`gender`列排序:
	
	SELECT id, name, gender, score FROM students ORDER BY score DESC, gender;

![](https://i.imgur.com/GVGQn04.png)


默认的排序规则是`ASC`：“升序”，即从小到大。`ASC`可以省略，即`ORDER BY score ASC`和`ORDER BY score`效果一样。

如果有`WHERE`子句，那么`ORDER BY`子句要放到`WHERE`子句后面。例如，查询一班的学生成绩，并按照倒序排序：

	SELECT id, name, gender, score
	FROM students
	WHERE class_id = 1
	ORDER BY score DESC;

![](https://i.imgur.com/p9IzV7d.png)

## 分页查询

使用SELECT查询时，如果结果集数据量很大，比如几万行数据，放在一个页面显示的话数据量太大，不如分页显示，每次显示100条。

要实现分页功能，实际上就是从结果集中显示第1~100条记录作为第1页，显示第101~200条记录作为第2页，以此类推。

因此，分页实际上就是从结果集中“截取”出第M~N条记录。这个查询可以通过`LIMIT <M> OFFSET <N>`子句实现。我们先把所有学生按照成绩从高到低进行排序，把结果集分页，每页3条记录。要获取第1页的记录，可以使用`LIMIT 3 OFFSET 0`：

	SELECT id, name, gender, score
	FROM students
	ORDER BY score DESC
	LIMIT 3 OFFSET 0;

![](https://i.imgur.com/acZdmlm.png)

上述查询`LIMIT 3 OFFSET 0`表示，对结果集从0号记录开始，最多取3条。注意SQL记录集的索引从0开始。

如果要查询第2页，那么我们只需要“跳过”头3条记录，也就是对结果集从3号记录开始查询，把OFFSET设定为3：

	SELECT id, name, gender, score
	FROM students
	ORDER BY score DESC
	LIMIT 3 OFFSET 3;

如果OFFSET设定超过查询的最大数量不会报错，而是得到一个空的结果集。

__注意__

`OFFSET`是可选的，如果只写`LIMIT 15`，那么相当于`LIMIT 15 OFFSET 0`。

在MySQL中，`LIMIT 15 OFFSET 30`还可以简写成`LIMIT 30, 15`。

使用`LIMIT <M> OFFSET <N>`分页时，随着N越来越大，查询效率也会越来越低。


## 聚合查询

对于统计总数、平均数这类计算，SQL提供了专门的聚合函数，使用聚合函数进行查询，就是聚合查询，它可以快速获得结果。

以查询`students`表一共有多少条记录为例，我们可以使用SQL内置的`COUNT()`函数查询：

	SELECT COUNT(*) FROM students;


`COUNT(*)`表示查询所有列的行数，要注意聚合的计算结果虽然是一个数字，但查询的结果仍然是一个二维表，只是这个二维表只有一行一列，并且列名是`COUNT(*)`。

通常，使用聚合查询时，我们应该给列名设置一个别名，便于处理结果：

	SELECT COUNT(*) num FROM students;

![](https://i.imgur.com/jEcvkHn.png)


`COUNT(*)`和`COUNT(id)`实际上效果是一样的。聚合查询同样可以使用`WHERE`条件，因此我们可以方便地统计出有多少男生、多少女生，多少80分以上的学生等：

	SELECT COUNT(*) boys FROM students WHERE gender = 'M';

![](https://i.imgur.com/PDcJJpL.png)

### 聚合函数

|函数 |	说明|
|---|	
|	SUM	 |	计算某一列的合计值，该列必须为数值类型 |	
|	AVG	 |	计算某一列的平均值，该列必须为数值类型 |	
|	MAX	 |	计算某一列的最大值 |	
|	MIN	 |	计算某一列的最小值 |	

	//计算男生平均成绩:
	SELECT AVG(score) average FROM students WHERE gender = 'M';
	//
	SELECT AVG(score) average FROM students WHERE gender = 'X';


__注意__：如果聚合查询的WHERE条件没有匹配到任何行，COUNT()会返回0，而SUM()、AVG()、MAX()和MIN()会返回`NULL`。
	
	
## 分组

如果我们要统计一班的学生数量，我们知道，可以用

	SELECT COUNT(*) num FROM students WHERE class_id = 1;

如果要继续统计二班、三班的学生数量，难道必须不断修改WHERE条件来执行SELECT语句吗？

对于聚合查询，SQL还提供了“分组聚合”的功能。如下：

	SELECT  COUNT(*) num FROM students GROUP BY class_id;

![](https://i.imgur.com/eIpJkem.png)


执行这个查询，`COUNT()`的结果不再是一个，而是3个，这是因为，`GROUP BY`子句指定了按`class_id`分组，因此，执行该`SELECT`语句时，会把`class_id`相同的列先分组，再分别计算，因此，得到了3行结果。

	SELECT class_id, COUNT(*) num FROM students GROUP BY class_id;

如果把`name`放入结果集中，不出意外，执行这条查询我们会得到一个语法错误，因为在任意一个分组中，只有`class_id`都相同，`name`是不同的，SQL引擎不能把多个`name`的值放入一行记录中。因此，聚合查询的列中，只能放入分组的列。

__使用多个列进行分组__

	SELECT class_id, gender, COUNT(*) num FROM students GROUP BY class_id, gender;

![](https://i.imgur.com/nVOaaS3.png)

### HAVING

分组后的条件使用HAVING来限定，WHERE是对原始数据进行条件限定，几个关键字使用的顺序为：

	where \ group by\ having \ order by

例如：

SELECT class_id, gender, COUNT(*) num FROM students GROUP BY class_id, gender;

![](https://i.imgur.com/kWTybnz.png)

## 多表查询

SELECT查询不但可以从一张表查询数据，还可以从多张表同时查询数据。查询多张表的语法是：`SELECT * FROM <表1> <表2>`。

![](https://i.imgur.com/Hugtf8y.png)

这种一次查询两个表的数据，查询的结果也是一个二维表，它是students表和classes表的“乘积”，即students表的每一行与classes表的每一行都两两拼在一起返回。结果集的列数是students表和classes表的列数之和，行数是students表和classes表的行数之积。

这种多表查询又称__笛卡尔查询__，使用笛卡尔查询时要非常小心，由于结果集是目标表的行数乘积，对两个各自有100行记录的表进行笛卡尔查询将返回1万条记录，对两个各自有1万行记录的表进行笛卡尔查询将返回1亿条记录。

你可能还注意到了，上述查询的结果集有两列id和两列name，两列id是因为其中一列是students表的id，而另一列是classes表的id，但是在结果集中，不好区分。两列name同理

要解决这个问题，我们仍然可以利用投影查询的“设置列的别名”来给两个表各自的id和name列起别名：

	SELECT
	students.id sid,
	students.name,
	students.gender,
	students.score,
	classes.id cid,
	classes.name cname
	FROM students, classes;

可以给表设置一个别名，这样可以简化使用`表名.列名`这样的方式来引用列和设置别名	：

	SELECT 
	s.id sid,
	s.name, 
	s.gender,	
	s.score,
	c.id cid,
	c.name, cname
	FROM students s, classes c;

![](https://i.imgur.com/ReJkL2G.png)


多表查询也可以使用WHERE条件，如下：

	
	SELECT 
	s.id sid,
	s.name, 
	s.gender,	
	s.score,
	c.id cid,
	c.name cname
	FROM students s, classes c
	WHERE s.gender = 'M' AND c.id = 1;

![](https://i.imgur.com/cUM6L19.png)

注意：

使用多表查询可以获取M x N行记录。


## 连接查询

连接查询是另一种类型的多表查询。连接查询对多个表进行__JOIN运算__，简单地说，就是先确定一个主表作为结果集，然后，把其他表的行有选择性地“连接”在主表结果集上。

例如，我们想选出`students`表的所有学生信息，可以用一条简单的SELECT语句完成：

	SELECT s.id, s.name, s.class_id, s.gender, s.score FROM students s;

但是，假设我们希望结果集同时包含所在班级的名称，上面的结果集只有class_id列，缺少对应班级的name列。

现在问题来了，存放班级名称的name列存储在classes表中，只有根据students表的class_id，找到classes表对应的行，再取出name列，就可以获得班级名称。

这时，连接查询就派上了用场。我们先使用最常用的一种内连接——INNER JOIN来实现：

	SELECT s.id, s.name, s.class_id, c.name class_name, s.gender, s.score
	FROM students s
	INNER JOIN classes c
	ON s.class_id = c.id;

![](https://i.imgur.com/YhvkGkg.png)

注意INNER JOIN查询的写法是：

- 先确定主表，仍然使用`FROM <表1>`语法；
- 再确定需要连接的表，使用`INNER JOIN <表2>`的语法；
- 然后确定连接条件，使用`ON <条件>`，这里的条件是s.class_id = c.id，表示students表的class_id列与classes表的id列相同的行需要连接；
- 可选，加上WHERE子句、ORDER BY等子句。


有内连接（INNER JOIN）就有__外连接（OUTER JOIN）__。我们把内连接查询改成外连接查询，看看效果:


	SELECT s.id, s.name, s.class_id, c.name class_name, s.gender, s.score
	FROM students s
	RIGHT OUTER JOIN classes c
	ON s.class_id = c.id;

![](https://i.imgur.com/6EeUS0f.png)

执行上述RIGHT OUTER JOIN可以看到，和INNER JOIN相比，RIGHT OUTER JOIN多了一行，多出来的一行是“四班”，但是，学生相关的列如name、gender、score都为NULL。

这也容易理解，因为根据ON条件s.class_id = c.id，classes表的id=4的行正是“四班”，但是，students表中并不存在class_id=4的行。

有RIGHT OUTER JOIN，就有LEFT OUTER JOIN，以及FULL OUTER JOIN。它们的区别是：

__INNER JOIN只返回同时存在于两张表的行数据，__由于students表的class_id包含1，2，3，classes表的id包含1，2，3，4，所以，INNER JOIN根据条件s.class_id = c.id 返回的结果集仅包含1，2，3。

__RIGHT OUTER JOIN返回右表都存在的行。__如果某一行仅在右表存在，那么结果集就会以NULL填充剩下的字段。

__LEFT OUTER JOIN则返回左表都存在的行。__如果我们给students表增加一列，并添加class_id=5，由于classes表并不存在id=5的列，所以，LEFT OUTER JOIN的结果会增加一列，对应的class_name是NULL：

	-- 先增加一列class_id=5:
	INSERT INTO students (class_id, name, gender, score) values (5, '新生', 'M', 88);
	-- 使用LEFT OUTER JOIN
	SELECT s.id, s.name, s.class_id, c.name class_name, s.gender, s.score
	FROM students s
	LEFT OUTER JOIN classes c
	ON s.class_id = c.id;

![](https://i.imgur.com/Lx5O3Nk.png)

__最后，我们使用FULL OUTER JOIN，它会把两张表的所有记录全部选择出来，__并且，自动把对方不存在的列填充为NULL：

	SELECT s.id, s.name, s.class_id, c.name class_name, s.gender, s.score
	FROM students s
	FULL OUTER JOIN classes c
	ON s.class_id = c.id;


### 总结

对于这么多种JOIN查询，到底什么使用应该用哪种呢？其实我们用图来表示结果集就一目了然了。

假设查询语句是：

	SELECT ... FROM tableA ??? JOIN tableB ON tableA.column1 = tableB.column2;

![](https://i.imgur.com/fZl1xtt.png)


## 判断语句
	
	CASE expression
	    WHEN condition1 THEN result1
	    WHEN condition2 THEN result2
	   ...
	    WHEN conditionN THEN resultN
	    ELSE result
	END

CASE 表示函数开始，END 表示函数结束。如果 condition1 成立，则返回 result1, 如果 condition2 成立，则返回 result2，当全部不成立则返回 result，而当有一个成立之后，后面的就不执行了。

例如，对students表中同学的成绩分等级：

	SELECT id,  class_id, name, score, 
	case 
		WHEN score > 90 THEN '优秀' 
		WHEN score > 80 AND score <= 90 THEN '良好' 
		WHEN score > 70 AND score <= 80 THEN '中等' 
		WHEN score > 60 AND score <= 70 THEN '及格' 
		ELSE '不及格' 
	END score_num 
	FROM students;


![](https://i.imgur.com/VGDFtPw.png)

## 注释

单行注释：

	create database database-x   -- 创建数据库

多行注释：

	create database database-x
	/*
	创建一个数据库
	名字为database-x
	*/


## 函数

[时间函数、数值函数、字符串函数](http://www.runoob.com/mysql/mysql-functions.html)

## 格式规范

[https://zhuanlan.zhihu.com/p/27466166](https://zhuanlan.zhihu.com/p/27466166)

[https://www.sqlstyle.guide/](https://www.sqlstyle.guide/)


## 参考资料：

[RUNOOB教程]([http://www.runoob.com/mysql/mysql-tutorial.html](http://www.runoob.com/mysql/mysql-tutorial.html))

[张雪峰SQL教程](https://www.liaoxuefeng.com/wiki/001508284671805d39d23243d884b8b99f440bfae87b0f4000)


[Datawhale-MySQL](https://wx.zsxq.com/mweb/views/topicdetail/topicdetail.html?topic_id=244445842852211&group_id=822245485482)


## 作业

### 项目一：查找重复的电子邮箱（难度：简单）

创建 email表，并插入如下三行数据

	+----+---------+
	| Id | Email   |
	+----+---------+
	| 1  | a@b.com |
	| 2  | c@d.com |
	| 3  | a@b.com |
	+----+---------+

编写一个 SQL 查询，查找 email 表中所有重复的电子邮箱。
根据以上输入，你的查询应返回以下结果：

	+---------+
	| Email   |
	+---------+
	| a@b.com |
	+---------+

说明：所有电子邮箱都是小写字母。

### 答案

SELECT Email FROM email_table GROUP BY Email HAVING COUNT(Email) >= 2;

![](https://i.imgur.com/kbS2Knh.png)


### 项目二：查找大国（难度：简单）

创建如下 World 表

	+-----------------+------------+------------+--------------+---------------+
	| name            | continent  | area       | population   | gdp           |
	+-----------------+------------+------------+--------------+---------------+
	| Afghanistan     | Asia       | 652230     | 25500100     | 20343000      |
	| Albania         | Europe     | 28748      | 2831741      | 12960000      |
	| Algeria         | Africa     | 2381741    | 37100000     | 188681000     |
	| Andorra         | Europe     | 468        | 78115        | 3712000       |
	| Angola          | Africa     | 1246700    | 20609294     | 100990000     |
	+-----------------+------------+------------+--------------+---------------+

如果一个国家的面积超过300万平方公里，或者(人口超过2500万并且gdp超过2000万)，那么这个国家就是大国家。
编写一个SQL查询，输出表中所有大国家的名称、人口和面积。
例如，根据上表，我们应该输出:

	+--------------+-------------+--------------+
	| name         | population  | area         |
	+--------------+-------------+--------------+
	| Afghanistan  | 25500100    | 652230       |
	| Algeria      | 37100000    | 2381741      |
	+--------------+-------------+--------------+

### 答案
	
	SELECT name, population, area
    FROM World
    WHERE area > 3000000 OR (population > 25000000 AND gdp > 20000000);


![](https://i.imgur.com/YLS5jZU.png)


附作业创建表代码：

	--项目一
	-- 创建表
	CREATE TABLE email (
	ID INT NOT NULL PRIMARY KEY,
	Email VARCHAR(255)
	)
	
	-- 插入数据
	INSERT INTO email VALUES('1','a@b.com');
	INSERT INTO email VALUES('2','c@d.com');
	INSERT INTO email VALUES('3','a@b.com');


	--项目二
	-- 创建表
	CREATE TABLE World (
	name VARCHAR(50) NOT NULL,
	continent VARCHAR(50) NOT NULL,
	area INT NOT NULL,
	population INT NOT NULL,
	gdp INT NOT NULL
	);
	
	
	-- 插入数据
	INSERT INTO World VALUES( 'Afghanistan', 'Asia',652230,25500100,20343000);
	INSERT INTO World VALUES( 'Albania', 'Europe' ,28748,2831741,12960000);
	INSERT INTO World VALUES( 'Algeria', 'Africa' ,2381741,37100000,188681000);
	INSERT INTO World VALUES( 'Andorra' , 'Europe' ,468,78115,3712000);
	INSERT INTO World VALUES( 'Angola' , 'Africa' ,1246700,20609294,100990000);






