---

title: 廖雪峰SQL教程--表操作(四)
date: 2019-2-28 22:09:50
tags:  SQL
categories: "SQL"
mathjax: true

---


关系数据库的基本操作就是增删改查，即CRUD：Create、Retrieve、Update、Delete。其中，对于查询，前面已经详细讲述了SELECT语句的详细用法。<!--more-->

而对于增、删、改，对应的SQL语句分别是：

INSERT：插入新记录；
UPDATE：更新已有记录；
DELETE：删除已有记录。

## MySQL表数据类型

|名称 | 类型	| 说明 |
|---|	
| INT  | 整型 | 4字节整数类型，范围约+/-21亿 |
| BIGINT | 长整型 | 8字节整数类型，范围约+/-922亿亿 |
| REAL | 浮点型 | 4字节浮点数，范围约+/-1038 |
| DOUBLE | 浮点型 | 8字节浮点数，范围约+/-10308 |
| DECIMAL(M,N)	| 高精度小数	| 由用户指定精度的小数，例如，DECIMAL(20,10)表示一共20位，其中小数10位，通常用于财务计算 |
| CHAR(N)	| 定长字符串 | 存储指定长度的字符串，例如，CHAR(100)总是存储100个字符的字符串 |
| VARCHAR(N)	| 变长字符串 | 存储可变长度的字符串，例如，VARCHAR(100)可以存储0~100个字符的字符串 |
| BOOLEAN	| 布尔类型	| 存储True或者False |
| DATE	| 日期类型	| 存储日期，例如，2018-06-22 |
| TIME	| 时间类型	| 存储时间，例如，12:20:59 |
| DATETIME	| 日期和时间类型	| 存储日期+时间，例如，2018-06-22 12:20:59 |

## 创建表CREATE

创建数据库：

	CREATE DATABASE <数据库名>;

使用创建数据库：

	USE >数据库名>;


删除数据库：

	DROP DATABASE <数据库名>;


建MySQL数据表需要以下信息：

- 表名
- 表字段名
- 定义每个表字段

语法：

	CREATE TABLE table_name (column_name column_type);

下面以创建RUNOOB数据库为例：
	
	CREATE TABLE IF NOT EXISTS `runoob_tbl`(
	   `runoob_id` INT UNSIGNED AUTO_INCREMENT,
	   `runoob_title` VARCHAR(100) NOT NULL,
	   `runoob_author` VARCHAR(40) NOT NULL,
	   `submission_date` DATE,
	   PRIMARY KEY ( `runoob_id` )
	)ENGINE=InnoDB DEFAULT CHARSET=utf8;


解析：

- 如果你不想字段为 NULL 可以设置字段的属性为 NOT NULL， 在操作数据库时如果输入该字段的数据为NULL ，就会报错。
- AUTO_INCREMENT定义列为自增的属性，一般用于主键，数值会自动加1。
- PRIMARY KEY关键字用于定义列为主键。 您可以使用多列来定义主键，列间以逗号分隔。
- ENGINE 设置存储引擎，CHARSET 设置编码。


## 插入数据INSERT

基本语法：

	INSERT INTO <表名> (字段1, 字段2, ....) VALUES (值1, 值2, ....);

例如，我们向`students`表插入一条新记录，先列举出需要插入的字段名称，然后在`VALUES`子句中依次写出对应字段的值：

	INSERT INTO student (class_id, name, gender, score) 
	VALUES (2, '大牛', 'M', 80);

