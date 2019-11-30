---

title: Android基础知识数据存储
date: 2018-11-29 15:40:14
tags: APP开发
categories: APP开发

---

创建存储数据的变量，当屏幕旋转时，Activity被销毁，存储的数据就会消失。因此需要一个更永久的方式来存储用户数据，当关闭应用并重新打开时，用户数据依然存在，这一概念叫做数据持久性。<!--more-->


### 计算机内存

计算机有两种类型的存储器：

- 临时存储器：提到计算机内存就是临时存储器，比如随机存储器（RAM）
- 永久存储器：包括硬盘


### 数据存储选择

Android提供了几种不同的数据持久性选择，用来将数据存储到本地存储器：

- 文件：图片、音视频
- SharedPreferences：存储用户偏好设置，SharedPrederences使用键值对来保存少量的数据
- SQLiteDatabases： 是一种数据库类型，数据库是有组织的数据集合，可以从中提取所需的数据，并加以分析。SQLite数据库是一种具体的数据库示例，由一个甚至多个表格组成，每个表格由列和行组成，每一行是一个对象。


### SQLite数据库

SQLite是一种数据库，使应用能够与互动的设备上创建一个本地数据库。Lite一词是指典型数据库的轻量级版本。对应的重量级数据，比如MSQL，可以提供更加复杂的功能。SQLite不需要服务器，数据存储在设备上的本地文件中。SQLite是开源免费的，并且Android自带的数据库。

sqlite安装，可以从网上直接搜索下载sqlite3。然后在C盘下创建sqlite文件，并添加到环境变量。然后打开命令提示符（cmd）,进入目录`c:\sqlite`，输入`sqlite3`进入程序。

`echo`命令用户在屏幕上输出相关内容。

	echo %cd%

`cd`表示当前目录，`%`是定界符，表示不是向屏幕输出字母cd，而是输出当前的目录


SQL是结构化查询语言的简写，是一种计算机语言，专门用户数据库。SQL专门用来创建表格，然后添加和删除以及操纵条目。

打开一个文件：

	.open pets.db

如果文件不存在，则会自动创建一个`pets.db`文件。

结束SQLite程序

	.quit

直接进入程序并直接打开数据库：


	sqlite3 pets.db

查看数据库中的表格：

	.table

创建表格：

	CREATE TABLE table_name (column_name1 TEXT,
							 column_name2 INTEGER);

示例：

	CREATE TABLE pets (_id INTEGER, 
						name TEXT, 
						breed TEXT, 
						gender INTEGER, 
						weight INTEGER);

显示创建表格语句：

	.schema talbe_name

利用SQL指令查看表格详情，包括表格所有列的名称和类型：

	PRAGMA TABLE_INFO(table_name);

注意`.`指令是SQLite程序指令，大写的指令，以分号结尾，则表示是SQL语句，即结构化查询语言。**SQL语句不区分大小写**，但是SQLite程序指令区分大小写。

删除表格，完全清空表格：

	DROP TABLE talbe_name;

修改表格内容输出模式：

	.mode tabs //以制表符分割值
	.mode ascii 
	.mode column //以表格形式显示

输出表格内容时，添加表头：
	
	.head on

示例：

