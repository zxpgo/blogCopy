---

title:  C++ Primer Plus--内存模型和名称空间（九）
date: 2018-10-18 15:53:14
tags: C++
categories: "C++ Primer Plus学习笔记"
mathjax: true

---

单独编译，存储持续性、作用域和链接性，定位运算符，名称空间。<!--more-->

## 9.1 单独编译

和C语言一样，C++允许甚至鼓励程序员将组件函数放在独立的文件中。可以单独地编译这些文件，然后将它们链接成可执行文件的程序。如果只修改了一个文件，则可以只重新编译该文件，然后将它与其他文件的编译版本链接。这使得大程序的管理更便捷。

大多数C++环境提供了其他工具来帮助管理。比如：Linux系统具有make程序，可以跟踪程序依赖文件以及这些文件的最后修改时间。运行make时，如果检测上次编译后修改了源文件，make将记住重新构建所需的步骤。大多数继承环境都在project菜单中提供了类似的工具。

C++提供了#include来处理不同文件使用同一个结构的问题。与其将结构声明加入到每一个文件中，不如将其放在头文件中，然后在每一个源代码文件中包含该文件头。这样，要修改结构声明时，只需在头文件中做一次改动即可。另外，也可以将函数原型放在头文件中。因此，可以将原来的程序分成三部分：

- 头文件：包含结构声明和使用这些结构的函数的原型。
- 源代码文件：包含与结构有关的函数的代码。
- 源代码文件：包含调用与结构相关的函数的代码。

这是一种非常有用的组织程序的策略。例如，如果编写另一个程序，也需要使用这些函数，则只需要包含头文件，并将函数文件添加到项目列表或make列表中即可。另外，这种组织方式也与OOP方法一致。一个文件（头文件）包含了用户定义类型的定义；另一个文件包含操作用户定义类型的函数的代码。这两个文件组成了一个软件包，可用于各种程序中。

**请不要将函数定义或变量声明放在头文件中**。这样做对于简单的情况可能是可行的，但通过会迎来麻烦。例如，如果头文件包含一个函数定义，然后在其他两个文件中包含该头文件，则同一个程序中包含同一个函数的两个定义，除非函数是内联的，否则这将出错。

下面列出了头文件中包含的内容：

- 函数原型
- 使用#define或const定义的符合常量
- 结构声明
- 类声明
- 模板声明
- 内联函数

将结构放在头文件中可以的，因为它们不会创建变量，而只是在源代码文件中声明结构时，告诉编译器如何创建变量。同样，模板声明不是被编译的代码，它们指示编译器如何生成与源代码中的函数调用相匹配的函数定义。被声明为const的数据和内联函数有特殊的链接属性，因此可以放在头文件中。

下面三个程序是将第7章中坐标转换程序分成几个独立部分后得到的结果。注意，在包含头文件时，使用“coordin.h”，而不是<coordin.h>。如果文件名包含在尖括号中，则C++编译器将在存储标准头文件的主机系统的文件系统中查找；但如果文件名包含在双引号中，则编译器将首先查找当前的工作目录或源代码目录。如果没有在那里找到头文件，则将在标准位置查找，因此在包含自己的头文件时，应使用双引号而不是尖括号。


coordin.h

	#ifndef COORDIN_H_
	#define COORDIN_H_
	
	struct polar
	{
		double distance;
		double angle;
	};
	
	struct rect 
	{
		double x;
		double y;
	};
	
	polar rect_to_polar(rect xypos);
	void show_polar(polar  dapos);
	
	#endif


file1.cpp

	#include <iostream>
	#include "coordin.h"
	using namespace std;
	
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

file2.cpp

	#include <iostream>
	#include <cmath>
	#include "coordin.h"
	using namespace std;
	
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


**编译多文件**

	g++ file1.cpp file.cpp -o file
	
**运行可执行文件**

	./file


结果：

	Enter the x and y value: 30 100
	Distance = 104.403, Angle =  73.3008 degrees.
	Nest two number(q to quit): q

我们讨论的是根据文件进行单独编译，但为保持通用性，C++标准使用了术语"翻译单元"，而不是文件；文件并不是计算机组织信息时的唯一方式。出于简化的目的，可将文件术语解释为“翻译单元”。

**头文件管理**

在同一个文件中只能将同一个头文件包含一次。记住这个规则很容易，但很可能在不知情的情况下将头文件包含多次。例如，可能使用包含了另外一个头文件的头文件。有一种标准的C/C++技术可以避免多次包含同一个头文件。它是基于预处理器编译指令#ifndef(即if not defint)的。下面的代码片段意味着仅当以前没有使用预处理器编译指令#define定义名称COORDIN_H_时，才处理#ifndef和endif之间的语句：

	#ifndef COORDIN_H_
	...
	#endif

通常，使用#define语句用来创建符合常量，如下：

	#define MAXIMUM  4096

但只要将#define用于名称，就足以完成该名称的定义，如下：

	#define COORDIN_H_

使用这种技术将文件内容包含在#ifdef中：

	#ifndef COORDIN_H_
	#define COORDIN_H_
	...
	#endif

编译器首次遇到该文件时，名称COORDIN_H_没有定义（根据include文件名来选择名称，并加上一些下划线，以创建一个在其他地方不太可能被定义的名称）。在这种情况下，编译器将查看#ifndef和#endif之间的内容，并读取定义COORDIN_H_的这一行。如果在同一文件中遇到其他包含coordin.h的代码，编译器将知道COORDIN_H_已经被定义了，从而跳到#endif后面的一行上。注意，这种方法并不能防止编译器将文件包含两次，而只是让它忽略地磁包含之外的所有内容。大多数标准C/C++头文件都使用这种防护方案。否则，可能在一个文件中定义同一个结构两次，这将导致错误。


**多个库的链接**

C++标准允许每个编译器设计人员以他认为合适的方式实现名称修饰（第8章已经介绍），因此由不同的编译器创建的二进制模板可能无法正确地链接。即，两个编译器将为同一个函数生成不同的修饰名称。名称的不同将使链接器无法将一个编译器生成的函数调用与另一个编译器生成的函数定义匹配。在链接编译模块时，请确保所有对象文件或库都是由同一个编译器生成的。如果有源代码，通常可以用自己的编译器重新编译源代码来消除链接错误。

	
## 9.2 存储连续性、作用域和链接性

接下来扩展第4章对内存方案的讨论，即存储类别如何影响信息在文件间的共享。先复习一下内存有关的知识。C++使用三种不同的方案存储数据，这些方案区别在于数据保留在内存的时间。

- 自动存储持续性：在函数定义中声明的变量（包括函数参数）的存储持续性为自动的。它们在程序开始执行其所属的函数或代码块时被创建，在执行完函数或代码块时，它们使用的内存被释放。C++有两种存储持续性为自动的变量。
- 静态存储存储性：在函数定义外定义的变量和使用的关键字static定义的变量的存储持续性都为静态。它们在程序整个运行过程中都存在。
- 线程存储持续性（C++11）：当前，多核处理很常见，这些CPU可同时处理多个执行任务。这让程序能够将计算放在可并行处理的不同线程中。如果变量使用关键字thread_local声明的，则其声明周期与所属的线程一样长。（本系列不讨论）
- 动态存储持续性：用new运算符分配的内存将一直存在，直到使用delete运算符将其释放或程序结束为止。这种内存的存储持续性为动态，有时候被称为自由存储或堆。

下面介绍，各种变量合适在作用域内或可间以及链接性的细节。链接性决定了哪些可在文件间共享。

### 9.2.1 作用域和链接

**作用域**描述了名称在文件（翻译单元）的多大范围可见。例如，函数中定义的变量可在该函数中使用，但不能在其他函数中使用；而在文件的函数定义之前定义的变量则可在所有函数中使用。

**链接性**描述了名称如何在不同单元（文件）间共享。链接性为外部的名称可文件间共享，链接性为内部的名称只能由一个文件中的函数共享。自动变量的名称没有链接性，因为它们不能共享。

