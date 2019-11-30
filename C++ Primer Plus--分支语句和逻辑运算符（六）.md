---

title:  C++ Primer Plus--分支语句和逻辑运算符（六）
date: 2018-10-02 09:48:14
tags: C++
categories: "C++ Primer Plus学习笔记"
mathjax: true

---

C++提供了if和switch语句来进行决策，还有逻辑运算符和条件运算符。<!--more-->


## 6.1 if语句

使用if语句来统计语句中的空格总数，实现如下：

if.cpp

	#include <iostream>
	using namespace std;
	int main()
	{
		char ch;
		int spaces = 0;
		int total = 0;
		cin.get(ch);
		while (ch != '.')
		{
			if (ch == ' ')
				spaces++;
			total++;
			cin.get(ch);
		}
		cout <<  spaces << " spaces, " << total ;
		cout << " charachers total in sentence.\n";
		return 0;
	}

结果：

	The balloonist was an airhead
	with lofty goals.
	6 spaces, 46 charachers total in sentence.

只要当ch为空格时，语句spaces++才被执行。因为语句total位于if语句的外面，因此每轮循环中都被执行。

### 6.1.1 if else语句

	if (test-condition)
		statement1
	else
		statement2

如果测试条件为true，则程序将执行statement1，跳过statement2；如果测试条件为false，则程序将跳过statement1，执行statement2。

### 6.1.2 格式化if else语句

如果操作部分需要多条语句，需要使用大括号将它们括起来，组成一个语句块。

	if (ch == 'z')
	{
		zorro++;
		cout << "Another zorro candidate\n";
	}
	else 
	{
		dull++;
		cout << "Not a Zorro candidate\n";
	}

### 6.1.3 if else if else结构

实际中，可能需要程序提供两个以上的选择：

	if (ch == 'A')
		a_grade++;
	else 
		if (ch == 'B')
			b_grade++;
		else 
			soso++;

可以写成如下形式：

	if (ch == 'A')
		a_grade++;
	else if (ch == 'B')
		b_grade++;
	else 
		soso++;

修改后的格式更为清晰。

ifelseif.cpp

	#include <iostream>
	using namespace std;
	const int Fave = 27;
	int main()
	{
		int n;
		cout << "Enter a number in the range 1-00 to find my favorite nmber: \n";
		do 
		{	cin >> n;
			if (n > Fave)
				cout <<  "Too high -- guess again:";
			else if (n < Fave)
				cout <<  "Too low -- guess agin: ";
			else
				cout << Fave << " is right!\n";
		}while (n != Fave);
		return 0;
	}


结果：
	Enter a number in the range 1-00 to find my favorite nmber: 
	22
	Too low -- guess agin: 44
	Too high -- guess again:27
	27 is right!

**错误防范：**

表达式variable == value反转写成value == varibale。这样，可以捕获将相等运算符误写成赋值运算符的错误。

	if (3 = var)

将一个变量赋给一个常量，编译器将生成错误信息。

	if (var = 3)

这样，将3赋给一个变量，正确的语法，编译不会报错，于是会导致程序出错。

## 6.2 逻辑表达式

C++提供了三种逻辑运算符，分别是：逻辑OR(||)、逻辑AND(&&)和逻辑NOT(!)。

### 6.2.1 逻辑OR运算符

如果表达式中的任意一个或全部都为true，则得到的表达式为true。

	5 == 5 || 5 == 9 //true
	3 <= 5 || 5 >= 3 //true
	1 > 2 || 2 > 3 //False

||运算符的优先级比关系运算符低，因此不需要添加括号。

C++规定，||运算符是个顺序点。即，先修改左侧的值，再对右侧的值进行判定。例如:

	i++ < 6 || i == j

假设i越来的值为10，则在对i和j进行比较时，i的值将为11。另外，如果左侧的表达式为true，则C++将不会去判定右侧的表达式，因为只要一个表达式为true，则整个逻辑表达式为true。

### 6.2.2 逻辑AND运算符

仅当两个表达式都为true时，得到的表达式才为true。

	5 == 5 && 4 == 4//true
	5 < 3 && 3 > 2 //false

