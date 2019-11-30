---

title:  C++ Primer Plus--函数探幽（八）
date: 2018-10-12 09:30:14
tags: C++
categories: "C++ Primer Plus学习笔记"
mathjax: true

---

内联函数、按引用传递变量、默认的参数值、函数动态以及模板函数。<!--more-->

## 8.1 C++内联函数

内联函数是C++为提高运行速度所做的一项改进。常规函数和内联函数之间的主要区别不在于编写方式，而在于C++编译器如何将它们组合到程序中。

编译过程的最终产品是可执行程序--由一组机器语言指令组成。运行程序时，操作系统将这些指令载入到计算机内存中，因此每条指令都有特定的内存地址。计算机随后逐步执行这些指令。

常规函数调用也是程序跳到另一个地址，并在函数结束时返回。下面更详细介绍这一过程的典型实现。执行到函数调用指令时，程序将在函数调用后立即存储该指令的内存地址，并将函数参数复制到堆栈（为此保留的内存块），跳到标记函数起点的内存单元，执行函数代码（也许还需将返回值放入到寄存器中），然后跳回到地址被保存的指令处。来回跳跃并记录跳跃位置意味着以前使用函数时，需要一定的开销。

C++内联函数提供了另一种选择。内联函数的编译代码与其他程序代码“内联”起来。即，编译器将使用相应的函数的代码替换函数调用。对于内联代码，程序无需跳到另一个位置处执行代码，再跳回来。因此，内联函数的运行速度比常规函数稍快，但代价是需要占用更多内存。如果程序在10个不同的地方调用同一个内联函数，则该程序包含该函数代码的10个副本。

应有选择地使用内联函数。如果执行函数代码的时间比处理函数调用机制的时间长，则节省的时间吗将只占用整个过程的很小一部分。如果代码执行时间很短，则内联调用就可以声明非内联调用使用的大部分时间。由于这个过程相当快，因此尽管节省了该过程的大部分时间，但节省的时间绝对值并不大，除非函数经常被调用。

要使用这项特性，必须采取下述措施之一：

- 在函数声明前加上关键字inline;
- 在函数定义前加上关键字inline。

通常的做法是省略原型，将整个定义放在本应提供原型的地方。

程序员请求将函数作为内联函数时，编译器不一定会满足这种要求。它可能认为该函数过大或注意到函数调用了自己(内联函数不能递归)，因此不将其作为内联函数；而有些编译器没有启动或实现这这种特性。

inline.cpp

	#include <iostream>
	using namespace std;
	
	inline double square(double x) {return x * x;}
	
	int main()
	{
		double a, b;
		double c = 13.0;
		a = square(5.0);
		b = square(5.5+7.5);
		cout << "a = " << a << ", b = " << b << endl;
		cout << "c = " << c;
		cout << ", c squared = " << square(c++) << endl;
		cout << "Now c = " << c << endl;
		return 0;
	}
	

结果
	
	a = 25, b = 169
	c = 13, c squared = 169
	Now c = 14

输出表明，内联函数和常规函数一样，也是按值来传递参数的。尽管程序没有提供独立的原型，但C++原型特性仍在起作用。这是因为在函数首次使用前出现了整个函数定义充当了原型。

**内联与宏**

inline工作是C++新增的特定。C语言使用预处理语句#define来提高宏---内联代码的原始实现。例如：

	#define SQUARE(x) x*x

这并不是通过传递参数实现的，而是通过文本替换实现的--x是“参数”的符号标记。

	a = SQUARE(4.5 + 7.5);
	b = SQUARE(c++);

上述语句不能正常工作，可以通过使用括号来进行改进:

	#define SQUARE(x) ((x)*(x))

但仍然存在问题，即弘不能按值传递。即使使用新的定义，SQUARE(c++)仍将c递增两次，程序inline.cpp中，内联函数square()计算的c的结果，传递它，以计算其平方值，然后将c递增一次。

## 8.2 引用变量

C++增加了一种复合类型--引用变量。引用是已定义的变量的别名(另一个名称)。例如：如果将twain作为clement变量的引用，则可以交替使用twain和clement来表示变量。

引用变量的主要用途是用作函数的形参。通过将引用变量用作参数，函数将使用原始数据，而不是其副本。这样除指针之外，引用也为函数处理大型结构提供了一种非常方便的途径，同时对于设计来说，引用也是必不可少的。

### 8.2.1 创建应用变量

C++给&符号赋予了另一个含义，将其用来声明引用。例如，要将rodents作为rats变量的别名，可以这样做：

	int rats;
	int & rodents = rats;

其中，&不是地址运算符，而是类型标识符的一部分。就像声明中的char$\ast$指的是指向char的指针一样，int&指的是指向int的引用。上述声明允许将rats和rodents互换--它们指向相同的值和内存单元。

firstref.cpp

	#include <iostream>
	using namespace std;
	int main()
	{
		int rats = 101;
		int & rodents = rats;
		cout << "rats = " << rats;
		cout << ", rodents = " << rodents << endl;
		rodents ++;
		cout << "rats = " << rats;
	        cout << ", rodents = " << rodents << endl;
	
		cout << "rats addresss = " << &rats;
		cout << ", rodents address = " << &rodents << endl;
	
		return 0;
	}

 
结果

	rats = 101, rodents = 101
	rats = 102, rodents = 102
	rats addresss = 0x7ffc899ccdf4, rodents address = 0x7ffc899ccdf4


创建rats的指针和引用：

	int rats = 101;
	int & rodents = rats;
	int * prats = &rats;

这样，表达式rodents和$\ast$prats都可以同rats互换；表达式&rodents和prats都可以同&rats互换。

引用与指针差别之一是：引用必须在声明引用的时将其初始化，而不能像指针那样，先声明，再赋值。

引用更接近const指针，必须在创建时进行初始化，一旦与某个变量关联起来，就一致效忠于它。即：

	int & rodents = rats;

实际上是下述代码的伪装表示：

	int * const pr = &rats;

其中，因此rodents扮演的角色与表达式$\ast$pr相同。

sceref.cpp

	#include <iostream>
	using namespace std;
	int main()
	{
		int rats = 101;
		int & rodents = rats;
		
		cout << "rats = " << rats;
		cout << ", rodents = " << rodents << endl;
		
		cout << "rats address = " << &rats;
		cout << ", rodents address = " << &rodents << endl;
	
		int bunnies = 50;
		rodents = bunnies;
		cout << "bunnies = " << bunnies;
		cout << ", rats = " << rats;
		cout << ", rodents = " << rodents << endl;
		cout << "bunnies address = " << &bunnies;
		cout << ", rodents address =  " << &rodents << endl;
		return 0;
	}


结果

	rats = 101, rodents = 101
	rats address = 0x7ffddcafd674, rodents address = 0x7ffddcafd674
	bunnies = 50, rats = 50, rodents = 50
	bunnies address = 0x7ffddcafd670, rodents address =  0x7ffddcafd674


最初，rodents引用的rats，但随后程序**试图**将rodents作为bunnies的引用：

	rodents = bunnies;

咋一看，这种意图暂时成功，因此rodents的值从101变成了50，rats的值也变成了50。但是bunnies的地址与rats和rodents的地址不同。由于rodents是rats的别名，因此上述赋值语句与下面的语句等效：

	rats = bunnies;

即，这意味着将bunnies变量的值赋给rats变量。简而言之，可以通过初始化声明来设置引用，但不能通过赋值来设置。

程序员试图这样做：

	int rats = 101;
	int *pt = &rats;
	int & rodents = *pt;
	int bunnies = 50;
	pt = &bunnies;

将rodents初始化为$\ast$pt使得rodents指向rats，接下来将pt改为指向bunnies，并不能改变rodents和rats的值，即rodents引用的是rats。

### 8.2.2 将引用作为函数参数

引用经常被用作函数参数，使得函数中的变量名称为调用程序中的变量的别名。这种传递参数的方法称为**按引用传递**。按引用传递允许被调用的函数能够访问调用函数中的变量。

交换两个变量的值，对使用引用和使用指针进行比较。

swaps.cpp

	#include <iostream>
	using namespace std;
	void swapr(int & a, int & b);
	void swapp(int * p, int * q);
	void swapv(int a, int b);
	
	int main()
	{
	
		int wallet1 = 300;
		int wallet2 = 250;
		cout << " wallet1 = " << wallet1;
		cout << ", wallet2 = " << wallet2 << endl;
		
		
		cout << "Using references to swap contents:\n";
		swapr(wallet1, wallet2);	
		cout << " wallet1 = " << wallet1;
		cout << ", wallet2 = " << wallet2 << endl;
	
		cout << "Using pointers to swap contents:\n";
	        swapp(&wallet1, &wallet2);
	        cout << " wallet1 = " << wallet1;
	        cout << ", wallet2 = " << wallet2 << endl;
	
		cout << "Using values to swap contents:\n";
	        swapv(wallet1, wallet2);
	        cout << " wallet1 = " << wallet1;
	        cout << ", wallet2 = " << wallet2 << endl;
	
		return 0;
	}
	
	void swapr(int & a, int & b)
	{
		int temp = a;
		a = b;
	 	b = temp;
	}
	
	void swapp(int * p, int * q)
	{
		int temp = *p;
		*p = *q;
		*q = temp;
	}
	
	void swapv(int a, int b)
	{
		int temp = a;
		a = b;
		b = temp;
	}