C++函数的作用域可以是整个类或整个名称空间（包括全局的），但不能是局部的。

### 9.2.2 自动存储持续性

在默认情况下，在函数中声明的函数参数和变量的存储性为自动，作用域为局部，没有链接性。即，如果在main()中声明一个名为tesas的变量，并在函数oil()中也声明一个名为tesax变量，则创建了两个独立的变量---只有在定义它们的函数中才能使用它们。

如果在代码块中定义了变量，则该变量的存在时间和作用域将被限制在该代码块内。例如，假设在main()的开头定义了一个名为teledeli的变量，然后在main()中开始一个新的代码块，并其中定义了一个新的websight，则teledeli在内部代码块和外部代码块都是可见的，而wabsight就只在内部代码块中可见，它的作用域是从定义它的位置到该代码块的结尾：

	int main()
	{
		int teledeli = 5;
		{
			int websight = -2;
			cout << websight << ' ' << teledeli << endl;
		}
		cout << teledeli << endl;
	}

而，如果将内部代码块中的变量命名为teledeli，而不是websight，使得有两个同名的变量（一个位于外部代码块中，另一个位于内部代码块中）。在这种情况下，程序执行内部代码块中的语句时，将teledeli解释为局部代码块变量。新的定义隐藏了以前的定义，新定义可见，旧定义暂时不见。在程序离开该代码块时，原来的定义又重新可见。

auto.cpp
	
	#include <iostream>
	using namespace std;
	void oil(int x);
	
	int main()
	{
		int texas = 31;
		int year = 2018;
		cout << "In main(), texas = " << texas << ", &texas = "<< &texas << endl;
		oil(texas);	
		cout << "In main(), texas = " << texas << ", &texas = "<< &texas << endl;
		return 0;
	}
	
	void oil(int x)
	{
		int texas = 5;
		cout << "In oil(), texas = " << texas << ", &texas = "<< &texas << endl;
		cout << "In oil(), x = " << x << ", &x = "<< &x << endl;
		{
			int texas = 113;
			cout << "In block, texas = " << texas << ", &texas = "<< &texas << endl;
			cout << "In block, x = " << x << ", &x = "<< &x << endl;
		}
		cout << "Post-block, texas = " << texas << ", &texas = "<< &texas << endl;
	}

结果

	In main(), texas = 31, &texas = 0x7fffa7f13e28
	In oil(), texas = 5, &texas = 0x7fffa7f13dfc
	In oil(), x = 31, &x = 0x7fffa7f13dec
	In block, texas = 113, &texas = 0x7fffa7f13df8
	In block, x = 31, &x = 0x7fffa7f13dec
	Post-block, texas = 5, &texas = 0x7fffa7f13dfc
	In main(), texas = 31, &texas = 0x7fffa7f13e28


执行main()时，程序为texas分配空间，使得这些变量可见。当程序调用oil()时，这些变量仍留在内存中，但不可见。为两个新变量（x和texas）分配内存，从而使它们可见。在执行到oil()中的内部代码块时，新的texas将不可见，它被一个更新的定义代替。然而，变量x仍可见，这是因为该代码块中没有定义x变量。当程序流程离开该代码块时，将释放最新的texas使用的内存，而第二个texas再次可见。当oil()函数结束时，texas和x都将过期，而最初的texas再次变得可见。

**使用C++11中的auto**

C++11中，关键字auto用于自动类型推断。但在C语言和以前的C++中，auto的含义截然不同，它用于显式地指出变量为自动存储：

	int froob(int n)
	{
		auto float ford;
		...
	}

由于只能将关键字auto用于默认为自动的变量，因此程序员几乎不使用它。它的主要用途是指出当前变量为局部自动变量。

在C++11中，这种用法不再合法。指定标准的人不愿引入新关键字，因为这样做可能导致将关键字用于其他目的代码非法。考虑到auto的老用法很少使用，因此赋予其新含义比引入新关键字是更好的选择。

#### 1.自动变量的初始化

可以使用任何在声明时其值为已知的表达式来初始化自动变量，下面的示例初始化变量x,y和z：

	int w;
	int x = 5;
	int big = INT_MAX - 1;
	int y = 2 * x;
	cin >> w;
	int z = 3 * w;


#### 2.自动变量和栈

由于自动变量的数目随函数的开始和结束而增减，因此程序必须在运行时对自动变量进行管理。常用的方法是**留出一段内存，并将其视为栈**，以管理变量的增减。之所以称为栈，是由于新数据被象征性地放在原有数据的上面（即，在相邻的内存单元中，而不是在同一内存单元中），当程序使用完后，将其从栈中删除。栈的默认长度取决于实现，但编译器通常提供改变栈长度的选项。

程序使用两个指针来跟踪栈，一个指针指向栈低---栈的开始位置；另一个指针指向栈顶--下一个可以内存单元。当函数调用时，其自动变量将加入到栈中，栈顶指针指向变量后面的下一个可用的内存单元。函数结束时，栈顶指针被重置为函数被调用前的值，从而释放新变量使用的内存。

**栈是LIFO(后进先出)的**，即最后加入栈的变量最先被弹出。这种设计简化了参数传递。函数调用将其参数的值放在栈顶，然后重新设置栈顶指针。被调用的函数根据其形参描述来确定每个参数的地址。


#### 3.寄存器

关键字register最初是由C语言引入的，它建议编译器使用CPU寄存器来存储自动变量：
	
	register int count_fast;

这旨在提供访问变量的速度。

在C++之前，这个关键字在C++中的用法始终未变，只是随着硬件和编译器变得越来越复杂，这种提示表明变量用的很多，编译器可对其做特殊处理。在C++中，这种提示作用也失去了，关键字register只是显式地指出变量是自动的。鉴于关键字register只能用于原本就是自动的变量，使用它的唯一原因是指出程序员想使用一个自动变量，这个变量的名称可能于外部变量相同。这与auto以前的用途完全相同。然后，保留关键字register的重要原因是，避免使用了该关键字的现有代码非法。

### 9.2.3 静态持续变量

和C语言一样，C++也为静态存储持续性变量提供了3中链接性：外部链接（可在其他文件中访问）、内部链接（只能在当前文件中访问）和无链接（只能在当前函数或代码块中访问）。这3种链接性都在整个程序执行期间存在，与自动变量相比，它们的寿命更长。由于静态变量的数目在程序运行期间是不变的，因此程序不需要使用特殊的装置（如栈）来管理它。编译器将分配固定的内存块来存储所有的静态变量，这些变量在整个程序执行期间一直存在。另外，如果没有显式地初始化为静态变量，编译器将把他设置为0.在默认情况下，静态数组和结构每个元素或成员的所有都设置为0。

下面介绍如何创建3种静态持续变量：

- 要想创建链接性为外部的静态持续变量，必须在代码块的外部声明它；
- 要想创建链接性为内部的静态持续变量，必须在代码块的外面声明它，并使用static限定符；
- 要创建没有链接性的静态持续变量，必须在代码块内部声明它，并使所以static限定符。

下面的代码说明了3种变量：

	int gloabl =1000;          //静态持续性变量，外部链接
	static int one_file = 50;  //静态持续性变量，内部链接
	int main()
	{...}
	void fun1(int n)
	{
		static int count = 0; //静态持续性变量，没有链接性
		int llama = 0;
	}
	void fun2(int q)
	{...}


正如前面指出的，所有静态持续变量（global\one_file\count）在整个程序执行期间都存在。

在fun1中声明的变量count作用域为局部，没有链接性，这意味只能在fun1()函数中使用它，就像自动白llama一样。然而，与llama不同的是，即使在func1()函数没有被执行时，count也留在内存中。

global和one_file的作用于都为整个文件，即从声明位置到文件结尾的范围内都可以被使用。具体说，可以在main()、fun1()和fun2()中使用它。由于one_file的链接性为内部，因此只能在包含上述代码的文件中使用它；由于global的链接性为外部，因此可在程序的其他文件中使用它。

