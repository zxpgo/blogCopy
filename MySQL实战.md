
---

title:  MySQL实战
date: 2019-03-04 13:44:14
tags: SQL
categories: "SQL"
mathjax: true

---

MySQL实战：行程和用户、工资排名前N、分数排名。<!--more-->

## 项目十：行程和用户（难度：困难）

Trips 表中存所有出租车的行程信息。每段行程有唯一键 Id，Client_Id 和 Driver_Id 是 Users 表中 Users_Id 的外键。Status 是枚举类型，枚举成员为 (‘completed’, ‘cancelled_by_driver’, ‘cancelled_by_client’)。

	+----+-----------+-----------+---------+--------------------+----------+
	| Id | Client_Id | Driver_Id | City_Id |        Status      |Request_at|
	+----+-----------+-----------+---------+--------------------+----------+
	| 1  |     1     |    10     |    1    |     completed      |2013-10-01|
	| 2  |     2     |    11     |    1    | cancelled_by_driver|2013-10-01|
	| 3  |     3     |    12     |    6    |     completed      |2013-10-01|
	| 4  |     4     |    13     |    6    | cancelled_by_client|2013-10-01|
	| 5  |     1     |    10     |    1    |     completed      |2013-10-02|
	| 6  |     2     |    11     |    6    |     completed      |2013-10-02|
	| 7  |     3     |    12     |    6    |     completed      |2013-10-02|
	| 8  |     2     |    12     |    12   |     completed      |2013-10-03|
	| 9  |     3     |    10     |    12   |     completed      |2013-10-03| 
	| 10 |     4     |    13     |    12   | cancelled_by_driver|2013-10-03|
	+----+-----------+-----------+---------+--------------------+----------+

Users 表存所有用户。每个用户有唯一键 Users_Id。Banned 表示这个用户是否被禁止，Role 则是一个表示（‘client’, ‘driver’, ‘partner’）的枚举类型。

	+----------+--------+--------+
	| Users_Id | Banned |  Role  |
	+----------+--------+--------+
	|    1     |   No   | client |
	|    2     |   Yes  | client |
	|    3     |   No   | client |
	|    4     |   No   | client |
	|    10    |   No   | driver |
	|    11    |   No   | driver |
	|    12    |   No   | driver |
	|    13    |   No   | driver |
	+----------+--------+--------+

写一段 SQL 语句查出 2013年10月1日 至 2013年10月3日 期间非禁止用户的取消率。基于上表，你的 SQL 语句应返回如下结果，取消率（Cancellation Rate）保留两位小数。

	+------------+-------------------+
	|     Day    | Cancellation Rate |
	+------------+-------------------+
	| 2013-10-01 |       0.33        |
	| 2013-10-02 |       0.00        |
	| 2013-10-03 |       0.50        |
	+------------+-------------------+

赠送创建表格代码，见文末附录。



## 答案


		SELECT t.Request_at as Day,
	    ROUND(SUM(CASE WHEN t.status='completed' THEN 0 ELSE 1 END)/COUNT(*), 2) 
		AS 'Cancellation Rate'
	    FROM Trips t INNER JOIN Users u1 
		ON t.Client_Id=u1.Users_Id AND u1.Banned='No'
	    WHERE t.Request_at BETWEEN '2013-10-01' AND '2013-10-03'
	    GROUP BY t.Request_at;

备注：ROUND(x,d)  ，x指要处理的数，d是指保留几位小数