结果

	 wallet1 = 300, wallet2 = 250
	Using references to swap contents: //第一交换，成功
	 wallet1 = 250, wallet2 = 300
	Using pointers to swap contents: //在第一次交换的基础上，交换，成功
	 wallet1 = 300, wallet2 = 250
	Using values to swap contents: //交换失败
	 wallet1 = 300, wallet2 = 250

引用和指针方法都成功地交换了两个值，而按值传递的方法没能完成这项工作。


按引用传递和按值传递看来相同，只能通过隐形或函数定义才能知道swapr()是按引用传递的。按引用传递的函数和按值传递的函数的代码，唯一区别是声明函数参数的方式不同。

	void swapr(int & a, int & b)
	void swapv(int a, int b)

当然还有内在区别，在swapr()中，变量a和b是wallet1和wallet2的别名，所以交换a和b的值相当于交换wallet1和wallet2的值。但在swapv()中，变量a和b是wallet1和wallet2的副本，因此交换a和b的值并不会影响wallet1和wallet2的值。

比较按引用传递的函数和按指针传递的函数，第一区别是声明函数的方式：

	void swapr(int & a, int & b)
	void swapp(int * p, int * q)

**另一个区别是指针版本需要在函数使用p和q的整个过程中使用解除引用运算符$\ast$。**

### 8.2.3 引用的属性和特别之处

使用两个函数计算参数的立方，其中一个函数接受double类型的参数，另一个接受doulbe引用。

cubes.cpp
	
	#include <iostream>
	double cube(double a);
	double refcube(double &ra);
	using namespace std;
	int main()
	{
		cout << cube(x) <<  " = cube of " << x << endl;
		cout << refcube(x) << " = cube of " ;
		cout << x << endl; //主要该处x必须重启一行
		return 0;
	}
	
	double cube(double a)
	{
		a *= a * a;
		return a;
	}
	
	double refcube(double &ra)
	{
		ra *= ra * ra;
		return ra;
	}


结果

	27 = cube of 3
	27 = cube of 27

注意，如果程序中命令修改为如下：

		cout << refcube(x) << " = cube of " << x << endl;

输出结果为：

	27 = cube of 3

refcube()函数修改main()函数中的x的值，而cube()没有影响。如果程序员的意图是让函数使用传递给它的信息，而不对这些信息进行修改，同时又想使用引用，则应使用常量引用。例如：

	double refcube(const double &ra);

如果要编写类似于上述示例的函数(即使用基本数值类型)，应采用按值传递的方式，而不要采用按引用传递的方式。当数据量比较大(如结构和类)时，引用参数将很有用。

按引用传递时，传递引用的参数的限制更严格，比如：不能使用x+0.2这样：

	doulbe z = refcube(x+0.2); //非法

如果这样做，由于x+0.2不是double类型的变量，因此程序创建一个临时的无名变量，并将其初始化为表达式x+0.2的值。然后，ra将成为该临时变量的引用。

**临时变量、引用参数和const**

如果实参与引用参数不匹配，C++将生成临时变量。当前，仅当参数为const引用时，C++才允许这种做。

如果引用参数是const，则编译器将下面两种情况下生成临时变量：

- 实参的类型正确，但不是左值
- 实参的类型不正确，但可以转换为正确的类型

左值参数是可被引用的数据对象，例如，变量、数组元素、结构成员、引用和解除引用的指针都是左值。非左值包括字面常量和包含多项的表达式。在C语言中，左值最初指的是可出现在赋值语句左右的实体，但这是引入关键字const之前的情况。现在，常规变量和const变量都可视为左值，因此可通过地址访问它们。但常规变量属于可修改的左值，而const变量属于不可能修改的左值。

重新定义refcube()，使其接受一个常量引用参数：

	double refcube(const double & ra)
	{
		return ra * ra * ra;
	}

现在考虑如下代码：

	double side = 3.0;
	double * pd = &side;
	double & rd = side;
	long edge = 5L;
	double lens[3] = {2.1,2.3,1.4};
	double c1 = refcube(side);
	double c2 = refcube(lens[2]);
	double c3 = refcube(rd);
	double c4 = refcube(*pd);
	double c5 = refcube(edge); //ra是临时变量
	double c6 = refcube(7.0); //ra是临时变量
	doubel c7 = refcube(side+1.0); //ra是临时变量

参数side、lens[2]、rd和$\ast$pd都是有名称的、double类型的数据对象，因此可以为其创建引用，而不要临时变量。然而，edge虽然是变量，类型却不正确，double引用不能指向long。另一方面，参数7.0和side+1.0的类型正确，但没有名称，在这些情况下，编译器将生成一个临时匿名变量，并让ra指向它。这些临时变量只在函数调用期间存在，此后编译器边可以随意将其删除。

如果接受引用参数的函数的意图是修改作为参数传递的变量，则创建临时变量将组织这种意图的实现。解决方法是：禁止创建临时变量。

现在来看refcube()函数。该函数的目的只是使用传递的值，而不是修改它们，因此临时变量不会造成任何不利的影响，反而是函数在可处理的参数种类方面更同样。因此，如果声明将引用指定为const，C++将在必要时生成临时变量。实际上，对于形参为const引用的C++函数，如果实参不匹配，则其行为类似于按值传递，为确保原始数据不被修改，将使用临时变量来存储值。

注意：如果函数调用的参数不是左值或相应const引用参数的类型不匹配，则C++将创建类型正确的匿名变量，将函数调用的参数的值传递给该匿名变量，并让参数来引用该变量。


**应尽可能使用const**

将引用参数声明为常数数据的引用的理由有三个：

- 使用const可以避免无意中修改数据的编程错误；
- 使用const使函数能够处理const和非const实参，否则将只能接受非const数据；
- 使用const引用使函数能够正确生成并使用临时变量。

C++11新增了另一种引用--右值引用。这种引用可指向右值，是使用&&声明的：

	double && rref == sqrt(36.0);
	double j = 15.0;
	double && jref = 2.0*j+18.5;
	cout << rref << endl;
	cout << jref << end;

新增右值引用的主要目的是，让库设计人员能够提供有些操作的更有效实现。以前的引用(使用&声明的引用)现在称为左值引用。

### 8.2.4 将引用用于结构

引用非常适合用于结构和类（C++的用户定义类型）。确实，引入引用主要是为了用于这些类型的，而不是基本的内置类型。

使用结构引用参数的方式与基本变量引用相同，只需在声明结构参数时使用引用运算符&即可。比如：

	struct free_throws
	{
		string name;
		int made;
		int attempts;
		float percent;
	};

则可以这样编写函数原型，在函数中将指向该结构的引用作为参数：

	woid set_pc(free_throws & ft);

如果不希望函数修改传入的结构，可使用const：

	void display(const free_throws & ft);

strtref.cpp
	
	#include <iostream>
	#include <string>
	using namespace std;
	struct free_throws
	{
		string name;
		int made;
		int attempts;
		float percent;
	};
	
	void display(const free_throws & ft);
	void set_pc(free_throws & ft);
	free_throws & accumulate(free_throws & target, const free_throws & source);
	
	int main()
	{
		free_throws one = {"Ifelsa Branch", 13, 14};
		free_throws two = {"Andor Knott", 10, 16};
		free_throws three = {"Minnie Max", 7, 9};
		free_throws four = {"Whily Looper", 5, 9};
		free_throws five = {"Long Long", 6, 14};
		free_throws team = {"Throwgoods", 0, 0};
	
		free_throws dup;
		set_pc(one);
		display(one);
		accumulate(team, one);
		display(team);
		display(accumulate(team, two));
		accumulate(accumulate(team, three), four);
		display(team);
		dup = accumulate(team, five);
		cout << "Displyaing team:\n";
		display(team);
		cout << "Display dup after assignment:\n";
		display(dup);
		set_pc(four);
		accumulate(dup,five) = four;
		cout << "Displaying dup after ill-advised assignment:\n";
		display(dup);
		return 0;
	}
	
	void display(const free_throws & ft)
	{
		cout << "Name: " << ft.name << endl;
		cout << " Made: " << ft.made << "\t";
		cout << "Attempts: " << ft.attempts << "\t";
		cout << "Percent: " << ft.percent << endl;
	}
	
	void set_pc(free_throws & ft)
	{
		if (ft.attempts != 0) 
			ft.percent = 100.0f * float(ft.made) / float(ft.attempts);
		else 
			ft.percent = 0;
	}
	
	free_throws & accumulate(free_throws & target, const free_throws & source)
	{
		target.attempts += source.attempts;
		target.made += source.made;
		set_pc(target);
		return target;
	}