&&运算符的优先级低于关系运算符，因此没必要添加括号。和||运算符一样，&&运算符也是顺序点，因此将先判定左侧，并且在右侧判定之前产生所有的副作用。如果左侧为false，则整个逻辑表达式为false，这种情况下，C++将不会再对右侧进行判定。

### 6.2.3 用&&来设置取值范围

&&运算符还允许建立一系列if else if else语句，其中每种选择都对应一个特定的取值范围。

	if	(age > 17 && age < 35)
		index = 0;
	else if (age >= 35 && age < 50)
		index = 1;
	else if (age >= 50 && age < 65)
		index = 2;
	else
		index = 3;

注意，下面语句：

	if (17 < age < 35)

编译器不会捕获这种错误，因为它仍然是合法的C++语法。<运算符是从左向右结合，因此表达式含义如下：

	if ((17 < age) < 35)

但（17<age）的结果要么为1，要么为0。不管哪种情况，表达式的值17<age都要小于35，所以整个测试的结果总是true。

### 6.2.4 逻辑NOT运算符

!运算符将它后面的表达式的真值取反。!运算符的优先级高于所有的关系运算符和算术运算符。因此，要对表达式取反，必须使用括号将其括起来，如下：

	!( x > 5);
	!x > 5; // 该表达式总是为false，!x为1或0，总是小于5

!运算符对于返回true-false值或可以被解释为true-false值的函数是很有用的。比如：

	!strcmp(s1,s2);

下面的程序来筛选可赋给int变量的数字输入。

not.cpp

	#include <iostream>
	#include <climits>
	using namespace std;
	bool is_int(double);
	int main()
	{
		double num;
		cout << "Enter an integer value:";
		cin >> num;
		while (! is_int(num))
		{
			cout << "Out of range -- please try again:";
			cin >> num;
		}
		int val = int(num);
		cout << "You've entered the integer " << val << endl;
		return 0;
	}
	
	bool is_int(double x)
	{
		if (x <= INT_MAX && x >= INT_MIN)
			return true;
		else 
			return false;
	}


结果：

	Enter an integer value:1000000000000000
	Out of range -- please try again:-1000000000000
	Out of range -- please try again:444
	You've entered the integer 444

布尔函数is_int()使用了climits文件中定义的两个符号常量INT_MIN和INT_MAX来确定int类型的范围。

### 6.2.5 逻辑运算符细节

AND和OR运算符的优先级都低于关系运算符。

!运算符的优先级都高于所有关系运算符和算符运算符。

逻辑AND运算符的优先级高于逻辑OR运算符，因此:

	age > 30 && age < 45 || weight > 300

被解释为：

	(age > 30 && age < 45) || weight > 300

当然，还可以使用括号来将所希望的解释告诉程序。例如：

	(age > 30 || weight > 300) && donation > 1000

C++确保程序从左向右进行逻辑运算表达式，并在知道了答案立刻停止。

### 6.2.6 其他表示方式

使用标识符and、or和not来表示三种逻辑运算符。

| 运算符 | 另一种表示方式 |
|---|
| && | and |
| || | OR |
| ! | NOT |

## 6.3 字符函数库cctype

C++继承了一个与字符相关的、非常方便的函数软件包，他可以简化如确定字符是否为大小写字母、数字、标点符号等工作，这些函数的原型在头文件cctype(老式的风格ctype.h)中定义。

下面使用AND和OR来测试字符ch是不是字母字符的代码：

	if (ch >= 'a' and ch <= 'z') || (ch >= 'A' && ch <= 'Z')

与使用isalpha()相比：

	if (isalpha(ch))

isslpha()不仅更容易使用，而且更通用。

下面程序演示cctype库函数。

cctypes.cpp

	#include <iostream>
	#include <cctype>
	using namespace std;
	int main()
	{
		cout << "Enter text for analysis and type @ to terminate input:";
		char ch;
		int whitespace = 0;
		int digits = 0;
		int chars = 0;
		int punct = 0;
		int others = 0;
		cin.get(ch);
		while (ch != '@')
		{
			if (isalpha(ch))
				chars ++;
			else if (isspace(ch))
				whitespace++;
			else if (isdigit(ch))
				digits++;
			else if (ispunct(ch))
				punct++;
			else
				others++;
			cin.get(ch);
		}
		cout <<  chars << " letters, " 
		     <<  whitespace << " whitespace, "
		     << digits << " digits, "
		     << punct << " punctuations, "
		     << others << " others.\n";
		return 0;
	}