所有的静态持续性变量又下述初始化特征：未被初始化的静态变量的所有为被设置为0。这种变量被称为零初始化的。

下表总结了5中变量的存储特性。

**5种变量存储方式**

| 存储描述 | 持续性 | 作用域 | 链接性 | 如何声明 |
|---|
| 自动 | 自动 | 代码块 | 无 | 在代码块中 |
| 寄存器 | 自动 | 代码块 | 无 | 在代码块中，使用关键字register |
| 静态，无链性 | 静态 | 代码块 | 无 | 在代码块中，使用关键字static |
| 静态，外部链接性 | 静态 | 文件 | 外部 | 不在任何函数内 |
| 静态，内部链接性 | 静态 | 文件 | 内部 | 不在任何函数内，使用关键字static |


**静态变量初始化**
	
除默认的零初始化外，还可对静态变量进行常量表达式初始化和动态初始化。

零初始化和常量表达式初始化被统称为静态初始化，这意味着在编译器处理文件时初始化变量。动态初始化意味着变量将在编译后初始化。

初始形式由什么因素决定？首先，所有静态变量都被零初始化，而不管程序员是否显式地初始化了它。接下来，如果使用常量表达式初始化了变量，且编译器仅根据文件内容就可计算表达式，编译器将执行常量表达式初始化。必要时，编译器将执行简单计算。如果没有足够的信息，变量将被动态初始化。情况如下代码：

	#include <cmath>
	int x;              //零初始化
	int y = 5;          //常量表达式初始化
	long z = 13 * 13;   //常量表达式初始化
	const double pi = 4.0 * atan(1.0); //动态初始化


首先，x,y,z和pi被零初始化。然后，编译器计算常量表达式，并将y和z分别初始化为5和169。但要初始化pi，必须调用函数atan()，这需要等到函数被链接且程序执行时。

常量表达式并非只能是使用字面常量的算术表达式。例如，它还可以**使用sizefo运算符**：

	int enough = 2 * sizeof(long) + 1;
	
C++新增了关键字constexpr，这增加了创建常量表达式的方式。


### 9.2.4 静态持续性、外部链接性

链接性未外部的变量简称为外部变量，它们的存储持续性为静态，作用域为整个文件。外部变量是在函数外部定义的，因此对所有函数而言是都是外部的。

#### 1.单定义规则

一方面，在每个使用外部变量的文件中，都必须声明它；另一方面，C++有“单定义规则”，该规则指出，变量只能有一次定义。为满足这种需求，C++提供了两种变量声明，一种是定义声明或简称定义，它给变量分配存储空间；另一种是引用声明或简称声明，它不给变量分配存储空间，因为它引用已有变量。

引用声明使用关键字extern，且不进行初始化；否则，声明为定义，导致分配存储空间:

	double up;	
	extern int blem; //声明
	extern char gr = 'z'; //定义，因为初始化

如果要在多个文件中使用外部变量，只需在一个文件中包含该变量的定义，但在使用该变量的其他所有文件中，都必须使用关键字extern声明它：

	//file1.cpp
	extern int cats = 20; //定义
	int dogs = 22;        //定义
	int fleas;            //定义

	//file2.cpp
	//使用来自文件file1.cpp的变量cats,dogs
	extern int cats;
	extern int dogs;

	//file3.cpp
	//使用来自文件file1.cpp的变量cats,dogs,fleas
	extern int cats;
	extern int dogs;
	extern int fleas;
	
在这里，所有文件都使用了file1.cpp中定义的变量cats和dogs，但file2.cpp没有重新声明变量fleas，因此无法访问它。在file1.cpp中，关键字extern并非必不可少，因为即使省略它，效果也相同。

注意，单定义规则并非意味着不能有多个变量的名称相同。例如，在不同函数中声明的同名自动变量是彼此独立，它们都有自己的地址。另外，正如前面示例所表示的，局部变量可能隐藏同名的全局变量。然而，虽然程序中可包含多个同名的变量，单每个变量都只有一个定义。

如果在函数中声明了一个与外部变量同名的变量，这种声明将被视为一个自动变量的定义，当程序执行自动变量所属的函数时，该变量将位于作用域内。		

external.cpp

	#include <iostream>
	using namespace std;
	double warming = 0.3;
	void update(double dt);
	void local();
	
	int main()
	{
		cout << "Global warming is " << warming << " degrees.\n";
		update(0.1);	
		cout << "Global warming is " << warming << " degrees.\n";
		local();
		cout << "Global warming is " << warming << " degrees.\n";
		return 0;
	}

support.cpp

	#include <iostream>
	using std::cout;
	extern double warming;
	void update(double dt);
	void local();
	void update(double dt)
	{
		extern double warming;
		warming += dt;
		cout << "Updating global warming to " << warming << " degrees.\n";
	}
	void local()
	{
		double warming = 0.8;
		cout << "Local warming = " << warming << " degrees.\n";
		cout << "But global warming = " <<  ::warming << "dgrees.\n";
	}
	


结果

	Global warming is 0.3 degrees.
	Updating global warming to 0.4 degrees.
	Global warming is 0.4 degrees.
	Local warming = 0.8 degrees.
	But global warming = 0.4dgrees.
	Global warming is 0.4 degrees.

main()和update()都可以访问外部变量warming，注意update()修改了warming，这种修改在随后的使用该变量时显现出来了。

在external.cpp中，warming的定义如下：

	double external = 0.3; //定义

在support.cpp中，使用关键字external声明变量warming，让该文件中的函数能够使用它：

	extern double warming; //使用来自external文件中的变量warming

另外，函数update()使用关键字extern重新声明了变量warming，这个关键字的意思是，通过这个关键字的意思是，通过这个名称使用在外部定义的变量。由于即使省略该声明，update()的功能也相同，因此该声明是可选的。它指出了函数被设计成使用外部变量。

local()函数表明，定义与全局变量同名的局部变量后，局部变量将隐藏全局变量。例如，local()函数显示warming的值时，将使用warming的局部定义。

C++提供了作用域解析运算符（::）。放在变量前面时，该运算符表示使用的全局变量版本。因此，local()将warming显示为0.8，但::warming显示为0.4。

**全局变量和局部变量**

所有的函数能访问全局变量，因此不用传递参数。但易于访问的代价很大--程序不可靠。程序越能避免对数据进行不必要的访问，就越能保持数据的完整性。通过情况下，应使用局部变量，应在需要知晓时才传递数据，而不应不加区分地使用全局变量来使数据可用。全局变量尤其适合于表示常量数据，因为这样可以使用关键字const来防止数据被修改。比如，月份数组：

	const char * const months[12] = 
	{
		"January", "February", "March", "April", "May",
		"June", "July", "Auguest", "September", "October",
		"November", "December"
	}

第一个const表示数组中的元素为常量，防止修改字符串，而第二个const表示数组中每个指针始终指向它最初指向的字符串。

### 9.2.5 静态持续性、内部链接性

将static限定符用于作用域为整个文件的变量时，该变量的链接性将为内部。在多文件中程序，内部链接性和外部链接性之间的差别很有意义。链接性为内部的变量只能在其所属的文件中使用；但常规外部变量都具有外部链接性，即可以在其他文件中使用。

如果要在其他文件中使用相同的名来表示其他变量，该如何办呢？只需省略关键字extern即可吗？
	
	//file1.cpp
	int errors;
	//file2.cpp
	int errors;
	void froobins(){cout << errors;}
	
这种做法将失败，因为它违反了单定义规则。file2.cpp中的定义试图创建一个外部变量，因此程序将包含errors的两个定义，这是错误的。

如果文件定义了一个静态外部变量，其名称与另一个文件声明的常规外部变量相同，则在该文件中，静态变量将隐藏常规外部变量：

	//file1.cpp
	int errors;
	//file2.cpp
	static int errors = 5;
	void froobins(){cout << errors;}

这没有违反单定义规则，因为关键字static指出标识符errors的链接性为内部，因此并非要提供外部定义。