结果

	Name: Ifelsa Branch
	 Made: 13	Attempts: 14	Percent: 92.8571
	Name: Throwgoods
	 Made: 13	Attempts: 14	Percent: 92.8571
	Name: Throwgoods
	 Made: 23	Attempts: 30	Percent: 76.6667
	Name: Throwgoods
	 Made: 35	Attempts: 48	Percent: 72.9167
	Displyaing team:
	Name: Throwgoods
	 Made: 41	Attempts: 62	Percent: 66.129
	Display dup after assignment:
	Name: Throwgoods
	 Made: 41	Attempts: 62	Percent: 66.129
	Displaying dup after ill-advised assignment:
	Name: Whily Looper
	 Made: 5	Attempts: 9	Percent: 55.5556

#### 1.程序说明

该程序首先初始化了多个结果对象。如果指定的初始值比成员少，余下的成员(这里是percent)将被设置为零。第一个函数调用如下：

	set_pc(one);

由于函数set_pc()的形参ft为引用，因此ft指向one，函数set_pc()的代码设置成员one.percent。按值传递不行，因此这将导致设置的是one的临时拷贝的成员percent。另一种方法是使用指针参数，并传递结构的地址，要复杂些。

下一个函数调用如下：

	display(one);

由于display()显示结构的内容，而不修改它，因此这个函数使用了一个const引用参数。就这个函数而言，也可按值传递，但与复制原始结构的拷贝相比，使用引用可节省时间和内存。

再下一个函数调用如下：

	accumulate(team, one);

函数accumulate()接收两个结构参数，并将第二结构的成员attempts和made的数据添加到第一个结构的相应成员中。只修改了第一个结构，因此第一个参数为引用，而第二个参数为const引用：

	free_throws & accumulate(free_throws & target, const free_throws & source)

当前讨论的函数调用没有使用它，就目前而言，原本可以将返回值声明void，但请看下述函数调用：

	display(accumulate(team, two));

首先，结构对象team作为第一个参数传递给了accumulate()。意味着在函数accumulate()中，target指向的是team。函数accumulate()修改team，在返回指向它的引用。注意到返回语句如下：

	return target;

光看这条语句并不能知道返回的是引用，但函数头和原型中指出了这一点。如果返回类型被声明为free_throws而不是free_throws &，上述返回语句将返回target（即team）的拷贝。但返回类型为引用，这意味着返回的是最初传递给accumulate()的team对象。

接下来，将accumulate()的返回值作为参数传递给了display()，这意味着将team传递给了display()。display()的参数为引用，这意味着函数display()中的ft指向是team，因此将显示team的内容。


#### 2.为何要返回引用

下面更深入地讨论返回引用与传统返回机制的不同。传统返回机制与按值传递函数参数类似：计算关键字return后面的表达式，并将结果返回给调用函数。从概念上说，这个值被复制到一个临时位置，而调用程序将使用这个值。
	
	double m = sqrt(16.0);
	cout << sqrt(25.0);

在第一天语句中，值4.0被复制到一个临时位置，然后被复制给m。在第二条语句中，值5.0被复制到一个临时位置，然后被传递给cout。

	dup = accumulate(team, five);

如果accumulate()返回一个结构，而不是指向结构的引用，将把整个结构复制到一个临时位置，再将这个拷贝复制给dup。但在返回值为引用时，将直接把team复制到dup，其效率更高。

#### 3. 返回引用时需要注意的问题

返回引用时最重要的一点是，应避免返回函数终止时不再存在的内存单元引用。应避免写下面这样的代码：

	const free_throws & clone2(free_throws & ft)
	{
		free_throws newguy;
		newguy = ft;
		return newguy;
	}

该函数返回一个指向临时变量的引用，函数运行完毕它将不再存在。也应避免返回指向临时变量的指针。

为避免这种问题，最简单的方法是，返回一个作为参数传递给函数的引用。作为参数的引用将指向调用函数使用的数据，因此返回的引用也将指向这些数。正如accumulate()一样，第一个引用就是这个作用。

另一种方法是用new来分配新的存储空间。前面使用过，使用new为字符串分配内存空间，并返回指向该内存空间的指针。下面是使用引用来完成类似的工作：

	const free_throws & clone(free_throws & ft)
	{
		free_throws * pt;
		*pt = ft;
		return *pt;
	}

第一条语句创建一个无名的free_throws结构，并让指针pt指向该结构，因此*pt就是该结构。上述代码似乎会返回结构，但函数声明表明，该函数实际上将返回这个结构的引用。这样，便可以这样使用该函数：

	free_throws & jolly = clone(three);

这使得jolly成为新结构的引用，这种方法存在一个问题：在不再需要new分配的内存时，应使用delete来释放它们。调用clone()隐藏了对new的调用，这使得以后很容易忘记使用delete来释放内存。第16章讨论的auto_ptr模板以及C++新增的unique_ptr可帮助程序员自动完成释放工作。


#### 4. 为何将const用于引用返回类型

程序中：

	accumulate(dup, five) = four;

其效果如下：首先将five的数据添加到dup中，再使用four的内容覆盖dup的内容。这条语句为何能够通过编译呢？在赋值语句中，左边必须是可修改的左值，即在赋值表达式中，左边的子表达式必须标识一个可修改的内存块。在这里，函数返回值指向dup的引用，它确实标识的是一个这样的内存块，因此这条语句是合法的。

另一方面，常规（非引用）返回类型是右值---不能通过地址访问的值。这种表达式可出现在赋值语句的右边，但不能出现在左边。其他右值包括字面值(如10.0)和表达式(x+y)。显然，获取字面值的地址没有意义，但为何常规函数返回值是右值呢？这是因为这种返回值位于临时内存单元中，运行到下一条语句时，它们可能不再存在。

假设要使用引用返回值，但又不允许执行像给accumulate()赋值这样的操作，只需要将返回类型声明为const引用：

	const free_throws & accumulate(free_throws & target, const free_throws & source);

现在返回类型为const，是不可修改的左值，因此下面的赋值语句不合法：

	accumulate(dup, five) = four;

返回类型为const引用后，下面的语句仍合法：

	display(accumulate(team, two));

这是因为display()的形参也是const free_throws &类型。但下面的赋值语句不合法，因此accumulate()的第一形参不是const:

	accumulate(accumualte(team, three), four);

在此，影响不大，仍可以这样做：

	accumulate(team, three);
	accumulate(team, four);


### 8.2.5 将引用用于对象

将类对象传递给函数是，C++通过的做法是使用引用。例如，可以通过使用引用，让函数将类string、ostream、istream、ofstream和ifstream等类的对象作为参数。

使用string类，演示一些不同的设计方案。这个例子的基本思想是:创建一个函数，它将指定的字符串加入到另一个字符串的前面和后面。下面程序提供了三个函数，然后其中的一个存在非常大的缺陷，可能导致程序崩溃甚至不通过编译。

strquote.cpp

	#include <iostream>
	#include <string>
	using namespace std;
	string version1(const string & s1, const string & s2);
	const string & version2(string & s1, const string & s2);
	const string & version3(string & s1, const string & s2);
	
	int main()
	{
		string input;
		string copy;
		string result;
	
		cout << "Enter a string: ";
		getline(cin, input);
		copy = input;
		cout << "Your string as entered: " << input << endl;
		result = version1(input, "***");
		cout << "Your string enhanced: " << result << endl;
		cout << "Your original string: " << input << endl;
		
		result = version2(input, "###");
	        cout << "Your string enhanced: " << result << endl;
	        cout << "Your original string: " << input << endl;
	
		cout << "Resetting original string.\n";
		input = copy;
		result = version3(input, "@@@");
	        cout << "Your string enhanced: " << result << endl;
	        cout << "Your original string: " << input << endl;
	
		return 0;
	}
	
	string version1(const string & s1, const string & s2)
	{
		string temp;
		temp = s2 + s1 + s2;
		return temp;
	}
	
	const string & version2(string & s1, const string & s2)
	{
		s1 = s2 + s1 + s2;
		return s1;
	}
	
	const string & version3(string & s1, const string & s2)
	{
		string temp;
		temp = s2 + s1 + s2;
		return temp;
	}

结果：

	Enter a string: It's my fault
	Your string as entered: It's my fault
	Your string enhanced: ***It's my fault***
	Your original string: It's my fault
	Your string enhanced: ###It's my fault###
	Your original string: ###It's my fault###
	Resetting original string.
	Your string enhanced: 
	Your original string: It's my fault


编译时给出警告

	strquote.cpp: 在函数‘const string& version3(std::string&, const string&)’中:
	strquote.cpp:50:9: 警告：返回了对局部变量的‘temp’的引用 [-Wreturn-local-addr]
	  string temp;

即version3尝试返回一个局部变量的引用。temp是一个新的string对象，只在函数version1()中有效，该函数执行完毕，它将不再存在。因此返回指向temp的引用不可行，因此该函数的返回类型为string，这意味着temp的内容将被复制到一个临时存储单元中，然后在main()中，该存储单元的内容被复制到一个名为result的strin中。

**将C-风格字符串用作string对象引用参数**

对于函数version1()，可能注意到有趣的一点：该函数的两个形参(s1和s2)的类型都是const sting &，但实参(input和"$\ast\ast\ast$")的类型分别为string和const char *。由于input的类型为string，因此让s1指向它没有任何问题，然而，程序怎么能够接受char指针赋给string引用呢？