结果：

	Enter text for analysis and type @ to terminate input:
	AdrenalVision Internationsl producer Adrienne Vismonger
	announced production of their new 3-D film, a remake of
	"My Dinner with Andre," scheduled for 2013.
	@
	123 letters, 23 whitespace, 5 digits, 6 punctuations, 0 others.

cctype中的字符函数：

| 函数名称 | 返回值 |
|---|
| isalnum() | 如果参数是字母数字，即字母或数字，该函数返回true|
| isalpha() | 如果参数是字母，该函数返回true |
| iscntrl() | 如果参数是控制字符，该函数返回true |
| isdigit() | 如果参数是数字(0~9),该函数返回true |
| isgraph() | 如果参数是除空格之外的打印字符，该函数返回true |
| islower() | 如果参数是小写字母，该函数返回true |
| isprint() | 如果参数打印字符（包括空格），该函数返回true |
| ispunct() | 如果参数是标点符号，该函数返回true |
| isspace() | 如果参数是标准空白字符，如空格、换行符、回车、制表符，该函数返回true |
| isupper() | 如果参数是大写字母，该函数返回true |
| isxdigit() | 如果参数是十六进制，该函数返回true |
| tolower() | 如果参数是大写字母，则返回小写字母 |
| toupper() | 如果参数是小写字母，则返回大写字母 |

## 6.4 ?:运算符

C++常用?:运算符来代替if else语句，它C++中唯一的3个操作数的运算符。通用格式如下：

	expression1 ? expression2 : expression3

如果expression1为true，则整个表达式的值为expression2；否则，整个表达式的值为expression3。如下：

	5 > 3 ? 10 : 12 //5>3为true，所以该表达式的值为19
	3 == 0 ？ 25 : 18 // 3==0为false，所以表达式的值为18

condit.cpp

	#include <iostream>
	int main()
	{
		using namespace std;
		int a, b;
		cout << "Enter two integers: ";
		cin >> a >> b;
		cout << "The larger of " << a << " and " << b;
		int c = a > b ? a :b;
		cout << " is " <<  c << endl;
		return 0;
	}

结果:

	Enter two integers: 2 3
	The larger of 2 and 3 is 3

将条件表达式嵌套在另一个条件表达式中，如下：

	const char x[2][20] = {"Jason ", "at your serviece\n"};
	const char * y = "Quillstone ";

	for (int i = 0; i < 3; i++)
		cout << ((i < 2) ? !i ? x[i]:y : x[i]);

这是一种费解的方式，他按下面的顺序打印3个字符串：

	Jasn Qullstone at your service

从可读性来说，条件运算符最适合简单关系和简单表达式的值。

## 6.5 switch语句

用户从5个选项中选择一个，虽然可以扩展if else if else来处理，但C++的switch语句能够更容易地从大型列表中进行选择。switch语句的通用格式：

	switch (integer-expression)
	{
		case label1: statement(s);
		case label2: statement(s);
	
		default: statement(s);
	} 

switch.cpp

	#include <iostream>
	using namespace std;
	void showmenu();
	void report();
	void comfort();
	int main()
	{
		showmenu();
		int choice;
		cin >> choice;
		while (choice != 5)
		{
			switch (choice)
			{
				case 1 : cout << "\a\n";
					break;
				case 2 : report();
					break;
				case 3 : cout << "The boss was in all day.\n";
					break;
				case 4 : comfort();
					break;
				default : cout << "That's not a choce.\n";
			}
			showmenu();
			cin >> choice;
		}
		cout << "Bye!\n";
		return 0;
	}
	
	void showmenu()
	{
		cout << "Please enter 1,2,3,4,5:\n"
			"1) alarm	 2) report\n"
			"3) alibi	 4) comfort\n"
			"5) quit\n";
	}
	
	void report()
	{
		cout << "It's been an excellent week for business.\n";
	}
	
	void comfort()
	{
		cout << "Your employees think you are the fines CEO in the induesty.\n";
	}