![](https://i.imgur.com/x4jVaPI.png)


#### SQLite中的类型

- INTEGER： 1、2、3、4、6、8byte Integer，即1字节整数，3字节整数
- NULL: 空值
- REAL: 浮点型
- TEXT： 字符串
- BOLB：表示数据完全按照输入的样式存储，例如图片或二进制数据

注意SQLite没有单独的Bool（布尔）类型。有效的方式是将布尔型存储为整型。


#### CRUD

对表格进行的操作，可以总结为一个词：CRUD。分别表示：Create、Read、Update、Delete。

- Read

从一个表格中读取某一列或所有列：

	SELECT <columns>  FROM <table_name>；
	SELECT * FROM <table_name>;  //读取所有列，*为通配符

- Create

向表格中插入数据行：

	INSERT INTO <table_name> (<columns_name_1>, <colums_name_2>,...)
						VALUES (<value_1>, <value_2>);

更新表格中的数据：

	UPDATE table_name SET column_name = update_value WHERE column_name == value;

![](https://i.imgur.com/Gt4HPQX.png)

删除表格中的数据：

	DELETE FROM table_name; //删除所有数据行
	DELETE FROM table_name WHERE column_name == value; //删除特定的行

![](https://i.imgur.com/m16JnRL.png) 

删除一个表格：

	DROP TABLE talbe_name

示例：

![](https://i.imgur.com/7qadFme.png)


#### 关键字

SQL提供了许多便捷的关键字，帮助我们来避免出现意外情况。

- PRIMARY KEY:将相关列作为标识唯一的行，可以确保唯一性，表示每个表格只能只有一个主键，通常与AUTO INCREMENT关键字一起使用

- AUTOINCREMENT：可以确保ID列是唯一的，并且当你添加一行是，自动生成新的唯一ID

- NOT NULL：表示向表格插入某个值时，必须具有相关的值

- DEFAULT <value>：设置默认值，当该值缺省时，使用默认值

示例：

![](https://i.imgur.com/pOqMwWM.png)


![](https://i.imgur.com/5Se9fsm.png)


#### 选择、位置和排序

WHERE: 根据条件查询行

![](https://i.imgur.com/q9ewfxb.png)

ORDER BY:更改输出顺序, ASC表示升序，DESC表示降序。

![](https://i.imgur.com/kpcuRhm.png)


![](https://i.imgur.com/L7ARKka.png)

注意，当使用条件查询并排序时，需要先使用WHERE条件语句，在使用ORDER BY语句，否则会发生语法错误。



### 在Android应用中使用数据库


#### Contract类

使用Contract类的作用：

- 帮助定义架构，并且规定去哪查找数据库常量
- 生成SQL指令时，消除出现拼写错误的可能性
- 更容易更新数据库结构


创建Contract类的步骤：

- 首先需要一个外部类，可以命名为BlankContrack
- 其次，数据块中的每个表格还需要一个内部类，每个类叫做BlankEntry，每个内部类实现BaseColumns类
- Contract中应该包含表格名和标题或列名称的字符串常量
- 对于特定情形，还需要为每个性别创建一个常量，并用刀相应的类中


#### SQLiteOpenHelper类

帮助我们创建、打开、管理数据库关联关系。

- 当第一次访问数据库时，SQLiteOpenHelper类将创建我们使用的数据库，它会使用getWritableDatabase()或getReadableDateabase()方法进行创建。
- 再次打开该应用是，SQLite帮助我们访问已经存在的数据库，而不是创建新的数据库。
- 如果数据库版本发生任何更改，SQLiteOpenHelper类将帮助我们更新数据库架构。

创建SQLiteOpenHelper类的步骤：

- 首先，创建一个扩展自SQLiteOpenHelper抽象类的类；
- 第二步，为数据库名称和数据库版本创建常量；
- 接着，需要创建构造函数；
- 然后，实现onCreate()方法，当数据库第一次被创建时，会使用到该方法；
- 最好，还要实现onUpgrade()方法，当数据库架构发生变化时，会用到该方法，比如向数据库中添加列。


`onCreate()`中用`db.execSQL()`来使用SQL语句创建和初始化架构，该方法的输入参数是一个字符串常量，包含`CREATE TABLE`等字符串的SQL语句。`execSQL()`方法不得与任何`SELECT`语句一起使用，因为该方法不会返回任何实际的数据，只是用来执行语句从而修改数据库的配置和结构。

`onUpgrade()`方法的作用是：使你能够根据你对代码结构所做的更改更新数据库文件。


在终端检查数据库是否构建正确：


- 使用`displayDatabaseInfo()`方法
- 从云端下载数据库到计算机，在终端检查数据库的正确性


### 连接数据库

 	PetDbHelper mDbHelper = new PetDbHelper(this);

构建了一个PetDbHelper类的实例

	SQLiteDatabase db = mDbHelper.getReadableDatabase();

将开发关联的数据库或创建一个数据库。可以看作是`.open`命令。


### SQLite数据库对象插入数据

如果想执行创建、更新或删除操作，则需要调用`getWritableDatabase()`方法

对于读取或选择操作，可以使用`getReadableDatabase()`方法

#### ContentValues类

Content Values是一种类，其中存储了大量的键值对，键是数据库中的列名称，值是需要插入的值。

![](https://i.imgur.com/EB7IH6J.png)

ContentValues使用起来非常简单。只需创建一个ContentValues实例，然后调用put()方法其中包含列名称和列值。这仅仅创建了一个对象，其中包含列名称和列值，并没有向数据库里插入任何内容。

要在数据库实际地创建新的一行，需要使用SQLiteDatabase类的insert()方法。

	db.insert(PetEntry.TABLE_NAME, null, values);

第一个参数是表名，第二个参数null是在向数据库中插入完全为空的数据时使用，所以默认设置为null；第三个参数是创建的ContentValues实例。其会返回一个新插入行的ID，如果出现错误返回-1。


### 数据库查询方法

SQLiteDatabase.query()

![](https://i.imgur.com/EI3d2Fa.png)

#### Cursor

Cursor是指代表数据库中的多行内容的对象。

![](https://i.imgur.com/bRycNmL.png)

不同的Get方法：

![](https://i.imgur.com/aFu8jnL.png)


具体使用：

	  String[] projection = {
                PetEntry._ID,
                PetEntry.COLUMN_PET_NAME,
                PetEntry.COLUMN_PET_BREED,
                PetEntry.COLUMN_PET_GENDER,
                PetEntry.COLUMN_PET_WEIGHT
        };
        Cursor cursor = db.query(PetEntry.TABLE_NAME,
                projection,
                null,
                null,
                null,
                null,
                null);

![](https://i.imgur.com/8po0MgH.png)

使用完成后，记得调用:

	cursor.close();

这样会完全情况Cuusor使其无效，仅在完全操作完毕后调用该方法。不关闭Cursor的话，会因为内存泄漏而降低性能。

### SQL 注入

假设你提供了可编辑文本，允许用户输入整数并获得 ID。友好型用户可能会输入“2”来搜索数据库中的第二个宠物。你可以让字符串 userInput 变成 “2”

	String selection = “PetEntry._ID + " == " + userInput + “;”;

这里就是：

	_ID == 2;

但是如果他们知道如何使用 SQLite 并且充满恶意，就会输入类似于下文的内容：

	“1; DROP TABLE pets;”

如果我们采用了这一输入

	String selection = “PetEntry._ID + " == " + userInput + “;”;

就会让最终的字符串变成：

	_ID == 1; DROP TABLE pets;

问题是，当 SELECT 语句被调用时，恶意用户“注入”的 DROP TABLE 语句也会被调用。本质上，我们使用户能够对宠物数据库执行毁灭性的攻击，因为我们让他们有机会对数据库执行任何 SQL 语句。