这里有两点需要说明：首先，string类定义了一种char$\ast$到string的转换功能，这使得可以使用C-风格字符日来初始化string对象。其次，本章前面讨论过的类型为const引用的形参的一个属性。假设实参的类型与引用参数类型不匹配，但可被转换为引用类型，程序将创建一个正确类型的临时变量，使用转换后的实参值来初始化它，然后传递一个指向该临时变量的引用。例如，将int实参传递给const double &形参时，就是以这种方式处理的。同样，也可以将实参char$\ast$或const char$\ast$传递给形参const string &。

这种属性的结果是，如果形参类型为const string &，在调用函数时，使用的实参可以是string对象或C-风格字符串，如用引号括起来的字符串字面量、自空字符结尾的char数组或指向char的指针变量。因此，下面代码是可行的：

	result = version1(input, "***");

函数version2()不创建临时string对象，而是这将修改原来的string对象。由于s1指向main()中一个对象(input)的引用，因此将s1作为引用返回是安全的。由于s1指向input的引用，调用该函数将带来修改input的副作用。

verson3()函数的做法是错误的非法的。它存在一个致命的错误：返回一个指向version3()中声明的变量的引用。这个函数能够通过编译（但编程器会给出警告），但当程序试图执行该函数时将崩溃。具体地说，问题是由下面的赋值语句引起的：

	result = version3(input, "@@@");

程序试图引用已经释放的内存。



### 8.2.6 对象、继承和引用

ostream和ofstream类凸显了引用的一个有趣属性。ofstream对象可以使用ostream类的方法，这使得文件输入/输出的格式与控制台输入/输出相同。使得能够将特性从一个类传递到另一个类的语言特性被称为继承，这将在第13章讨论。简单地说，ostream是基类，而ofstream是派生类。派生类继承了基类的方法，这意味着ofstream对象可以使用基类的特性。

继承的另一个特征是，基类引用可以指向派生类对象，而无需进行强制转换。这种特征的一个实际结果是，可以定义一个接受基类引用作为参数的函数，调用该函数时，可以将基类作为参数，也可以将派生类对象作为参数。例如：参数类型为ostream &的函数可以接受ostream对象(如 cout)或声明的ofstream对象作为参数。

下面程序，通过调用一个函数将数据写入文件和显示到屏幕上来说明这一点。该程序要求用户输入望远镜物镜和一些目镜的焦距，然后计算并显示每个目镜的放大倍数。放大倍数等于物镜的焦距除以目镜的焦距。

filefun.cpp

	#include <iostream>
	#include <fstream>
	#include <cstdlib>
	using namespace std;
	
	void file_it(ostream & os, double fo, const double fe[], int n);
	const int LIMIT = 5;
	int main()
	{
		ofstream fout;
		const char * fn = "ep-data.txt";
		fout.open(fn);
		if (!fout.is_open())
		{
			cout << "Can't open " << fn << ". Bye.\n";
			exit(EXIT_FAILURE);
		}
		double objective;
		cout << "Enter the focal length of your telescope objective in mm: ";
		cin >> objective;
		double eps[LIMIT];
		cout << "Enter the focal lengths, in mm, of " << LIMIT << " eyepieces:\n";
		for (int i = 0; i < LIMIT; i++)
		{
			cout << "Eyepiece #" << i + 1 << ": " ;
			cin >> eps[i];
		}
		file_it(fout, objective, eps, LIMIT);
		file_it(cout, objective, eps, LIMIT);
		
		return 0;
	}
	
	void file_it(ostream & os, double fo, const double fe[], int n)
	{
		ios_base::fmtflags initial;
		initial = os.setf(ios_base::fixed);
		os.precision(0);
		os << "Foacal length of objective: " << fo << " mm\n";
		os.setf(ios::showpoint);
		os.precision(1);
		os.width(2);
		os << "f.1 eyepiece";
		os.width(15);
		os << "magnification" << endl;
		for (int i = 0; i < n; i++)
		{
			os.width(12);
			os << fe[i];
			os.width(15);
			os << int (fo/fe[i] + 0.5) << endl;
		}
		os.setf(initial);
	}


结果：

	Enter the focal length of your telescope objective in mm: 1800 
	Enter the focal lengths, in mm, of 5 eyepieces:
	Eyepiece #1: 30
	Eyepiece #2: 19
	Eyepiece #3: 14
	Eyepiece #4: 8.8
	Eyepiece #5: 7.5
	Foacal length of objective: 1800 mm
	f.1 eyepiece  magnification
	        30.0             60
	        19.0             95
	        14.0            129
	         8.8            205
	         7.5            240


对于该程序，最重要的一点是，参数os（其类型为ostream &）可以指向ostream对象（如cout），也可以指向ofstream对象（如fcout）。

方法setf()让您能够设置各种格式化状态。例如，方法调用setf(ios_base::fixed)将对象置于使用定点表示法模式；set(ios_base::showpoint)将对象置于显示小数点的模式，即使小数部分为零。方法precision()指定显示多少位小数。所有这些设置都将保持不变，直到再次调用相应的方法重新设置它们。方法width()设置下一次输出操作使用的字段宽度，这种设置只在显示下一值时有效，然后将恢复到默认设置。默认的字段宽度为零，这意味着刚好能容纳下要显示的内容。

函数file_it()使用了两个有趣的方法调用：

	ios_base::fmtflags initial;
	initial = os.setf(ios_base::fixed);
	...
	os.setf(initial);

方法setf()返回调用它之前有效的所有格式设置。ios_base::fmtflags是存储这种信息所需的数据类型名称。因此，将返回值赋给initial将存储调用file_it()之前的格式化设置，然后便可以使用变量initial作为参数来调用setf()，将所有的格式化设置恢复到原来的值。因此，该函数将对象回到传递给file_it()之前的状态。

需要说明的最后一点，每个对象都存储了自己的格式化设置。因此，当程序将cout传递给file_it()时，cout的设置将被修改，然后被恢复；当程序将fout传递给file_it()时，fout的设置将被修改了，然后被恢复默认。

### 8.2.7 何时使用引用参数

使用引用参数的主要原因有两个：

- 程序员能够修改调用函数中的数据对象
- 通过传递引用而不是整个数据对象，可以提高程序的运行速度

当数据对象比较大时（如结果和类对象），第二个原因最重要。这些也是使用指针参数的原因。

对于使用传递值而不作修改的函数。

- 如果数据对象很小，如内置数据类型或小型结构，则按值传递。
- 如果数据对象是数组，则使用指针，因为这是唯一的选择，并将指针声明为指向const的指针。
- 如果数据对象是比较大的结构，则使用const指针和const引用，以提高程序的效率。这样可以节省复制结构所需的时间和空间。
- 如果数据对象是类对象，则使用const引用。类设计的语义常常要求使用引用，这是C++新增这种特性的主要原因。因此，传递类对象参数的标准方式是按引用传递。

对于修改调用函数中数据的函数：

- 如果数据对象是内置数据类型，则使用指针。如果看到诸如fixit(&x)这样的代码(其中x是int)，则很明显，该函数将修改x。
- 如果数据对象是结构，则使用引用或指针。
- 如果数据对象是数组，则只能使用指针。
- 如果数据对象是类对象，则使用引用。

当然，这只是一些值得原则，很可能有充分的理由做出其他选择。


## 8.3 默认参数

默认参数指的是当函数调用中省略了实参时自动使用的一个值。

由于编译器通过查看原因来了解函数所使用的参数数目，因此函数原型必须将可能的默认参数告知程序。方法是将值赋给原型中的参数。例如：

	char * left (const char * str, int n = 1);

对于带参数列表的函数，必须**从右到左添加默认值**。即要为某个参数设置默认值，则必须为它右边的所有参数提供默认：

	int harpo(int n, int m = 4, int j = 5);
	int chico(int n, int m = 6, int j); //非法的
	int groucho(int k = 1, int m = 2, int n = 3);

丽日，harpo()原型允许调用该函数时提供1、2或3个参数：

	beeps = harpo(2);
	beeps = harpo(1,8);
	beeps = harpo(8,7,6);

实参按从左至右的顺序依次被赋给相应的形参，而不能跳过任何参数。因此，下面的调用是**不允许**的：

	beeps =	harpo(3, ,8);

left.cpp

	#include <iostream>
	using namespace std;
	const int ArSize = 80;
	char * left(const char * str, int n = 1);
	int main()
	{
		char sample[ArSize];
		cout << "Enter a string:\n";
		cin.get(sample, ArSize);
		char *ps  = left(sample, 4);
		cout << ps << endl;
		delete [] ps;
		ps = left(sample);
		cout << ps << endl;
		delete [] ps;
		return 0;
	}
	
	char * left(const char * str, int n)//注意在函数定义时，不要添加默认参数，否则会报错
	{
		if (n < 0)
			n = 0;
		char * p = new char[n+1];
		int i;
		for (i = 0; i < n && str[i]; i++)
			p[i] = str[i];
		while (i <= n)
			p[i++] = '\0';
		return p;
	}


结果

	Enter a string:
	forthcoming
	fort
	f


