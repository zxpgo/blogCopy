---

title:  C++ Primer Plus--对象和类（十）
date: 2018-10-25 19:08:14
tags: C++
categories: "C++ Primer Plus学习笔记"
mathjax: true

---

面向对象编程（OOP）<!--more-->

## 10.1 过程性编程和面向对象编程

采用过程性编程时，首先考虑要遵循的步骤，然后考虑如何表示这些数据（并不需要程序一直运行，用户可能希望能够将数据存储在一个文件中，然后从这个文件中读取数据）。

对于OOP程序员，首先考虑数据--不仅考虑如何表示数据，还要考虑如何使用数据。总之，采用OOP方法时，首先从用户的角度考虑对象---描述对象所需的数据以及描述用户与数据交互所需的操作。完成对接口的描述后，需要确定如何实现接口和数据存储。最后，使用新的设计方案创建出程序。

## 10.2 抽象和类

生活中充满复杂性，处理复杂性的方法之一是简化和抽象。将问题的本质特征提取出来，并根据特征来描述解决方案。

### 10.2.1 类型

指定基本类型完成三项工作：

- 决定数据对象需要的内存数据；
- 决定如何解释内存中的位（long和float在内存中占用的位数相同，但将它们转换为数值的方法不同）；
- 决定可使用数据对象执行的操作和方法。

对于内置类型，有关操作的信息被内置到编译器中。但在C++中定义用户自定义的类型时，必须自己提供这些信息。


### 10.2.2 C++类

类是一种将抽象转换为用户定义类型的C++工具，它将数据表示和操纵数据的方法组合成一个整洁的包。下面来看一个表示股票的类：

首先，必须考虑如何表示股票。可以将一股作为一个基本单元，定义一个表示一股股票的类。然而，这意味着需要100个对象才能表示100股，这不现实。相反，eyi将某人当前持有的某种股票作为一个基本单元，数据表示中包含它持有的股票数据。一种比较现实的方法是，必须记录最初够买价格和购买价格等内容。另外，还必须管理诸如拆股等事件。首先定义就考虑这么多因素有些苦难，因此我们对其进行简化。具体地说，应该将可执行的操作限制为：

- 获得股票
- 增持
- 卖出股票
- 更新股票价格
- 现实关于所持股票的信息

可以根据上述清单定义stock类型的公有接口。为支持该接口，需要存储一些信息。再次进行简化，例如，不考虑标准的美式股票计价方式。我们将存储下面的信息：

- 公司名称
- 所持股票数量
- 每股价格
- 股票总值

接下来定义类，一般来说，类规范由两个部分组成：

- 类声明：以数据成员的方式描述数据部分，以成员函数的方式描述公有接口。
- 类方法定义：描述如何实现类成员函数。

**接口**

接口是一个共享框架，供两个系统交互时使用；例如，用户可能是您，而程序可能是字处理器。使用字处理器时，您不能直接将脑子中想到的词传输到计算机内存中，而必须同程序提供的交互接口。您敲打键盘时，计算机将字符串显示到屏幕；您移动鼠标时，计算机移动屏幕上的光标；您无意间单击鼠标时，计算机对您输入的段落进行奇怪的处理。程序接口将您的意图转换为存储在计算机中的具体信息。

对于类，我们说的公共接口。在这里，公众是使用类的程序，交互系统由类对象组成，而接口由编写类的人提供的方法组成的。接口让程序员能够编写与类交互的代码，从而让程序能够使用类对象。例如，要计算sting对象中包含多少个字符，您无需打开对象，只需要使用sting类提供的size()方法。类设计禁止公共用户直接访问类，但公众可以使用方法size()。方法size()是用户和string类对象之间的公共接口的组成部分。通常，方法getline()是对象istream类的公共接口的组成部分，使用cin的程序不是直接与cin对象内部交互来读取一行输入，而是使用getline()。

为开发一个类并编写一个使用它的程序，需要完成多个步骤。这里将开发过程分为多个阶段，而不是一次性完成。通过，C++程序员将接口（类定义）放在头文件中，并将实现（类方法的代码）放在源代码文件中。第一个文件，它是Stock类的类声明。

**类名首字母大写**。

stock00.h

	#ifndef STOCK00_H_
	#define STOCK00_H_
	
	#include <string>
	class Stock
	{
		private:
			std::string company;
			long shares;
			double share_val;
			double total_val;
			void set_tot() { total_val = share_val * shares;}
		public:
			void acquire(const std::string & co, long n, double pr);
			void buy(long num, double price);
			void sell(long num, double price);
			void update(double price);
			void show();
	};
	
	#endif



先看一下类的通用特性。首先，C++关键字class指出这些代码定义了一个类设计（不同于模板参数中，在这里，关键字class和typename不是同意词，不能使用typename代替class）。这种语法指出，Stock是这个新类的类型名。该声明让我们能够声明Stock类型的变量--称为对象或实例。每个对象都表示一支股票。例如，下面的声明创建两个Stock对象，它们名称分别为sally和solly:

	Stock sally;
	Stock solly;

例如，sally对象可以表示Sally持有的某公司股票。

接下来，要存储的数据以类数据成员（如company和shares）的形式出现。例如，sally的company成员存储了公司的名称，share成员存储了sally持有的股票数量，...。同样，要执行的操作以类函数成员（方法，如sell()或update()）的形式出现。成员函数可以就地定义（如set_tot()），也可以用原型表示。将数据和方法组合成一个单元是类最吸引人的特性。有了这种设计，创建Stock对象时，将自动制定使用对象规则。

#### 1.访问控制

关键字private和public，描述了对类成员的访问控制。使用类对象的程序都可以直接访问公有部分，但只能通过公有成员函数（或友员函数，参见第11章）来访问对象的私有成员。例如，要修改Stock类的shares成员，只能通过Stock的成员函数。因此，公有成员函数是程序和对象的私有成员之间的桥梁，提供了对象和程序之间的接口。防止程序直接访问数据被称为数据隐藏。C++还提供了第三个访问控制关键字protected（第13章介绍）。

类设计尽可能将公有接口与实现细节分开。公有接口表示设计的抽象组件。将实现细节放在一起并将它们与抽象分开被称为封装。数据隐藏是一种封装，将实现的细节隐藏在私有部分中，就像Stock类对set_tot()所做的那样，也是一种封装。封装的另一个例子是，将函数定义和类声明放在不同的文件中。

数据隐藏不仅可以防止直接访问数据，还可以让开发者无需了解数据是如何标识的。例如，show()成员将显式某支股票的总价格（还有其他内容），这个值可以存储在对象中，也可以在需要时通过计算得到。从使用类的角度看，使用哪种方法没有声明区别。所需要知道的只是各种成员函数的功能；即，需要知道成员函数接受什么样的参数以及返回生命类型的值。原则是将实现细节从接口设计中分离出来。如果以后找到了更好的、实现数据表示或成员函数细节的方法，可以对这些细节进行修改，而无需修改程序接口，这使得程序维护起来更容易。


#### 2.控制对成员的访问：公有还是私有

无论类成员是数据成员还是函数成员，都可以在类的公有部分或私有部分中声明它。但由于隐藏数据是OOP主要的目标之一，因此数据项通常放在私有部分，组成类接口的成员函数放在公有部分；否则就无法从从程序中调用这些函数。正如Stock声明所表明的，也可以把成员函数放在私有部分中。不能直接从程序中调用这种函数，但公有方法却可以访问使用它们。通常，程序使用私有成员函数来处理不属于公有接口的实现细节。