注意：在多文件程序中，可以在一个文件中定义一个外部变量。使用该变量的其他文件必须使用关键字extern声明它。

可使用外部变量在多个文件程序的不同部分之间共享数据；可使用链接为内部的静态变量在同一个文件中的多个函数之间共享数据。另外，如果将作用域为整个文件的变量变为静态，就不必担心其名称与其他文件中的做业务为整个文件的变量发送冲突。

下面程序演示了如何处理链接性为外部和内部的变量。

twofile1.cpp

	#include <iostream>
	int tom = 3;              //外部变量
	int dick = 30;            //外部变量
	static int harry = 300;   //静态外部变量
	void remote_access();
	using namespace std;
	
	int main()
	{
		cout << "main() reports the following addresses:\n"	;
		cout << &tom  << " = &tom. "  << &dick << " = &dick, ";
		cout << &harry << " = &harry\n";
		remote_access();
		return 0;
	}

twofile2.cpp

	#include <iostream>
	extern int tom;          //声明，使用twofile1.cpp中定义的tom变量
	static int dick = 10;	 //覆盖外部变量dick
	int harry = 200;         //外部变量定义，不与twofile1.cpp中的harry冲突
	using namespace std;
	
	void remote_access()
	{
		cout << "remote_access() reports the following addresses:\n";
		cout << &tom << " =  &tom, " << &dick << " = &dick, ";
		cout << &harry << " = &harry";
	}



结果

	main() reports the following addresses:
	0x60104c = &tom. 0x601050 = &dick, 0x601054 = &harry
	remote_access() reports the following addresses:
	0x60104c =  &tom, 0x601058 = &dick, 0x60105c = &harry

从上述地址可知，这两个文件使用了同一个tom变量，但使用了不同的dick和harry变量。


### 9.2.6 静态存储持续性、无链接性

创建无连接性的局部变量，将static限定符用于代码块中定义的变量。在代码块中使用static时，将导致局部变量的存储持续性为静态的。这意味着虽然该变量只在该代码块中可用，但它在该代码块中不处于活动状态时仍然存在。因此，在两次函数调用之间，静态局部变量的值将保持不变。

另外，如果初始化了静态局部变量，则程序只在启动时进行一次初始化。以后调用函数时，将不会像自动变量那样再次被初始化。

static.cpp

	#include <iostream>
	using namespace std;
	const int ArSize = 10;
	void strcount(const char * str);
	int main()
	{
		char input[ArSize];
		char next;
		cout << "Enter a line:\n";
		cin.get(input, ArSize);
		while (cin)
		{
			cin.get(next);
			while (next != '\n')
				cin.get(next);
			strcount(input);
			cout << "Enter next lin (empty line to quit):\n";
			cin.get(input, ArSize);
		}
		cout << "Bye.\n";
		return 0;
	}
	
	void strcount(const char * str)
	{
		static int total = 0;
		int count = 0;
		cout << "\"" << str << "\" contains ";
		while (*str++) 
			count ++;
		total += count;
		cout << count << " characters\n";
		cout << total << " characters total.\n";
	}


结果

	Enter a line:
	nice pants
	"nice pant" contains 9 characters
	9 characters total.
	Enter next lin (empty line to quit):
	thanks
	"thanks" contains 6 characters
	15 characters total.
	Enter next lin (empty line to quit):
	parting is such sweet sorrow
	"parting i" contains 9 characters
	24 characters total.
	Enter next lin (empty line to quit):
	ok
	"ok" contains 2 characters
	26 characters total.
	Enter next lin (empty line to quit):
	
	Bye.


该程序中使用了一种处理行输入可能长于目标数组的方法，cin.get(input, ArSize)将一直读取输入，直到达到行尾或读取了ArSize-1个字符为止。它把换行符留在队列中。该程序使用cin.get(next)读书行输入后的字符。如果next是换行符，在说明cin.get(input, ArSize)读取了整行；否则说明行中还有字符没有被读取。随后，程序使用一个循环来丢弃余下的字符。该程序还是用cin.get(char*, int)读取空行将导致cin为false。

注意到，每次函数被调用时，自动变量count被重置为0。而静态变量total只在程序运行时被设置为0，以后在两次函数调用之间，其值保持不变，因此能够记录读取的字符总数。

### 9.2.7 说明符和限定符

有些被称为存储说明符或cv-限定符的C++关键字提供了其他有关存储的信息。下面是存储说明符：

- auto(C++中不再是说明符): 在C++之前，可以在声明中使用关键字auto指出变量为自动变量，但在C++11中，auto用于自动类型推断。
- register: 关键字register用于在声明中指示寄存器存储，而在C++11中，它指示显示地指出变量是自动的。
- static: 关键字static被用在作用域为整个文件的声明中时，表示内部链接性；被用于局部声明中，表示局部变量的存储持续性为静态的。
- extern: 关键字extern表明引用声明，即声明引用在其他地方定义的变量。
- thread_local(C++新增): 关键字thread_lcoal指出变量的持续性雨其所属线程的持续性相同。thread_local变量之于线程，犹如常规静态变量之于整个程序。
- mutable： 关键字mutable的含义将根据const来解释。thread_local可与static或extern结合使用。

#### 1. CV限定符

下面是cv限定符：

- const
- volatile

最常用的cv-限定符是const，它表明，内存被初始化后，程序边便不能再对它进行修改。

关键字volatile表明，即使程序代码没有对他内存单元进行修改，其值也可能发送变化。例如，可以将一个指针指向某个硬件位置，其中包含了来自串行端口的时间或信息。在这种情况下，硬件（而不是程序）可能修改其中的内容。或者两个程序可能互相影响，共享数据。该关键字的作用是为了改善编译器的优化能力。例如，假设编译器发现，程序在几条与剧中两次使用了某个变量的值，则编译器可能不是让程序查找这个值两次，而是将这个值缓存到寄存器中。这种优化假设变量的值在这两次时间之间不会变化。如果不将变量声明为volatile，则编译器将进行这种优化；将变量声明为volatile，相当于告诉编译器，不要进行这种优化。


#### 2. mutable

mutable可以指出，即使结构（或类）变量为const，其某个成员也可以被修改。例如：

	struct data
	{
		char name[30];
		mutable int accesses;
	};
	const data veep = {"Clodde", 0};
	strcpy(veep.name, "Joye"); // 非法
	veep.accesses++; //被声明为mutable，所以是合法的

veep的const限定符禁止程序修改veep的成员，但accesses成员的mutable说明符是的accesses不受这种限制。

#### 3. 再谈const

在C++中，const限定符对默认存储类型稍有影响。在默认情况下全局变量的链接性为外部，但const全局变量的链接性为内部。即，在C++看来，全局const定义就像使用了static说明符一样：

	const int fingers = 10; //同static const int fingers = 10一样
	int main()
	{...}

C++修改了常量类型的规则。例如，假设将一组常量放在头文件中，并在同一个程序的多个文件中使用该头文件。那么，预处理器将头文件的内容包含到每个源文件中后，所有的源文件都将包含类似下面的定义：

	const int fingers = 10;
	const char * waring = "Wak!";

如果全局const声明的链接性像常规变量那样为外部的，则根据单定义规则，这将出错。即，只能有一个文件可以包含前面的声明，而其他文件必须使用extern关键字来提供引用声明。另外，只有使用extern关键字的声明才能进行初始化：

	extern const int fingers; //不能被初始化
	extern const char * waring;

因此，需要为某个文件使用一组定义，而其他文件使用另一组声明。然而，由于外部定义的const数据的链接性为内部，因此可以在所有文件中使用相同的声明。

内部链接性还意味着，每个文件都有自己的一组常量，而不是所有文件共享一组常量。每个定义都是其所属文件私有的，这就是能够将常量定义放在头文件中的原因。这样，只要在两个源代码文件中包括同一个头文件，则它们可以获得同一组常量。

