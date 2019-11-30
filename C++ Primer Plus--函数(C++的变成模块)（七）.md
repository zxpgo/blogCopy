---

title:  C++ Primer Plus--函数(C++的编程模块)（七）
date: 2018-10-04 10:30:14
tags: C++
categories: "C++ Primer Plus学习笔记"
mathjax: true

---

函数<!--more-->

## 7.1 复习函数的基本知识

使用C++函数，必须完成的工作：

- 提供函数定义
- 提供函数原型
- 调用函数

库函数是已经定义和编译好的函数，同时可以使用标准库头文件提供其原型，因此只需正确地调用这种函数。

### 7.1 定义函数

可以将函数分成两类：没有返回值的函数和有返回值的函数。没有返回值的函数称为void函数，其通用格式如下：

	void functionName(parameterList)
	{
		statement
		return ;
	}

其中，parameterList指定了传递给函数的参数类型和数量。可选的返回语句标记了函数的结尾；否则函数将在右花括号处结束。void类型的函数，通常用来执行某种操作。比如，打印执行文字n次：

	void cheers(int n)
	{
		for (int i = 0; i < n; i++)
			cout << "cheers" << endl; 
	}

参数列表int n 意味着调用函数cheers()时，应将一个int值作为参数传递给它。

有返回值的函数将生成一个值，并将它返回给调用函数。通用格式如下：

	typeName functionName(parameterList)
	{
		statement
		return value;
	}

有返回值的函数，必须使用返回语句，以便将值返回给调用函数。值本身可以是常量、变量，也可以是表达式，只是其结果的类型必须为typeName类型或可以被转换为typeName（比如，如果声明的返回类型为double,而函数返回一个int表达式，则该int值被强制转换为double）。

C++对返回值的类型有一定的限制：不能是数组，但可以是其他任何类型---整型、浮点数、指针，甚至可以是结构和对象。虽然C++函数不能之间返回数组，但可以将数组作为结构或对象组成部分来返回。

函数如何返回值：函数通过将返回值复制到指定CPU寄存器或内存单元中来将其返回。随后，调用程序将查看该内存单元。返回函数和调用函数必须就该内存单元中存储的数据的类型达成一致。


函数包含多条返回语句：

	int bigger (int a, int b)
	{
		if (a > b)
			return a;
		else //else可以省略，程序意图未变
			return b;	
	}


### 7.1.2 函数原型和函数调用

#### 1.函数原型必要性

原型描述了函数到编译器的接口，即，它将函数返回值的类型以及参数的类型和数量告诉编译器。比如，函数调用：

	double volume = cube(side);

首先，原型告诉编译器，cube()有一个double参数。如果程序没有提供这样的参数，原型将让编译器能够捕获这种错误。其次，cube()函数完成计算后，将把返回值放在执行位置--可能是CPU寄存器，也可能是内存。然后，调用，调用函数将从这个位置取得这个返回值。由于原型指出了cube()的类型为double，因此编译器知道检索多少个自己以及如何解释它们。

编译器需要原型，难道它就不能在文件中进一步查找，以了解函数是如何定义的吗？这种方法的一个问题是效率不高。编译器在搜索文件的剩余部分时，必须停止对mian()的编译。一个更严重的问题时，函数甚至可能并不在文件中。C++允许将一个程序放在多个文件中，单独编译这些文件，然后组合起来。这种情况下，编译器在编译mian()时，可能无权访问函数代码。


#### 2. 原型的语法

函数原型是一条语句，因此必须以分号结束。获得原型最简单的方法是：复制函数定义中的函数头，并添加分号。

	double cube(double x);

然而，函数原型不要求提供变量名，有类型列表就足够了。
	
	void cheers(int);

原型中的变量名相当于占位符，因此不必与函数定义中的变量名相同。

#### 3. 原型的功能

- 编译器正确处理函数返回值；
- 编译器检查使用的参数数目是否正确；
- 编译器检查使用的参数类型是否正确。如果不正确，则转换为正确的类型（如果可以的话）。

	cheers(cube(2));

首先，cube()原型指定了一个doulbe类型参数，因此将2转换为2.0。接下来，cube()返回一个double值0.8，这个值被用作cheers()的参数。编译器再一次检查原型，并发现cheers()要求一个int参数，因此将返回值转换为整数8。

## 7.2 函数参数和按转传递

C++通常按值传递参数，这意味着将数值参数传递给函数，而后者将其复制给一个新的变量。如下：

	double volume = cube(side);

其中，side是一个变量。cube()的函数头如下:

	double cube(double x);

被调用时，该函数将创建一个新的名为x的double变量，并将其初始化为5。这样，cube()执行的操作将不会影响main()中的数据，因此cube()使用的是side的副本，而不是原来的数据。

用于接收传递值得变量称为形参。传递给函数得值被称为实参。C++标准使用参数来表示实参，使用参量来表示形参，因此参数传递将参数赋给参量。

在函数中声明的变量(包括参数)是该函数私有的。在函数被调用时，计算机将为这些变量分配内存；在函数结束时，计算机将释放这些变量使用的内存。这样的变量被称为局部变量。


### 7.2.1 多个参数

函数可以有多个参数。在调用函数时，只需使用逗号将这些参数分开即可：

	n_chars('R', 25);

同样，在定义函数时，也在函数头中使用由逗号分隔的参数声明列表：

	void n_chars(char c, int n)

该函数头指出了，函数n_chars()接收一个char参数和一个int参数。传递给函数的值被赋值给参数c和n。如果函数的两个参数的类型相同，必须分别指定每个参数的类型，而不能像声明常规变量那样，将声明组合在一起：

	void fifi(float a, float b)
	void fifi(float a, b) //非法的

和其他函数一样，只需添加分号就可以得到该函数的原型：

	void n_chars(char c, int n);

和一个参数一样，原型中的变量名不必与定义中的变量名相同，而且可以省略：

	void n_chars(char, int);

然而，提供变量名将使原型更容易理解，尤其是两个参数的类型相同时，这样，变量名可以提醒参量和参数之间的对应关系。

twoarg.cpp

	#include <iostream>
	using namespace std;
	void n_chars(char, int);
	int main()
	{
		int times;
		char ch;
		cout << "Enter a character: ";
		cin >> ch;
		while (ch !=  'q')
		{
			cout << "Enter an integer: ";
			cin >> times;
			n_chars(ch, times);
			cout << "\nEnter another character or presss the q to quit: ";
			cin >> ch;
		}
		cout << "The value of times is  " << times << ".\n";
		return 0;
	}
	
	void n_chars(char c, int n)
	{
		while (n-- > 0)
			cout << c;
	}