为了防止用户要求的字符数目可能多于字符串包含的字符数，为预防这种情况，函数使用了一个组合测试：

	i < n && str[i]

i<n测试让循环复制了n个字符后就终止。测试的第二部分--表达式str[i]，是要复制的字符的编码。遇到空值字符后，循环结束。

另一种设置新字符串长度的方法是，将n设置为传递的值和字符长度中较小的一个：

	int len = strlen(str);
	n = (n < len) ? n : len;
	char *p = new char[n+1];

这件确保new分配的空间不会多余存储字符串所需的空间。如果用户执行像left("Hi!", 1000)这样的调用，这种方法很有用。第一种方法是，将“Hi!”复制到由1000个字符组成的数组中，并将除前3个字符之外的所有字符都设置为空值字符；第二种方法是，将“Hi！”复制到由4个字符组成的数组中。但由于添加了另一个函数调用strlen()，因此程序将更长，运行速度将检查，同时还必须包括头文件cstring。

如果调用strlen()所需的时间很长，则可以让left()直接确定n和字符串长度哪个小。例如，当m的值等于n或到达字符串结尾时，下面的循环都将停止：

	int m = 0;
	while (m <= n && str[m] != '\0') // 也可以写成while (m <= n && str[m])
		m++;
	char *p = new char[m+1];


## 8.4 函数重载

函数重载指的是可以有多个同名的函数，因此对名称进行了重载。可以通过函数重载来设计一系列函数--它们完成相同的工作，但使用不同的参数列表。C++根据使用上下文来确定要使用的重载函数版本。

函数重载的关键是函数的参数列表--也称为函数特征标。如果两个函数的参数数目和类型相同，同时参数的排序顺序也相同，则它们的特征标相同，而变量名是无关紧要的。C++允许定义名称相同的函数，条件是它们的特征标不同。如果参数数目和/或参数类型不同，则特征标也不同。例如，可以定义一组原型如下print()函数：

	void print(const char * str, int width);
	void print(double d, int width);
	void print(long l, int width);
	void print(int i, int width);
	void print(const char * str);

使用print()函数时，编译器根据所采用的用法使用相应特征标的原型。

使用被重载的函数时，需要在函数调用中使用正确的参数类型，例如，对如下面的语句：

	unsigned int year = 3210;
	print(year, 6);

print()调用与哪个原型匹配呢？它不与任何原型匹配！没有匹配的原型并不会自动停止使用其他的某个函数，因为C++将尝试使用标准类型强制转换进行匹配。如果原型只有第二个，则函数调用print(year,6)将把year转换为doule类型。但在上面的代码中，有3个将数字作为第一个参数的原型，因此有3种转换year的方式。在这种情况下，C++拒绝这种函数调用，并将其视为错误。

一些看起来彼此不同的特征标是不能共存的，例如：

	double cube(double x);
	double cube(double & x);

编译器考虑这个问题时，请看如下代码：

	cout << cube(x);

参数x与double x原型和 double & x原型都匹配，因此编译器不知道究竟使用哪个原型。为避免这种混乱，编译器在检查函数特征标时，将把类型引用和类型本身视为同一个特征标。

匹配函数时，并不区分const和非const变量。

	void dribble(char * bits);
	void dribble(const char * cbits);
	void babble(char * bits);
	void drivel(const char * bits);

下面列出了各种函数调用对应的原型：

	const char p1[20] = "How's the weather?";
	char p2[20] = "How's business?";
	dribble (p1);
	dribble(p2);
	dabble(p1); //不匹配
	dabble(p2); 
	drivel(p1);
	drivel(p2);

dribble()函数有两个原型，一个用于const指针，另一个用于常规指针，编译器根据实参是否为const来决定使用哪个原型。dabble()函数只与带非const参数的调用匹配，而drivel()函数可以与带const和非const参数的调用匹配。drivel()和dabble()在行为上有这种差别，主要是由于将非const值赋给const变量是合法的，但反之是非法的。

请记住，是特征标，而不是函数类型使得可以对函数进行重载。例如，下面两个声明是互斥的：

	long gronk(int n, float m);
	double gronk(int n, float m);

因此，C++不允许以这种方式重载gronk()。返回类型可以不同，但特征标也必须不同：

	long gronk(int n, float m);
	double gronk(float n, float m);


**重载引用参数**

类设计和STL经常使用引用参数，因此直到不同引用类型的重载很有用：

	void sink(doube & r1);
	void sank(const double &r2);
	void sunk(double && r3);

左值引用参数r1与可修改的左值参数匹配；const左值引用参数r2与可修改的左值参数、const作者参数和右值参数(如两个double值的和)匹配；最后，右值引用参数r3与右值匹配。注意到r1或r3匹配的参数都与r2匹配。

注意到与r1或r3匹配的参数都与r2匹配。这就带来一个问题：如果重载使用这三种参数的函数，结果将如何？答案是将调用最匹配的版本：

	void staff(doube & rs);
	void staff(const double & rcs);
	void stove(double & r1);
	void stove(const double & r2);
	void stove(double && r3);

这让您根据参数是左值、const还是右值来定制函数的行为：

	double x = 55.5;
	const double y = 32.0;
	stove(x); //调用stove(double &)
	stove(y); //调用stove(const double &)
	stove(x+y); //调用 stove(doubel &&)

如果没有定义stove(double &&)，stove(x+y)将调用函数stove(const double &)。

### 8.4.1 重载示例  

前面创建了left()函数，它返回一个指针，指向字符串的前n个字符。下面添加另一个left()函数，它返回整数的前n位。

一种方法是，先计算数字包含多少位，将数字除以10便可以去掉一位，因此可以使用除法来计算数位。更准确的，可以使用下面循环：
	
	nsigned digits = 1;
	while (n /= 10)
			digits++;

上述循环计算每次删除n中的一位时，需要多少次才能删除所有的位。

现在假设数字共有5位，并要返回前3位，则将这个数除以10再除以10，便可以得到所需的值。每除以10就删除数字的最后一位。要直到需要删除多少位，只需要将总数减去要获得的位数即可。可以如下编写：

	ct = digits - ct;
	while(ct--)
		num /= 10;
	return num;

leftover.cpp

	#include <iostream>
	unsigned long left(unsigned long num, unsigned ct);
	char * left (const char * str, int n = 1);
	using namespace std;
	
	int main()
	{
		char * trip = "Hawaii!!";
		unsigned long n = 12345678;
		int i;
		char * temp;
		
		for (i = 1; i < 10; i++)
		{
			cout << left(n, i) << endl;
			temp = left(trip, i);
			cout << temp << endl;	
			delete [] temp;
		}
	
		return 0;
	}
	
	unsigned long left(unsigned long num, unsigned ct)
	{
		unsigned digits = 1;
		unsigned long n = num;
		
		if (ct == 0 || num == 0)
			return 0;
		while (n /= 10)
			digits++;
		if (digits > ct)
		{
			ct = digits - ct;
			while(ct--)
				num /= 10;
			return num;
		}
		else
			return num;
	}
	
	char * left(const char * str, int n)
	{
		if (n < 0)
			n = 0;
		char * p = new char [n+1];
		int i;
		for (i = 0; i < n && str[i]; i++ )
			p[i] = str[i];
		while (i <= n)
			p[i++] = '\0';
		return p;
		
	}


结果：

	1
	H
	12
	Ha
	123
	Haw
	1234
	Hawa
	12345
	Hawai
	123456
	Hawaii
	1234567
	Hawaii!
	12345678
	Hawaii!!
	12345678
	Hawaii!!

### 8.4.2 何时使用函数重载

函数重载一般使用在：仅当函数基本上执行相同的任务，但使用不同形式的数据时，才应采用函数重载。另外，是否可以通过使用默认参数来实现同样的目的。例如，可以用两个重载函数来代替面向字符串的left()函数：

	char * left(const char * str, unsigned n);
	char * left(const char * str);

使用一个带默认参数的函数要简单些。只需编写一个函数，程序也只需为一个函数请求内存；需要修改函数时，只需修改一个。然而，如果需要使用不同类型的参数，则默认参数便不管用了，在这种情况下，应使用函数重载。

**名称修饰**

C++如何跟踪每一个重载函数？它给这些函数指定了秘密身份。使用C++开发工具中的编辑器编写和编译程序时，C++编译器将执行一些神奇的操作--名称修饰或名称矫正，它根据函数原型中指定的形参类型对每个函数名进行加密。请看下述未经修饰的函数原型：

	long MyFunctionFoo(int, float);

这种格式对于人类来说很合适；我们直到函数接受两个参数，并返回一个long值。而编译器将名称转换为不太好看的内部表示，来描述该接口，如下所示：

	？MyfunctionFoo@@YAXH

对原始名称进行的表面看来无意义的修饰将对参数数目和类型进行编码。添加的一组符号随函数特征标而异，而修饰时使用的约定随编译器而异。

## 8.5 函数模板

函数模板是通用的函数描述，即它们使用泛型来定义函数。其中的泛型可以具体的类型(如int或double)替换。通过将类型作为参数传递给模板，可使编译器生成该类型的函数。由于模板允许以泛型的方式编写程序，因此有时也称为通用编程。由于类型是用参数表示的，因此模板特性有时也称为参数化类型。