**不必在类声明中使用关键字private，因为这是类对象的默认访问控制**:

	class World
	{
		float mass;
		char name[20];
	public: 
		void tellall(void);
	}

然而，为强调数据隐藏的概念，本书显式地使用了private。

**类和结构**

类描述看上去很像是包含成员函数以及public和private可见性标签的结构声明。实际上，C++对结构进行了扩展，使之具有与类相同的特性。它们之间的唯一区别是，结构的默认访问类型是public，而类为private。C++程序员通常使用类实现类描述，而把结构限制为只表示纯粹的数据对象。

### 10.2.3 实现类成员函数

还需要创建类描述的第二部分：为那些由类声明中的原型表示的成员函数提供代码。成员函数定义与常规函数定义非常相似，它们由函数头和函数体，也可以返回类型和参数。但它们两个特殊的特征：

- 定义成员函数，使用作用域解析运算符(::)来标识函数所属的类；
- 类方法可以访问类的private组件。

首先，成员函数的函数头使用作用域运算符解析（::）来指出函数所属的类。例如，update()成员函数的函数头如下：

	void Stock::update(double prive)

这种表示法意味着我们定义的update()函数是stock类的成员。这不仅将update()标识为成员函数，意味着我们可以将另一个类的成员函数也命名为update()。例如，Buffon类的update()函数的函数头如下：

	void Buffon::update()

因此，作用域解析运算符确定了方法定义对应的类的身份。Stock类的其他成员函数不必使用作用域解析运算符，就可以使用update()方法，这是因为它们属于同一个类，因此update()是可见的。然而，在类声明和方法定义职位使用update()时，需要采用特殊的措施。

类方法的完整名称中包括类名。即，Stock::update()是函数的限定名；而简单的update()是全名的缩写，它只能在类作用域中使用。

方法的第二个特点是，方法可以访问类的私有成员，例如，Show()方法可以使用这样的代码：


	std::cout << "Company: " << compangy
			  << " Shares: " << shares << endl;


其中，company和shares都是Stock类的私有数据成员。如果试图使用非成员函数访问这些数据成员，编译器禁止这样做。


stock00.cpp
	
	#include <iostream>
	#include "stock00.h"
	
	void Stock::acquire(const std::string & co, long n, double pr)
	{
		company = co;
		if (n < 0)
		{
			std::cout << "Number of shares can't be nagative: "
				  << company << " share set to 0.\n";
			shares = 0;
		}
		else
			shares = n;
		share_val = pr;
		set_tot();
	}
	
	void Stock::buy(long num, double price)
	{
		if (num < 0)
		{
			std::cout << "Number of shares purchased can't be nagative. "
				  << "Transaction is aborted.\n";
		}
		else
		{
			shares += num;
			share_val = price;
			set_tot();
		}
	}
	
	void Stock::sell(long num, double price)
	{
		using std::cout;
		if (num < 0)
		{
			cout << "Number of shares sold can't be nagative. "
				<< "Transaction is aborted.\n";
		}
		else if (num > shares)
		{
			cout << "You can't sell more than you have!"
				<< " Transantion is aborted.\n";
		}
		else
		{
			shares -= num;
			share_val = price;
			set_tot();
		}
	}
	
	void Stock::update(double price)
	{
		share_val = price;
		set_tot();
	}
	
	void Stock::show()
	{
		std::cout << "Company: " << company
			<< " Shares: " << shares << std::endl
			<< " Share Price: $" << share_val
			<< " Total Worth: $" << total_val << std::endl;
	}

#### 1.成员函数说明

acquire()确保管理对某个公司购买的首次购买，而buy()和sell()管理增加或减少持有的股票。方法buy()和sell()确保买入或卖出的股票不能为负数。另外，如果用户试图卖出超过他所拥有的股票数量，则sell()函数结束这次交易。这些使数据私有并限于对公有函数的计数允许我们能够控制数据如何被使用。

set_tot()只是实现代码的一种方式，而不是公有接口的组成部分，因为这个类将其声明为私有成员函数。

#### 2. 内联方法

**其定义位于类声明中的函数都将自动为内联函数**，因此Stock::set_tot()是一个内联函数。类声明常将短小的成员函数作为内联函数。

如果愿意，也可以类声明以外定义成员函数，并使其成员内联函数。只需要在定义函数时使用inline限定符，如下：

	inline void Stock::set_tot()
	{
		total_val = shares * share_val;
	}

内联函数的特殊规则要求在每个使用它们的文件中都对其进行定义。确保内联定义对多个文件程序中的所有文件都可用，最简单的方法是：将内联定义放在定义类的头文件。

#### 3. 方法使用哪个对象

下面介绍使用对象时最重要的一个方面；比如将类方法应用于对象。下面代码使用了一个对象的shares成员：

	shares += num;

则问题是使用哪个对象的shares成员。首先，看如何创建对象：

	Stock kate, joe;

接下来，看看如何使用对象的成员函数：

	kate.show();
	joe.show();

第一条语句调用kate对象show()成员。这意味着show()方法把shares解释为kate.shares，将share_val解释为kate.share_val。

所创建的每个新对象都有自己的存储空间，用于存储其内部变量和成员；但同一个类的所有对象共享同一组类方法，即每种方法只有一个副本。例如，假设kate和joe都是Stock对象，则kate.shares将占据一个内存块，而joe.shares占用另一个内存块，但kate.show()和joe.show()都调用同一个方法。

### 10.2.4

现在，创建一个程序，他创建并使用对象。C++的目标是使用使得类和使用基本的内置类型尽可能相同。要创建类对象，可以声明类变量，也可以使用new为类对象分配存储值。可以对象作为参数和返回值，也可以将一个对象赋给另一个对象。

usestock00.cpp

	#include <iostream>
	#include "stock00.h"
	int main()
	{
		Stock fluffy;
		fluffy.acquire("NanoSmart", 20, 12.50);
		fluffy.show();
		fluffy.buy(15, 19.125);
		fluffy.show();
		fluffy.sell(400, 20.00);
		fluffy.show();
		fluffy.buy(300000, 40.125);
		fluffy.show();
		fluffy.sell(300000, 0.125);
		fluffy.show();
		return 0;
	}


结果：

	Company: NanoSmart Shares: 20
	 Share Price: $12.5 Total Worth: $250
	Company: NanoSmart Shares: 35
	 Share Price: $19.125 Total Worth: $669.375
	You can't sell more than you have! Transantion is aborted.
	Company: NanoSmart Shares: 35
	 Share Price: $19.125 Total Worth: $669.375
	Company: NanoSmart Shares: 300035
	 Share Price: $40.125 Total Worth: $1.20389e+07
	Company: NanoSmart Shares: 35
	 Share Price: $0.125 Total Worth: $4.375

**客户/服务器模式**

OOP程序常依照客户/服务器模式来讨论程序设计。客户是使用类的程序，类声明（包括类方法）构成了服务器，它是程序可以使用的资源。客户只能通过以公有方式定义的接口使用服务器，这意味着客户唯一责任是了解该接口。


### 10.2.5 修改实现

在前面的程序输出中，数字的格式不一致。现在可以改进实现，但保持接口不变。ostream类包含一些可用于控制格式的成员函数。如下：

	std::cout.setf(std::ios_base::fixed, std::ios_base::floatfiled);