结果

	Enter a character: h
	Enter an integer: 10
	hhhhhhhhhh
	Enter another character or presss the q to quit: q
	The value of times is  10.

程序中使用cin>>ch，而不使用cin.get(ch)或ch=cin.get()来读取一个字符，这样因为cin.get()函数读取所有的字符，包括空格和换行符，而cin>>跳过空格和换行符。当用户对程序提示做出响应时，必须在每行的最后按下Enter键，以生成换行符。cin>>ch方法可以轻松跳过这些换行符。

n的自减对main()中的times的值没有影响。

#### 7.2.2 另一个接收两个参数的函数

假设从51个数中选取6个，计算不同组合的数量。

totto.cpp

	#include <iostream>
	using namespace std;
	long double probability(unsigned numbers, unsigned picks);
	int main()
	{
		double total, choices;
		cout << "Enter the total nember of choices on the game card and the number of picks allowed: \n";
		while ((cin >> total >> choices) && total >=  choices)
		{
			cout << "You have one chance in ";
			cout <<  probability(total, choices);
			cout << " of wining.\n";
			cout << "Next two numbers (q to quit): ";
		}
		return 0;
	}
	long double probability(unsigned numbers, unsigned picks)
	{
		long double result = 1.0;
		long double n;
		unsigned p;
		for (n = numbers, p = picks; p > 0; p--, n--)
			result *= n/p;
		return result;
	}


结果

	Enter the total nember of choices on the game card and the number of picks allowed: 
	51 6
	You have one chance in 1.80095e+07 of wining.
	Next two numbers (q to quit): 10 2
	You have one chance in 45 of wining.
	Next two numbers (q to quit): q


## 7.3 函数和数组

将一个数组名作为参数传递给函数，为使函数通用，而不限于特定长度的数组，还需要传递数组长度。将一个形参声明为数组名。函数头如下：

	int sum_arr(int arr[], int n)

方括号指出arr是一个数组，而方括号为空则表明，可以将任何长度的数组传递给该函数。实际情况是：arr并不是数组，而是一个指针！在编写函数时，可以将arr看作时数组。

arrfun1.cpp

	#include <iostream>
	const int ArSize = 8;
	int sum_arr(int arr[], int n);
	using namespace std;
	int main()
	{
		int cookies[ArSize] = {1,2,4,8,16,32,64,128};
		int sum = sum_arr(cookies, ArSize);
		cout << "Total: " << sum << endl;
		return 0;
	}
	int sum_arr(int arr[], int n)
	{
		int total = 0;
		for (int i = 0; i < n; i++)
			total += arr[i];
		return total;
	}


结果： Total: 255


### 7.3.1 函数如何使用指针来处理数组

在前面介绍过，C++将数组名解释为起第一个元素的地址：

	cookies = &cookies[0];

该规则有一些例外。首先，数组声明使用数组名来标记存储位置；其次，对数组名使用sizeof将得到整个数组的长度；第三，正如前面指出的，将地址运算符&用于数组名时，将返回整个数组的地址，例如，&cookes将返回一个32字节内存卡的地址。

	int sum = sum_arr(cookies, ArSize);

其中，cookies是数组名，而根据C++规则，cookies是其第一个元素的地址，因此函数传递的是地址。由于数组的元素为int，因此cookies的类型必须是int指针，即int*。正确的函数头为：

	int sum_arr(int *arr, int n)

其中用int * arr替换了int arr[]。这证明这两个函数头都是正确的。这种表示仅在函数头和函数原型中等价，不能在函数体中，使用int arr[]来声明指针。

下面两个等式等价：

	arr[i] = *(arr+i);
	&arr[i] = arr + i;

### 7.3.2 将数组作为参数意味着什么


arr_sum()函数将cookies的地址赋给指针变量arr,将ArSize赋给int变量n。这意味着并没将数组内容传递给函数，而是将数组的位置、包含的元素种类以及元素数目提交给函数

传递常规变量时，函数将使用该变量的拷贝；但传递数组时，函数将使用原来的数组。实际上，这种区别并不违反C++按值传递的方法，sum()函数传递了一个值，这个值被赋给一个新变量，但这个值是一个地址，而不是数组的内容。

如果数组很大，则使用拷贝的系统开销将非常大；程序不仅需要更多的计算机内存，还需要花费时间来复制大块的数据。另一方面，使用原始数据增加了破坏数据的风险。

arrfun2.cpp

	#include <iostream>
	const int ArSize = 8;
	int sum_arr(int arr [], int n);
	using namespace std;
	int main()
	{
		int cookies[ArSize] = {1,2,4,8,16,32,64,128};
		cout <<  cookies << " = array address, " << sizeof(cookies) << " = sizeof cookies.\n";
		
		int sum = sum_arr(cookies, ArSize);
		cout << "Total  cookies eaten: " << sum << endl;
	
		sum = sum_arr(cookies, 3);
		cout << "First three eaters ate " << sum << endl;
		sum = sum_arr(cookies+4, 4);
		cout << "Last four eaters ate " << sum << endl;
		return 0;
	}
	int sum_arr(int arr[],int n)
	{
		int total = 0;
		cout << arr << " = arr, ";
		cout << sizeof(arr) << " =  sizeof arr.\n"; //实际得到的地址的长度
		for (int i = 0; i < n; i++)
			total += arr[i];
		return total;
	}


结果

	0x7ffff779f8b0 = array address, 32 = sizeof cookies.
	0x7ffff779f8b0 = arr, 8 =  sizeof arr.
	Total  cookies eaten: 255
	0x7ffff779f8b0 = arr, 8 =  sizeof arr.
	First three eaters ate 7
	0x7ffff779f8c0 = arr, 8 =  sizeof arr.
	Last four eaters ate 240


注意，地址值和数组的长度随系统而异。

cookies和arr指向同一个地址，但sizeof(cookies)的值为32，而sizeof(arr)为8。这是由于sizeof(cookies)是整个数组的长度，而sizeof(arr)只是指针变量的长度。这是必须现实传递数组长度，而不能在sum_arr()中使用sizeof(arr)的原因；指针本身没有指出数组的长度。


**注意：**

将数组类型和元素数量告诉数组处理函数，**不要**试图使用方括号表示法来传递数组长度：

	void filarray(int arr[size]); //非法的

### 7.3 更多数组函数示例