![](https://i.imgur.com/x0z7k13.png)

注意到我们并没有列出`id`字段，也没有列出id字段对应的值，这是因为`id`字段是一个自增主键，它的值可以由数据库自己推算出来。此外，如果一个字段有默认值，那么在`INSERT`语句中也可以不出现。

要注意，字段顺序不必和数据库表的字段顺序一致，但值的顺序必须和字段顺序一致。也就是说，可以写`INSERT INTO students (score, gender, name, class_id) ...`，但是对应的VALUES就得变成`(80, 'M', '大牛', 2)`。

还可以一次性添加多条记录，只需要在`VALUES`子句中指定多个记录值，每个记录是由`(...)`包含的一组值：

	
	INSERT INTO students (class_id, name, gender, score) VALUES
	  (1, '大宝', 'M', 87),
	  (2, '二宝', 'M', 81);

![](https://i.imgur.com/hqvn3Rp.png)


## 删除表和数据

删除表语法：

	DROP TABLE table_name;

删除数据语法：

	DELETE FROM <表名> WHERE ...;

例如，我们想删除`students`表中`id=1`的记录：

	DELETE FROM studnets WHERE id = 1; 

![](https://i.imgur.com/dFOzA3w.png)

如果WHERE条件没有匹配到任何记录，DELETE语句不会报错，也不会有任何记录被删除。

最后，要特别小心的是，和UPDATE类似，不带WHERE条件的DELETE语句会删除整个表的数据。

删除表格中所有数据，但是保留表格：

	TRUNCATE TABLE <表名>;

三者区别：__当你不再需要该表时， 用 drop；当你仍要保留该表，但要删除所有记录时， 用 truncate；当你要删除部分记录时（always with a WHERE clause), 用 delete。__

### 不能使用TRUNCAATE的情况


1、由 FOREIGN KEY 约束引用的表。（您可以截断具有引用自身的外键的表。）

2、参与索引视图的表。

3、通过使用事务复制或合并复制发布的表。

4、对于具有以上一个或多个特征的表，请使用 DELETE 语句。

5、TRUNCATE TABLE 不能激活触发器，因为该操作不记录各个行删除。

## 修改数据

语法：

	UPDATE <表名> SET 字段1=值1, 字段2=值2, ... WHERE ...;
	--例子
	UPDATE students SET name='大牛', score=66 WHERE id=1;

交换数据中的值：

	 UPDATE salary
	    	SET sex = CASE
	    	WHEN sex = 'f' THEN 'm'
	    	ELSE 'f'
	    	END;
		SELECT * FROM salary;

## 修改表

当我们需要修改数据表名或者修改数据表字段时，就需要使用到MySQL `ALTER`命令。

### 删除、修改或添加字段


首先创建一张表，表明testalter_tbl:

	create table testalter_tbl
	    (
	    i INT,
	    c CHAR(1)
	    );

![](https://i.imgur.com/fFRj1WJ.png)

如下命令使用了ALTER命令及DROP子句来删除表格中的字段：

	ALTER TABLE table_name DROP <删除的字段名>;

![](https://i.imgur.com/mf4wyAm.png)

如下命令使用ADD子句来想数据表中添加列：

	ALTER TABLE table_name ADD <列名> <类型>；

![](https://i.imgur.com/K6u1yGh.png)


如果你需要指定新增字段的位置，可以使用MySQL提供的关键字 FIRST (设定位第一列)， AFTER 字段名（设定位于某个字段之后）。

FIRST 和 AFTER 关键字可用于 ADD 与 MODIFY 子句，所以如果你想重置数据表字段的位置就需要先使用 DROP 删除字段然后使用 ADD 来添加字段并设置位置。

### 修改字段类型及名称

如果需要修改字段类型及名称, 你可以在ALTER命令中使用 MODIFY 或 CHANGE 子句 。

例如，把字段 c 的类型从 CHAR(1) 改为 CHAR(10)，可以执行以下命令:

 	ALTER TABLE testalter_tbl MODIFY c CHAR(10);
 	SHOW COLUMNS FROM testalter_tbl;
	
![](https://i.imgur.com/1pVGi0b.png)


使用 CHANGE 子句, 语法有很大的不同。 在 CHANGE 关键字之后，紧跟着的是你要修改的字段名，然后指定新字段名及类型。尝试如下实例：

	ALTER TABLE testalter_tbl CHANGE i j BIGINT;

![](https://i.imgur.com/FTdrolN.png)

当你修改字段时，你可以指定是否包含值或者是否设置默认值。

 	ALTER TABLE testalter_tbl 
    MODIFY j BIGINT NOT NULL DEFAULT 100;

![](https://i.imgur.com/kyIjX3D.png)


### 修改字段默认值

	ALTER TABLE testalter_tbl ALTER i SET DEFAULT 1000;


![](https://i.imgur.com/6U5hHDo.png)


## 作业

### 项目三：超过5名学生的课（难度：简单）

创建如下所示的courses 表 ，有: student (学生) 和 class (课程)。
例如,表:
	
	+---------+------------+
	| student | class      |
	+---------+------------+
	| A       | Math       |
	| B       | English    |
	| C       | Math       |
	| D       | Biology    |
	| E       | Math       |
	| F       | Computer   |
	| G       | Math       |
	| H       | Math       |
	| I       | Math       |
	| A      | Math        |
	+---------+------------+

编写一个 SQL 查询，列出所有超过或等于5名学生的课。
应该输出:

	+---------+
	| class   |
	+---------+
	| Math    |
	+---------+

Note: 学生在每个课中不应被重复计算。


### 答案


创建表：

 	CREATE TABLE courses (student VARCHAR(10), class VARCHAR(20));

![](https://i.imgur.com/JQhLz4d.png)

插入数据：

	INSERT INTO courses (student, class) VALUES
	    ('A', 'Math'),
	    ('B', 'English'),
	    ('C', 'Math'),
	    ('D', 'Biology'),
	    ('E', 'Math'),
	    ('F', 'Computer'),
	    ('G', 'Math'),
	    ('H', 'Math'),
	    ('I', 'Math'),
	    ('A', 'Math');

![](https://i.imgur.com/mVw4wtS.png)


查询操作：

	SELECT class FROM 
	(SELECT DISTINCT student, class FROM courses) as cour
	GROUP BY class HAVING COUNT(class) >=5;

![](https://i.imgur.com/b3dS9dC.png)


### 项目四：交换工资（难度：简单）

创建一个 salary表，如下所示，有m=男性 和 f=女性的值 。
例如:

	| id | name | sex | salary |
	|----|------|-----|--------|
	| 1  | A    | m   | 2500   |
	| 2  | B    | f   | 1500   |
	| 3  | C    | m   | 5500   |
	| 4  | D    | f   | 500    |

交换所有的 f 和 m 值(例如，将所有 f 值更改为 m，反之亦然)。要求使用一个更新查询，并且没有中间临时表。
运行你所编写的查询语句之后，将会得到以下表:

	| id | name | sex | salary |
	|----|------|-----|--------|
	| 1  | A    | f   | 2500   |
	| 2  | B    | m   | 1500   |
	| 3  | C    | f   | 5500   |
	| 4  | D    | m   | 500    |


### 答案

创建表：

 	CREATE TABLE salary (id BIGINT NOT NULL AUTO_INCREMENT PRIMARY KEY,
	name VARCHAR(10) NOT NULL, 
	sex CHAR(1) NOT NULL, 
	salary BIGINT);

插入数据：

	INSERT INTO salary (name, sex, salary) VALUES
    ('A', 'm', 2500),
    ('B', 'f', 1500),
    ('C', 'm', 5500),
    ('D', 'f', 500);


![](https://i.imgur.com/bSpDOb1.png)

查询


	 UPDATE salary
    	SET sex = CASE
    	WHEN sex = 'f' THEN 'm'
    	ELSE 'f'
    	END;
	SELECT * FROM salary;


![](https://i.imgur.com/bGsLcR9.png)