这设置了cout对象的一个标记，命令cout使用定点表示法。同样，下面的语句导致cout在使用定点表示法，显式3位小数：

	std::cout.precision(3);

第17章介绍更多细节。

可在方法show()中使用这些工具来控制格式，但是还有一点需要考虑。修改方法的实现时，不应影响客户程序的其他部分。上述格式修改将一直有效，因为它们可能影响客户程序中的后续输出。因此，show()应重置格式信息，使其恢复到自己被调用前的状态。为此，可以像乳腺程序，使用返回的值：

	std::streamsize prec = std::cout.precision(3);//保存修改之前的值
	...
	std::cout.precision(prec); //重置为旧值

	std::ios_base::fmtflags orig = std::cout.setf(std::ios_base);
	...
	std::cout.setf(orig, std::ios_base::floatfield);

您可能还记得，fmtflags是在ios_base类中定义的一种类型，而ios_base类又是在名称空间std中定义的，因此orig的类型名非常长。其次，orig存储了所有的标记，而重置语句使用这些信息来重置floatfield，而floatfield包含定点表示法标记和科学表示法标记。第三，请不要过多考虑这里的细节。这里的要旨是，将修改限定在实现文件中，以免影响程序的其他方面。

根据上面的介绍，可在实现文件中将方法show()的定义修改如下所示：

	void Stock::show()
	{
		using std::cout;
		using std::ios_base;
		ios_base::fmtflags orig = cout.setf(ios_base::fixed, ios_base::floatfield);
		std::streamsize prec = cout.precision(3);


		std::cout << "Company: " << company
			<< " Shares: " << shares << std::endl
			<< " Share Price: $" << share_val
			
		//set format to #.##
		cout.precision(2);
		cout  << " Total Worth: $" << total_val << std::endl;

		cout.setf(orig, ios_base::floatfield);
		cout.precision(prec);
	}

完成上述修改后（保留头文件和客户文件不变），可重新编译该程序。



### 10.2.6 小结

指定类设计的第一步是提供类的声明。类声明类似结构的声明，可以包括数据成员和函数成员。声明有私有部分，在其中声明的成员只能通过成员函数进行访问；声明还具有公有部分，在其中声明的部分中，因此典型的声明的格式如下：


	class className
	{
	private:
		data number declarations;
	public:
		member function prototypes;
	};

公有部分的内容构成了设计的抽象部分---公有接口。将数据封装到私有部分中可以保护数据的完整性，这被称为数据隐藏。因此，C++通过类使得实现抽象、数据隐藏和封装等OOP特性很容易。

指定类设计的第二步是实现类成员函数。可以在类声明中提供完整的函数定义，而不是函数原型，但是通常的做法是单独提供函数定义（除非函数很小）。在这种情况下，需要使用作用域解析运算符来指出成员函数属于哪个类。例如，假设Bozo有一个名为Retort()的成员函数，该函数返回char指针，则其函数头如下所示：

	char * Bozo::retort();

换句话来说，Retort()不仅仅是一个char $\ast$类型的函数，而是一个属于Bozo类的Char $\ast$函数。该函数的全名（或限定名）为Bozo::Retort()。而名称Retort()是限定名的缩写，只能在某些特定的环境中使用。如类方法的代码中。


另一种描述这种情况的方式是，名称Retort的作用域为整个类，因此在类声明和类方法之外使用该名称时，需要使用作用域解析运算符进行限定。

要创建对象（类的实例），只需将类名视为类型名即可：

	Bozo bozetta;

这样做是可行的，因为类是用户定义的类型。

类成员函数（方法）可通过类对象来调用。为此，需要使用成员运算符句点：

	cout << bozetta.Retort();

这将调用Retort()函数，每当其中的代码引用某个数据成员时，该函数都将使用bozetta对象中相应成员的值。



## 10.3 类的构造函数和析构函数

对于Stock类，还有其他一些工作要做。应为类提供称为构造函数和析构函数的标准函数。下面介绍为什么需要这些函数以及如何使用这些函数。

C++的目标之一是让类对象就像使用标准类型一样，然而，到现在为止，本章提供的代码还不能让您初始化int或结构那样来初始化Stock对象。即，常规的初始化语法不适用于类型Stock:

	int year = 2018;
	Struct thing	
	{
		char * pn;
		int m;
	};
	thing amabob = {"wodget", -23};
	Stock hot = {"Sukie's Autos, Inc.", 200, 50.25} //编译错误

不能像上面这样初始化Stock对象的原因在于，数据部分的访问状态**私有的**，这意味着程序不能直接访问数据成员。您已经看到，程序只能通过成员函数来访问数据成员，因此需要设计合适的成员函数，才能成功地将对象初始化（如果使数据成员成为公有，而不是私有，就可以按刚才介绍的方法初始化类对象，但使数据成为公有的违背了类的一个主要初衷：数据隐藏）。

一般来说，最好是在创建对象时对它进行初始化。例如，请看下面的代码：

	Stock gift;
	gift.buy(10, 24.75);

就Stock类当前的实现而言，gift对象的company成员是没有值的。类设计假设用户在调用任何其他成员函数之前调用acquire(),但无法强加这种假设。避开这种问题的方法之一是在创建对象时，自动对它进行初始化。为此，C++提供了特殊的成员函数---类构造函数，专门构造新对象、将值赋给它们的数据成员。更准确地说，C++为这些成员函数提供了名称和使用语法，而程序员需要提供方法定义。名称与类名相同。例如，Stock类一个可能的构造函数是名为Stock()的成员函数。构造函数的原型和函数头有一个有趣的特征----虽然没有返回值，但没有被声明为void类型。实际上，构造函数没有声明类型。

### 10.3.1 声明和定义构造函数

现在需要创建Stock的构造函数。由于需要Stock对象提供3个值，因此应为构造函数提供3个参数。（第4个值，total_val成员，是根据shares和share_val计算得到的，因此不必为构造函数提供这个值。）程序员可能只想设置company成员，而将其他值设置为0；这可以使用默认参数来完整。因此原型如下所示：

	Stock(const string & co, long n = 0; double pr = 0.0);