假设要使用一个数组记录房地产的价值。

#### 1. 填充数组

由于接收数组名参数的函数访问的是原始数组，而不是副本，因此可以通过调用该函数将值赋给数组元素。该函数的一个参数是要填充的数组的名称。第二个参数是数组的长度。由于用户输入的元素数目可能少于数组的长度，因此函数返回实际输入的元素数目。

停止循环的方法：使用一个特殊值来指出输入结束。由于所有的属性都不为负数，因此可以使用负数来指出输入结束。

#### 2. 显示数组及使用const保护数组

接受数组名的函数将使用原始数据，为防止函数无意中修改数组的内容，可在声明形参时使用关键字const。

	void show_array(const double ar[], int n);

该声明表明，指针ar指向的是常量数组。这意味着不能使用ar修改该数据。

#### 3. 修改数组

具体见代码

#### 4. 组合

arrfun3.cpp

	#include <iostream>
	const int Max = 5;
	int fill_array(double ar[], int n);
	void show_array(const double ar[], int n);
	void revalue(double r, double ar[], int n);
	using namespace std;
	int main()
	{
		double properties[Max];
		int size = fill_array(properties,Max);
		show_array(properties, size);
		if (size > 0)
		{
			cout << "Enter revaluation factor: ";
			double factor;
			while (!(cin >> factor))
			{
				cin.clear();
				while (cin.get() != '\n')
					continue;
				cout << "Bad input; Please enter a number: ";
			}
			revalue(factor, properties, size);
			show_array(properties, size);
		}
		return 0;
	}
	int fill_array(double ar[], int n)
	{
		double temp;
		int i;
		for (i = 0; i < n; i++)
		{
			cout << "Enter values #"<< i+1 << " : " ;
			cin >> temp;
			if (!cin)
			{
				cin.clear();
				while (cin.get() != '\n' ) //读取输入队列中的所有内容
					continue;
				cout << "Bad input; input process terminated.\n";
				break;
			}
			else if (temp < 0)
				break;
			ar[i] = temp;
		}
		return i;
	}
	
	void show_array(const double ar[], int n)
	{
		for (int i = 0; i < n; i++)
			cout << "Property #" << i+1 << ": $ " << ar[i] << endl;
	}
	
	void revalue(double r, double ar[], int n)
	{
		for (int i = 0; i < n; i++)
			ar[i] *=  r;
	}

结果：

	Enter values #1 : 30000
	Enter values #2 : 20000
	Enter values #3 : 10000
	Enter values #4 : -3
	Property #1: $ 30000
	Property #2: $ 20000
	Property #3: $ 10000
	Enter revaluation factor: 0.8
	Property #1: $ 24000
	Property #2: $ 16000
	Property #3: $ 8000
	[root@localhost ~]# ./a.out
	Enter values #1 : oo
	Bad input; input process terminated.


#### 5. 数组处理函数的常用编写方式

如果函数修改数组，其原型可能如下：

	void f_modify(double ar[], int n);

如果函数不修改数组，其原型可能如下：

	void f_no_change(const double ar[], int n);


### 7.3.4 使用数组区间的函数

前面，对于处理数组的C++函数，必须将数组中的数据类型、数组的起始位置和数组中元素的数量提交给它。

还有一种给函数提供所需信息的方法，即指定元素区间，这块车通过传递两个指针来完成；一个指针表示数组的开头，另一个指针表示数组的尾部。

	double elbuod[20];

则指针elbuod和elbuod+20定义了区间。首先，数组名elbuod指向第一个元素。表达式elbuod+19指向最后一个元素，因此elbuod+20指向数组结尾后的一个位置。

arrfun4.cpp

	#include <iostream>
	const int ArSize = 8;
	int sum_arr(int * begin, int * end);
	using namespace std;
	int main()
	{
		int cookies[ArSize] = {1,2,4,8,16,32,64,128};
		cout <<  cookies << " = array address, " << sizeof(cookies) << " = sizeof cookies.\n";
		
		int sum = sum_arr(cookies, cookies+ArSize);
		cout << "Total  cookies eaten: " << sum << endl;
	
		sum = sum_arr(cookies, cookies+3);
		cout << "First three eaters ate " << sum << endl;
		sum = sum_arr(cookies+4, cookies+8);
		cout << "Last four eaters ate " << sum << endl;
		return 0;
	}
	int sum_arr(int * begin, int * end)
	{
		const int * pt;
		int total = 0;
		for (pt = begin; pt !=  end; pt++)
			total +=  *pt;
		return total;
	}


结果

	Total  cookies eaten: 255
	First three eaters ate 7
	Last four eaters ate 240


程序中，将pt设置为指向第一个元素的指针，并将*pt加入到total扎。然后，循环通过递增操作来更新pt，使之指向下一个元素。

### 7.3.5 指针和const

可以用两种不同的方式将const关键字用于指针。第一种方法是让指针指向一个常量对象，这样可以防止使用该指针来修改所指向的值；第二种方法是将指针本身声明为常量，这样可以防止改变指针指向的位置。

#### 1. const int * 

	int age = 39;
	const int * pt = &age;

该声明指出，pt指向一个const int，因此不能使用pt来修改这个值。即，*pt的值为const，不能被修改：

	*pt += 1; //非法
	cin >> *pt; //非法

一个微妙的问题，pt的声明并不意味着它指向的值实际上就是一个常量，而只是意味着对pt而言，这个值是常量。比如，pt指向age，而age不是const。可以直接通过age变量来修改age的值，但是不能使用pt指针来修改它：

	*pt = 20; //非法
	age = 20; //合理

还有两种可能：第一种，将const变量的地址赋给指向const的指针；第二种，将const的地址赋给常规指针。第一种可行，第二种不可行：

	cont float g_earth = 9.80;
	cont float * pe = &g_earth; //合理

	const float g_moon = 1.63;
	float *pm = &g_moon; //非法

对于第一种情况，既不能修改g_earth来修改值9.80，也不能使用pe来修改。C++禁止第二种情况的原因：如果将g_moon的地址赋给pm，则可以使用pm来修改g_moon的值，这使得g_moon的const状态很荒诞。

如果将指针指向指针，则情况更复杂。假如涉及的是一级间接关系，则将非const指针赋给const指针是可以的：
	
	int age = 39;
	int *pd = &age;
	const int * pt = pd;