当用户输入5时，while循环结束。输入1到4将执行switch列表中相应的操作，输入6将执行默认语句。

同时，也可以使用字符(而不是整数)作为菜单选项和switch标签，则可以为大写标签和小写标签提供相同的语句：
	
			char choice;
			switch (choice)
			{
				case 'a' :
				case 'A' : cout << "\a\n";
					break;
				case 'b' :
				case 'B' : report();
					break;
				case 'c' :
				case 'C' : cout << "The boss was in all day.\n";
					break;
				case 'd' :
				case 'D' : comfort();
					break;
				default : cout << "That's not a choce.\n";
			}

由于case 'a'后面没有break语句，因此程序将执行下一行--case 'A'后面的语句。

### 6.5.1 将枚举用作标签

使用enum定义了一组相关常量，然后在switch语句中使用这些常量。通常，cin无法识别枚举类型，因此该程序要求用户选择选项时输入一个整数。将switch语句将int值和枚举标签进行对比时，将枚举类型提升为int。

enum.cpp

	#include <iostream>
	enum {red, orange, yellow, green, blue, violet, indigo};
	using namespace std;
	int main()
	{
		cout << "Enter color code(0-6):";
		int code;
		cin >> code ;
		while (code >= red and code <= indigo)
		{
			switch(code)	
			{
				case red : cout << "Her lips were red.\n"; break;
				case orange : cout << "Her hair was orange.\n"; break;
				case yellow : cout << "Her sheos were yellow.\n"; break;
				case green : cout << "Her nails were green.\n"; break;
				default : cout << "Others.\n";
			}
			cout << "Enter color code (0-6):";
			cin >> code;
		}
		cout << "Bye!\n";
		return 0;
	}

结果：

	Enter color code(0-6):0
	Her lips were red.
	Enter color code (0-6):2
	Her sheos were yellow.
	Enter color code (0-6):6
	Others.
	Enter color code (0-6):7
	Bye!

### 6.5.2 switch和if else

switch语句和if else语句都允许程序从选项中进行选择。if else更通用，它可以处理取值范围。而switch并不是为处理范围而设计的，switch语句中的每一个case标签都必须时一个单独的值。另外，这个值必须是整数（包括char）,因此switch无法处理浮点测试。另外，case标签值必须是常量。

## 6.6 break和continue

break和continue语句都是程序能过跳过部分代码。可以在switch语句或任何循环中使用break语句，使程序跳到switch或循环后面的语句处执行。continue语句用于循环中，让程序跳过循环体中余下的代码，并开始新一轮循环。如下图：