第一个参数是指向字符串的指针，该字符串用户初始化成员company。n和pr参数为shares和share_val成员提供值。注意，没有返回类型。原型位于类声明的公有部分。

	Stock::Stock(const string & co, long n, double pr
	{
		company = co;
		if (n < 0)
		{ 
			std::cerr << "Number of shares can't be nagative; "
					  << company << " shares set to 0.\n";
			shares = 0;
		}
		else
			shares = n;
		share_val = pr;
		set_tot();
	}
	
上述代码和本章前面的函数acquire()相同。区别在于，程序声明对象时，将自动调用构造函数。



<center>__成员名和参数名__</center>

不熟悉构造函数的您会试图将类成员名称为构造函数的参数名，如下所示：

	Stock::Stock(const string & company, long shares, double share_val)	
	{
		...
	}


__这样是错误的__。构造函数的参数表示的不是类成员，而是赋给类成员的值。因此，参数名不能与类成员相同，否则最终的代码是这样的：

	shares = shares;

为避免这种混乱，一种常见的做法是在数据成员名中使用m_前缀：

	class Stock
	{
	private:
		string m_company;
		long m_shares;
		...
	};

另一种常见的做法是，在成员名中使用后缀_:

	class Stock
	{
	private:
		string company;
		long shares;
		...
	}

无论采用哪种做法，都可在公有接口中在参数名中包含company和shares。

### 10.3.2 使用构造函数

C++提供了两种使用构造函数来初始化对象的方式。__第一种方式__是显示地调用构造函数：

	Stock food = Stock("World Cabbage", 250, 1.25);

这将food对象的company成员设置为字符串"World Cabbage",将shares成员设置为250，以此类推。

__另一种方式__是隐式调用构造函数：

	Stock garment("Furry Mason", 50, 2.5);

这个格式更为紧凑，它与下面的显示调用等价：

	Stock garment = Stock("Furry Mason", 50, 2.5);

每次创建对象（甚至使用new动态分配内存）时，C++都使用类构造函数。下面是将构造函数与new一起使用的方法：

	Stock *pstock = new Stock("Electroshock Games", 18, 19.0);

这条语句创建一个Stock对象，将其初始化为参数提供的值，并将该对象的地址赋给pstock指针。在这种情况下，对象没有名称，但可以使用指针来管理该对象。下一章将进一步讨论对象指针。

构造函数的使用方式不同于其他类方法。一般来说，使用对象来调用方法；

	stock1.show();

但无法使用对象来调用构造函数，因为在构造函数构造出对象之前，对象是不存的。因此构造函数被用来创建对象，而不能通过对象来调用。

### 10.3.3 默认构造函数

默认构造函数是在未提供显式初始值时，用来创建对象的构造函数。也就是说，它是用于下面这种声明的构造函数：

	Stock fluffy_the_cat; //使用默认的构造函数

程序10.3就是这样做的！这样语句管用的原因在于，如果没有提供任何构造函数，则C++将自动提供默认构造函数。它是默认构造函数的隐式版本，不做任何工作。对于Stock类来说，默认构造函数可能如下：

	Stock::Stock(){}

因此将创建fluffy_the_cat对象，但不初始化其成员，这和下面的语句创建x，但没有提供值给它一样：

	int x;

默认构造函数没有参数，因为声明中不包含值。

但是，当且仅当没有定义任何构造函数时，编译器才会提供默认构造函数。为类定义了构造函数后，程序员就必须为它提供默认构造函数。如果提供了非默认构造函数(如Stock(const char $\ast$co,int n, double pr))，但没有提供默认构造函数，则下面的声明将出错：

	Stock stock1; //没有默认的构造函数

这样做的原因可能是想禁止创建未初始化的对象。然而，如果要创建对象，而不显式地初始化，则必须定义一个不接受任何参数的默认构造函数。定义默认构造函数的方式有两种。一种给已有构造函数的所有参数提供默认值：

	Stock::Stock(const string & co = "Error", int n= 0, double pr = 0.0);

另一种方式是通过函数重载来定义一个构造函数----一个没有参数的构造函数：

	Stock::Stock();

由于只能有一个默认构造函数，因此不要同时采用这两种方式。实际上，通常应初始化所有的对象，以确保所有成员一开始就有已知的合理值。因此，用户定义的默认构造函数通常给所有成员提供隐式初始值。例如，下面未Stock类定义的一个默认构造函数：

	Stock::Stock()//默认构造值
	{
		company = "no name";
		shares = 0;
		share_val = 0.0;
		total_val = 0.0;
	}

__提示__：在设计类时，通常应提供对所有类成员做隐式初始化的默认构造函数。


使用上述任何一种方式（没有参数或所有参数都有默认值）创建默认构造函数后，便可以声明对象变量，而不对它们进行显示初始化：

	Stock first;
	Stock first = Stock();
	Stock *prelief = new Stock;

然而，不要被非默认构造函数的隐式形式所误导：

	Stock first("Concrete Conglomerate"); //调用非默认构造函数
	Stock second();  //声明一个函数
	Stock thrid;   //隐式调用默认构造函数

第一个声明调用非默认构造函数，即接受参数的构造函数；第二个声明指出，second()是一个返回Stock对象的函数。隐式地调用默认构造函数时，不要使用圆括号。


### 10.3.4 析构函数

用构造函数创建对象后，程序负责跟踪对象，直到到起为止。对象到期时，程序将自动调用一个特殊的成员函数，该函数的名称令人生畏---析构函数。

析构函数完成清理工作，因此实际上很有用。例如，如果构造函数使用new来分配内存，则析构函数将使用delete来释放这些内存。Stock的构造函数没有使用new，因此析构函数实际上没有需要完成的任务。在这种情况下，只需让编译器生成一个什么不要做的的隐式析构函数即可，Stock类第一版正是这样做的。然而，了解如何声明和定义析构函数是绝对必要的，下面为Stock类提供一个析构函数。

和构造函数一样，析构函数的名称也很特殊：在类名之前加上~。因此，Stock类的析构函数为~Stock()。另外，和构造函数一样，析构函数也快车没有返回值和声明类型。与构造函数不同的是，析构函数没有参数，因此Stock析构函数的原型必须是这样的：

	~Stock();

由于Stock的析构函数不承担任何重要的工作，因此可以将它编写为不执行任何操作的函数：

	Stock::~Stock(){}

然而，为让您看出析构函数何时被调用，这样编写其代码：

	Stock::~Stock()
	{
		cout << "Bye. " << company << " !\n";
	} 


由于类对象过期时析构函数将自动被调用，因此必须有一个析构函数。如果程序员没有提供析构函数，编译器将隐式地声明一个默认析构函数，并在发现导致对象被删除的代码后，提供默认析构函数的定义。

### 10.3.5 改进的Stock类

下面将构造函数和析构函数加入到类和方法的定义中。

__1.头文件__

程序10.4列出了头文件。它将构造函数和析构函数的原型加入到原来的类声明中。另外，它还删除了acquire()函数---现在已经不再需要它了，因为有构造函数。

程序10.4 stock00.h

	#ifndef STOCK00_H_
	#define STOCK00_H_
	
	#include <string>
	class Stock
	{
		private:
			std::string company;
			long shares;
			double share_val;
			double total_val;
			void set_tot() { total_val = share_val * shares;}
		public:
			Stock();
			Stock(const std::string & co, long n = 0, double pr = 0.0);
			~Stock();
			void buy(long num, double price);
			void sell(long num, double price);
			void update(double price);
			void show();
	};
	
	#endif


__2.实现文件__

程序10.5提供了方法的定义。它包含了头文件stock00.h，以提供类声明。

程序10.5 stock00.cpp

	#include <iostream>
	#include "stock00.h"
	
	Stock::Stock()
	{
		std::cout <<  "Default constructor called\n";
		company = "no name";
		shares = 0;
		share_val = 0.0;
		total_val = 0.0;
		
	}
	
	Stock::Stock(const std::string & co, long n, double pr)
	{
		std::cout << "Constructor using " << co << " called\n";
		company = co;
		if (n < 0)	
		{ 
			std::cerr << "Number of shares can't be nagative; "
				   << company << " shares set to 0.\n";
			shares = 0;
		}
		else
			shares = n;
		share_val = pr;
		set_tot();
	
	}
	
	Stock::~Stock()
	{
		std::cout << "Bye, " << company << " !\n";
	}
	
	void Stock::buy(long num, double price)
	{
		if (num < 0)
		{
			std::cout << "Number of shares purchased can't be nagative. "
				  << "Transaction is aborted.\n";
		}
		else
		{
			shares += num;
			share_val = price;
			set_tot();
		}
	}
	
	void Stock::sell(long num, double price)
	{
		using std::cout;
		if (num < 0)
		{
			cout << "Number of shares sold can't be nagative. "
				<< "Transaction is aborted.\n";
		}
		else if (num > shares)
		{
			cout << "You can't sell more than you have!"
				<< " Transantion is aborted.\n";
		}
		else
		{
			shares -= num;
			share_val = price;
			set_tot();
		}
	}
	
	void Stock::update(double price)
	{
		share_val = price;
		set_tot();
	}
	
	void Stock::show()
	{
		std::cout << "Company: " << company
			<< " Shares: " << shares << std::endl
			<< " Share Price: $" << share_val
			<< " Total Worth: $" << total_val << std::endl;
	}
	

__3.客户文件__

程序10.6提供了一个测试这些新方法的小程序。

程序10.6 usestock2.cpp

	#include <iostream>
	#include "stock00.h"

	int main()
	{
		{
			using std::cout;
			cout << "Using constructors to create new objects\n";
			Stock stock1("NanoSmart", 12, 20.0);
			stock1.show();
			Stock stock2 = Stock("Boffo objects", 2, 2.0);
			stock2.show();
	
			cout << "Assigning stock1 to stock2: \n";
			stock2 = stock1;
			cout << "Listing stock1 and stock2: \n";
			stock1.show();
			stock2.show();
	
			cout << "Using a constructor to  reset an object\n";
			stock1 = Stock("Nifty Foods", 10, 50.0);
			cout << "Revised stock1:\n";
			stock1.show();
			cout << "Done\n";
		}
		return 0;
	}

编译：

	g++ usestock2.cpp stock00.cpp -o file

运行:

	./file

输出结果：

	Using constructors to create new objects
	Constructor using NanoSmart called
	Company: NanoSmart Shares: 12
	 Share Price: $20 Total Worth: $240
	Constructor using Boffo objects called
	Company: Boffo objects Shares: 2
	 Share Price: $2 Total Worth: $4
	Assigning stock1 to stock2: 
	Listing stock1 and stock2: 
	Company: NanoSmart Shares: 12
	 Share Price: $20 Total Worth: $240
	Company: NanoSmart Shares: 12
	 Share Price: $20 Total Worth: $240
	Using a constructor to  reset an object
	Constructor using Nifty Foods called
	Bye, Nifty Foods !
	Revised stock1:
	Company: Nifty Foods Shares: 10
	 Share Price: $50 Total Worth: $500
	Done
	Bye, NanoSmart !
	Bye, Nifty Foods !


__提示：__您可能注意到，在程序10.6中，main()的开头和结尾多了一个大括号。诸如stock1和stock2等自动变量将在程序退出其定义所属代码块时消失。如果没有大括号，代码块将为整个main()，因此仅当main()执行完毕后，才会调用析构函数。在窗口环境中，这意味着将在两个析构函数调用前关闭，导致您无法看到最后两条消息。但添加这些大括号后，最后两个析构函数调用将在到达返回语句前执行，从而显示相应的消息。


__4.程序说明__

略

__5.C++11列表初始化__

在C++11中，可将列表初始化语法用于类，只要提供与某个构造函数的参数列表匹配的内容，并用大括号将它们括起来：

	Stock hot_tip = {"Derivatives Plus Plus", 1-, 45.0};
	Stock jock {"Sport Age Storage, Inc"};
	Stock temp {};

在前两个声明中，用大括号括起来的列表和下面的构造函数匹配：

	Stock::Stock(const std::string & co, long n = 0, double pr = 0.0);

因此，将使用该构造函数来创建这两个对象。创建对象jockk时，第二和第三个参数将为默认值0和0.0。第三个声明与默认构造函数匹配，因此将使用该构造函数创建对象temp。

另外，C++还提供了名为std::initialize_list的类，可将其用作函数参数或方法参数的类型。这个类可表示任意长度的列表，只要所有列表项的类型都相同或可转换为相同的类型。

__6.const成员函数__

请看下面的代码段：

	const Stock land = Stock{"Kludgehorn Properties"};
	land.show();


对于当前的C++来说，编译将拒绝第二行。因为show()的代码无法确保调用对象不被修改---调用对象和const一样，不应被修改。我们以前通过将函数参数声明为const引用或指向const的指针来解决这个问题。但这里存在语法问题：show()方法没有任何参数。相反它所有使用的对象是由方法调用隐式提供的。需要一种新的语法---保证函数不会修改调用对象。C++的解决方法是将const关键字放在函数的括号后面。即，show()声明如下：

	void show() const;

同样，函数定义的开头也应该如下：

	void Stock::show() const

以这种方式声明和定义的类函数被称为__const成员函数__。就像应尽可能将const引用和指针用作函数形参一样，只要类方法不修改调用对象，就应该将其声明为const。


### 10.3.6 构造函数和析构函数小结

构造函数是一种特殊的类成员函数在创建类对象时被调用。构造函数的名称和类名相同，但通过函数重载，可以创建多个同名的构造函数，条件是每个函数的参数列表都不同。另外，构造函数没有声明类型。通常，构造函数用于初始化类对象的成员，初始化应与构造函数的参数列表匹配。例如：

	Bozo (const char * fname, const char * lname);

初始化新对象：

	Bozo boeztta = Bozo("Bozetta", "Biggens");
	Bozo fufu("Fufu","O'Dweeb");
	Bozo *pc = new Bozo("Popo", "Le pu");

如果支持C++，则可使用列表初始化：

	Bozo bozetta = {"Bozetta", "Biggens"};
	Bozo fufu{"Fufu", "O'Dweeb"};
	Bozo *pc = new Bozo{"Popo", "Le pu"};


默认构造函数没有参数，因此如果创建对象时没有进行显示初始化，则将调用默认构造函数。


就像对象被创建时程序调用构造函数一样，当对象被删除时，程序将调用析构函数。每个类只能有一个析构函数。析构函数没有返回类型，也没有参数，其名称为类名称前加上~。

如果构造函数使用了new，则必须提供使用delete的析构函数。


## 10.4 this指针

到目前为止，每个类成员函数都只涉及一个对象，即调用它的对象。但有时候方法方法可能涉及到两个对象，在这种情况下需要使用C++的this指针。

虽然Stock类声明可以显示数据，但它缺乏分析能力。例如，从show()的输出我们可以知道持有哪一支股票价格最，但由于程序无法直接访问total_val，因此无法作出判断。要让程序知道存储的数据，最直接的方式让方法返回一个值。为此，通常使用内联代码，如下：

	class Stock
	{
	private:
		double total_val;
		...
	public:
		double total() const ({return total_val;}
	}


就直接程序访问而言，上述定义实际上是使total_val为只读。也就是说，可以使用方法total_val()来获得total_val的值，但这个类没有提供专门用于重新设置total_val的值的方法（作为一种副产品，其他方法，如buy()、sell()、update()确实在重新设置成员shares和share_val的值的同时修改了total_val的值）。

通过将该函数添加到类声明中，可以让程序查看一系列股票，找到价格最高的那一只。然而，可以采用另一种方法----一种帮助您了解this指针的方法。这种方法是：定义一个成员函数，它查看两个Stock对象，并返回股价较高者的那个对象的引用。


首先，如何将两个要比较的对象提供给成员函数呢？可以按引用来传递参数，即，topval()方法使用一个类型为const Stock & 的参数。

其次，如何将方法的答案传回给调用程序呢？最直接的方法是让方法返回一个引用，该引用指向股价总值较高的对象。因此，用于比较的方法的原型如下：

	const Stock & topval(const Stock & s) const;

该函数隐式地访问一个对象，而显式地访问另一个对象，并返回其中一个对象的引用。括号中的const表明，该函数不会修改被显式地访问的对象；而括号后的const表明，该函数不会修改被隐式地访问的对象。由于该函数返回了两个const对象之一的引用，因此返回类型也应为const引用。

假设要对Stock对象stock1和stock2进行比较，并将其中股价总值较高的那一个赋给top对象，则可以使用下面两条语句之一：

	top = stock1.topval(stock2);
	top = stock2.topval(stock1);

注意的是topval()的实现，它将引发一个小问题。下面的部分实现强调了这个问题：

	const Stock & Stock::topval(const Stock & s) const
	{
		if (s.total_val > total_val)
			return s;
		else	
			return ????  //隐式对象
	}

如果s.total_val大于total_val，则函数将返回指向s的引用；否则，将返回用来调用该方法的对象。__问题在于，如何称呼这个对象？__

C++解决这种问题的方法是：使用被称为__this的特殊指针__。this指针__指向用来调用成员函数的对象__（this被作为隐藏参数传递给方法）。这样，函数调用stock1.topval(stock2)将this设置为stock1对象的地址，这使得这个指针可用于topval()方法。

__注意：__

每个成员函数（包括构造函数和析构函数）都有一个this指针。this指针指向调用对象。如果方法需要引用整个调用对象，则可以使用表达式$\ast$this。在函数的括号后面使用const限定符将this限定为const，这样将不能使用this来修改对象的值。

然而，要返回的并不是this，因为this是对象的地址，而是对象的本身，即$\ast$this。现在，可以将$\ast$this作为调用对象的别名来完成前面的方法定义。

	const Stock & Stock::topval(const Stock & s) const
	{
		if (s.total_val > this.total_val)
			return s;
		else
			return *this;
	}

返回对象为引用意味着返回的是调用对象本身，而不是副本。程序10.7列出了新的头文件。


程序10.7 stock20.h

	#ifndef STOCK00_H_
	#define STOCK00_H_
	
	#include <string>
	class Stock
	{
		private:
			std::string company;
			long shares;
			double share_val;
			double total_val;
			void set_tot() { total_val = share_val * shares;}
		public:
			Stock();
			Stock(const std::string & co, long n = 0, double pr = 0.0);
			~Stock();
			void buy(long num, double price);
			void sell(long num, double price);
			void update(double price);
			void show() const;
			const Stock & topval(const Stock & s) const;
	};
	
	#endif


程序10.8 stock20.cpp
	
	#include <iostream>
	#include "stock00.h"
	
	Stock::Stock()
	{
		std::cout <<  "Default constructor called\n";
		company = "no name";
		shares = 0;
		share_val = 0.0;
		total_val = 0.0;
		
	}
	
	Stock::Stock(const std::string & co, long n, double pr)
	{
		std::cout << "Constructor using " << co << " called\n";
		company = co;
		if (n < 0)	
		{ 
			std::cerr << "Number of shares can't be nagative; "
				   << company << " shares set to 0.\n";
			shares = 0;
		}
		else
			shares = n;
		share_val = pr;
		set_tot();
	
	}
	
	Stock::~Stock()
	{
		std::cout << "Bye, " << company << " !\n";
	}
	
	void Stock::buy(long num, double price)
	{
		if (num < 0)
		{
			std::cout << "Number of shares purchased can't be nagative. "
				  << "Transaction is aborted.\n";
		}
		else
		{
			shares += num;
			share_val = price;
			set_tot();
		}
	}
	
	void Stock::sell(long num, double price)
	{
		using std::cout;
		if (num < 0)
		{
			cout << "Number of shares sold can't be nagative. "
				<< "Transaction is aborted.\n";
		}
		else if (num > shares)
		{
			cout << "You can't sell more than you have!"
				<< " Transantion is aborted.\n";
		}
		else
		{
			shares -= num;
			share_val = price;
			set_tot();
		}
	}
	
	void Stock::update(double price)
	{
		share_val = price;
		set_tot();
	}
	
	void Stock::show() const
	{
		std::cout << "Company: " << company
			<< " Shares: " << shares << std::endl
			<< " Share Price: $" << share_val
			<< " Total Worth: $" << total_val << std::endl;
	}
	
	const Stock & Stock::topval(const Stock & s) const
	{
		if (s.total_val > total_val)
			return s;
		else
			return *this;
	}


当然，我们想知道this指针是否有用。显然，应在一个包含对象数组的程序中使用这种新方法。因此接下来介绍对象数组。


## 10.5 对象数组

和Stock示例一样，用户通常要创建同一类的多个对象。可以创建独立对象变量，就像前面介绍的一样，但创建对象数组更合适。声明对象数组的方法与声明标准类型数组相同：

	Stock mystuff[4];

前面讲过，当程序创建未被显式初始化的类对象时，总是调用默认构造函数。上述声明要求，这个类要么没有显式地定义任何构造函数（在这种情况下，将使用不执行任何操作的隐式默认构造函数），要么定义了一个显式默认构造函数。每个元素都是Stock对象，可以使用Stock方法：

	mystuff[0].updata();
	mystuff[1].show();
	const Stock * top = mystuff[2].topval(mystuff[1]);

可以用构造函数来初始化数组元素。在这种情况下，必须为每个元素调用构造函数：

	cosnt int STKS = 4;
	Stock socks[STKS] = {
			Stock("NanoSmart", 12.5, 20),
			Stock("Boffo Objects", 200, 2.0),
			Stock("Monolithic Obelisks", 130, 3.25),
			Stock("Fleep Enterprises", 60, 6.5)
		};

这里的代码使用标准格式对数组进行初始化：用括号括起来，以逗号分割的值列表。其中，每次构造函数调用表示一个值。如果类包含多个过早函数，则可以对不同的元素使用不同的构造函数：

	const int STKS = 10;
	Stock stocks[STKS] = {
			Stock(”Nanosmart“, 12.5, 20),
			Stock(),
			Stock("Monolithic Obelisks", 130, 3.25)
		};

上述代码使用Stock(const string & co, long n, double pr)初始化stocks[0]和stocks[2]，使用构造函数Stock()初始化stock[1]。由于该声明只初始化了数组的部分元素，因此余下7个元素将使用默认构造函数进行初始化。


初始化对象数组的方案是：首先使用默认构造函数创建数组元素，然后花括号种的构造函数将创建临时对象，然后将临时对象的内容复制到相应的元素中。因此，要创建类对象数组，则这个类必须有默认构造函数。

程序10.9在一个小程序中使用了这些原理，该程序对4个数组进行初始化，显示它们的内容，并找出这些元素中总值最高的一个。由于topval()每次只检查两个对象，因此程序使用for循环来检查整个数组。

程序10.9 usestock2.cpp

	#include <iostream>
	#include "stock00.h"
	
	int main()
	{
		{
			using std::cout;
			cout << "Using constructors to create new objects\n";
			Stock stock1("NanoSmart", 12, 20.0);
			stock1.show();
			Stock stock2 = Stock("Boffo objects", 2, 2.0);
			stock2.show();
	
			cout << "Assigning stock1 to stock2: \n";
			stock2 = stock1;
			cout << "Listing stock1 and stock2: \n";
			stock1.show();
			stock2.show();
	
			cout << "Using a constructor to  reset an object\n";
			stock1 = Stock("Nifty Foods", 10, 50.0);
			cout << "Revised stock1:\n";
			stock1.show();
			cout << "Done\n";
		}
		return 0;
	}

程序输出：

	Constructor using NanoSmart called
	Constructor using Boffo Objects called
	Constructor using Monolithic Obelisks called
	Constructor using Fleep Enterprises called
	Stock holding:
	Company: NanoSmart Shares: 12
	 Share Price: $20 Total Worth: $240
	Company: Boffo Objects Shares: 200
	 Share Price: $2 Total Worth: $400
	Company: Monolithic Obelisks Shares: 130
	 Share Price: $3.25 Total Worth: $422.5
	Company: Fleep Enterprises Shares: 60
	 Share Price: $6.5 Total Worth: $390
	
	Most valuable holding:
	Company: Monolithic Obelisks Shares: 130
	 Share Price: $3.25 Total Worth: $422.5
	Bye, Fleep Enterprises !
	Bye, Monolithic Obelisks !
	Bye, Boffo Objects !
	Bye, NanoSmart !



值得注意的是，大部分工作是在类设计完成的。完成类设计后，编写程序的工作本身相当简单。

顺便说一句，知道this指针就可以更深入了解C++的工作方式。例如，最初UNIX实现使用C++前端cfront将C++程序转换为C程序。处理方法的定义，只需要将下面这样的C++方法定义：


	void Stock::show() const
	{
		std::cout << "Company: " << company
			<< " Shares: " << shares << std::endl
			<< " Share Price: $" << share_val
			<< " Total Worth: $" << total_val << std::endl;
	}


转换为下面这样C-风格定义：

	void Show(const Stock * this)
	{
		std::cout << "Company: " << company
			<< " Shares: " << shares << std::endl
			<< " Share Price: $" << share_val
			<< " Total Worth: $" << total_val << std::endl;
	}


即将Stock::限定符转换为函数参数（指向Stock的指针），然后用这个指针来访问类成员。

同样，该前端将下面的函数调用：

	top.show();

转换为：

	show(&top);

这样，将调用对象的地址赋给了this指针。


## 10.6 类作用域

第9章介绍了全局（文件）作用域和局部（代码块）作用域。可以在全局变量所属文件的任何地方使用它，而局部变量只能在其所属的代码块中使用。函数名称的作用域也可以是全局的，但不能是局部的。C++类引入了一种新的作用域：类作用域。


在类中定义的名称（如类数据成员和类成员函数名）的作用域都为整个类，作用域为整个类的名称只在该类是已知的，在类外是不可知的。因此，可以在不同类中使用相同的类成员名而不会引起冲突。另外，类作用域意味着不能从外部直接访问类的成员，公有成员函数也是如此。即，要调用公有成员函数，必须通过对象：

	Stock sleeper{"Exclusive Ore", 100, 0.25};
	sleeper.show();
	show(); //无效，不能直接调用方法

同样，在定义成员函数时，必须使用作用域解析运算符：

	void Stock::update(double price)
	{...}

总之，在类声明或成员函数定义中，可以使用未修饰的成员名称（未限定的名称），就像sell()调用set_tot()成员函数时那样。构造函数名称在被调用时，才能被识别，因为它的名称与类名相同。在其他情况下，使用类名称时，必须根据上下文使用直接成员运算符(.)、间接成员运算符(->)或作用域解析运算符(::)。下面的代码片段演示了如何访问具有类作用域的标识符：


	class IK
	{
	private:
		int fuss;
	public:
		IK(int f = 9) {fuss = f;} //过早函数
		void ViewIK() const;
	}
	
	void IK::ViewIK() const
	{
		cout << fuss << endl;
	}

	int main()
	{
		IK * pik = new Ik;
		Ik ee = IK(8);
		ee.ViewIk();
		pik->ViewIk();
	}


### 10.6.1 作用域为类的常量

有时候，使符号常量的作用域为类很有用。例如，类声明eng使用字面值30来指定数组的长度，由于该常量对于所有对象来说都是相同的，因此创建一个由所有对象共享的常量是个不错的注意。您可能以为这样做可行：

	class Bakery
	{
	private:
		const int Months = 12;
		double consts[Months];
		...


但这是行不通的，因为声明类只是描述了对象的形式，并没有创建对象。因此在创建对象前，将没有用于存储值的空间。然而，由两种方式可以实现这个目标，并且效果相同。

第一种方式是在类中__声明一个枚举__。在类声明中声明的枚举的作用域为整个类，因此可以用枚举为整个整型常量提供作用域为整个类的符号名称。即，可以这样开始Bakery声明：

	class Bakery
	{
	private:
		enum {Months = 12};
		double costs[Months];
	...

注意，用这种方式声明枚举并不会创建类数据成员。即，所有对象中都不包含枚举。另外，Months只是一个符号名称，在作用域为整个类的代码中遇到它，编译器将用12来替换它。

由于这里使用枚举只是为了创建符号常量，并不打算创建枚举类型的变量，因此不需要提供没居民。顺便说一句，在很多实现中，ios_base类在其公有部分中完成了类似的工作，诸如ios_base::fixed等标识符就来自这里。其中，fixed是ios_base类中定义的典型的枚举量。

C++提供了另一种在类中定义常量的方式---__使用关键字static__:

	class Bakery
	{
	private:
		static const int Months = 12;
		double costs[Months];
	...

这将创建一个名为Months的常量，该常量将与其他静态变量存储在一起，而不是存储在对象中。因此，只有一个Months常量，被所有Bakery对象共享。第12章将深入介绍静态类成员。

在C++98中，只能使用这种技术声明值为整数或枚举的静态常量，而不能存储doubLe常量。C++11消除了这种限制。

### 10.6.2 作用域内枚举(C++11)

传统的枚举存在一些问题，其中之一就是__两个枚举定义的枚举量可能发生冲突__。假设有一个出差鸡蛋和T恤的项目，其中可能包含类似下面的代码：

	enum egg {Small, Medium, Large, Jumbo};
	enum t_shirt {Small, Medium, Large, Xlarge};

这将无法通过编译，因为egg Small和t_shirt Small位于相同的作用域内，它们将发生冲突。为避免这种问题，C++11提供了一种新枚举，__其枚举量的作用域为类__。这种枚举的声明类似下面这样：

	enum class egg {Small, Medium, Large, Jumbo};
	enum class t_shirt {Small, Medium, Large, Xlarge};

__也可使用关键字struct代替class__。无论使用哪种方式，都需要使用枚举名来限定枚举量：

	egg choice = egg::Large;
	t_shirt Floyd = t_shirt::Large; 

枚举量的作用域为类后，不同枚举定义中的枚举量就不会发生冲突了，而您可继续编写处理鸡蛋和T恤的项目。

C++还提供了作用域内枚举的类型安全。在有些情况下，__常规枚举将自动转换为整型__，如将其赋给int变量或用于比较表达式时，但__作用域内枚举不能隐式地转换为整型__：

	enum egg_old {Small, Medium, Large, Jumbo};
	enum  class t_shirt {Small, Medium, Large, Jumbo};
	egg_old one = Mdeium;
	t_shirt rolf = t_shirt::Large;
	int king = one;
	int ring = rolf; //错误，不能隐式类型转换
	if (king < Jumbo)
		std::cout << "Jumbo converted to int before comparison.\n";
	if (king < t_shirt::Medium) //错误
		std::cout << "Not allowed: < not defined for scoped enum.\n";

但在必要时，可进行显式类型转换：

	int Frodo = int(t_shirt::Small);

枚举用某种底层整型类型表示，在C++98中，如何选择取决于实现，因此包含枚举的结构的长度可能随系统而异。对于作用域内枚举，C++11消除了这种依赖性。默认情况下，C++11作用域内枚举的底层类型为int。另外，还提供了一种语法，可用于做出不同的选择：

	enum class : short pizza {Small, Medium, Large, Xlarge};

:short将底层类型指定为short。底层类型必须为整型。在C++11中，也可使用这种语法来指定常规枚举的底层类型，但如果没有指定，编译器的底层类型将随实现而异。


## 10.7 抽象数据类型

Stock类非常具体。然而，程序员常常通过定义类来表示更通用的概念。例如，就实现计算机专家们所说的抽象数据类型(abstract data type, ADT)而言，使用类是一种非常好的方式。顾名思义，ADT以通用的方式描述数据类型，而没有引入语言或实现细节。例如，通过使用栈，可以以这样的方式存储数据，即总是从堆顶添加或删除数据。例如，C++程序使用栈来管理自动变量。当新的自动变量生成后，它们被添加到堆顶；消亡时，从栈顶中删除它们。

下面介绍一下栈的特征。首先，栈存储了多个数据项（该特征使得栈称为一个容器---一种更为通用的抽象）；其次，栈可对他执行的操作来描述。

- 可创建空栈；
- 可将数据项添加到堆顶(压入)；
- 可从栈顶删除数据项(弹出)；
- 可查看栈是否填满；
- 可插件栈是否为空。

可以将上述描述转换为一个类声明，其中公有成员函数提供了表示栈操作的接口，而私有数据成员负责存储数据。类概念非常适合于ADT方法。

私有部分必须表明数据存储的方式。例如，可以使用常规数组、动态分配数组或更高级的数据结构（如链表）。然而，公有接口应隐藏数据表示，而以通用的术语来表示，如创建栈、压入等。程序10.10演示了一种方法，它假设系统实现了bool类型。

程序10.10 stack.h

	#ifndef STACK_H_
	#define STACK_H_
	
	typedef unsigned long Item;
	
	class Stack
	{
	private:
		enum {MAX = 10};
		Item items[MAX];
		int top;
	public:
		Stack();
		bool isempty() const;
		bool isfull() const;
		bool push(const Item & item);
		bool pop(Item & item);
	};
	
	#endif

在程序10.10所示的示例中，私有部分表示，栈是使用数组实现的；而公有部分隐藏了这一点。因此，可以使用动态数组来代替数组，而不会改变类的接口。这意味着修改栈的实现后，不需要重新编写使用栈的程序，而只需要重新编译栈代码，并将其与已有的程序代码链接起来即可。

接口时冗余的，因此pop()和push()返回有关栈状态的信息（满或空），而不是void类型。在如何处理超出栈限制或清空栈方面，这位程序员提供了两种选择。它可以在修改栈前使用isempty()和isfull()来查看，也可以使用push()和pop()的返回值来确定操作是否成功。

这个类不是根据特定的类型来定义栈，而是根据通用的Item类型来描述。在这个例子中，头文件使用typedef用Item代替unsigned long。如果需要double栈或结构的栈，则只需要修改typedef语句，而类声明和方法定义保持不变。类模板（参见第14章）提供了功能更强大的方法，来讲存储的数据类型于类设计隔离开来。

接下来需要实现类方法。

程序10.11 stack.cpp

	#include "stack.h"
	#include <iostream>
	using namespace std;
	
	Stack::Stack()
	{
		top = 0;
	}
	
	bool Stack::isempty() const
	{
		return top == 0;
	}
	
	bool Stack::isfull() const
	{
		return top == MAX;
	}
	
	bool Stack::push(const Item & item)
	{
		if (top < MAX)
		{
			items[top++] = item;
			return true;
		}
		else
			return false;
	}
	
	bool Stack::pop(Item & item)
	{
		if (top > 0)
		{
			item = items[--top];
			return true;
		}
		else
			return false;
	}

默认构造函数确保所有栈被创建时都是为空。pop()和push()的代码确保栈顶被正确处理。这种保证措施是OOP更可靠的原因之一。

下面来测试该栈。程序10.12模拟售货员的行为---使用栈的后进先出方式，从购物框的最上面开始处理购物订单。


程序10.12 stacker.cpp

	#include "stack.h"
	#include <iostream>
	#include <cctype>
	
	int main()
	{
		using namespace std;
		Stack st;
		char ch;
		unsigned long po;
		cout << "Please enter A to add a purchase order,\n"
			<< "P to process a PO, or Q to quit.\n";
		while (cin >> ch && toupper(ch) != 'Q')
		{
			while (cin.get() != '\n')
				continue;
			if (!isalpha(ch))
			{
				cout << '\a';
				continue;
			}
			switch(ch)
			{
				case 'A':
				case 'a':
					cout << "Enter a PO number to add: ";
					cin >> po;
					if (st.isfull())
						cout << "stack already full\n";
					else
						st.push(po);
					break;
				case 'p':
				case 'P':
					if (st.isempty())	
						cout << "stack already empty\n";
					else{
						st.pop(po);
						cout << "PO #" << po << " popped\n";
					}
					break;
			}
			cout << "Please enter A to add a purchase order, \n"
				<< "P to process a PO, or Q to quit.\n";
		}
		cout << "Bye.\n";
		return 0;
	}



程序运行结果：

	Please enter A to add a purchase order,
	P to process a PO, or Q to quit.
	A
	Enter a PO number to add: 17885
	Please enter A to add a purchase order, 
	P to process a PO, or Q to quit.
	P
	PO #17885 popped
	Please enter A to add a purchase order, 
	P to process a PO, or Q to quit.
	A
	Enter a PO number to add: 17965
	Please enter A to add a purchase order, 
	P to process a PO, or Q to quit.
	A
	Enter a PO number to add: 18002
	Please enter A to add a purchase order, 
	P to process a PO, or Q to quit.
	P
	PO #18002 popped
	Please enter A to add a purchase order, 
	P to process a PO, or Q to quit.
	P
	PO #17965 popped
	Please enter A to add a purchase order, 
	P to process a PO, or Q to quit.
	P
	stack already empty
	Please enter A to add a purchase order, 
	P to process a PO, or Q to quit.
	Q
	Bye.


## 10.8 总结

类声明分成两部分组成。类声明放在头文件中；定义成员函数的源代码放在方法文件中。

类是用户定义的类型，对象是类的实例。即，对象是这种类型的变量。

每个对象都存储自己的数据，而共享类方法。

如果希望成员函数堆多个对象进行操作，可以讲额外的对象作为参数传递给它。如果方法需要显式地引用调用它的对象，则可以使用this指针。

类适合用于描述ADT。公有成员函数接口提供了ADt描述的服务，类的私有部分和类方法的代码提供了出现，这些实现对类的客户隐藏。