然而，进入两级间接关系时，与一级间接关系一样，将const和非const混合的指针赋值方式将不再安全。如果允许这样做，则可以编写这样的代码：

	const int  **pp2;
	int *p1;
	const int n = 13;
	pp2 = &p1; //非法，但是假设其成立
	*pp2 = &n; //合理
	*p1 = 10; //合理

上述代码将非const地址(&p1)赋给了指向const指针(pp2)，因此可以使用p1来修改const数据。因此，仅当只有一层间接关系时，才可以将非const地址或指针赋给const指针。

假设一个由const数据组成的数组：

	const int monthes[5] = {31, 28, 31, 30, 31};

则禁止将常量数组的地址赋给非常量指针，这意味着不能将数组名作为参数传递给非常量形参的函数：

	int sum(int arr[], int n); // 应该写为： const int arr[]
	int j = sum(months, 12); //不被允许

上述函数调用试图将const指针赋给非const指针，编译器将禁止这种函数调用。

另一个微妙之处，声明：

	int age = 39;
	const int * pt = &age;

第二个声明种的const只能防止修改pt指向的值，但不能修改pt的值，即可以将一个新地址赋给pt:

	int sage = 40;
	pt = &sage;

但仍然不能使用pt来修改它指向的值。

#### 2. int * const

使用const的方式使得无法修改指针的值：

	int sloth = 3;
	const int *ps = &sloth;
	int * const finger = &sloth;

最后一个声明，关键字const的位置与以前不同。这种声明格式使得finger只能指向sloth，但允许使用finger来修改sloth的值。中间的声明不允许使用ps来修改sloth的值，但可以将ps指向另一位置。即，finger和$\ast$p都是const，而$\ast$finger和ps不是。

如果愿意还可以声明指向const对象的const指针：

	double trouble = 2.0E30;
	const double * const stick = &trouble;

其中，stick只能指向troulbe，而stick不能用来修改trouble的值。即const和$\ast$const都是const。


## 7.4 函数和二维数组

为编写将二维数组作为参数的函数，必须牢记，数组名被视为其地址，因此响应的形参是一个指针。比如：

	int data[2][4] = {{1,2,3，4},{4,5,6，7}};
	int total = sum(data, 3);

data是一个数组名，该数组有2个元素。第一个元素本身就是一个数组，有4个int值组成。因此data的类型是指向由4个int组成的数组的指针，正确的原型如下：

	int sum(int (*ar2)[4], int size);

其中的括号是必不可少的，因为下面声明将声明一个由4个指向int的指针组成的数组，而不是由一个指向由4个int组成的数组的指针；林伟函数不能是数组：

	int *ar2[4]; //4个指针指针组成的数组

还有另一个种格式，这种格式与上述原型的含义完全相同，但可读性更强:

	int sum(int ar2[][4], int size);

上述两个原型都指出，ar2是指针而不是数组。指针类型指出，它指向由4个int组成的数组。因此，指针类型指定了列数。

由于指针类型指定了列数，因此sum()函数只能接受由4列组成的数组。但长度变量指定了行数，因此sum()对数组的行数没有限制：

	int a[100][4];
	int b[60][4];

	int total1 = sum(a, 100);
	int total2 = sum(b, 60);

对于参数ar2是指向数组的指针，在函数定义中使用它，最简单的办法是将ar2看作一个二维数组的名称。

同样行数被传递给size参数，当无论是参数ar2的声明或是内部for循环中，列数都是固定的--4列。

ar2[r]是编号为r的元素，由于该元素本身就是一个由4个int组成的数组，因此ar2[r]是由4个int组成的数组的名称。将下标用于数组名将得到一个数组元素，因此ar2[r][c]是由4个int组成的数组中的一个元素，是一个int值。必须对指针ar2执行两次解除引用，才能得到数据。也可以使用运算符*两次：

	ar2[r][c] = *(*(ar2+r)+c)


## 7.5 函数和C-风格字符串

C-风格自身串由一系列字符组成，以空值字符结尾。

### 7.5.1 将C-风格字符串作为参数的函数

假设要将字符串作为参数传递给函数，则表示字符串的方式有三种：

- char数组
- 用引号括起来的字符串常量（字符串字面值）
- 被设置为字符串的地址的char指针

但上述3中选择的类型都是char指针（准确地说是char$\ast$），因此可以将其作为字符串处理函数的参数：

	char ghost[15] = "galloping";
	char * str = "galumphing";
	int n1 = strlen(ghone);
	int n2 = shrlen(str);

可以说，将字符串作为参数来传递，但实际上传递的是字符串第一个字符的地址。这意味着字符串函数原型将其表示字符串的形参声明为char$\ast$类型。

C-风格字符串与常规数组之间的重要区别是：字符串有内置的结束字符（不以空值字符结尾的char数组只是数组，而不是字符串）。这意味着不必将字符串长度作为参数传递给函数，而函数可以使用循环依次检查字符串中的每个字符，直到遇到结尾的空字符为止。

strfun.cpp

	#include <iostream>
	unsigned int c_in_str(const char * str, char ch);
	using namespace std;
	int main()
	{
		char mmm[15] = "minimum";
		const char *wail = "ululate";
		unsigned int ms = c_in_str(mmm, 'm');
		unsigned int us = c_in_str(wail, 'u');
		cout << ms << " m characters in " << mmm << endl;
		cout << us << " u characters in " << wail << endl;
		return 0;
	}
	
	unsigned int c_in_str(const char * str, char ch)
	{
		unsigned int count = 0;
		while(*str)
		{
			if (*str == ch)
				count ++;
			str++;
		}
		return count;
	}


结果：

	3 m characters in minimum
	2 u characters in ululate

为了防止c_in_str()函数修改原始字符串，因此在函数声明形参str时使用了限定符const。

str最初指向字符串的第一个字符，因此*str表示的第一个字符。


### 7.5.2 返回C-风格字符串的函数

strgback.cpp

	#include <iostream>
	char * buildstr(char c, int n);
	using namespace std;
	int main()
	{
		int times;
		char ch;
		cout << "Enter a character: ";
		cin >> ch;
		cout << "Enter a integer: ";
		cin >> times;
		char * ps = buildstr(ch, times);
		cout << ps << endl;
		delete [] ps;
		ps = buildstr('+', 20);
		cout << ps <<  "-DONE-" << ps << endl;
		delete [] ps;
		return 0;
	}
	
	char * buildstr(char c, int n)
	{
		char * pstr = new char [n+1];
		pstr[n] = '\0';
		while(n--)
			pstr[n] = c;
		return pstr;
	}