如果出于某种原因，程序员希望某个常量的链接为外部，则可以使用extern关键字来覆盖默认的内部链接性：

	extern const int states = 50;

在这种情况下，必须在所有使用该常量的文件中使用extern关键字来声明它。这与常规外部变量不同，定义常规外部变量时，不必使用extern，但使用该变量的其他文件中必须使用extern，然而，鉴于单个const在多个文件之间共享，因此只有一个文件可对其进行初始化。

在函数或代码块中声明const，其作用域为代码块，即仅当程序执行该代码块中的代码时，该常量才可用的。这意味着在函数或代码块中创建常量时，不必担心其名称与其他地方定义的常量冲突。

### 9.2.8 函数和链接性

和变量一样，函数也有链接性，虽然可选择的范围比变量小。和C语言一样，C++不允许在一个函数中定义另一个函数，因此所有的函数的存储新都自动为静态的，即在整个程序执行期间都一直存在。默认情况下，函数的链接性为外部的，即可以文件间共享。

实际上，可以在函数原型中使用关键字extern来指出函数是在另一个文件中定义的，不过这是可选的。还可以使用关键字static将函数的链接性设置为内部，使之只能在一个文件中使用。必须同时在原型和函数定义中使用该关键字：

	static int private(double x);
	static int private(double x)
	{...}


这意味着该函数只在这个文件中可见，还意味着可以在其他文件中可以定义同名函数。和变量一样，在定义静态函数的文件中，静态函数将覆盖外部定义，因此即使在外部定义了同名的函数，该文件仍将使用静态函数。

单定义规则也适用于非内联函数，因此对于每个非内联函数，程序只能包含一个定义。对于链接性为外部的函数来说，这意味着在多文件程序中，只能有一个文件包含该函数的定义，但使用该函数的每个文件都应包含其函数原型。

内联函数不受这项规则的约束，这允许程序员能够将内联函数的定义放在头文件中。这样，包含了头文件的每个文件都有内联函数的定义。然而，C++要求同一个函数的所有内联定义都必须相同。

**C++在哪里查找函数**

假设在程序的某个文件中调用了一个函数，C++将到哪里去寻找该函数的定义呢？如果该文件中的函数原型指出该函数是静态的，则编译器将只在该文件中查找函数定义；否则，编译器（包括链接程序）将在所有的程序文件中查找。如果找到两个定义，编译器将发出错误消息，因为每个外部函数只能有一个定义。如果在程序文件中没有找到，编译器将在库中搜索。这意味着如果定义了一个与库函数同名的函数，编译器将使用程序员定义的版本，而不是库函数。

### 9.2.9 语言链接性

另一种形式的链接性--称为语言链接性也对函数有影响。

首先介绍一些背景知识，链接程序要求每个不同的函数都有不同的符合名。在C语言中，一个名称只对应一个函数，因此容易实现。为满足内部需要，C语言编译器可能将spiff这样的函数名翻译为_spiff。这种方法称为**C语言链接性**。但在C++中，同一名称可能对应多个函数，必须将这些函数翻译为不同的符号名称。因此，C++编译器执行名称矫正或名称修饰（参考第八章），为重载函数生成不同的符号名称。例如，可能将spiff(int)转换为_spiff_i，而将spiff(double,double)转换为_spiff_d_d。这种方法被称为**C++语言链接**。

链接程序寻找与C++函数调用匹配的函数时，使用的方法与C语言不同。但如果要在C++程序中使用C库中预编译的函数。将出现什么情况？例如：

	spiff(22); //spiff(int)来子一个C库

它在C库文件中的符号名称为_spiff，但对于我们假设的链接程序来说，C++查询约定是查找符号名称_spiff_i。为解决这种问题，可以**用函数原型来指出要使用的约定**：

	extern "C" void spiff(int);
	extern void spiff(int);
 	extern "C++" spiff(int); 

第一个原型使用C语言链接性；而后面的两个使用C++语言链接性。第二个原型是通过默认方式指出这一点的。

C和C++链接性是C++标准指出的说明符，但实现可提供其他语言链接性说明符。


### 9.2.10 存储方案和动态分配

前面介绍C++用来为变量（包括数组和结构）分配内存的5中方案，它们不适用于使用C++运算符new(或C函数malloc())分配内存，这种内存被称为**动态内存**。第4章介绍过，动态内存有运算符new和delete控制，而不是由作用域和链接性规则控制。因此，可以在一个函数中分配动态内存，而在另一个函数中将其释放。与自动内存不同，动态内存不是LIFO，其分配和释放顺序要取决于new和delete在何时以何种方式被使用。通常，编译器使用三块独立的内存：一块用于静态变量（可能再细分），一块用于自动变量，另一块用于动态存储。

虽然存储方案概念不再适用于动态存储，但适用于用来跟踪动态内存的自动和静态指针变量。例如，假设一个函数中包含下面的语句：

	float * p_fees = new float[20];

由new分配的80个字节（假设float为4个字节）的内存将一直保留再内存中，直到使用delete运算符将其释放。但当包含该声明的语句块执行完毕时，p_fees指针将消失。如果希望另一个函数能够使用这80个字节中的内容，则必须将其地址传递或返回给该函数。另一方面，如果将p_fees的链接性声明为外部的，则文件中位于该声明后面的所有函数都可以使用它。另外，通过在另一个文件中使用下述声明，便可在其中使用该指针：

	extern float * p_fees;

注意： 当程序结束时，由new分配的内存通常都将被释放，不过情况也并不总是这样。例如，在不那么健壮的操作系统中，有某些情况下，请求大型内存块将导致该代码块在程序结束不会被自动释放。最佳的做法是，使用delete来释放new分配的内存。

#### 1.使用new运算符初始化

为内置的标量类型分配存储空间并初始化，可在类型后后面加上初始值，并将其用括号括起来：

	int * pi = new int (6);
	double * pd = new double (99.99);

这种语法也可用于由合适构造函数的类型。

然而，要初始化常规结构或数组，需要使用大括号的列表初始化，这要求**编译器支持C++11**:

	struct where {double x; double y; double z};
	where * one = new where{3.5, 4.5, 6.6};
	int *ar = new int [4] {1,2,3,4};

在C++11中，还可以将列表初始化用于单值变量：

	int * pin = new int {6};
	double * pdo = new double {99.99};

#### 2.new失败时

new可能找不到请求的内存量。在最初的10年中，C++在这种情况下让new返回空指针，但现在将引发异常std::bad_alloc。第15章通过一些简单的例子演示了这两种方法的工作原理。

#### 3.new: 运算符、函数和替换函数

运算符new和new[]分别调用如下函数：

	void * operator new(std::size_t);
	void * operator new[](std::size_t);

这些函数被称为**分配函数**，它们位于全局名称空间中。同样，也有由delete和delete[]调用的**释放函数**：

	void operator delete(void *);
	void operator delete[] (void *);

它们使用第11章将讨论的运算符重载语法。std::size_t是一个typedef，对应于合适的整型。对于下面这样的基本语句：

	int * pi = new int;

被转换为下面的语句：

	int * pi = new(sizeof(int));

而下面的语句：

	int * pa = new int [14];

被转换为下面这样：

	int * pa = new(14 * sizeof(int));

 
使用new的语句也可能包含初始值，因此，使用new运算符时，不仅仅调用new()函数。

同样， 下面的语句：

	delete pi;

将转换位如下函数调用：

	delete (pi);

C++将这些函数称为**可替换的**。这意味着如果您有足够的知识和意愿，可为new和delete提供替换函数，并根据需要对其进行定制。例如，可定义作用域为类的替换函数，并对其进行定制，以满足该类的内存分配需求。在代码中，仍将使用new运算符，但它将调用您定义的new()函数。

#### 4.定位new运算符

通过，new负责在**堆**中找到一个足以能够满足需求的内存块。new运算符还有另一个种变体，被称为**定位new运算符**，它让您能够指定要使用的位置。程序员可能使用这种特性来设置其内存管理规程、处理需要通过特定地址进行访问的硬件或在特定位置创建对象。