前面，定义了一个交换两个int的值的函数。假设要交换两个double值，则一种方法是复制原来的代码，并用double替换所有的int。如果需要需要交换两个char值，可以再次使用同样的技术。进行这种修改将浪费宝贵时间，且容易出错。

C++的函数模板功能能自动完成这一过程，可以节省时间，而且更可靠。

函数模板允许以任意类型的方式来定义函数。例如，可以这样建立一个交换模板：

	template <typename AnyType>
	void Swap(AnyType & a, AnyType &b)
	{
		AnyType temp;
		temp = a;
		a = b;
		b = temp;
	}

第一行指出，要建立一个模板，并将类型命名为AnyType。关键字template和type是是必需的，除非可以使用关键字class代替typename。另外，必须使用尖括号。类型名可以任意选择，只要遵守C++命名规则即可。模板不创建任何函数，而只是告诉编译器如何定义函数。需要交换int的函数时，编译器将按模板创建这样的函数，并用int代替AnyType。同样，需要交换double的函数时，编译器将按模板创建这样的函数，并用double代替AnyType。

在标准C++98添加关键字typename之前，C++使用关键字class来创建模板。即，可以如下编写模块定义：

	template <class AnyType>
	void Swap(AnyType & a, AnyType &b)
	{
		AnyType temp;
		temp = a;
		a = b;
		b = temp;
	}


funtemp.cpp

	#include <iostream>
	using namespace std;
	
	template <typename T>
	void Swap(T &a, T &b);
	
	int main()
	{
		int i = 10;
		int j = 20;
		cout << "i, j = " << i << ", " << j << endl;
		Swap(i, j);
		cout << "Now i, j = " << i << ", " << j << endl;
		
		double x = 24.5;
		double y = 81.4;
		cout << "x, y = " << x << ", " << y << endl;
		Swap(x, y);
		cout << "Now x, y = " << x  << ", " << y << endl;
		return 0;
	}
	
	template<typename T>
	void Swap(T &a, T &b)
	{
		T temp;
		temp = a;
		a = b;
		b = temp;
	}


结果

	i, j = 10, 20
	Now i, j = 20, 10
	x, y = 24.5, 81.4
	Now x, y = 81.4, 24.5


当Swap()函数接受两个int参数，因此编译器生成该函数的int版本。即用int替换所有的int，生成下面的代码：

	void Swap(int &a, int &b)
	{
		int temp;
		temp = a;
		a = b;
		b = temp;
	}

程序员看不到这些代码，当编译器确实生成并在程序中使用了它们。

注意函数模板不能缩短可执行程序。对于上述程序，最终仍将有两个独立的函数定义，就像以手工的方式定义了这些函数一样最终代码不包含任何模板，而只包含为程序生成的时间函数。

更常见的情形时是，将模板放在头文件中，并在需要使用模板的文件中包含头文件。

### 8.5.1 重载的模板

需要多个对不同类型使用同一种算法的函数时，可使用模板。然而，并非所有的类型都使用相同的算法。为满足这种需求，可以像重载常规函数一样重载模板定义。和常规重载一样，被重载的模板的函数特征标必须相同。例如，上面程序新增了一个交换模板，用于交换两个数组中的元素。

cat twotemps.cpp

	#include <iostream>
	using namespace std;
	template <typename T>
	void Swap(T &a, T &b);
	
	template <typename T>
	void Swap(T a[], T b[], int n);
	void Show(int a[]);
	const int Lim = 4;
	int main()
	{
		int i = 10, j = 20;
		cout << "i, j = " << i << ", " << j << endl;
		Swap(i, j);
		cout << "Now i, j = " << i << ", " << j << endl;
	
		int d1[Lim] = {0,7,0,4};
		int d2[Lim] = {1,9,6,9};
		cout << "Original arrays:\n";
		Show(d1);
		Show(d2);
		Swap(d1, d2, Lim);
		cout << "Swapped arrays:\n";
		Show(d1);
		Show(d2);
		return 0;
	}
	template <typename T>
	void Swap(T &a, T &b)
	{
		T temp;
		temp = a;
		a = b;
		b = temp;
	}
	
	template <typename T>
	void Swap(T a[], T b[], int n)
	{
		T temp;
		for (int i = 0; i < n; i++)
		{
			temp = a[i];
			a[i] = b[i];
			b[i] = temp;
		}
	}
	
	void Show(int a[])
	{
		for (int i = 0; i < Lim; i++)
			cout << a[i] << " ";
		cout << endl;
		
	}

结果：

	i, j = 10, 20
	Now i, j = 20, 10
	Original arrays:
	0 7 0 4 
	1 9 6 9 
	Swapped arrays:
	1 9 6 9 
	0 7 0 4 


### 8.5.2 模板的局限性

假设有如下模板函数：

	template <class T>
	void f(T a, T b)
	{}

通常，代码假定可执行哪些操作。例如，下面的代码假定定义了赋值，但如果T是数组，这种假设将不成立:

	a = b;

同样，下面语句假设定义了<，但如果T为结构，该假设便不成立：

	if (a > b)

另外，为数组名定义了运算符，但由于数组名为地址，因此它比较的是数组的地址，而这可能不是您所希望的。下面的语句假定为类型T定义了乘法运算，但如果T为数组、指针或结构，这种假设便不成立：

	T c = a*b;

总之，编写的函数模板很可能无法处理某些类型。另一方面，有时候通用化是有意义的，但C++语法不允许这样左。例如，将两个包含位置坐标的结构相加是有意义的，虽然没有为结构定义运算+。一种解决方案是，C++允许您重载运算符+，以便能够将其用于特定的结构或类。这样使用运算符+的模板便可处理重载了运算符的结构。另一种解决方案是，为特定类型提供具体化的模板定义，接下来介绍。

### 8.5.3 显示具体化

假设定义了如下结构：

	struct job
	{
		char name[40];
		double salary;
		int floor;
	}

另外，希望能够交换两个这种结构的内容。原来模板使用下面的代码来完成交换：

	temp= a;
	a = b;
	b = temp;

由于C++允许将一个结构赋给另一个结构，因此即使T是一个job结构，上述代码也适用。然而，假设只想交换salary和floor成员，而不交换name成员，则需要使用不同的代码，但Swap()的参数将保持不变，因此无法使用模板重载来提供其他代码。

然而，可以提供一个具体化函数定义--称为显式具体化，其中包含所需的代码。当编译器找到与函数调用的具体化定义时，将使用该定义，而不再寻找模板。

C++标准定义的形式：

#### 1.第三代具体化

C++98标准选择了下面的方法：

- 对于给定的函数名，可以有非模板函数、模板函数和显式具体化模板以及它们的重载版本。
- 显式具体化的原型和定义应以teplate<>开头，并通过名称来指出类型。
- 具体化优先于常规模板，而非模板函数由于具体化和常规模板。

下面是用于交换job结构的非模板函数、模板函数和具体化的原型：

	void Swap(job &, job &);

	template <typename T>
	void Swap(T &, T &);

	template <> void Swap<job>(job &, job &); //具体化

下面看一看显式具体化的工作方式。

#### 2.显式具体化示例

twoswap.cpp
	
	#include <iostream>
	using namespace std;
	template <typename T>
	void Swap(T &a, T &b);
	struct job
	{
		char name[40];
		double salary;
		int floor;
	};
	template <> void Swap<job>(job &j1, job &j2);
	void Show(job &j);
	int main()
	{	
		cout.precision(2);
		cout.setf(ios::fixed,ios::floatfield);
		int i = 10, j = 20;
		cout << "i, j = " << i << ", " << j << endl;
		Swap(i,j);
		cout << "Now, i, j = " << i << ", " << j << endl;
	
		job sue = {"Susan", 73000.6, 7};
		job sidney = {"Sidney", 78060.7, 9};
		cout << "Before job swapping:\n";
		Show(sue);
		Show(sidney);
		Swap(sue, sidney);
		cout << "After job swapping:\n";
		Show(sue);
		Show(sidney);
		return 0;
	}
	
	template <typename T>
	void Swap(T & a, T & b)
	{
		T temp;
		temp = a;
		a = b;
		b = temp;
	}
	
	template <> void Swap<job>(job & j1, job & j2)
	{
		double t1;
		t1 = j1.salary;
		j1.salary = j2.salary;
		j2.salary = t1;
		int t2;
		t2 = j1.floor;
		j1.floor = j2.floor;
		j2.floor = t2;
	}
	
	void Show(job &j)
	{
		cout << j.name << ": $" << j.salary << " on floor " << j.floor << endl;
	}


结果：

	i, j = 10, 20
	Now, i, j = 20, 10
	Before job swapping:
	Susan: $73000.60 on floor 7
	Sidney: $78060.70 on floor 9
	After job swapping:
	Susan: $78060.70 on floor 9
	Sidney: $73000.60 on floor 7

### 8.5.4 实例化和具体化