结果：

	Enter a character: V
	Enter a integer: 46
	VVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVVV
	++++++++++++++++++++-DONE-++++++++++++++++++++

要创建包含n个字符的字符串，需要能够存储n+1个字符的空间，以便能够存储空值字符。程序中请求分配了n+1个字节的内存来存储字符串，并将最后一个字节设置为空值字符。

n--表示先在测试条件中比较，然后将其值递减。因此，当n=20时，首先20大于0，然后将其值递减1，于是循环体里面的n=19。

注意pstr的作用为buildstr函数内，因此该函数结束时，pstr使用的内存将被释放。但由于函数返回了pstr的值，因此程序仍可以通过main()中的指针ps来访问新建的字符串。当该字符串不再需要时，使用delete释放该字符串所占用的内存。


## 7.6 函数和结构

使用结构编程时，最直接的方式就像处理基本类型那样来处理结构；即将结构作为参数传递，并在需要时将结构用作返回值使用。在这种情况下，函数将使用原始结构的副本，与处理数组不同。如果结构非常大，则复制结构将增大内存要求，降低系统运行速度。出于这些愿意，许多C程序倾向于传递结构的地址，然后使用指针来访问结构的内容。C++提供了第三种选择---按引用传递（下一章介绍）。

### 7.6.1 传递和返回结构

当结构比较小时，按值传递结构最合理。第一个例子处理行程时间，比如从Falls到Bingo需要3小时50分钟，而Bingo到Gotesquo需要1小时25分钟。对于这种时间，可以使用结构表示--一个成员表示小时值，另一个成员表示分钟值。将两个时间加起来需要一些技巧，因为可能需要将分钟值转换为小时。具体如下：

travel.cpp

	#include <iostream>
	using namespace std;
	struct travel_time
	{
		int hours;
		int mins;
	};
	
	const int Mins_per_hr = 60;
	
	travel_time sum(travel_time t1, travel_time t2);
	void show_time(travel_time t);
	
	int main()
	{
		travel_time day1 = {5, 45};
		travel_time day2 = {4, 55};
	
		travel_time trip = sum(day1, day2);
		cout << "Two-day total: ";
		show_time(trip);
		
		return 0;
	}
	
	travel_time sum(travel_time t1, travel_time t2)
	{
		travel_time total;
		total.mins = (t1.mins + t2.mins) % Mins_per_hr;
		total.hours = t1.hours + t2.hours + (t1.mins + t2.mins) / Mins_per_hr;
		return total;
	}
	
	void show_time(travel_time t)
	{
		cout << t.hours << " hours, "
			<< t.mins << " mins.\n";
	}


结果

	Two-day total: 10 hours, 40 mins.

其中，travel_time就像一个标准类型名，可被用来声明变量、函数的返回类型和函数参数类型。


### 7.6.2 另一个处理结构的函数示例

表示二维坐标中的一个点的方法有两种：一种是指出该点相对远点的水平偏移和垂直便宜（x-y）；另一个种是指出它偏离远点的距离和方向（极坐标）。实现表示两种表示方法，以及之间的相互转换，如下程序：

atrctfun.cpp

	#include<iostream>
	#include<cmath> 
	using namespace std;
	
	struct rect 
	{
		double x;
		double y;
	};
	struct polar
	{
		double distance;
		double angle;	
	};
	
	polar rect_to_polar(rect xypos);
	void show_polar(polar dapos);
	
	int main()
	{
		rect rplace;
		polar pplace;
		
		cout << "Enter the x and y value: ";
		while (cin >> rplace.x >> rplace.y)
		{
			pplace = rect_to_polar(rplace);
			show_polar(pplace);
			cout << "Nest two number(q to quit): ";
		}
		return 0;
	}
	
	polar rect_to_polar(rect xypos)
	{
		polar answer;
		answer.distance = sqrt(xypos.x * xypos.x + xypos.y * xypos.y);
		answer.angle = atan2(xypos.y, xypos.x);
		return answer;
	}
	
	void show_polar(polar dapos)
	{
		const double Rad_to_deg = 57.29577951;
		cout <<  "Distance = " << dapos.distance;
		cout << ", Angle =  " << dapos.angle * Rad_to_deg << " degrees.\n";
	}


结果
	
	Enter the x and y value: 30 40
	Distance = 50, Angle =  53.1301 degrees.
	Nest two number(q to quit): -100 100
	Distance = 141.421, Angle =  135 degrees.
	Nest two number(q to quit): q

### 7.6.3 传递结构的地址

需要修改的滴很：

- 调用函数时，将结构的地址(&pplace)而不是结构本身(pplace)传递给它；
- 将形参声明为指向polar的指针，即polar$\ast$类型，由于函数不应该修改结构，因此使用了const修饰符；
- 由于形参是指针而不是结构，因此应使用间接成员运算符(->)，而不是成员运算符（句点）。

strctptr.cpp

	#include<iostream>
	#include<cmath> 
	using namespace std;
	
	struct rect 
	{
		double x;
		double y;
	};
	struct polar
	{
		double distance;
		double angle;	
	};
	
	void rect_to_polar(const rect *xypos,  polar *dapos);
	void show_polar(const polar *dapos);
	
	int main()
	{
		rect rplace;
		polar pplace;
		
		cout << "Enter the x and y value: ";
		while (cin >> rplace.x >> rplace.y)
		{
			rect_to_polar(&rplace, &pplace);
			show_polar(&pplace);
			cout << "Nest two number(q to quit): ";
		}
		return 0;
	}
	
	void  rect_to_polar(const rect *xypos, polar *dapos)
	{
		dapos->distance = sqrt(xypos->x * xypos->x + xypos->y * xypos->y);
		dapos->angle = atan2(xypos->y, xypos->x);
	}
	
	void show_polar(const polar *dapos)
	{
		const double Rad_to_deg = 57.29577951;
		cout <<  "Distance = " << dapos->distance;
		cout << ", Angle =  " << dapos->angle * Rad_to_deg << " degrees.\n";
	}

结果同上

# 遇到的问题(待解决)

在编写时，发现如果开始将结构初始为结构指针，如下：

	rect *rplace;
	polar *pplace;

而这样在输入x,y的之后出现**段错误(吐核)**。根据网上教程，在./a.out之前，加上strace，即 strace ./a.out，看不懂到底是什么原因。而使用上述程序种的方式不会有问题。