要使用定位new特性，首先需要包含头文件new，它提供了这种版本的new运算符的原型；然后将new运算符用于提高了所需地址的参数。除需要指定参数外，句法与常规new运算符相同。具体说，使用定位new运算符时，变量后面可以有方括号，也可以没有。下面的代码演示了new运算符的4种用法：

	#include <new>
	struct chaff
	{
		char dross[20];
		int slag;
	};

	char buffer1[50];
	char buffer2[500];
	int main()
	{
		chaff *p1, *p2;
		int *p3, *p4;
		//常规new运算符
		p1 = new chaff;
		p3 = new int [20];
		//定位new运算符
		p2 = new (buffer1) chaff;
		p4 = new (buffer2) int [20];
	}
	
出于简化的目的，这个示例使用两个静态数组来定位new运算符提供内存空间。因此，上述代码从buffer1中分配空间给结构chaff，从buffer2中分配空间给一个包含20个元素的int数组。

下面程序使用常规new运算符和定位new运算符创建动态分配的数组。

newplace.cpp

	#include <iostream>
	#include <new>
	using namespace std;
	const int BUF =  512;
	const int N = 5;
	char buffer[BUF];
	int main()
	{
		double *pd1, *pd2;
		int i;
		cout << "Calling new and placement new:\n";
		pd1 = new double[N];
		pd2 = new (buffer) double[N];
		for (i = 0; i < N; i++)
			pd2[i] = pd1[i] = 1000 + 20.0 * i;
		cout << "Memory addresser:\n" << " heap: " <<  pd1
	             << " static: " << (void *) buffer << endl;
		cout << "Memory contens:\n";
		for (i = 0; i < N; i++)
		{
			cout << pd1[i] << " at " << &pd1[i] << ";";
			cout << pd2[i] << " at " << &pd2[i] << endl;
		}
		cout << "\n Calling new and placement new a secong time:\n";
		double *pd3, *pd4;
		pd3 = new double[N];
		pd4 = new (buffer) double[N];
		for (i = 0; i < N; i++)
			pd3[i] = pd4[i] = 1000 + 400 * i;
		cout << "Memory contents:\n";
		for (i = 0; i < N; i++)
		{
			cout << pd3[i] << " at " << &pd3[i] << ";";
			cout << pd4[i] << " at " << &pd4[i] << endl;
		}
	
	
		cout << "\n Calling new and placement new a secong time:\n";
		delete [] pd1;
		pd1 = new double[N];
		pd2 = new (buffer + N * sizeof(double)) double[N];
		for (i = 0; i < N; i++)
			pd2[i] = pd1[i] = 1000 + 60.0 * i;
		cout << "Memory contens:\n";
		for (i = 0; i < N; i++)
		{
			cout << pd1[i] << " at " << &pd1[i] << ";";
			cout << pd2[i] << " at " << &pd2[i] << endl;
		}
		delete [] pd1;
		delete [] pd3;
		return 0;
	
	}


结果

	alling new and placement new:
	Memory addresser:
	 heap: 0x72a010 static: 0x6021a0
	Memory contens:
	1000 at 0x72a010;1000 at 0x6021a0
	1020 at 0x72a018;1020 at 0x6021a8
	1040 at 0x72a020;1040 at 0x6021b0
	1060 at 0x72a028;1060 at 0x6021b8
	1080 at 0x72a030;1080 at 0x6021c0
	
	 Calling new and placement new a secong time:
	Memory contents:
	1000 at 0x72a040;1000 at 0x6021a0
	1400 at 0x72a048;1400 at 0x6021a8
	1800 at 0x72a050;1800 at 0x6021b0
	2200 at 0x72a058;2200 at 0x6021b8
	2600 at 0x72a060;2600 at 0x6021c0
	
	 Calling new and placement new a secong time:
	Memory contens:
	1000 at 0x72a010;1000 at 0x6021c8
	1060 at 0x72a018;1060 at 0x6021d0
	1120 at 0x72a020;1120 at 0x6021d8
	1180 at 0x72a028;1180 at 0x6021e0
	1240 at 0x72a030;1240 at 0x6021e8


#### 5.程序说明

定位new运算符确实将数组pd2放在了数组buffer中，pd2和buffer的地址都是0x6021a0。然而，它们的类型不同，pd2是double指针，而buffer是char指针。（顺便说一句，这也是程序使用(void $\ast$)对buffer进行强制转换的原型，如果不这样做，cout将显示一个字符串。）。同时，常规new将数组pd1放在很远的地方，其地址为0x72a010，位于动态管理的堆中。

需要指出的第二点是，第二个常规new运算符查找一个新的内存块，其起始地址为0x72a040；但第二个定位new运算符分配与以前相同的内存块；起始地址为00FD9138的内存块。定位new运算符使用传递给它的地址，它不跟踪哪些内存块已被使用，也不查找未使用的内存块。这将一些内存管理的负担交给了程序员。例如，在第三次调用定位new运算符时，提供了一个从数组buffer开头算起的偏移量，因此将分配性的内存：

	pd2 = new (buffer + N * sizeof(double)) double[N];

第三点差别是，是否使用delete来释放内存。对于常规new运算符，下面的语句释放起始地址为0x72a010的内存块，因此接下来再次调用new运算符时，该内存块是可用的：

	delete [] pd1;

然而，程序中没有使用delete来释放定位new运算符分配的内存。事实上，在这个例子不能这样做。buffer指定的内存是静态内存，而delete只能用于这样的指针：指向常规new运算符分配的堆内存。即，数组buffer位于delete的管辖区之外，下面的语句将引发运行阶段错误：

	delete [] pd2;

**另一方面，如果buffer是使用常规new运算符创建的，便可以使用常规delete运算符来释放整个内存块。**

定位new运算符的另一种用法，将其与初始化结合使用，从而将信息放在特定的硬件地址处。

定位new运算符的工作原理：基本上，它只是返回传递给它的地址，并将其强制转换为void $\ast$，以便能够赋给任何指针类型。但这说的是默认定位new函数，C++允许程序员重载定位new函数。

将定位new运算符用于类对象时，情况更复杂，将在第12章介绍。

#### 6.定位new的其他形式

将向常规new调用一个接受一个参数的new()函数一样，标准定位new()调用一个接受两个参数的new()函数：

	int * pi = new int;				//调用new(sizeof(int))
	int * p1 = new(buffer) int;     //调用new(sizeof(int), buffer)
	int * p2 = new(buffer) int[40]; //调用new(40*sizeof(int), buffer)


定位new函数不可替换，但可重载。它至少需要接受两个参数，其中第一个指向std::size_t，指出了请求的字节数。这样的重载函数都被称为**定义new**，即使额外的参数没有指定位置。

##  9.3 名称空间

在C++中，名称可以时变量、函数、结构、枚举、类以及类和结构的成员。当随着项目的增大，名称空间的冲突可能也将增加。使用多个厂商的类库时，可能导致名称冲突。例如，两个库可能都定义了名为List、Tree和Node的类，但定义的方式不兼容。用户可能希望使用一个库的List类，而使用另一个库的Tree类。这种冲突被称为名称空间问题。

C++提供了名称空间工具，以便更好地控制名称的作用域。

### 9.3.1 传统的C++名称空间

首先介绍一些术语：

- 声明区域：是可以在其中进行声明的区域。例如，可以在函数外面声明全部变量，对于这种变量，其声明区域为其声明所在文件。对于在函数中声明的变量，其声明区域为其声明所在代码块。
- 潜在作用域： 变量的潜在作用域从声明点开始，到其声明区域的结尾。因此潜在作用域比声明区域小，这是由于变量必须定义后才能使用。


然而，变量并非在其潜在作用域内的任何位置都是可见的。例如，它可能被另一个在嵌套声明区域中声明的同名变量隐藏。例如，在函数中声明的局部变量（对于这种变量，声明区域为整个函数）将隐藏在同一个文件中声明的全局变量（对于这种变量，声明区域为整个文件）。变量对程序而言可见的范围被称为**作用域**。