![](https://i.imgur.com/TZEwd3a.png)

## 项目十一：各部门前3高工资的员工（难度：中等）

将昨天employee表清空，重新插入以下数据（其实是多插入5,6两行）：

	+----+-------+--------+--------------+
	| Id | Name  | Salary | DepartmentId |
	+----+-------+--------+--------------+
	| 1  | Joe   | 70000  | 1            |
	| 2  | Henry | 80000  | 2            |
	| 3  | Sam   | 60000  | 2            |
	| 4  | Max   | 90000  | 1            |
	| 5  | Janet | 69000  | 1            |
	| 6  | Randy | 85000  | 1            |
	+----+-------+--------+--------------+

编写一个 SQL 查询，找出每个部门工资前三高的员工。例如，根据上述给定的表格，查询结果应返回：

	+------------+----------+--------+
	| Department | Employee | Salary |
	+------------+----------+--------+
	| IT         | Max      | 90000  |
	| IT         | Randy    | 85000  |
	| IT         | Joe      | 70000  |
	| Sales      | Henry    | 80000  |
	| Sales      | Sam      | 60000  |
	+------------+----------+--------+

此外，请考虑实现各部门前N高工资的员工功能。


## 答案

	CREATE TABLE Department (Id INT NOT NULL AUTO_INCREMENT PRIMARY KEY,
    Name VARCHAR(10));
	
	CREATE TABLE Employee (Id INT NOT NULL AUTO_INCREMENT PRIMARY KEY,
    Name VARCHAR(10), Salary BIGINT, DepartmentId INT NOT NULL);
	
	INSERT INTO Employee (Id, Name, Salary, DepartmentId) VALUES
     (1, "Joe", 70000, 1),
     (2, "Henry", 80000, 2),
     (3, "Sam", 60000, 2),
     (4, "Max", 90000, 1),
	 (5, "Janet", 69000, 1),	
	 (6, "Randy", 85000, 1);
	
	INSERT INTO Department (Id, Name) VALUES
     (1, "IT"),
     (2, "Sales");

	SELECT Department.Name AS Department, e1.Name AS Employee, e1.Salary AS Salary
	FROM Employee e1
	JOIN Department
	ON e1.DepartmentId = Department.Id
	WHERE 3 >   (
	            SELECT COUNT(DISTINCT e2.Salary) 
	            FROM Employee e2
	            WHERE e2.Salary > e1.Salary AND e1.DepartmentId = e2.DepartmentId
	            )
	ORDER BY Department.Name, e1.Salary DESC;

注意：计算比当前员工工资高的员工数量，如果工资比其高的员工数量小于3，则其排名前3

![](https://i.imgur.com/SEKeEEL.png)


## 项目十二  分数排名 - （难度：中等）

依然是昨天的分数表，实现排名功能，但是排名是非连续的，如下：

	+-------+------+
	| Score | Rank |
	+-------+------+
	| 4.00  | 1    |
	| 4.00  | 1    |
	| 3.85  | 3    |
	| 3.65  | 4    |
	| 3.65  | 4    |
	| 3.50  | 6    |
	+-------+------+

## 答案

	--创建表
	CREATE TABLE score (Id INT NOT NULL AUTO_INCREMENT PRIMARY KEY,
	Score DOUBLE);
		
	--插入数据	
	INSERT INTO score (Id, Score) VALUES
	(1, 3.50),
	(2, 3.65),
	(3, 4.00),
	(4, 3.85),
	(5, 4.00),
	(6, 3.65);

	SELECT Score,
	(SELECT COUNT(DISTINCT Score) 
	FROM score WHERE Score >= s.Score) 
	AS Rank 
	FROM score s 
	ORDER BY Score DESC;

![](https://i.imgur.com/stW878T.png)

__项目十建表附录__


	CREATE TABLE IF NOT EXISTS Trips (
	Id         INT, 
	Client_Id  INT, 
	Driver_Id  INT, 
	City_Id    INT, 
	Status     ENUM('completed', 'cancelled_by_driver', 'cancelled_by_client'), 
	Request_at VARCHAR(50)
	);
	
	CREATE TABLE IF NOT EXISTS Users (
	Users_Id INT, 
	Banned   VARCHAR(50), 
	Role     ENUM('client', 'driver', 'partner')
	);
	
	
	TRUNCATE TABLE Trips;
	INSERT INTO Trips (Id, Client_Id, Driver_Id, City_Id, Status, Request_at) VALUES ('1', '1', '10', '1', 'completed', '2013-10-01');
	INSERT INTO Trips (Id, Client_Id, Driver_Id, City_Id, Status, Request_at) VALUES ('2', '2', '11', '1', 'cancelled_by_driver', '2013-10-01');
	INSERT INTO Trips (Id, Client_Id, Driver_Id, City_Id, Status, Request_at) VALUES ('3', '3', '12', '6', 'completed', '2013-10-01');
	INSERT INTO Trips (Id, Client_Id, Driver_Id, City_Id, Status, Request_at) VALUES ('4', '4', '13', '6', 'cancelled_by_client', '2013-10-01');
	INSERT INTO Trips (Id, Client_Id, Driver_Id, City_Id, Status, Request_at) VALUES ('5', '1', '10', '1', 'completed', '2013-10-02');
	INSERT INTO Trips (Id, Client_Id, Driver_Id, City_Id, Status, Request_at) VALUES ('6', '2', '11', '6', 'completed', '2013-10-02');
	INSERT INTO Trips (Id, Client_Id, Driver_Id, City_Id, Status, Request_at) VALUES ('7', '3', '12', '6', 'completed', '2013-10-02');
	INSERT INTO Trips (Id, Client_Id, Driver_Id, City_Id, Status, Request_at) VALUES ('8', '2', '12', '12', 'completed', '2013-10-03');
	INSERT INTO Trips (Id, Client_Id, Driver_Id, City_Id, Status, Request_at) VALUES ('9', '3', '10', '12', 'completed', '2013-10-03');
	INSERT INTO Trips (Id, Client_Id, Driver_Id, City_Id, Status, Request_at) VALUES ('10', '4', '13', '12', 'cancelled_by_driver', '2013-10-03');
	
	TRUNCATE TABLE Users;
	INSERT INTO Users (Users_Id, Banned, Role) VALUES ('1',  'No',  'client');
	INSERT INTO Users (Users_Id, Banned, Role) VALUES ('2',  'Yes', 'client');
	INSERT INTO Users (Users_Id, Banned, Role) VALUES ('3',  'No',  'client');
	INSERT INTO Users (Users_Id, Banned, Role) VALUES ('4',  'No',  'client');
	INSERT INTO Users (Users_Id, Banned, Role) VALUES ('10', 'No',  'driver');
	INSERT INTO Users (Users_Id, Banned, Role) VALUES ('11', 'No',  'driver');
	INSERT INTO Users (Users_Id, Banned, Role) VALUES ('12', 'No',  'driver');
	INSERT INTO Users (Users_Id, Banned, Role) VALUES ('13', 'No',  'driver');