![](https://i.imgur.com/W5MWJz7.png)

## 7.7 函数核string对象

虽然C-风格字符串和string对象的用途几乎相同，但与数组相比，string对象与结构的更相似。例如：可以将一个结构赋给另一个结构，也可以将一个对象赋给另一个对象。可以将结构作为完整的实体传给函数，也可以将对象作为完整的实体进行传递。如果需要多个字符串，可以声明一个string对象数组，而不是二维char数组。

topfive.cpp

	#include <iostream>
	#include <string>
	using namespace std;
	const int SIZE = 5;
	void display(const string sa[], int n);
	int main()
	{
		string list[SIZE];
		cout << "Enter your " << SIZE << " favorite astronomicla sights: \n";
		for (int i = 0; i < SIZE; i++)
		{
			cout << i + 1 << ": ";
			getline(cin, list[i]);
		}
		
		cout << "Your list: \n";
		display(list, SIZE);
		return 0;
	}
	
	void display(const string sa[], int n)
	{
		for (int i = 0; i < n; i++)
			cout << i + 1<< ": " << sa[i] << endl;
	}


结果

	Enter your 5 favorite astronomicla sights: 
	1: Mon
	2: Jupiter
	3: Saturn
	4: M13
	5: Orion Nebula
	Your list: 
	1: Mon
	2: Jupiter
	3: Saturn
	4: M13
	5: Orion Nebula


## 7.8 函数和array对象

假设要使用一个array对象来存储一年四个季度的开支：

	std::array<double, 4> expenses;

要使用array类，需要包含头文件array，而名称array位于名称空间std中。如果函数来显示expenses的内容，可按值传递expenses:

	show(expenses);

但如果函数要修改对象expenses，则需将对象的地址传给函数：

	fill(&expenses);

声明上述两个函数为array<double, 4>，因此必须在函数原型中指定这种类型：

	void show(std::array<double, 4> da);
	void fill(std::array<doulbe, 4> * pa);

首先，使用符号常量替换4：

	const int Seasons = 4;

使用一个const array对象，该对象包含4个string对象，用于表示几个季度：

	const std::array<std::string, Season> Sname = {"Spring", "Summer", "Fall", "Winter"};


arrobj.cpp

	#include <iostream>
	#include <array>
	#include <string>
	using namespace std;
	const int Seasons = 4;
	const array<string, Seasons> Sname = {"Spring", "Summer", "Fall", "Winter"};
	
	void fill(array<double, Seasons> * pa);
	void show(array<double, Seasons> da);
	
	int main()
	{
		array<double, Seasons> expenses;
		fill(&expenses);
		show(expenses);
		return 0;
	}
	
	void fill(array<double, Seasons> *pa)
	{
		for (int i = 0; i< Seasons; i++)
		{
			cout << "Enter " << Sname[i] << " expenses: ";
			cin >> (*pa)[i];
		}
	}
	
	void show(array<double, Seasons> da)
	{
		double total = 0.0;
		cout << "EXPENSES\n";
		for (int i = 0; i < Seasons; i++)
		{
			cout << Sname[i] << ": $" << da[i] << endl;
			total += da[i];
		}
		cout << "Total expenses: $" << total << endl;
	}


结果

	Enter Spring expenses: 1000
	Enter Summer expenses: 2000
	Enter Fall expenses: 2000
	Enter Winter expenses: 4000
	EXPENSES
	Spring: $1000
	Summer: $2000
	Fall: $2000
	Winter: $4000
	Total expenses: $9000


注意，String是C++11中的内容，在使用Linux编译时，需要告诉编译器使用C++11的编译器，如下：

	g++ -std = c++ arrobj.cpp

因为在代码最前面添加了编译指令using，因此可以不适用std::来限定array和string。


## 7.9 递归

C++函数有一种有趣的特点--可以自己调用自己，这种功能被称为递归。

### 7.9.1 包好一个递归调用的递归

如果递归函数调用自己，则被调用的函数也将调用自己，这将无限循环下去，除非代码中包含终止调用链的内容。通常的方法将递归调用放在if语句中。如下：

	void recurs(argumentlist)
	{
		statements1
		if (test)
			recurs(arguments)
		statements2
	}

test为false，调用链断开。只要if语句为true，每个recurs()调用都将执行statements1，然后再调用recurs()，而不会执行statements2。当if语句为false时，当前调用执行statements2。当前调用结束后，程序控制权将返回给调用它的recurs()，而该recurs()将执行其statements2部分，然后结束，并将控制权返回给前一个调用，依次类推。因此，如果recurs()进行了5次递归调用，则第一个statements1部分将按函数调用的顺序执行5次，然后，statements部分将以与函数调用相反的顺序执行5次。进入5层递归后，程序将沿进入的路径返回。

recur.cpp
	
	#include <iostream>
	void countdown(int n);
	using namespace std;
	int main()
	{
		countdown(4);
		return 0;
	}
	
	void countdown(int n)
	{
		cout << "Count down ... " << n << endl;
		if (n > 0)
			countdown(n-1);
		cout << n << ": Kaboom!\n";
	}


结果
	
	Count down ... 4
	Count down ... 3
	Count down ... 2
	Count down ... 1
	Count down ... 0
	0: Kaboom!
	1: Kaboom!
	2: Kaboom!
	3: Kaboom!
	4: Kaboom!

注意，每个递归调用都创建自己的一套变量，因此当程序达到第5次调用时，将有5个独立的变量n，其中每个变量的值都不同。为了验证这一点，使之显示n的地址和值：

	cout << "Count down ... " << n << "(n at" << &n << " )" << endl;
	if (n > 0)
		countdown(n-1);
	cout << n << ": Kaboom!" <<  "      " << "(n at" << &n << " )" << endl;

程序输出结果为：

	Count down ... 4(n at0x7ffc9be6862c )
	Count down ... 3(n at0x7ffc9be685fc )
	Count down ... 2(n at0x7ffc9be685cc )
	Count down ... 1(n at0x7ffc9be6859c )
	Count down ... 0(n at0x7ffc9be6856c )
	0: Kaboom!      (n at0x7ffc9be6856c )
	1: Kaboom!      (n at0x7ffc9be6859c )
	2: Kaboom!      (n at0x7ffc9be685cc )
	3: Kaboom!      (n at0x7ffc9be685fc )
	4: Kaboom!      (n at0x7ffc9be6862c )

### 7.9.2 包含多个递归调用的递归

在需要将一定工作不断分为两项较小的、类似的工作时，递归非常有用。例如：考虑使用这种方法来绘制绘制标尺的情况。标出两端，找到重点并将其标出。然后，将同样的操作用于标尺的左半部分和右半部分。如果要进一步细分，同将同样的操作用于当前的每一部分。递归方法有时被称为分而治之策略。

下面程序使用subdivide()演示了这种方法，该函数使用一个字符串，该字符串处两端为|字符为，其他都为空格。main函数使用循环调用subdivide()函数6次，每次见递归层编号加1，并打印得到的字符。这样，每行输出表示一层递归。

ruler.cpp

	#include <iostream>
	const int len = 66;
	const int Divs = 6;
	void subdivide(char ar [], int low, int  hight, int level);
	using namespace std;
	int main()
	{
		char ruler[len];
		int i ;
		for (i = 1; i < len - 2; i++)
			ruler[i] = ' ';
		ruler[len-1] = '\0';
		int max = len - 2;
		int min = 0;
		ruler[min] = ruler[max] = '|';
		cout << ruler << endl;
		for (i = 1; i <= Divs; i++)
		{
			subdivide(ruler, min, max, i);
			cout << ruler << endl;
			for (int j = 1; j < len -2; j++)
				ruler[j] = ' ';
		}
		return 0;
	}
	
	void subdivide(char ar[], int low, int high, int level)
	{
		if (level == 0)
			return ;
		int mid = (high + low) / 2;
		ar[mid] = '|';
		subdivide(ar, low, mid, level-1);
		subdivide(ar, mid, high, level-1);
	}


结果

	|                                                               |
	|                               |                               |
	|               |               |               |               |
	|       |       |       |       |       |       |       |       |
	|   |   |   |   |   |   |   |   |   |   |   |   |   |   |   |   |
	| | | | | | | | | | | | | | | | | | | | | | | | | | | | | | | | |
	|||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||


subdivide()使用变量level来控制递归层。函数调用自身时，将把level减1，当level为0时，该函数将不再调用自己。注意，subdivide()调用自己两次，一次针对左半部分，另一个针对右半部分。最初的中点被用作一次调用的右端点和另一次调用的左端点。调用次数从指数级增长。如果要求的递归层次很多，这种递归方式将是一种糟糕的选择；然而对于递归层次较少，这将是一种精致而简单的选择。

## 7.10 函数指针

与数据项类似，函数也有地址。函数的地址是存储器机器语言代码的内存的地址。通常，这些地址对于用户而言，既不重要，也没有什么用处，但对程序而言，却很重要。可以编写将另一个函数第地址作为参数的函数。这样第一个函数将能够找到第二个函数，并运行它。与直接调用另一个函数相比，这种方法很笨拙，但它允许在不同的时间传递不同的函数的地址，这意味着可以在不同的时间使用不同的函数。

### 7.10.1 函数指针的基础知识

通过一个例子来阐述这一过程。假设要设计一个名为estimate()函数，估算编写执行函数的代码所需的时间，并且希望不同的程序员都可以使用这个函数。对于所有用户来说，estimate()中一部分代码都是相同的，但该函数允许每个程序员提供自己的算法来估算时间。为实现这个目标，采用的机制是，将程序员使用的算法函数的地址传给estimate()。为此，必须完成如下工作：

- 获取函数地址
- 声明一个函数指针
- 使用函数指针来调用函数

#### 1.获取函数地址

获取函数的地址很简单：只要使用函数名(后面不跟参数)即可。即，如果think()是一个函数，则think就是该函数的地址。

要将函数作为参数进行传递，必须传递函数名。一定要区分传递的是函数的地址还是函数的返回值：

	process(think);
	process(think());//传递的是函数的返回值

#### 2.声明函数指针

声明指向某种数据类型的指针时，必须指定指针指向的类型。同样，声明指向函数的指针时，也必须指定指针指向的函数类型。这意味着声明应指出函数的返回类型以及函数的特征标(参数列表)。即声明应像函数原型一样指出函数信息。一个函数原型如下：

	double pam(int);

则正确的指针类型声明如下：

	double (*pf) (int); 

这与pam()声明类似，这是将pam替换为了($\ast$pf)。由于pam是函数，因此($\ast$pf)也是函数。而如果($\ast$pf)是函数，则pf就是函数指针。

括号的优先级比$\ast$的运算符高，因此$\ast$pf(int)意味着pf()是一个返回指针的函数，而($\ast$pf)(int)意味着pf是一个指向函数的指针：

	double (*pf)(int);
	double * pf (int); //一个返回指针的函数pf()

正确声明pf后，便可以将相应函数的地址赋给它：

	pf = pam;

现在，给出estimate()函数的原型：

	void estimate(int lines, double (*pf)(int));

使用estiemate()如下：

	estimate(50, pam);

pam为函数pam()的指针。

#### 3.使用指针来调用函数

使用指针来调用被指向的函数。($\ast$pf)扮演的角色与函数名相同，因此使用($\ast$pf)时，只需将它看作函数名：

	double pam(int);
	double (*pf) (int); 
	double x = pam(3);
	double y = (*pf)(3);

实际上，C++也允许像使用函数名一样使用pf:

	double y = pf(3);

### 7.10.2 函数指针示例

下面的程序，两次调用estimate()函数，一次传递betsy()函数的地址，另一次则传递pam()函数地址。

fun_ptr.cpp
	
	#include <iostream>
	double betsy(int);
	double pam(int);
	using namespace std;
	void estimate(int lines, double (*pf)(int));
	
	int main()
	{
		int code;
		cout << "How many lines of code do you need? ";
		cin >> code;
		cout << "Here's Betsy's estimate:\n";
		estimate(code, betsy);
		cout << "Here's Pam's estimate:\n";
		estimate(code, pam);
		return 0;
	}
	
	double betsy(int lns)
	{
		return 0.05 * lns;
	}
	
	double pam(int lns)
	{
		return 0.03 * lns + 0.0004 * lns * lns;
	}
	
	void estimate(int lines, double (*pf)(int))
	{
		cout << lines << " lines will take ";
		cout << (*pf)(lines) << " hour(s)\n";
	}


结果

	How many lines of code do you need? 30  
	Here's Betsy's estimate:
	30 lines will take 1.5 hour(s)
	Here's Pam's estimate:
	30 lines will take 1.26 hour(s)


### 7.10.3 深入探讨函数指针

下面一些函数的原型，它们的特征标（参数列表）和返回类型相同：

	const double * f1(const double ar[], int n);
	const double * f2(const double [], int);
	const double * f3(const double *, int);

这些函数特征标看似不同，但实际上相同。前面说过，在函数原型中，参数列表const double ar[]与const double $\ast$ar的含义相同。其次，在函数原型中，可以省略标识符（参数名）。因此，const double ar[]可简化为const double []，而const double $\ast$ar可简化为const double $\ast$。因此，上述的函数特征标的含义都相同。另一方面，函数定义必须通过标识符，因此需要使用const double $\ast$ar或const double ar[]。

接下来，声明一个指针，它可指向这三个函数之一。假设该指针名为pa，则只需要将目标函数原型中的函数名替换为($\ast$pa):

	const double * (*pa)(cosnt double * , int);

可在声明的同时初始化：

		const double * (*pa)(cosnt double * , int) = f1;

使用C++的自动类型推断功能时，代码要简单得多：

	auto p2 = f2;

将于使用三个函数，如果有一个函数指针数组将很方便。这种声明声明类似于单个函数指针的声明，但必须在某个地方加上[3]，以指出一个包含三个函数指针的数组：
	
	const double * (*pa[3])(const double *, int) = {f1, f2, f3};

pa是一个包含三个元素的数组，而要声明这样的数组，首先需要使用pa[3]。该声明的其他部分指出了数组包含的元素是什么样的。运算符[]的优先级高于$\ast$，因此$\ast$pa[3]表明pa是一个包含三个指针的数组。在此不能使用auto，自动类型推断只能用于单值初始化，而不能用于初始化列表。但是声明数组pa后，声明同样类型的数组就简单了：

	auto pb = pa;

使用它们来调用函数：pa[i]和pb[i]表示数组中的指针，因此可将任何一种函数调用表示法用于它们：

	const double * px = pa[0](av,3);
	const double * py = (*pb[1])(av,3);

获取指向的double值，可使用运算符$\ast$:

	double x = *pa[0](av,3);
	double y = *(*pb[1])(av,3);

可做的另一件事是创建指向整个数组的指针。由于数组名pa是指向函数指针的指针，因此指向数组的指针将是这样的指针，即它指向数组指针的指针。由于可使用单个值对其进行初始化，因此可以使用auto:

	auto pc = &pa;

如果指针名为pd，则需要指出它是一个指针，而不是数组。这意味着声明的核心部分应为($\ast$pd)[3]，其中的括号让标识符pd与$*$先结合：

	*pd[3]// 一个数组有三个指针
	(*pd)[3] //一个指针指向三个元素

即pd是一个指针，它指向一个包含三个元素的数组。由pa的声明的其他部分描述，如果如下：

	const double *(*(*pd)[3])(const double *, int) = &pa;

要调用函数，需要认识：pd指向数组，$\ast$pd就是数组，而($*$pd)[i]是数组中的元素，即函数指针。因此，较简单的函数调用是($\ast$pd)[i](av,3)，而$\ast$($\ast$pd)[i](av,3)是返回的指针所指向的值。也可以使用第二种使用指针调用函数语法：使用($\ast$($\ast$pd[i]))(av,3)，而$\ast$($\ast$($\ast$pd))[i](av,3)是指向的double值。

pa和&的区别：pa都是数组第一个元素的地址，即&pa[0]，因此，它是单个指针的地址。但&pa是整个数组的地址（即三个指针块）。从数字上来说，pa和&pa的值相同，但它们的类型不同，一种差别是，pa+1为数组中的下一个元素，而&pa+1为数组pa后面的12字节内存块的地址(假定地址为4字节)。另一个差别是，要得到第一个元素的值，对pa解除一次引用，但需要对&pa解除两次引用：

	**&pa == *pa == pa[0]

arfupt.cpp

	#include <iostream>
	using namespace std;
	const double * f1(const double ar[], int n);
	const double * f2(const double [], int);
	const double * f3(const double *, int);
	
	int main()
	{
		double av[3] = {1112.3, 1542.6, 2227.9};
		const double *(*p1)(const double *, int) = f1;
		auto p2 =  f2;
		cout << "Using pointers to functions:\n";
		cout << "Address Value\n";
		cout << (*p1)(av,3) << ": " << *(*p1)(av,3) << endl;
		cout << p2(av,3) << ": " << *p2(av,3) << endl;
	
		const double *(*pa[3])(const double *, int) = {f1, f2, f3};
		auto pb = pa;
		//const double *(**pb)(const double *, int) = pa;
		cout << "\nUsing an array of pointers to functions:\n";
		cout << "Address Value\n";
		for (int i =  0; i < 3; i++)
			cout << pb[i](av,3) << ": " << *pb[i](av,3) << endl;
	
		cout << "\nUsing pointers to an array pointers:\n";
		cout << "Address Value\n";
		auto pc = &pa;
		cout << (*pc)[0](av,3) << ": " << *(*pc)[0](av,3) << endl;
		const double *(*(*pd)[3])(const double *, int) = &pa;
		const double * pdb = (*pd)[1](av,3);
		cout << pdb << ": " << *pdb << endl;
		cout <<  (*(*pd)[2])(av,3) << ": " << *(*(*pd)[2])(av,3) << endl;
		return 0;
	}
	
	const double * f1(const double * ar, int n)
	{
		return ar;
	}
	
	const double * f2(const double ar[], int n)
	{
		return ar+1;
	}
	
	const double * f3(const double ar[], int n)
	{
		return ar+2;
	}



结果:

	Using pointers to functions:
	Address Value
	0x7fff5fac4b50: 1112.3
	0x7fff5fac4b58: 1542.6
	
	Using an array of pointers to functions:
	Address Value
	0x7fff5fac4b50: 1112.3
	0x7fff5fac4b58: 1542.6
	0x7fff5fac4b60: 2227.9
	
	Using pointers to an array pointers:
	Address Value
	0x7fff5fac4b50: 1112.3
	0x7fff5fac4b58: 1542.6
	0x7fff5fac4b60: 2227.9

auto在C++11才有，所以编译时使用：

	g++ -std=c++11 arfupt.cpp


### 7.10.4 使用typedef进行简化

除auto外，C++还提供了简化声明的工具。关键字typedef能够创建类型别名：

	typedef double real; //real为double的别名

这里采用的方法是，将别名当作标识符进行声明，并在开头使用关键字typedef。因此，可将p_fun声明为程序中使用的函数指针类型的别名：

	typedef const double *(*p_fun)(const double *, int);
	p_fun p1 = f1;

然后使用这个别名来简化代码：

	p_fun pa[3] = {f1,f2,f3};
	p_fun (*pd)[3] = &pa;


## 7.11 总结

函数原型

参数传递和返回值

函数指针