C++关于全局变量和局部变量的规则定义了一种名称空间层次。每个声明区域都可以声明名称，这些名称独立于在其他声明区域声明e名称。在一个函数中声明的局部变量不会与在另一个函数中声明的局部变量发送冲突。


### 9.3.2 新的名称空间特性

C++新增了这样一种功能，即通过定义一种新的声明区域来创建命名的名称空间，这样做的目的之一是提供一个声明名称的区域。一个名称空间中的名称不会与另一个名称空间的相同名称冲突，同时允许程序的其他使用该名称空间中声明的东西。例如，下面的代码使用新的关键字namespace创建了两个名称空间：

	namespace Jack{
		double pail;
		void fetch();
		int pal;
		struct Well{...};
	}
	namespace Jill{
		double bucket(double n) {...}
		double fetch;
		int pal;
		struct Hill{...};
	}

名称空间可以是全局的，也可以位于另一个名称空间中，但不能位于代码块中。因此，默认情况下，在名称空间中声明的名称的链接性为外部（除非它引用了常量）。

除了用户定义的名称空间外，还存在另一个名称空间--全局名称空间。它对应于文件级声明区域，因此前面所说的全局变量被描述为位于全局名称空间中。

任何名称空间中的名称都不会于其他名称空间中的名称发送冲突。因此，Jack中的fetch可以于Jill中的fetch共存，Jill中的Hill可以于外部Hill共存。名称空间中的声明和定义规则同全局声明和定义规则相同。

名称空间是开放的，即可以把名称加入到已有的名称空间中。例如，下面这条语句将名称goose添加到Jill中已有的名称列表中：

	namespace Jill{
		char * goose (const char *);
	}

同样，原来的Jack名称空间为fetch()函数提供了原型。可以在该文件后面再次使用Jack名称空间来提供函数的代码：

	namespace Jack{
		void fetch()
		{...}
	}

当然，需要有一种办法来访问给定名称空间中的名称。最简单的方法是，通过作用域解析运算符::，使用名称空间来限定该名称：

	Jack::pail = 12.34;
	Jill::Hill mole;
	Jack::fetch();

未被装饰的名称（如pail）称为未限定的名称；包含名称空间的名称（如Jack::pail）称为限定的名称。


#### 1.using声明和using编译指令

C++提供了两种机制（using声明和using编译指令）来简化对名称空间中名称的使用。using声明使特定的标识符可用，using编译指令使整个名称空间可用。

using声明由被限定的名称和它前面的关键字uing组成：

	uing Jill::fetch;//using声明

using声明将特定的名称添加到它所属的声明区域中。例如main()中的using声明Jill::fetch将fetch添加到main()定义的声明区域中。完成该声明后，便可以使用名称fetch代替Jill::fetch。下面的代码段说明了这几点：

	namespace Jill{
		double bucket (double n){...}
		double fetch;
		struct Hill{...};
	}
	char fetch;
	int main()
	{
		using Jill::fetch;
		double	fetch;  //非法的
		cin >> fetch;   //读取一个值到Jill::fetch
		cin >> ::fetch; //读取一个值到全局fetch
	}

由于using声明将名称添加到局部声明区域中，因此这个示例避免了将另一个局部变量也命名未fetch。另外，和其他局部变量一样，fetch也将覆盖同名的全局变量。

在函数的外面使用using声明时，将把名称添加到全局名称空间中：

	void other();
	namespace Jill{
		double bucket (double n){...}
		double fetch;
		struct Hill{...};
	}
	using Jill::fetch;
	int main()
	{
		cin >> fetch;
		other();
	}
	void other()
	{
		cout << fetch;
	}

**using声明使一个名称可用，而using编译指令使所有的名称都可用**。using编译指令由名称空间和它前面的关键字using namespace组成，它使名称空间中的所有名称都可用，而不需要使用作用域解析运算符：

	using namespace Jack;

在全局声明区域中使用using编译指令，将使该名称空间的名称全局可用。这种情况出现多次：

	#include <iostream>
	using namespace std;

在函数中使用using编译指令。将使其中的名称在该函数中可用，如下：

	int main()
	{
		using namespace Jack;
		...
	}

using编译指令和using声明，它们增加了名称冲突的可能性。即，如果名称空间jack和jill，并在代码中使用作用域解析运算符，则不会存在二义性：

	jack::pal = 3;
	jill::pal = 10;

变量jack::pal和jill::pal是不同的标识符，表示不同的内存单元。然而，如果使用using声明，情况将发送变法：

	using jack::pal;
	using jill::pal;
	pal = 3;

事实上，编译器不允许同时使用上述两个using声明，因为这将导致二义性。

#### 2.using编译指令和using声明之笔记

使用using编译指令导入一个名称空间中所有的名称，像大量使用作用域解析运算符。而使用using声明时，像声明了相应的名称一样。如果某个名称在函数中声明了，则不能使用using声明导入相同的名称。然而，使用using编译指令，将进行名称解析，就像在包含using声明和名称空间本身最小声明区域中声明了名称一样。下面的示例，名称空间未全局的。如果使用using编译指令导入一个已经在函数中声明的名称，则局部名称将隐藏名称空间名，就像隐藏同名的全局变量一样。不过仍可像下面的示例中那样使用作用域解析运算符：

	namespace Jill{
		double bucket(double n){...}
		double fetch;
		struct Hill{...};
	}
	char fetch;
	int main()
	{
		using namespace Jill;
		Hill Thrill;
		double water = bucket(2);
		double fetch;         //创建一个局部变量fetch，隐藏Jill::fetch和全局fetch
		cin >> fetch;
		cin >> ::fetch;
		cin >> Jill::fetch;
	}

	int foom()
	{
		Hill top;         //错误的
		Jill::Hill crest; //合理的
	}

在main()中，名称Jill::fetch被放在局部名称空间中，但其作用域不是局部，因此不会覆盖全局的fetch。然而，局部声明的fetch将隐藏Jill::fetch和全局fetch。然而，如果使用作用域解析运算符，则后两个fetch变量都是可用的。

需要指出的另一点是，虽然函数中的using编译指令将名称空间的名称视为在函数之外声明的，但它不会使得该文件中的其他函数能够使用这些名称。因此，在前一个例子中，foom()函数不能使用未限定的表示符Hill。

注意：假设名称空间和声明区域定义了相同的名称如果试图使用using声明将名称空间的名称导入该声明区域，则这两个名称回发送冲突，从而出错。如果使用using编译指令将该名称空间的名称导入该声明区域，则局部版本将隐藏名称空间的版本。

一般来说，使用using声明比使用using编译指令更安全，这是由于它只是导入指定的名称。如果该名称与局部名称发送冲突，编译器将指出来。using编译指令导入所有名称，包括可能并不需要的名称。如果与局部名称发送冲突，则局部名称将覆盖名称空间版本，而编译器并不会发出警告。另外，名称空间的开放性意味着名称空间的名称可能分散在多个地方，这使得难以准确直到添加了哪些名称。