![](https://i.imgur.com/cSVU6ZR.jpg)

jump.cpp

	#include <iostream>
	const int ArSize = 80;
	int main()
	{
		using namespace std;
		int letter = 0;
		char line[ArSize];
		cout << "Enter a line of text:\n";
		cin.get(line, ArSize);
		cout << "Complete line:\n " << line << endl;
		cout << "Line through first period:\n";
		for (int i = 0; line[i] != '\0'; i++)
		{
			cout << line[i];
			if (line[i] == '.')//遇到句号，结束循环
				break;
			if (line[i] == ' ') //只统计字母个数，空格跳过，则下面的语句将不会执行
				continue;
			letter++; //统计字母个数
			
		}
		cout << endl << letter << " letters.\n";
		return 0;
	}

结果：

	Enter a line of text:
	Let's do lunch today. you can pay!
	Complete line:
	 Let's do lunch today. you can pay!
	Line through first period:
	Let's do lunch today.
	17 letters.

**程序说明**

虽然continue语句导致该程序跳过循环体的剩余部分，但不会跳过循环的更新表达式。在for循环中,continue语句使程序直接跳到更新表达式，然后跳到测试表达式。然而，对于while循环，continue将使程序直接跳到测试表达式，因此while循环中位于continue之后的更新表达式都被跳过。在某些情况下，这是一个问题。


## 6.7 读取数字的循环

将一系列数字读入数组中，并允许用户在数组填满之前结束输入，利用如下代码：

	int n;
	cin >> n;

如果用户输入一个单词，而不是一个数字，发送这种类型匹配情况时，将发生4种情况：

- n的值保持不变
- 不匹配的输入将留在输入队列种
- cin对象中的一个错误标记被设置
- 对cin方法的调用将返回false

假设编写一个程序，来计算每天捕获的鱼的重量。这里假设每天最多捕获5条鱼，因此一个包含5个元素的数组将足以存储所以的数据，但也可能没有捕获这么多。如果数组被填满或输入了非数字输入，循环将结束。

cinfish.cpp

	#include <iostream>
	using namespace std;
	const int Max = 5;
	int main()
	{
		double fish[Max];
		cout << "Please enter the weights of your fish.\n";
		cout << "You may enter up to " << Max << " fish < q to terminate>.\n";
		cout << "fish #1: ";
		int i = 0;
		while (i < Max and cin >> fish[i])
	        {
			if (++i < Max)
				cout << "fish # " <<  i+1 << ": ";
		}
		double total = 0.0;
		for (int j= 0; j< i; j++)
			total += fish[j];
		if (i == 0)
			cout << "No fisht.\n";
		else 
			cout << total / i << " average weight of " << i << " fish.\n";
		return 0;
	}

结果：

	Please enter the weights of your fish.
	You may enter up to 5 fish < q to terminate>.
	fish #1: 10.2
	fish # 2: 2.3
	fish # 3: 4.5
	fish # 4: q
	5.66667 average weight of 3 fish.
	[root@localhost ~]# ./a.out
	Please enter the weights of your fish.
	You may enter up to 5 fish < q to terminate>.
	fish #1: q
	No fisht.


上述程序中，cin>>fish[i]实际上是一个cin方法函数调用，该函数返回cin。如果cin位于测试条件中，则将被转化为bool类型。如果输入成功，则转换后为true，否则为false。

当用户输入的不是数字时，该程序将不再读取输入。下面再看一个例子，假设程序要求用户提供5个高尔夫得分，以计算平均分。如果用户输入非数字，程序拒绝，并要求用户继续输入数字。可以看到，可以使用cin输入表达式来检测输入是不是数字。程序发现用户输入了错误内容时，应采取3个步骤：

- 重置cin以接受新的输入
- 删除错误输入
- 提示用户重新输入

cingolf.cpp

	#include <iostream>
	const int Max = 5;
	using namespace std;
	int main()
	{
	 	int golf[Max];
		cout << "Enter your golf scores.\n";
		cout << "You must enter " << Max << " rounds.";
		for (int i = 0; i < Max; i++)
		{
			cout << "Round# " << i+1 << " : " ;	
			while (!(cin >> golf[i]))
			{
				cin.clear();
				while(cin.get()!='\n')
					continue;
				cout << "Please enter a number: ";
			}
		}
		double total = 0.0;
		for (int i = 0; i< Max; i++)
			total += golf[i];
		cout << total/Max << " = average score " << Max << " rounds.\n";
		return 0;
	}


结果：

	Enter your golf scores.
	You must enter 5 rounds.
	Round# 1 : 88
	Round# 2 : i
	Please enter a number: k
	Please enter a number: 4
	Round# 3 : 5
	Round# 4 : 9
	Round# 5 : ?
	Please enter a number: 44
	30 = average score 5 rounds.


clear()方法重置输入，如果省略这条语句，程序将拒绝继续读取输入。程序中如下代码的作用是：

	while(cin.get()!='\n')
		continue;

读取行尾之前的所有输入，从而删除这一行的错误输入。


## 6.8 简单文件输入/输出

C++使得将读取键盘输入和在屏幕上显示输出的技巧用于文件输入/输出(文件I/O)非常简单。

### 6.81 文本I/O和文本文件

使用cin进行输入时，程序将输入视为一系列的字节，其中**每个字节都被解释为字符编码**。不管目标数据类型是什么，输入一开始都是字符数据--文本数据。然后，cin对象负责将文本转换为其他类型。

	char ch;
	cin >> ch;

输入实例为： 38.5 19.2

输入行中的第一个字符被赋给ch。在这里第一个字符是数字3，其字符编码(二进制)被存储在变量ch中。输入和目标变量都是字符，因此不需要进行转换。注意，这里存储的不是数值3，而是字符3的编码。

	int n;
	cin >> n;

在这种情况下，cin将不断读取，直到遇到非数字符号。也就是说，它读取3和8，这样句点将成为输入队列中的下一个字符。cin通过计算发现，这两个字符对应数字是38，因此将38的二进制编码复制到变量n中。

接下来看double类型：

	double x;
	cin >> x;

在这种情况下，cin将不断读取，直到遇到第一个不属于浮点数的字符。即，cin读取3、8、句点和5，使得空格成为输入队列中的下一个字符。cin通过计算发现，这四个字符对应数字38.5，因此将38.5的二进制编码复制到变量x中。

接下来看看char数组的情况：

	char word[50];
	cin >> word;

在这种情况下，cin将不断读取，直到遇到空白字符。即，它读取3、8、句点和5，使得空格成为输入队列中的下一个字符。然后，cin将这4个字符编码存储到输入word中，并在末尾加上一个空字符，这里需要进行任何转换。

最后，看一下另一种使用char数组来存储输入的情况：

	char word[50];
	cin.getline(word,50);

在这种情况下，cin将不断读取，直到遇到换行符。所有字符都将被存储到数组word中，并在末尾添加一个空字符。换行符被丢弃，输入队列中的下一个字符是下一行的第一个字符。这里不进行任何转换。

对于输出，将执行相反的操作。即整数被转换为数字字符序列，浮点数被转换为数字字符和其他字符组成的字符序列。字符数据不需要做任何转换。

这里的要点是：输入一开始都是文本。因此，控制台输入的文件版本是文本文件，即每个字节都存储了一个字符编码的文件。并非所有的文件都是文本文件，比如，数据块和电子表格以数值格式（即二进制整数或浮点格式）存储数值数据。

### 6.8.2 写入的文本文件中

文件输出：

- 必须包含头文件fstream;
- 头文件fstream定义了一个用于处理输出的ofstream类；
- 需要声明一个或多个ofstream变量，并以自己喜欢的方式对其进行命名；
- 必须指明命名空间std;
- 需要将ofstream对象与文件关联起来，为此方法之一是使用open()方法；
- 使用完文件后，应使用方法close()将其关闭；
- 可结合使用ofstream和运算符<<来输出各种类型的数据。

iostream头文件提供了一个预先定义好的名为cout的ostream对象，但您必须声明自己的ofstream对象，为其命名，并将其同文件关联。声明对象如下：

	ofstream outFile;

关联文件如下：

	outFile.open("fish.txt");
	char filename[50];
	cin >> filename;
	outFile.open(filename);

open()接受一个c-风格字符串作为输入，可以是字面字符串，也可以是存储在数组中的字符串。


使用文件输出的主要步骤如下：

- 包含头文件fstream;
- 创建一个ofstream对象；
- 将该ofstream对象同一个文件关联起来;
- 将像使用cout那样使用该ofstream对象。（如：<<,endl和setf()都可用于ofstream对象）。

outfile.cpp

	#include <iostream>
	#include <fstream>
	using namespace std;
	int main()
	{
		char automobile[50];
		int year;
		double a_price;
		double b_price;
		
		ofstream outFile;
		outFile.open("carinfo.txt");
	
		cout << "Enter the amke and model of automobile: ";
		cin.getline(automobile, 50);
		cout << "Enter the model year: ";
		cin >> year;
		cout << "Enter the original asing price: ";
		cin >> a_price;
		b_price = 0.913 * a_price;
	
		cout.precision(5);
		cout.setf(ios_base::showpoint);
		cout << "Make and model: " << automobile << endl;
		cout << "Year: " << year << endl;
		cout << "Was asing: $" << a_price << endl;
		cout << "Now asking: $" << b_price << endl;
	
		outFile.precision(5);
		outFile.setf(ios_base::showpoint);
		outFile << "Make and model: " << automobile << endl;
		outFile << "Year: " << year << endl;
		outFile << "Was asking: $" << a_price << endl;
		outFile << "Now asking: $" << b_price << endl;
		outFile.close();
		return 0;
	}


结果：

![](https://i.imgur.com/xTPy0bB.png)

在程序运行之前，并不存在carinfo.txt文件。在这种情况下，open()将新建一个名为carinfo.txt的文件。如果在此程序运行前，该文件已存在。默认情况下，open()将首先截断该文件，即将其长度截断为零---丢弃原有的内容，然后将新的输入加入到该文件中。

### 6.8.3 读取文本文件

文件输入：

- 必须包含头文件fstream;
- 头文件fstream定义了一个用于处理输入的ifstream类；
- 需要声明一个或多个ifstream变量，并以自己喜欢的方式对其进行命名；
- 必须指明命名空间std;
- 需要将ifstream对象与文件关联起来，为此方法之一是使用open()方法；
- 使用完文件后，应使用方法close()将其关闭；
- 可结合使用ifstream和运算符>>来输出各种类型的数据;
- 可以使用ifstream对象和get()方法来读取一个字符，使用ifstream对象和getline()来读取一行字符；
- 可以结合使用ifstream和eof()、fail()等方法来判断输入是否成功；
- ifstream对象本身被用作测试条件时，如果最后一个读取操作成功，它将转换为true，否则为false。

ifstream对象的声明：

	ifstream inFile;
	
关联文件：

	inFlie.open("bowling.txt");

如果试图打开一个不存的文件用于输入，这种错误将导致后面使用ifstream对象进行输入时失败。检查文件是否被成功打开的方法：is_open()。

	inFile.open("bowling.txt");
	if (!inFile.is_open())
		exit(EXIT_FAILURE);

函数exit()的原型在头文件cstdlib中定义的，在该头文件中，还定义了一个用于同操作系统通信的参数数值EXIT_FAILURE。函数exit()终止程序。

方法is_open()是C++中比较新的内容。可以使用方法good()来代替。

sumafile.cpp

	#include <iostream>
	#include <fstream>
	#include <cstdlib>
	using namespace std;
	const int SIZE = 60;
	int main()
	{
		char filename[SIZE];
		ifstream inFile;
		cout << "Enter name of data file: ";
		cin.getline(filename,SIZE);
		inFile.open(filename);
		if (!inFile.is_open())
		{
			cout << "Could not open the file " << filename << endl;
			cout << "Program terminating.\n";
			exit(EXIT_FAILURE);
		}
		double value;
		double sum = 0.0;
		int count = 0;
		
		inFile >> value;
		while (inFile.good())
		{
			++count ;
			sum += value;
			inFile >> value;
		}
		if (inFile.eof())
			cout << "End of file reached.\n";
		else if (inFile.fail())
			cout << "Input terminatd by data mismatch.\n";
		else 
			cout << "Input terminated for unknown reasion.\n";
		if (count == 0)
			cout << "No data processed.\n";
		else
		{
			cout << "Items read: " << count << endl;
			cout << "Sum: " << sum << endl;
			cout << "Average: " << sum/count << endl;
		}
		inFile.close();
		return 0;
	}


结果：

![](https://i.imgur.com/Fsax9RR.png)

文件循环读取设计时，需要注意几点。首先，程序读取文件时不应超过EOF。如果最后一次读取数据时遇到EOF，方法eof()返回true。其次，程序可能遇到类型不匹配，方法fail()返回true(如果遇到EOF，该方法也返回true)。最后，可能出现意外的问题，如文件损坏或硬件故障。如果最后一次读取文件出现了这样的问题，方法bad()将返回true。不要分布检查这些情况，一种更简单的方式是使用good()方法，该方法在没有发送任何错误时返回true。同时，可以使用其他方法来确定循环终止的原因，如下：

		if (inFile.eof())
			cout << "End of file reached.\n";
		else if (inFile.fail())
			cout << "Input terminatd by data mismatch.\n";
		else 
			cout << "Input terminated for unknown reasion.\n";

方法god()指出最后一次读取输入的操作成功与否。标准的做法：


		inFile >> value;
		while (inFile.good())
		{
			++count ;
			sum += value;
			inFile >> value;
		}

由于表达式inFile >> value的结果为inFile，而在需要一个bool值的情况下，inFile的结果为inFile().good()，即true或false。于是，两条输入语句用一条用作循环测试的语句代替。因此可以简化上面标准做法，如下：

	while(inFile >> value)
	{
		sum += value;
		++count;
	}
## 6.9 总结

if语句、if else语句和switch语句

逻辑运算符

文件输出和输入