在代码中包含函数模板本身并不会生成函数定义，它只是一个用于生成函数定义的方案。编译器使用模板为特定类型生成函数定义时，得到的是模板实例。例如，在上述程序中，函数调用Swap(i,j)导致编译器生成Swap()的一个实例，该实例使用int类型。模板并非函数定义，但使用int的模板实例是函数定义，这种实例化被称为**隐式实例化**。

最初，编译器只能通过隐式实例化，来使用模板生成函数定义，但现在C++还允许**显式实例化**。这意味着可以直接命令编译器创建特定的实例，如Swap<int>()。其语法是，声明所需的种类--用<>符号指出类型，并在声明前加上关键字template:

	template void Swap<int>(int, int);

实现了这种特性的编译器看上上述声明后，将使用Swap()模板生成一个使用int类型的实例。即，该声明的意思是：使用Swap()模板生成int类型的函数定义。

与显式实例化不同的是，**显式具体化**使用下面两个等价的声明之一：

	template <> void Swap<int>(int &, int &);
	template <> void Swap(int &, int &);

区别在于，这些声明的意思是：不要使用Swap()模板生成函数定义，而应使用专门为int类型显式地定义的函数定义。这些原型必须有自己的函数定义。显式具体化声明在关键字templat包含<>，而显式实例化没有。

**警告**： 试图在同一个文件中使用同一种类型的显式实例和显式具体化将出错。

还可以通过在程序中使用函数来创建显式实例化。例如，请看下面代码：

	templatte <class T>
	T Add(T a, T b)
	{
		return a + b;
	}
	int m = 6;
	double x = 10.2;
	cout << Add<double>(x, m) << endl;

这里的模板与函数调用Add(x,m)不匹配，因为该模板要求两个函数参数的类型相同。但通过使用Add<double>(x,m),可强制为double类型实例化，并将参数m强制转换为double类型，以便与函数Add<double>(double,double)的第二个参数匹配。

如果对Swap()做类型的处理：

	int m = 5;
	double x = 14.3;
	Swap<double>(m, x);

这将为类型double生成一个显式实例化，不幸的是，这些代码不管用，因此的哥形参的类型为double &，不能只想int变量m。 

隐式实例化、显式实例化和显式具体化统称为具体化。它们的相同之处在于，它们表示都是使用具体类型的函数定义，而不是通用描述。

引入显式实例化后，必须使用新的语法--在声明中使用前缀template和template<>，以区分显式实例化还是显式具体化。通常，功能越多，语法规则也越多。下面的代码片段中间了这些概念：

	template <class T>
	void Swap(T &a , T &b);
	
	template <> void Swap<job>(job &, job &);//显式具体化
	int main()
	{
		template void Swap<char>(char &, char &); //显式实例化
		
		short a, b;
		Swap(a, b); //隐私实例化
		job n, m;
		Swap(n, m); //使用显式具体化
		char g, h;
		Swap(g, h); //使用显式实例化
	}


编译器看到char的显式实例化后，将使用模板定义来生成Swap()的char版本。对于其他Swap()调用，编译器根据函数调用中实际使用的参数，生成相应的版本。例如，当编译器看淡函数调用Swap(a,b)	后，将生成Swap(a,b)的short版本，因为两个参数的类型都是short。当编译器看到Swap(n,m)后，将使用short类型提供的独立定义(显式具体化)。当编译器看到Swap(g,h)后，将使用处理显式实例化时生成的模板具体化。

### 8.5.5 编译器选择使用哪个函数版本

对于函数重载、函数模板和函数模板重载，C++需要（且有）一个定义良好的策略，来决定为函数调用使用哪一个函数的定义，尤其是有多个参数时。这个过程称为**重载解析**。大致了解一下这个过程是如何进行的：

- 第一步：创建候选函数列表。其中包含于被调用函数的名称相同的函数和函数模板。
- 第二步：使用候选函数列表创建可行函数列表。这些都是参数数目正确的函数，为此有一个隐式转换序列，其中包括实参类型与相应的形参类型完全匹配的情况。例如，使用float参数的函数调用可以将该参数转换为doule，从而与doule形参匹配，而模板可以为float生成一个实例。
- 第三步：确定是否有最佳的可行函数。如果有，则使用它，否则该函数调用出错。

考虑只有一个函数参数的情况，如下面的调用：

	may('B');

首先，编译器将寻找候选者，即名称为may()的函数和函数模板。然后，寻找那些可以用一个参数调用的函数。例如，下面的函数符合要求，因为其名称和被调用的函数函数相同，且可只给它传递一个参数：

	void may(int);                            #1
	float may(flaot, flaot = 3);              #2
	void may(char);							  #3						
	char * myay(const char *);                #4
	char may (const char &);                  #5
	template <class T> void may(const T &);   #6
	template <class T> void may(T *);         #7