本书的大部分示例采用的方法：

	#include <iostream>
	int main()
	{
		using namespace std;
	
首先，#include 语句将头文件iostream放到名称空间std中；然后，using编译指令使该名称空间在main()函数中可用。有些示例采取下述方法：

	#include <iostream>
	using namespace std;

这将名称空间std中的所有内容导出到全局名称空间中。使用这种方法的主要原因是方便。它易于完成，同时如果系统不支持名称空间，可以将前两行替换成：

	#incluce <iostream.h>

然而，名称空间的支持者希望有更多的选择，既可以使用解析运算符，也可以使用using声明，即，不要这样做：

	using namespace std;

而这样做：

	int x;
	std::cin >> x;
	std::cout << x << std::endl;

或者这样做：

	using std::cin;
	using std::cout;
	using std::endl;
	int x;
	cin >> x;
	cout << x;

可以用嵌套名称空间来创建一个包含常用using声明的名称空间（下一节介绍）。



#### 3.名称空间的其他特性

可以将名称空间声明进行嵌套：

	namespace elements
	{
		namespace fire{
			int flame;
			...
		}
		float water;
	}

这里，flame指的是elements::fire::flame。同样，可以使用下面的usin编译指令使内部的名称可用：

	using namespace elements::fire;

另外，也可以在名称空间中使用using编译指令和using声明，如下：

	namespace myth{
		using Jill::fetch;
		using namespace elements;
		using std::cout;
	}

假设要访问Jill::fetch。由于Jill::fetch现在位于名称空间myth中，因此可以如下访问：

	std::cin >> myth::fetch;

当然，由于它也位于Jill名称空间中，仍可以称作Jill::fetch

	std::cout << Jill::fetch;

如果没有与之冲突的局部变量，则也可以这样做：

	using namespace myth;
	cin >> fetch;


using编译指令是可传递的（A>B,B>C -> A>C）。在这种情况下，下面的语句将导入名称空间myth和elements：

	using namespace myth;

这条编译指令与下面两条编译指令等价：

	using namespace myth;
	using namespace elements;

可以名称空间创建别名，例如，假设有下面的名称空间：

	namespace my_very_favorite_things {...}

则可以使用下面的语句让mvft成为my_very_favorite_things别名：

	namespace mvft = my_very_favorite_things;

可以使用这种技术来简化对嵌套名称空间的使用：

	namespace MEF = myth::elements::fire;
	using MEF::flame;

#### 4.未命名的名称空间

可以通过声明名称空间的名称来创建未命名的名称空间：

	namespace{
		int ice;
		int bandycoot;
	}

这就像后面跟着using编译指令一样，即，在该名称空间中声明的名称的潜在作用域为：从声明点到该声明区域的末尾。从这个方面看，它们与全局变量相似。然而，由于这种名称空间没有名称，因此不能显式地使用using编译指令和using声明来使用它在其他位置都可用。具体地说，不能在未命名空间所属文件之外的其他文件中，使用该名称空间中的名称。这提供了链接性为内部的静态变量的替代品。例如：

	static int counts;
	int others();
	int main(){}
	int others(){}

采用名称空间的方法如下：

	namespace {int counts;}
	int others();
	int main(){}
	int others(){}



### 9.3.3 名称空间示例

该程序的第一个文件是头文件，其中包含头文件中常包含的内容：常量、结构定义和函数原型。在这个例子中，这些内容放在两个名称空间中。第一个名称空间叫做pers，其中包括Person结构的定义和两个函数原型---一个用来用人民填充结构，另一个函数显式结构的内容；第二个名称空间叫做debts，定义了一个结构，该结构用来存储人名和金额。该结构使用Person结构，因此debts名称空间使用一条using编译指令，让pers中的名称在debts名称空间可用。


namesp.h

	#include <iostream>
	namespace pers
	{
		struct Person
		{
			std::string fname;
			std::string lname;
		};
		void getPerson(Person &);
		void showPerson(const Person &);
	}
	
	namespace debts
	{
		using namespace pers;
		struct Debt
		{
			Person name;
			double amount;
		};
		void getDebt(Debt &);
		void showDebt(const Debt &);
		double sumDebts(const Debt ar[], int n);
	}

第二个文件是源代码文件，它提供了头文件中的函数原型对应的定义。在名称空间中声明的函数名的作用与为整个名称空间，因此定义和声明必须位于同一名称空间中。这正是名称空间的开放性发挥的地方。通过包含namesp.h导入原来的名称空间。然后，该文件将函数定义添加入到两个名称空间中。

namesp.cpp

	#include <iostream>
	#include "namesp.h"
	
	namespace pers
	{
		using std::cout;
		using std::cin;
		void getPerson(Person & rp)
		{
			cout << "Enter first name: ";
			cin >> rp.fname;
			cout << "Enter last name: ";
			cin >> rp.lname;
		}
		void showPerson(const Person & rp)
		{
			std::cout << rp.lname <<  ", " << rp.fname;
		}
	}
	
	namespace debts
	{
		void getDebt(Debt & rd)
		{
			getPerson(rd.name);
			std::cout << "Enter debt: ";
			std::cin >> rd.amount;
		}
		void showDebt(const Debt & rd)
		{
			showPerson(rd.name);
			std::cout << ": $" << rd.amount << std::endl;
		}
		double sumDebts(const Debt ar[], int n)
		{
			double total = 0;
			for (int i = 0; i < n; i++)
				total += ar[i].amount;
			return total;
		}
	}

第三个文件是一个源代码文件，它使用了名称空间中声明和定义的结构和函数。

namessp.cpp

	#include <iostream>
	#include  "namesp.h"
	void other();
	void another();
	int main()
	{
		using debts::Debt;
		using debts::showDebt;
		Debt golf = {{"Benny", "goatsniff"}, 120.0};
		showDebt(golf);
		other();
		another();
		return 0;
	}
	
	void other()
	{
		using std::cout;
		using std::cin;
		using std::endl;
		using namespace debts;
		Person dg = {"Doobles", "Glister"};
		showPerson(dg);
		cout << endl;
		Debt zippy[3];
		int i;
		for (i = 0; i < 3; i++)
			getDebt(zippy[i]);
		for (i = 0; i < 3; i++)
			showDebt(zippy[i]);
		cout << "Total debt: $" << sumDebts(zippy, 3) << endl;
	}
	
	void another()
	{
		using pers::Person;
		Person collector = {"Milo", "Rightshift"};
		pers::showPerson(collector);
		std::cout << std::endl;
	}


结果

	goatsniff, Benny: $120
	Glister, Doobles
	Enter first name: Z
	Enter last name: xp
	Enter debt: 1000
	Enter first name: W
	Enter last name: xp
	Enter debt: 2000
	Enter first name: Y
	Enter last name: xp
	Enter debt: 3000
	xp, Z: $1000
	xp, W: $2000
	xp, Y: $3000
	Total debt: $6000
	Rightshift, Milo


在第三个文件中，main()函数首先使用了两个using声明：

	using debts::Debt;
	using debts::showDebt;

注意，using声明只是用了名称，例如，第二个using声明没有描述showDebt的返回类型或函数特征标，而只是给出名称；因此，**如果函数重载，则一个using声明将导入所有的版本**。另外，虽然Debt和showDebt都使用了Person类型，但不必导入任何Person名称，因为debt名称空间有一条包含pers名称空间的using编译指令。

接下来，other()函数采用了一种不太好的方法，即使用一条using编译指令导入整个名称空间：

	using namespace debts;

由于debts中的using编译指令导入了pers名称空间，因此other()函数可用使用Person类型和showPerson()函数。

最后，another()函数使用using声明和作用域解析运算符来访问具体名称：

	using pers::Person;
	pers::showPerson(collector);


### 9.3.4 名称空间及其前途

指导原则：

- 使用在已命名的名称空间中声明的变量，而不是使用外部全局变量。
- 使用在已命名的名称空间中声明的变量，而不是使用静态全局变量。
- 如果开发了一个函数库或类库，，将其放在一个名称空间中。
- 仅将编译指令using作为一种将旧代码转换为使用名称看似吗的权宜之计。
- 不要在腾文件中使用using编译指令。首先，这样做掩盖了要让哪些名称可用；另外，包含头文件的顺序可能影响程序行为。如果非要使用编译指令using，应将其放在所有预处理编译指令#include之后。
- 导入名称时，首先十三亿作用域解析运算符或using声明的方法。
- 对于using声明，首先将其作用域设置为局部而不是全局。

使用名称空间的主旨时简化大型编程项目的管理工作。对于只有一个文件的简单程序，使用using编译指令是没影响。

老式头文件（如iostream.h）没有使用名称空间，但心头文件（如iostream）使用了std名称空间。

## 9.4 总结

头文件

存储方案：作用域、持续性、链接性

名称空间： using声明，usign编译指令，作用域解析运算符