注意，只考虑特征标，而不考虑返回类型。其中的两个函数(#4和#7)不可行，因为整型类型不能被隐式地转换（即没有显式强制类型转换）为指针类型。剩余的一个模板可用来生成具体化，其中T被替换为char类型。这样剩余5个可行的函数，其中的每一个函数，如果它式声明唯一一个函数，都可以被使用。

接下来，编译器必须确定哪个可行函数是最佳的。它查看为使函数调用参数与可行的候选函数的参数匹配所需进行的转换。通过从最佳到最差的顺序如下所述：

- 1. 完全匹配，但常规函数优先于模板。
- 2. 提升转换（例如，char和short自动转换为int, float自动转换为double）。
- 3. 标准转换（例如，int转换为char，long转换为double）.
- 4. 用户定义的转换，如类声明中定义的转换。

例如，函数#1优先于函数#2，因为char到int的转换为提升转换，而char到float的转换是标准转换。函数#3、函数#5和函数#6都优先于函数#1和#2，因为它们都是完全匹配的。#3和#5优先于#6，因为#6是函数模板。如果两个函数(如#3和#5)都完全匹配，将如何办？通常，有两个函数完全匹配是一种错误，但这一规则有两个例外。下面，深入探讨这一点：

#### 1. 完全匹配和最佳匹配

完全匹配允许的无关紧要的转换：

| 从实参 | 到形参 |
|---|
| Type | Type & |
| Type & | Type | 
| Type [] | Type $\ast$ | 
| Type (argument-list) | Type ($\ast$)(argument-list) |
| Type | const Type |
| Type | volatile Type |
| Type $\ast$ | const Type |
| Type $\ast$ | volatile Type $\ast$|

Type表示任意类型，例如int实参与int &形参完全匹配。Type(argument-list)意味着用作实参的函数名与用作形参的函数指针只要返回类型和参数列表相同，就是匹配的。volatile下一章介绍。

假设有下面的函数代码：

	struct blot {int a, char b[10]};
	blot ink{25, "spots"};
	recycle(ink);

在这种情况下，下面的原型都是完全匹配的：

	void recycle(blot);          #1
	void recycle(const blot);    #2
	void recycle(blot &);        #3
	void recycle(const blot &);  #4

正如预期的，如果有多个匹配的原型，则编译器将无法完成重载解析过程；如果没有最佳的可行函数，则编译程将生成一条错误消息，该消息可能会使用诸如“ambiguous(二义性)”这样的词语。

然而，有时候，即使两个函数都完全匹配，仍可完成重载解析。首先，**指向非const数据的指针和引用优先与非const指针和引用参数匹配**。即，在recycle()示例中，如果只定义了函数#3和#4是完全匹配的，则将选择#3，因为int没有被声明为const。然而，**const和非const之间的区别只适用于指针和引用指向的数据**。即，如果只定义了#1和#2，则将出现二义性。

一个完全匹配优先于另一个的另一种情况是，其中一个是非模板函数，而另一个不是。在这种情况下，**非模板函数将优先于模板函数（包括显式具体化）**。

如果两个完全匹配的函数都是模板函数，则**较具体的模板函数优先**。例如，这意味着显式具体化将优先于使用模板隐式生成的具体化：

	struct blot {int a; char b[10]};
	template <class Type> void recycle(Type t);
	template <> void recycle<blot> (blot & t);
	
	blot ink = {25, "spots"};
	recycle(int);

术语“最具体”并不一定意味显式具体化，而是指编译器推断使用哪种类型时执行的转换最少。例如，请看下面两个模板：

	template <class Type> void recycle(Type t);    #1
	template <class Type> void recyclle(Type * t); #2
	
	recycle(&ink);

recyele(&ink)调用与#1模板匹配，匹配时间Type解释为blot $\ast$。recycle(&ink)函数调用也与#2模板匹配，这样Type被解释为blot。因此两个隐式实例--recycle<blot$\ast$>(blot$\ast$)和recycle<blot>(bloat$\ast$)发送到可行函数池中。

在这两个模板函数中，recycle<blot$\ast$>(blot$\ast$)被认为是更具体地，因为在生成过程中，它需要进行地转换更少。即，#2模板已经显式指出，函数参数是指向Type的指针，因为出可以直接用blot标识Type；而#1模板见Type作为函数参数，因此Type必须被解释为指向blot的指针。即，在#2模板中，Type已经被具体化为指针，因为说它“更具体”。


#### 2. 部分排序规则实例

下述程序有两个用来显式数组内容的模板定义。第一个定义（模板A）假设作为参数传递的数组中包含了要显式地数据；第二个定义（模板B）假设数组元素为指针，指向要显示地数据。

temptempover.cpp

	#include <iostream>
	using namespace std;
	
	template <typename T>
	void ShowArray(T arr[], int n);
	
	template <typename T>
	void ShowArray(T * arr[], int n);
	
	struct debts
	{
		char name[30];
		double amount;
	};
	
	int main()
	{
		int things[6] = {13, 31, 103, 301, 310, 130};
		struct debts mr_E[3] = 
		{
			{"Volfe", 2400.0},
			{"Foxe", 1300.0},
			{"Stout", 1800.0}
		};
		double *pd[3];//3个指针组成的数组
		for (int i = 0; i < 3; i++)
			pd[i] = &mr_E[i].amount;
		cout <<  "Listing Mr. E's counts of things:\n";
		ShowArray(things, 6);
		cout << "Listing Mr. E's debts:\n";
		ShowArray(pd, 3);
		return 0;
	}
	
	template <typename T>
	void ShowArray (T arr[], int n)
	{
		cout << "Template A\n";
		for (int i = 0; i < n; i++)
			cout << arr[i] << ' ';
		cout << endl;
	}
	
	template <typename T>
	void ShowArray(T * arr[], int n)
	{
		cout << "Template B\n";
		for (int i = 0; i < n; i++)
			cout <<  *arr[i] << ' ';
		cout << endl;
	}


结果
	 
	Listing Mr. E's counts of things:
	Template A
	13 31 103 301 310 130 
	Listing Mr. E's debts:
	Template B
	2400 1300 1800 

标识符things是一个int数组的名称，因为与下面的模板匹配：

	template <typename T>	                   #模板A
	void ShowArray(T arr[], int n);

pd是一个doulbe $\ast$数组的名称，这与下面的模板匹配：

	template <typename T>                      #模板B
	void ShowArray(T * arr[], int n);


如果将模板B删除，则编译器将使用模板A来显示pd内容，因此显示的将是地址，而不是值。

简而言之，重载解析将寻找最匹配的函数。如果只存在一个这样的函数，则选择它；如果存在多个这样的函数，但其中只有一个是非模板函数，则选择该函数；如果存在多个适合的函数，且它们都为模板函数，但其中有一个函数比其他函数具体，则选择该函数。如果有多个同样合适的非模板函数或模板函数，但没有一个函数比其他函数具体，则函数调用将不是确定的，因此是错误；当然，如果不存在匹配函数，则也是错误。

#### 3. 创建自定义选择

在有些情况下，可通过编写合适的函数的调用，引导编译器做出您希望的选择。下面程序，将模板函数定义放在文件开头，从而无需提供模板原型。与常规函数一样，通过在使用函数前提供模板函数定义，让他充当原型。

choices.cpp

	#include <iostream>
	using namespace std;
	
	template <class T>            //#1
	T lesser(T a, T b)
	{
		return a < b ? a : b; 
	}
	
	int lesser (int a, int b)    //#2
	{
		a = a < 0 ? -a : a;
		b = b < 0 ? -b : b;
		return a < b ? a : b;
	}
	
	int main()
	{
		int m = 20;
		int n = -30;
		double x  = 15.5;
		double y  = 25.9;
		cout << lesser(m, n) << endl;     //使用#2       
		cout << lesser(x, y) << endl;     //使用#1
		cout << lesser<>(m, n) << endl;   //使用#1
		cout << lesser<int>(x, y) << endl;//使用#1
		return 0;
	}


结果

	20
	15.5
	-30
	15

上述程序提供了一个模板和一个标准函数，其中模板返回两个值较小的一个，而标准函数返回两个值绝对值较小的那个。如果函数定义是在使用函数前提供的，它将充当原型，因此这个示例无需提供原型

	lesser<>(m, n);

<>指出，编译器应选择模板，而不是非模板函数；编译器注意到实参的类型为int，因此使用int替代T对模板进行示例化。

最后，lesser<int>(x, y)这条语句要求进行显示实例化，将使用显示实例化得到的函数。x和y的值将被强制转换为int，该函数返回一个int值


#### 4.多个参数的函数

将有多个参数的函数调用与有多个参数的原型进行匹配时，情况将非常复杂。编译器必须考虑所有参数的匹配情况。如果找到比其他可行函数都合适的函数，则选择该函数。一个函数要比其他函数都合适，其所有参数的匹配程度都必须不必其他函数差，同时至少有一个参数的匹配程度比其他函数都高。


### 8.5.6 模板函数的发展

在C++发展的早期，大多数人都没有想到模板函数和模板类会有这么强大而有用。

#### 1.是什么类型

在C++98中，编写模板函数时，一个问题是并非总能知道应在声明中使用哪种类型。如下：

	template <class T1, class T2>
	void ft(T1 x, T2 y)
	{
		?type? xpy = x + y;
		...
	}

xpy应为什么类型。由于不知道ft()将如何使用，因此无法预先知道这一点。正确的类型可能是T1、T2或其他类型。例如，T1可能是double，而T2可能是int，在这种情况下，两个变量的和将为double类型。T1可能是short，而T2可能是int，在这种情况下，两个变量的和为int。T1还可能是short，而T2可能是char，在这种情况下，加法运算将导致自动整型提升，因此结果类型为int。另外，结构和类可能重载运算符+，这导致问题更加复杂。

#### 2.关键字decltype(C++11)

C++11新增的关键字decltype提供了解决方案，可这样使用该关键字：

	int x;
	decltype(x) y;  //是y的类型跟x相同

给decltype提供的参数可以是表达式，因此在前面的模板函数ft()中，可使用下面的代码：

	decltype(x + y) xpy;
	xpy = x + y;

另一种方法是，将这两条语句合二为一：

	decltype(x +  y) xpy = x + y;

decltype比这些示例演示的更复杂些。为确定类型，编译器必须遍历一个核对表。假设有如下声明：

	decltype(expression) var;

则核对表的简化版如下：

第一步：如果expression是一个没有用括号括起来的标识符，则var的类型与该标识符的类型相同，包括const等限定符：

	double x = 5.5;
	double y = 7.9;
	double &rx = x;
	const double * pd;
	decltype(x) w;
	dexltype(rx) u = y;
	decltype(pd) v;

第二步：如果expression是一个函数调用，则var的类型与函数的返回类型相同：

	long indeed(int);
	decltype(indeed(3)) m;

注意：并不会实际调用函数，编译器通过查看函数的原型来获悉返回类型，而无需实际调用函数。


第三步： 如果expression是一个左值，则var为指向其类型的引用。这好像意味着前面的w应为引用类型，因为x是一个左值。但别忘了，这种情况已经在第一步处理过了。要进入第三步，expression不能是用未用括号括起来的标识。那么，expression是用括号括起来的标识符：

	double xx = 4.4;	
	decltype((xx)) r2 = x; //r2是doule & 类型
	decltype(xx) w = x;   //w是double类型

顺便说一句，括号并不会改变表达式的值和左值性。例如下面两条语句等效：

	xx = 98.6;
	(xx) = 98.6;

第四步：如果前面的条件都不满足，则var的类型与expresssion类型相同：


	int j = 3;
	int &k = j;
	int &n = j;
	decltype(j+6) i1; //i1为int
	decltype(100L) i2; //i2为long
	decltype(k+n) i3; //i3为int


请注意，虽然n和k都是引用，但表达式k+n不是引用，它是两个int的和，因此类型为int。

如果需要多次声明，可结合typedef和decltyep:

	template <class T1, class T2>
	void ft(T1 x, T2 y)
	{
		typedef decltype(x + y)	 xytype;
		xytype xpy = x + y;
		xytype arr[10];
		xytyep & rxy = arr[2];
		... 
	}


#### 3. 另一种函数声明的语法（C++后置返回类型）


另一个相关的问题是decltype本身无法解决的。如下不完整的模板函数：

	template <class T1, class T2>
	?type? t(T1 x, T2 y)
	{
		...
		return x + y;
	}

同样，无法预先知道x和y相加得到的类型。好像可以将返回类型设置为decltype(x+y)。但不幸的是，此时还未声明参数x和y，它们不在作用域内。必须在声明参数后使用decltype。为此，C++新增了一种声明和定义函数的语法。下面使用内置类型来说明这种语法的工作原理。对于下面的原型：

	double h(int x, float y);

使用新增的语法可编写成这样：

	auto h (int x, float y) -> double;

这将返回类型移动到参数声明后面。->double被称为后置返回类型。其中auto是一个占位符，表示后置返回类型提供的类型。这是C++新增给auto的一种角色。这样语法可以用于函数定义：

	auto h(int x, float y) -> double
	{...}

通过结合使用这种语法和decltype，便可给出gt()指定返回类型，如下：

	template<class T1, class T2>
	auto gt(T1 x, T2 y) -> decltype(x + y)
	{
		return x + y;
	}

现在decltype在参数声明后面，因此x和y位于作用域内。

## 8.6 总结

函数

引用变量： & k

默认参数

重载

模板

模板重载 




