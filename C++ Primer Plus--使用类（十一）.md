---

title:  C++ Primer Plus--使用类（十一）
date: 2019-01-23 19:08:14
tags: C++
categories: "C++ Primer Plus学习笔记"
mathjax: true

---

本章首先介绍运算符重载，它允许将标准C++运算符(如+和=)用于类对象。然后介绍友元，这种C++机制使得非成员函数可以访问私有数据。最后，介绍如何命令C++对类执行自动类型转换。<!--more-->

## 11.1 运算符重载

运算符重载是一种形式的__C++多态__。第8章介绍了C++是如何使用户能够定义多个名称相同但特征标（参数列表）不同的韩剧是。这被称为函数重载或函数多态，旨在让您能过用同名的函数来完成相同的基本操作，即使这种操作被用于不同的数据类型。运算符重载将重载的概念扩展到运算符上，允许赋予C++运算符多种含义。实际上，很多C++运算符已经被重载。例如，将$\ast$运算符用于地址，将得到存储在这个地址中的值；但将它用于两个数字时，得到的将是它们的乘积。C++根据操作数的数目和类型来决定采用哪种操作。

C++允许将运算符重载扩展到用户定义的类型，例如，允许使用+将两个对象相加。编译器将根据操作数的数目和类型决定使用哪种加法定义。重载运算符可使代码看起来更自然。例如，将两个数组相加是一种常见的运算。通常，需要使用下面这样的for循环来实现：

	for (int i = 0; i < 20; i++)			
		evening[i] = sam[i] + janet[i];

但在C++中，可以定义一个表示数组的类，并重载+运算符。于是便可以由这样的语句：

	evenig = sam + janet;

这种简单的加法表示隐藏了内部机理，并强调了实质，这是OOP的另一个目标。

要重载运算符，需使用被称为__运算符函数的特殊函数形式__。运算符函数的格式如下：

	opeartor op (argument-list)

例如，operator+()重载+运算符，operator$\ast$()重载$\ast$运算符。op必须是有效的C++运算符，不能虚构一个新的符号。例如，不能有operator@()这样的函数，因为C++中没有@运算符。然而，operator【】()函数重载[]运算符，因为[]运算符是数组索引运算符。

例如，假设有一个Salesperson类，并为它定义了一个opeartor+()成员函数，以重载+运算符，以便能够将两个Saleperson对象的销量相加，则如果district2、sid和sara都是Salesperson类对象，便可以编写这样的等式：

	district2 = sid + sara;

编译发现，操作数是Salesperson类对象，因此使用相应的运算符函数替换上述运算符：

	district2 = sid.operator+(sara);

该函数将隐式地使用sid（因为它强调了方法），而显式地使用sara（因为他被作为参数传递），来计算总和，并返回这个值。当然最重要的是，可以使用简便的+运算符表示法，而不必使用笨拙的函数表示法。

## 11.2 计算时间：一个运算符重载示例

如果今天早上在Priggs的账户上花费了2个小时35分钟，下午又花费了2小时40分钟，则总共花了多少时间？这个示例与加法的概念很吻合，但要相加的单位(小时与分钟的混合)与内置的类型不匹配。第7章通过定义一个travel_time结构和将这种结构相加的sum()函数来处理类似的情况。现在将其推广，采用一个使用方法来处理加法的Time类。首先使用一个名为Sum()的常规方法，然后介绍如何将其转换为重载运算符。程序11.1列出了这个类声明。

程序11.1 mytime0h
	
	#ifndef MYTIME0_H_
	#define MYTIME0_H_
	
	class Time
	{
	private:
		int hours;
		int minutes;
	public:
		Time();
		Time(int h, int m = 0);
		void AddMin(int m);
		void AddHr(int h);
		void Reset(int h = 0, int m = 0);
		Time Sum(const Time & t) const;
		void Show() const;
	};
	
	#endif


程序11.2 mytime0.cpp

	#include <iostream>
	#include "mytime0.h"
	
	Time::Time()
	{
		hours = minutes = 0;
	}
	
	Time::Time(int h, int m)
	{
		hours = h;
		minutes = m;
	}
	
	void Time::AddMin(int m)
	{
		minutes += m;
		hours = minutes / 60;
		minutes = minutes % 60;
	}
	
	void Time::AddHr(int h)
	{
		hours += h;
	}
	
	void Time::Reset(int h, int m)
	{
		hours = h;
		minutes = m;
	}
	
	Time Time::Sum(const Time & t) const
	{
		Time sum;
		sum.minutes = minutes + t.minutes;
		sum.hours = hours + t.hours + sum.minutes / 60;
		sum.minutes %= 60;
		return sum;
	}
	
	void Time::Show() const
	{
		std::cout << hours << " hours, " << minutes << " minutes.";
	}

来看一下Sum()函数。注意参数是引用，但返回类型却不是引用。将参数声明为引用的目的是为了提供效率。如果直接传递Time对象，代码的功能将相同，但传递引用的速度更快，使用的内存将更少。

然而，返回值不是引用。因为函数将创建一个新的Time对象(sum)，来表示另两个Time对象的和。返回对象(如代码所做的那样)将创建副本对象，而调用函数可以使用它。然而，如果返回类型是为Time &，则引用的将是sum对象。但由于sum是局部变量，在函数结束时将被函数，因此引用将指向一个不存在的对象。使用返回类型Time意味着程序将在删除sum之前构造它的拷贝，调用函数将得到该拷贝。

__注意__：不要返回指向局部变量或临时对象的引用。函数执行完毕后，局部变量和临时对象将消失，引用将指向不存在的数据。


程序11.3 usetime0.cpp

	#include <iostream>
	#include "mytime0.h"
	
	int main()
	{
		using std::cout;
		using std::endl;
		Time planning;
		Time coding(2, 40);
		Time fixing(5, 55);
		Time total;
		
		cout << "planning time = ";
		planning.Show();
		cout << endl;
		
		cout << "coding time = ";
		coding.Show();
		cout << endl;
		
		cout << "fixing time = ";
		fixing.Show();
		cout << endl;
		
		total = coding.Sum(fixing);
		cout << "coding.Sum(fixing) = ";
		total.Show();
		cout << endl;
		return 0;
	}


输出：

	[root@localhost ~]# g++ mytime0.cpp usetime0.cpp -o file
	[root@localhost ~]# ./file
	planning time = 0 hours, 0 minutes.
	coding time = 2 hours, 40 minutes.
	fixing time = 5 hours, 55 minutes.
	coding.Sum(fixing) = 8 hours, 35 minutes.



### 11.2.1 添加加法运算

将Time类转换为重载的加法运算符很容易，只要将Sum()的名称改为operator + ()即可。这样做是对的。只要把运算符(这里为+)放到operator的后面，并将结果用作方法名即可。在这里，可以在标识符中使用字母、数字或下划线之外的其他支付。程序11.4和程序11.5反映了这些细微的修改。

程序11.4 mytime1.h

	#ifndef MYTIME1_H_
	#define MYTIME1_H_
	
	class Time
	{
	private:
		int hours;
		int minutes;
	public:
		Time();
		Time(int h, int m = 0);
		void AddMin(int m);
		void AddHr(int h);
		void Reset(int h = 0, int m = 0);
		Time operator+(const Time & t) const;
		void Show() const;
	};
	
	#endif


程序11.5 mytime1.cpp

	#include <iostream>
	#include "mytime1.h"
	
	Time::Time()
	{
		hours = minutes = 0;
	}
	
	Time::Time(int h, int m)
	{
		hours = h;
		minutes = m;
	}
	
	void Time::AddMin(int m)
	{
		minutes += m;
		hours = minutes / 60;
		minutes = minutes % 60;
	}
	
	void Time::AddHr(int h)
	{
		hours += h;
	}
	
	void Time::Reset(int h, int m)
	{
		hours = h;
		minutes = m;
	}
	
	Time Time::operator+(const Time & t) const
	{
		Time sum;
		sum.minutes = minutes + t.minutes;
		sum.hours = hours + t.hours + sum.minutes / 60;
		sum.minutes %= 60;
		return sum;
	}
	
	void Time::Show() const
	{
		std::cout << hours << " hours, " << minutes << " minutes.";
	}


程序11.6 usetime1.cpp

	#include <iostream>
	#include "mytime1.h"
	
	int main()
	{
		using std::cout;
		using std::endl;
		Time planning;
		Time coding(2, 40);
		Time fixing(5, 55);
		Time total;
		
		cout << "planning time = ";
		planning.Show();
		cout << endl;
		
		cout << "coding time = ";
		coding.Show();
		cout << endl;
		
		cout << "fixing time = ";
		fixing.Show();
		cout << endl;
		
		total = coding + fixing;
		cout << "coding + fixing = ";
		total.Show();
		cout << endl;
	
		Time morefixing(3, 28);
		cout << "more fixing time = ";
		morefixing.Show();
		cout << endl;
		total = morefixing.operator+(total);
		cout << "morefixing.operator+(total) = ";
		total.Show();
		cout << endl;
		
		return 0;
	}


输出：

	planning time = 0 hours, 0 minutes.
	coding time = 2 hours, 40 minutes.
	fixing time = 5 hours, 55 minutes.
	coding + fixing = 8 hours, 35 minutes.
	more fixing time = 3 hours, 28 minutes.
	morefixing.operator+(total) = 12 hours, 3 minutes.


总之，operator+()函数的名称使得可以使用函数表示法或运算符表示法来调用它。编译器根据操作的类型来确定如何做：

	int a, b, c;
	Time A, B, C;
	c = a + b; //使用整型加法
	C = A + B; //使用为Time对象定义的加法

可以将两个以上的对象相加吗？例如，如果t1,t2,t3,t4都是Time对象，可以这样做吗：

	t4 = t3 + t2 + t1;

为回答这个问题，来看一些上述语句将如何被转换为函数调用。由于+是从左向右的运算符，因此上述语句首先被转换成如下这样：

	t4 = t3.operator+(t2+t1);

然后，函数参数本身被转换成一个函数调用，如下：

	t4 = t3.operator(t2.operator+(t1));

上述语句合法。函数调用t2.operator+(t1)返回一个Time对象，后者是t2和t1的和。然而，该对象成为函数调用t3.operator+()的参数，该调用返回t3与表示t2和t1之和的Time对象的和。总之，最后的返回值为t1、t2和t3之和。

### 11.2.2 重载限制

多数C++运算符都可以用这样的方式重载。重载的运算符（有些例外情况）不必是成员函数，但必须至少有一个操作数是用户定义的类型。下面详细介绍C++对用户定义的运算符重载的限制。

1. 重载后的运算符必须至少有一个操作数是用户定义的类型，这将防止用户为标准类型重载运算符。因此，不能将减法运算符(-)重载为计算两个doulbe值的和，而不是它们的差。虽然，这种限制将对创造性有所影响，但可以确保程序正常运行。
2. 使用运算符时不能违反运算符原来的句法规则。例如，不能将求模运算符(%)重载成使用一个操作数：
	
	int x;
	Time shiva;
	% x;     //错误语法
	% shiva; //错误语法

同样，不能修改运算符的优先级。因此，如果将加法运算符重载成为两个类相加，则新的运算符与原来的加号具有相同的优先级。

3. 不能创建新运算符。例如，不能定义operator$\ast\ast$()函数来表示求幂。
4. 不能重载下面的运算符：

- sizof sizeof运算符。
- . 成员运算符。
- .$\ast$ 成员指针运算符。
- :: 作用域解析运算符
- ?: 条件运算符
- typeid 一个RTTI运算符
- const_cast 强制类型转换运算符
- dynamic_cast 强制类型转换运算时
- reinterpret_cast 强制类型转换运算符
- static_cast 强制类型转换运算符

然而表11.1中所有的运算符都可以重载。

5. 表11.1中的大多数运算生都可以通过成员或非成员函数进行重载，但下面的运算符只能通过成员函数进行重载。

- = 赋值运算符
- () 函数调用运算符
- [] 下表运算符
- -> 通过指针访问类成员的运算符


表11.1 <center>可重载的运算符</center>

| + | - | * | / | % | ^ |
| & | $|$ | ~= | ! | = | < |
| > | += | -= | *= | /= | %= |
| ^= | &= | $|=$ | << | >> | >>= |
| <<= | == | != | <= | >= | && |
| $||$ | ++ | -- | , | ->* | -> |
| () | [] | new | delete | new[] | delete[] |

除了这些限制外，还应在重载运算符时遵循一些明智的限制。例如，不要将$\ast$运算符重载成交换两个Time对象的数据成员。表示法中没有任何内容可以表明交换对象的运算符完成的工作，因此最好定义一个其名称具有说明性的类方法，如Swap()。


### 11.2.3 其中重载运算符

还有一些其他的操作对Time类来说是有意义的。例如，可能要将两个时间相减或将时间乘以一个因子，这需要重载减法和乘法运算符。这和重载加法运算符的技术相同，即创建operator-()和operator$\ast$()方法。即，将下面的原型添加到类声明中：

	Time operator-(const Time & t) const;
	Time operator*(const Time & t) const;

程序11.7 mytime2.h

	#ifndef MYTIME2_H_
	#define MYTIME2_H_
	
	class Time
	{
	private:
		int hours;
		int minutes;
	public:
		Time();
		Time(int h, int m = 0);
		void AddMin(int m);
		void AddHr(int h);
		void Reset(int h = 0, int m = 0);
		Time operator+(const Time & t) const;
		Time operator-(const Time & t) const;
		Time operator*(double mult) const;
		void Show() const;
	};
	
	#endif


程序11.8 mytime2.cpp

	#include <iostream>
	#include "mytime2.h"
	
	Time::Time()
	{
		hours = minutes = 0;
	}
	
	Time::Time(int h, int m)
	{
		hours = h;
		minutes = m;
	}
	
	void Time::AddMin(int m)
	{
		minutes += m;
		hours = minutes / 60;
		minutes = minutes % 60;
	}
	
	void Time::AddHr(int h)
	{
		hours += h;
	}
	
	void Time::Reset(int h, int m)
	{
		hours = h;
		minutes = m;
	}
	
	Time Time::operator+(const Time & t) const
	{
		Time sum;
		sum.minutes = minutes + t.minutes;
		sum.hours = hours + t.hours + sum.minutes / 60;
		sum.minutes %= 60;
		return sum;
	}
	
	Time Time::operator-(const Time & t) const
	{
		Time diff;
		int tot1, tot2;
		tot1 = t.minutes + 60 * t.hours;
		tot2 = minutes +  60 * hours;
		diff.minutes = (tot2 - tot1) % 60;
		diff.hours = (tot2 - tot1) / 60;
		return diff;
	}
	
	Time Time::operator*(double mult) const
	{
		Time result;
		long totalminutes = (minutes + hours * 60) * mult;
		result.minutes = totalminutes % 60;
		result.hours = totalminutes / 60;
		return result;
	}
	
	void Time::Show() const
	{
		std::cout << hours << " hours, " << minutes << " minutes.";
	}



程序11.9 usetime2.cpp

	#include <iostream> 
	#include "mytime2.h"
	
	int main()
	{
		using std::cout;
		using std::endl;
		Time weeding(4, 35);
		Time waxing(2, 47);
		Time total;
		Time diff;
		Time adjusted;
	
		cout << "weeding time = ";
		weeding.Show();
		cout << endl;
		
		cout << "waxing time = ";
		waxing.Show();
		cout << endl;
	
		cout << "total work time = ";
		total = weeding + waxing;
		total.Show();
		cout << endl;
		
		cout << "weeding time - waxing time = ";
		diff = weeding - waxing;
		diff.Show();
		cout << endl;
	
		cout << "adjusted work time = " ;
		adjusted = total * 1.5;
		adjusted.Show();
		cout << endl;
		return 0;
	}

输出：

	[root@localhost ~]# g++ mytime1.cpp usetime2.cpp -o file
	[root@localhost ~]# ./file
	weeding time = 4 hours, 35 minutes.
	waxing time = 2 hours, 47 minutes.
	total work time = 7 hours, 22 minutes.
	weeding time - waxing time = 1 hours, 48 minutes.
	adjusted work time = 11 hours, 3 minutes.


## 11.3 友元

C++控制对类对象私有部分的访问。通常，公有类方法提供唯一的访问途径，但有时候这种限制太严格，以至于不适合特定的编程问题。在这种情况下，C++提供了另一种形式的访问权限：友元。友元有3种：

- 友元函数；
- 友元类；
- 友元成员函数。

通过让函数成员类的友元，可以赋予该函数与类成员函数相同的权限。下面介绍友元函数，其他两种友元将在15章介绍。

首先，介绍为何需要友元。在为类重载二元运算符符时（带有两个参数的运算符）常常需要友元。将Time对象乘以实数就属于这种情况。

在前面的Time类示例中，重载的乘法运算符与其他两种重载运算符的差别在于，它使用了两种不同的类型。即，加法和减法运算符都结合两个Time值，而乘法运算符将一个Time值与一个doulbe值结合在一起。这限制了该运算符的使用方式。记住，左侧的操作数是调用对象。即，下面的语句：

	A = B * 2.75;

被转换为下面的成员函数调用：

	A = B.operatore*(2.75);

但下面的语句又如何呢？

	A = 2.75 * B;

从概念上讲，2.75$\ast$B和B$\ast$2.75相同。当地一个表达是不对应与成员函数，因为2.75不是Time类型的对象。记住，左侧的操作数应是调用对象，但2.75不是对象。因此，编译器不能使用成员函数来替换该表达式。

解决这个难题的一种方式是，告知每个人(包括程序员自己)，只能按B$\ast$2.75这种格式编写，不能写成2.75$\ast$B。这是一种对服务器友好-客户警惕的解决方法，与OOP无关。

然而，另一中解决方式是__非成员函数__(记住，大多数运算符都可以通过成员函数或非成员函数来重载)。非成员函数不是由对象调用，它使用的所有制(包括对象)都是显式参数。这样，编译器能够将下面的表达式：

	A = 2.75 * B;

与下面的非成员函数调用匹配：

	A = operator*(2.75, B);

该函数的原型如下：

	Time operator(double m, const Time & t);

对于非成员重载运算符函数来说，运算符表达式左侧的操作数对应于运算符函数的第一个参数，运算符表达式右侧的操作数对应于运算符函数的第二个参数。而原来的成员函数则按相反的顺序处理操作数，即double值乘以Time值。

使用非成员函数可以按所需的顺序获取操作数，但引发一个问题：__非成员函数不能直接访问类的私有数据，至少常规非成员函数不能访问__。然而，有一类特殊的非成员函数可以访问类的私有成员，它们被成为友元函数。


### 11.3.1 创建友元函数

创建友元函数的第一步是将其原型放在类声明中，并在原型声明前加上__关键字friend__：

	friend Time operator*(double m, const Time & t);

该原型意味着如下两点：

- 虽然operator*()函数在类声明中声明的，但它不是成员函数，因此不能使用成员运算符来调用。
- 虽然operator*()函数不是成员函数，但它与成员函数的访问权限相同。

第二步，编写函数定义。因为它不是成员函数，所以不要使用Time::限定符。另外，不要再定义中使用关键字friend，定义应该如下：

	Time operator*(double m, const Time & t)
	{
		Time result;
		long totalminutes = t.hours * 60 * m + t.minutes * m;
		result.hours = totalminutes / 60;
		result.minutes = totalminutes % 60;
		return result;
	}


<center>友元是否有悖于OOP</center>

乍一看，可能会认为友元违反了OOP数据隐藏的原则，因为友元机制允许非成员函数访问私有数据。然而，这个观点太片面。相反，应将友元函数看作类的扩展接口的组成部分。只有类声明可以决定哪一个函数是友元函数，因此类声明仍然控制了哪些函数可以访问私有数据。总之，类方法和友元知识表达类接口的两种不同机制。

实际上，按上面的方式定义进行修改（交换乘法操作的顺序），可以将这个友元函数编写为非友元函数：

	Time operator*(double m, const Time & t)
	{
		return t * m;
	}


原来的版本显式地访问t.minutes和t.hours，所以他必须是友元。这个版本Time对象t作为一个整体使用，让成员函数来处理私有制，因此不必是友元函数。然而，将该版本作用友元也是一个好主意。最重要的是，他将该作为正式类接口的组成部分。其次，如果以后发现需要函数直接访问私有数据，则只要修改函数定义即可，而不必修改类原型。

__提示__：如果要为类重载运算符，并将非类的项作为其第一个操作数，则可以用友元函数来反转操作数的顺寻。


### 11.3.2 常用的友元： 重载<<运算符

一个很有用的特性，可以对<<运算符进行重载，使之能与cout一起来显式对象的内容。与前面介绍的示例相比，这个重载要复杂些，因为我们分两步来完成。

假设trip是一种Time对象。为显式Time的值，前面使用的是Show()。然而，如果可以像下面这样操作将更好：

	cout << trip;

之所以可以这样做，是因为<<是可被重载的C++运算符之一。实际上，它已经被重载很多次了。最初，<<运算符是C和C++的位运算符，将值中的位左移。ostream类对该运算符进行了重载，将其转换为一个输出工具。前面讲过，cout是一个ostream对象，它是智能的，能够识别所有的C++基本类型。这是因为对于每种基本类型，ostream中都包含了相应的重载的operator>>()定义。即，一个定义使用int参数，一个定义使用double参数等等。因此要使cout能够识别cout对象，一种方法是将一个新的函数运算符定义添加到ostream类声明中。但修改ostream文件是个危险的注意，这样做会在标准接口上浪费时间。相反，通过Time类声明来让Time类知道如何使用cout。

__1. <<的第一种重载版本__

要使Time类知道使用cout，必须使用友元函数。这是因为下面这样的语句使用两个对象，其中第一个是ostream对象(cout):

	cout << trip;

如果使用一个Time成员函数来重载<<,Time对象将是第一个操作数，就像使用成员函数重载$\ast$运算符那样。这意味着必须这样使用<<:

	trip << cout;

这样会令人迷惑。但通过使用友元函数，可以像下面这样重载运算符：

	void operator<<(ostream & os, const Time & t)
	{
		os << t.hours << " hours, " << t.minutes << " minutes";
	}

这样做可以使用下面的语句：

	cout << trip;


<center>友元还是非友元</center>

新的Time类声明使operator<<()函数成为Time类的一个友元函数。但该函数不是ostream类的友元。operator<<()函数接受一个ostream参数和一个Time参数，因此表面看起来它必须同时是两个类的友元。然而，看看代码会发现，尽管该函数访问了Time对象的各个成员，但从始至终都将ostream对象看作一个整体使用。因为operator<<()访问了Time对象的私有成员，所以它必须是Time的友元。但由于它并不直接访问ostream对象的私有成员，所以并不一定必须是ostream类的友元。这样，就不需要修改ostream的定义。

<center> 不知道其他ostream函数?</center>

另一个ostream对象是cerr，它将输出发送到标准输出流---默认为显示器，但在UNIX、Linux和Windows命令行环境中，可以标准错误流重定向到文件中。另外，第6章介绍的ofstream对象将输出写入文件中。通过继承(参见第13章），ofstream对象可以使用ostream的方法。这样，便可以使用operator<<()定义来将Time的数据写入到文件和屏幕上，为此，只需传递一个经过适当初始化的ofstream对象(而不是cout对象)。


调用cout << trip应使用cout对象本身，而不是它的拷贝，因此该函数按引用来传递该对象。这样，表达式cout<<trip将导致ost成员cout的一个别名；而表达式cerr<<trip将导致os成为cerr的一个别名。Time对象可以按值或按引用来创建，因为这两种形式都使函数能够使用对象的值。按引用传递使用的内存和时间都比按值传递少。

__2. <<的第二种重载版本__

前面介绍的实现存在一个问题。就像下面这样的语句可以正常工作：

	cout << trip;

但这种实现不允许像通常那样将重新定义的<<运算符与cout一起使用：

	cout << "Trip time: " << trip << "(Tuesday)\n"; //错误

要理解这样做不可行的原因及必须如何做才能使其可行，首先需要了解关于cout操作的一点知识。请看下面的语句：

	int x = 5;
	int y = 8;
	cout << x << y;

C++从左到右读取输出语句，意味着它等同于：

	(cout << x) << y;

正如iostream中定义的那样，<<运算符要求左边是一个ostream对象。显然，因为cout是ostream对象，所以表达式cout<<x满足这种要求。然而，因为表达式cout<<x位于<<y的左侧，所以输出语句也要求该表达式是一个ostream对象。因此，ostream类将operator<<()函数实现为返回一个指向ostream对象的引用。具体说，它返回一个指向调用对象(这里是cout)的引用。因此，表达式(cout<<x)本身就是ostream对象cout，从而可以位于<<运算符的左侧。

可以对友元函数采用相同的方法。只要修改operator<<()函数，让它返回ostream对象的引用即可：

	ostream & operator<<(ostream & os, const Time & t)
	{
		os << t.hours << " hours. " <<  t.minutes << " minutes.";
		return os;
	}

注意返回类型是ostream &。这意味着该函数返回ostream对象的引用。因为函数开始执行时，程序传递了一个转向引用给它，这样做的最终结果时，函数的返回值就是传递给它的对象。及，下面的入局：

	cout << trip;

将被转换为下面的调用：

	operator<<(cout, trip);

而该调用函数返回cout对象。因此，下面的语句可以正常工作：

	cout << "Trip time: " << trip << "(Tuesday)\n";

有趣的是，这个operator<<()版本还可用于将输出写入文件中：

	#include <fstream>
	ofstream fout;
	fout.open("savetime.txt");
	Time trip(12, 40);
	fout << trip;

其中最后一条语句将转换为这样：

	operator<<(fout, trip);

另外，正如第8章指出的，类继承属性让ostream引用能够指向ostream对象和ofstream对象。

__提示：__一般来说，要重载<<运算符来显式c_name对象，可使用一个友元函数，其定义如下：

	ostream & operator<<(ostream & os, const c_name & obj)
	{
		os << ...;
		return os;
	}


程序11.10列出了修改后的类定义，其中包括operator*()和operator<<()这两个友元函数。他将一个友元函数作为内联函数。

程序11.10 mytime3.h

	#ifndef MYTIME3_H_
	#define MYTIME3_H_
	
	#include <iostream>
	class Time
	{
	private:
		int hours;
		int minutes;
	public:
		Time();
		Time(int h, int m = 0);
		void AddMin(int m);
		void AddHr(int h);
		void Reset(int h = 0, int m = 0);
		Time operator+(const Time & t) const;
		Time operator-(const Time & t) const;
		Time operator*(double mult) const;
		friend Time operator*(double mult, const Time & t)
			{ return t * mult;}
		friend std::ostream & operator<<(std::ostream & os, const Time & t);
	};
	
	#endif


程序11.11 mytime3.cpp

	#include <iostream>
	#include "mytime3.h"
	
	Time::Time()
	{
		hours = minutes = 0;
	}
	
	Time::Time(int h, int m)
	{
		hours = h;
		minutes = m;
	}
	
	void Time::AddMin(int m)
	{
		minutes += m;
		hours = minutes / 60;
		minutes = minutes % 60;
	}
	
	void Time::AddHr(int h)
	{
		hours += h;
	}
	
	void Time::Reset(int h, int m)
	{
		hours = h;
		minutes = m;
	}
	
	Time Time::operator+(const Time & t) const
	{
		Time sum;
		sum.minutes = minutes + t.minutes;
		sum.hours = hours + t.hours + sum.minutes / 60;
		sum.minutes %= 60;
		return sum;
	}
	
	Time Time::operator-(const Time & t) const
	{
		Time diff;
		int tot1, tot2;
		tot1 = t.minutes + 60 * t.hours;
		tot2 = minutes +  60 * hours;
		diff.minutes = (tot2 - tot1) % 60;
		diff.hours = (tot2 - tot1) / 60;
		return diff;
	}
	
	Time Time::operator*(double mult) const
	{
		Time result;
		long totalminutes = (minutes + hours * 60) * mult;
		result.minutes = totalminutes % 60;
		result.hours = totalminutes / 60;
		return result;
	}
	
	std::ostream & operator<<(std::ostream & os, const Time & t)
	{
		os << t.hours << " hours, " << t.minutes << "minutes.";
		return os;
	}


程序11.12 usetime3.cpp

	#include <iostream>
	#include "mytime3.h"
	
	int main()
	{
		using std::cout;
		using std::endl;
		Time aida(3, 35);
		Time tosca(2, 46);
		Time temp;
			
		cout << "Aida and Tosca: \n";
		cout << aida << "; " << tosca << endl;
		temp = aida + tosca;
		cout << "Aida + Tosca: " << temp << endl;
		temp = aida * 1.17;
		cout << "Aida * 1.17: " << temp << endl;
		cout << "10.0 * Tosca: " << 10.0 * tosca << endl;
	
		return 0;
	}


输出：
	
	Aida and Tosca: 
	3 hours, 35minutes.; 2 hours, 46minutes.
	Aida + Tosca: 6 hours, 21minutes.
	Aida * 1.17: 4 hours, 11minutes.
	10.0 * Tosca: 27 hours, 40minutes.



## 11.4 重载运算符：作为成员函数还是非成员函数


对于很多运算符来说，可以选择使用成员函数或非成员函数来实现运算符重载。一般来说，非成员函数应该是友元函数，这样它才能直接访问类的私有数据。例如，Time类的加法运算符在Time类声明中的原型如下：

	Time operator+(const Time & t) const;

这个类也可以使用下面的原型：

	friend Time operator(const Time &t1, const Time & t2);

加法运算符需要两个操作数。对于成员函数版本来说，一个操作数通过this指针隐式地传递，另一个通过操作数作为函数参数显式地传递；对于友元版本来说，两个操作数都作为参数传递。

记住两个格式必须选择一种格式，而不能同时选择两种格式。因为这两种格式都与同一个表达式匹配，同时定义这两种格式将被视为二义性，导致编译错误。

## 11.5 再谈重载：一个矢量类

描述矢量需要两个数：

- 可以用大小(长度)和方向(角度)描述矢量；
- 可以用分量x和y表示矢量。

程序11.13列出了这个类的声明。为复习名称空间，该清单见类声明放在VECTOR名称空间中。另外，该程序使用枚举创建了两个常量(RECT和POL)，用于标识两种表示法。


程序11.13 vector.h

	#ifndef VECTOR_H_
	#define VECTOR_H_
	
	#include <iostream>
	namespace VECTOR
	{
		class Vector
		{	
		public:
			enum Mode {RECT, POL};
		private:
			double x;
			double y;
			double mag;
			double ang;
			Mode mode;
			void set_mag();
			void set_ang();
			void set_x();
			void set_y();
		public:
			Vector();
			Vector(double n1, double n2, Mode form = RECT);
			void reset(double n1, double n2, Mode form = RECT);
			~Vector();
			double xval() const {return x;}
			double yval() const {return y;}
			double magval() const {return mag;}
			double angval() const {return ang;}
			void polar_mode();
			void rect_mode();
			Vector operator+(const Vector & b) const;
			Vector operator-(const Vector & b) const;
			Vector operator-() const;
			Vector operator*(double n) const;
			friend Vector operator*(double n, const Vector & a);
			friend std::ostream & operator<<(std::ostream & os, const Vector & v);
		};
	}
	
	#endif

注意，程序中4个报告分量值的函数是在类中声明的，因此自动成为内联函数。

程序14利用名称空间的开放性，将方法定义添加到VECTOR名称空间中。另外，C++的内置数学函数在使用角度时以弧度为单位，所以函数在度和弧度之间进行转换。

程序11.14 vector.cpp

	#include <iostream>
	#include <cmath>
	#include "vector.h"
	
	using std::sqrt;
	using std::sin;
	using std::cos;
	using std::atan;
	using std::atan2;
	using std::cout;
	
	namespace VECTOR
	{
		const double Rad_to_deg = 45.0 / atan(1.0);
		
	
		//private methods;
		void Vector::set_mag()
		{
			mag = sqrt(x * x + y * y);
		}
		
		void Vector::set_ang()
		{
			if (x == 0.0 && y == 0.0)
				ang = 0.0;
			else
				ang = atan2(y, x);
		}
	
		void Vector::set_x()
		{
			x = mag * cos(ang);
		}
	
		void Vector::set_y()
		{
			y = mag * sin(ang);
		}
	
		//public methods;
		Vector::Vector()
		{
			x = y = mag = ang = 0.0;
			mode = RECT;
		}
	
		Vector::Vector(double n1, double n2, Mode form)
		{
			mode = form;
			if (form == RECT)
			{
				x = n1;
				y = n2;
				set_mag();
				set_ang();
			}
			else if (form == POL)
			{
				mag = n1;
				ang = n2 / Rad_to_deg;
				set_x();
				set_y();
			}
			else
			{
				cout << "Incorrect 3rd argument to Vector() -- ";
				cout << "vector set to 0\n";
				x = y = mag = ang = 0.0;
				mode = RECT;
			}
		}
		
		void Vector::reset(double n1, double n2, Mode form)
		{
	                mode = form;
	                if (form == RECT)
	                {
	                        x = n1;
	                        y = n2;
	                        set_mag();
	                        set_ang();
	                }
	                else if (form == POL)
	                {
	                        mag = n1;
	                        ang = n2 / Rad_to_deg;
	                        set_x();
	                        set_y();
	                }
	                else
	                {
	                        cout << "Incorrect 3rd argument to Vector() -- ";
	                        cout << "vector set to 0\n";
	                        x = y = mag = ang = 0.0;
	                        mode = RECT;
	                }
	        }
		
		Vector::~Vector()
		{}
	
		void Vector::polar_mode()
		{
			mode = POL;
		}
	
		void Vector::rect_mode()
		{
			mode = RECT;
		}
	
		Vector Vector::operator+(const Vector & b) const
		{
			return Vector(x + b.x, y + b.y);
		}
	
		Vector Vector::operator-(const Vector & b) const
		{
			return Vector(x - b.x, y - b.y);
		}
	
		Vector Vector::operator-() const
		{
			return Vector(-x, -y);
		}
	
		Vector Vector::operator*(double n) const
		{
			return Vector(x*n ,y*n);
		}
	
		Vector operator*(double n, const Vector & a)
		{
			return a * n;
		}
	
		std::ostream & operator<<(std::ostream & os, const Vector & v)
		{
			if (v.mode == Vector::RECT)
				os << "(x, y) = (" << v.x << ", " << v.y  << ")";
			else if (v.mode == Vector::POL)
			{
				os << "(m, a) = (" << v.mag << ", " << v.ang << ")";
			}
			else
				os << "Vector object mode is invalid!";
			return os;
		}
	}


reset方法并非必不可少。假设shove是一个Vector对象，您编写了如下代码：

	shove.reset(100, 300);

可以使用构造函数来得到相同的结果：

	shove = Vector(100, 300);

然而，reset()直接修改shove的内容，而使用构造函数将增加额外的步骤：创建一个临时对象，然后将其赋值给shove。

### 11.5.1 使用状态成员

Vectore类存储了矢量的直角坐标和极坐标。它使用名为mode的成员来控制使用构造函数、reset()方法和重载operator<<()函数使用哪种形式，其中枚举RECT表示直角坐标模式(默认值)，POL表示极坐标模式。这样的成员被称为状态成员，因为这种成员描述的是对象所处的状态。

由构造函数的代码可知，第三个参数RECT或省略了，则将输入解释为直角坐标；如果为POL，则将输入解释为极坐标：

	Vector folly(3.0, 4.0);
	Vector foolery(20.0, 30.0, VECTOR::Vector::POL);

标识符POL的作用域为类，因此类定义可使用未限定的名称。但全限定名为VECTOR::Vector::POL，因为POL是在Vector类中定义的，而Vector是在名称空间VECTOR中定义的。

operator<<()函数也是用模式来确定如何显式值。由于operator<<()是一个友元函数，而不在作用域内，因此必须使用Vector::RECT，而不能直接使用RECT。因为友元函数在名称空间VECTOR中，因此无需使用全限定名VECTOR::Vector::RECT。

### 11.5.2 为Vector类重载算术运算符

在使用x,y坐标时，将两个矢量相加将非常简单，只要将两个x分量相加，得到最终的x分量，将两个y分量相加得到y分量即可。根据描述，可以使用如下代码：

	Vector Vector::operator+(const Vector & b) const
	{
		Vector sum;
		sum.x = x + b.x;
		sum.y = y + b.y;
		return sum;
	}

如果对象只存储x和y分量，则这很好。遗憾的是，上述代码无法设置极坐标值。可以通过添加另外一些代码来解决这个问题：

		Vector Vector::operator+(const Vector & b) const
	{
		Vector sum;
		sum.x = x + b.x;
		sum.y = y + b.y;
		sum.set_ang(sum.x, sum.y);
		sum.set_mag(sum.x, sum.y);
		return sum;
	}

然而，使用构造函数来完成这种工作，将更简单，更可靠：

	Vector Vector::operator+(const Vecotr & b) const
	{
		return Vector(x + b.x, y + b.y);
	}

上述的代码将新的x和y分量传递给Vector构造函数是，而后者将使用这些值来创建无名的新对象，并返回这个对象的副本。这确保了新的Vector对象是根据构造函数的标准规则创建的。

__提示：__如果方法通过计算得到一个新的类对象，则应考虑是否可以使用类构造函数来完成这种工作。

__1. 乘法__

将矢量与一个数相乘，将使该矢量加长或缩短。因此，将矢量乘以3得到的矢量的长度为原来的三倍，而角度不变。要在Vector类中实现矢量的这种行为很容易。对于极坐标，只要将长度进行伸缩，并保持角度不变；对于直角坐标，只需要将x和y分量进行伸缩。

	Vector  Vector::operator*(double n) const
	{
		return Vector(x * n, y * n);
	}

和重载加法一样，上述代码允许构造函数使用新的x和y分量来创建正确的Vector对象。上述函数用于处理Vector值和double相乘。可以像Time示例那样，使用一个内联友元函数来处理double与Vector相乘：

	friend Vector Vector::operator*(double n, const Vector & a) 
	{
		return a * n;
	}


__2. 对已重载的运算符进行重载__

在C++中，-(负号)运算符有两种函数。首先，使用两个操作数，它是减法运算符。其次，使用一个操作数时，它是负号运算符。对于矢量来说，这两种操作都是有意义的，因此Vector类有这两种操作。

要从矢量A中减去矢量B，只要将分量相减即可，因此重载减法与重载加法相似。

操作的顺序非常重要。下面的语句：
	
	diff = v1 - v2;
	
将转换为下面的成员函数调用：

	diff = v1.operator-(v2);

这意味着将从隐私矢量参数减去显式参数创建的矢量。

接下来，看一元负号运算符，它只有一个操作数。下面时定义：

	Vector Vector::operator-() const
	{
		return Vector(-x, -y);
	}

现在，operator-()有两种不同的定义，这是可行的，因为它们的特征标(参数)不同。

__11.5.3 对于实现的说明__

Vector类中，所有接口都只要能够显示这两种表示，并返回各个值。内部实现可以完全不同。因此，可以只存储x和y分量，而返回矢量的长度的magval()方法可以根据x和y的值来计算出长度，而不是查找对象中存储的这个值。这种方法改变了实现，但用户接口没有改变。将接口和实现分离时OOP的目标之一。

这两种实现各有利弊。存储数据意味着对象将占据更多的内存；但查找数据的速度比较快。如果应用经常访问矢量的这两种表示，则这个例子采用的实现比较合适。


__11.5.4 使用Vector来模拟随机漫步__

程序11.15是一个小程序，它使用了Vector类。该程序模拟了著名的醉鬼走路问题。实际上，醉鬼被认为是一个有许多健康问题的人，而不是大家娱乐消遣的谈资，因此这个问题通常被成为随机漫步问题。其意思，将一个领到街灯柱下。这个人开始走动，但没走一部的方向都是随机的。这个问题的一种表述时，这个人走到离灯柱50英尺的地方需要多少步。从矢量的角度，这相当于不断将方向随机的矢量相加，直到长度超过50英尺。

程序11.15 randwalk.cpp
	
	#include <iostream>
	#include "vector.h"
	#include <cstdlib> // rand(), srand()
	#include <ctime> // time()
	
	int main()
	{
		using namespace std;
		using VECTOR::Vector;
		srand(time(0));
		double direction;
		Vector step;
		Vector result(0.0, 0.0);
		unsigned long steps = 0;
		double target;
		double dstep;
		cout << "Enter target distance (q to quit):";
		while (cin >> target)
		{
			cout << "Ener step length: ";
			if (!(cin >> dstep))
				break;
			while(result.magval() < target)
			{
				direction = rand() % 360;
				step.reset(dstep, direction, Vector::POL);
				result = result + step;
				steps++;
			}
			cout << "After " << steps << "steps, the subject "
				"has the following location:\n";
			cout << result << endl;
			result.polar_mode();
			cout << "or\n" << result << endl;
			cout << "Average outward distance per step = "
				<< result.magval() / steps << endl;
			steps = 0;
			result.reset(0.0, 0.0);
			cout << "Enter target distance (q to quit):";
		}
		cout << "Bye!\n";
		cin.clear();
		while (cin.get() != '\n')
			continue;
		return 0;
	}


输出：

	Enter target distance (q to quit):100
	Ener step length: 5
	After 246steps, the subject has the following location:
	(x, y) = (85.9784, 54.1236)
	or
	(m, a) = (101.596, 0.56183)
	Average outward distance per step = 0.41299
	Enter target distance (q to quit):200     
	Ener step length: 5
	After 1087steps, the subject has the following location:
	(x, y) = (-57.2303, 193.208)
	or
	(m, a) = (201.506, 1.85877)
	Average outward distance per step = 0.185378
	Enter target distance (q to quit):q
	Bye!

__程序说明__

首先，需要指出的时，在程序11.15中使用VECTOR名称空间非常方便。下面using声明使Vector类的名称可用：

	using VECTOR::Vector;

接下来谈谈随机数。标准ANSI C库(C++也有)中有一个rand()函数，它返回一个从0到某个值(取决于实现)之间的随机数。该程序使用求模运算来获得一个0~359的角度值。

rand()函数将一种算法用于一个初始种子值来获得随机数，该随机数用在下一次函数调用的种子，以此类推。这些数实际上使伪随机数，因此10次连续的调用通常生成10个同样的随机数。然而，srand()函数允许覆盖默认的种子值，重新启动另一个随机序列。该程序使用time(0)的返回值来设置种子。time(0)返回当前时间，通常从某一个日期开始的秒数。因此，下面的语句在每次运行程序时，都将设置不同的种子，是随机数看上去更为随机：

	srand(time(0));

头文件cstdlib包含了srand()和rand()的原型，而ctime包含了time()的原型。

顺便说一句，在将一系列位置存储到文件中很容易。首先包含头文件fstream，声明一个ofstream对象，将其同一个文件关联起来：

	#include <fstream>

	ofstream fout;
	fout.open("thewalk.txt");

	four << resutl << endl;

这将调用友元函数operator<<(fout, result)，导致引用参数os指向fout，从而将输出写入文件中。

## 11.6 类的自动转换和强制转换

本节讨论如何处理用户定义类型的转换。首先，复习一下C++如何处理内置类型转换的。将一个标准类型变量的值赋给另一个标准类型的变量时，如果这两种类型兼容，则C++自动将这个值转换为接收变量的类型。例如，下面的语句都将导致类型转换：

	long count = 8;
	double time = 11;
	int side = 3.33;

上述赋值语句是可行的，因为在C++看来，各种数据类型都表示相同的东西----一个数字，同时C++包含用于转换的内置规则。这些转换将降低精度。

C++语言不自动转换不兼容的类型。例如，下面的语句是非法的，因为左边是指针类型，而右边是数字：

	int *p = 10;

虽然计算机内部可能使用证数来表示地址，但从概念上，整数和指针完全不同。例如，不能计算指针的平方。然而，在无法自动转换是，可以使用强制类型转换：

	int *p = (int *) 10;

上述语句将10强制转换为int指针类型，将指针设置为地址10。

可以将类定义成为与基本类型或另一个类相关，使得从一个类型转换为另一个类型是有意义的。在这种情况下，程序员可以指示C++如何自动进行转换，或通过强制类型转换来完成。

为了说明这个如何进行的，我们将第3章的磅转换为英石的程序改写为类形式。首先，设计一个合适的类型，我们基本上是以两种方式(磅和英石)来表示重量。对于在一个实体中包含一个概念的两种表示来说，类提供了一种非常好的方式。因此可以将重量的两种方法表示放在同一个类中，然后提供以这两种方式表达重量的类方法。

程序11.16 stonewt.h

#ifndef STONEWT_H_
#define STONEWT_H_

	class Stonewt
	{
	private:
		enum {Lbs_per_stn = 14};
		int stone;
		double pds_left;
		double pounds;
	public:
		Stonewt(double lbs);
		Stonewt(int stn, double lbs);
		Stonewt();
		~Stonewt();
		void show_lbs() const;
		void show_stn() const;
	
	};
	
	#endif

对于定义特定的常量来说，如果它们是整数，enum提供了一种方便的途径。也可以采用下面的方法：

	static const int Lbs_per_stn = 14;

Stonewt有3个构造函数，让您能够将Stonewt对象初始化为一个浮点数（单位为磅）或两个浮点数（分别代表英石和磅）。也可以创建Stonewt对象，而不进行初始化。

Stonewt提供了两个显示函数，一个以磅为单位显示重量，另一个以英石和磅为单位显示重量。

程序11.17sthonewt.cpp


	#include <iostream>
	#include "stonewt.h"
	using std::cout;
	
	Stonewt::Stonewt(double lbs)
	{
		stone = int (lbs) / Lbs_per_stn;
		pds_left = int (lbs) % Lbs_per_stn;
		pounds = lbs;
	}
	
	Stonewt::Stonewt(int stn, double lbs)
	{
		stone = stn;
		pds_left = lbs;
		pounds = stn * Lbs_per_stn + lbs;
	}
	
	Stonewt::Stonewt()
	{
		stone = pounds = pds_left = 0;
	}
	
	Stonewt::~Stonewt()
	{}
	
	void Stonewt::show_stn() const
	{
		cout << stone << " stone, " << pds_left << " pounds\n";
	}
	
	void Stonewt::show_lbs() const
	{
		cout << pounds << " pounds\n";
	}

因为Stonewt对象表示一个重量，所以可以提供一些将整数或浮点数转换为Stonewt对象的方法。我们已经这样做了！在C++中，接受一个参数的构造函数为将类型与该参数相同的值转换提供了蓝图。因此，下面的构造函数用于将double类型转换为Stonewt类型：

	Sthonewt(double lbs);

即，可以编写如下代码：

	Stonewt myCat;
	myCat = 19.6;

程序将使用构造函数Stonewt(double)来创建一个临时的Stonewt对象，并将19.6作为初始化值。随后，采用逐成员赋值方式将该临时对象的内容赋值到myCat中。这一过程成为隐式转换，因为它是自动进行的，而不需要显式强制类型转换。

__只有接受一个参数的构造函数才能作为转换函数。__下面的构造函数有两个参数，因此不能用来转换类型：

	Stonewt(int stn, double lbs);

然而，如果给第二个参数提供默认值，它便可用于转换int:
	
	Stonewt(int stn, double lbs = 0);

将构造函数用作自动类型转换函数似乎是一项不错的特定。然而，当程序员拥有更丰富的C++经验时，将发现这种自动特性并非总是合乎需要的，因为这会导致意外的类型转换。因此，C++增加了__关键字explicit用于关闭这种自动特性__。即，可以这样声明构造函数：

	explicit Stonewt(double lbs);

这将关闭上述示例中介绍的隐式转换，但仍然允许显式转换，即显式强制转换：

	Stonewt myCat;
	myCat = 19.6; //错误语法
	myCat = (Stonewt)19.6;
	myCat = Stonewt(19.6);

__注意：__只接受一个参数的构造函数时定义了从参数类型到类类型的转换。如果使用关键字explicit限定了这种构造函数，则它智能用于显式转换，否则也可以用于隐式转换。

编译器在什么时候将使用Stonewt(doule)函数呢？如果在声明中使用了关键字explicict，则Stonewt(doule)将只用于显式强制类型转换，否则还可以用于下面的隐式转换：

- 将Stonewt对象初始化为double值时。
- 将double值赋给Stonewt对象时。
- 将double值传递给接受Stonewt参数的函数时。
- 返回值被声明为Stonewt的函数试图返回double值时。
- 在上述任意一种情况下，使用可转换double类型的内置类型时。

下面详细介绍最后一点。函数原型化提供的参数匹配过程，允许使用Stonewt(doule)构造函数来转换其他数字类型。即，下面两条语句都首先将int转换为double，然后使用Stonewt(double)构造函数。

	Stonewt Jumbo(7000);
	Jumbo = 73000;

然而，当且仅当转换不存在二义性时，才会进行这种二步转换。即，如果这个类还定义了构造函数Stonewt(long)，则编译器将拒绝这些语句，可能指出：int可被转换为long或double，因此调用存在二义性。

程序11.18使用类的构造函数初始化一些Stonewt对象，并处理类型转换。


程序11.18 stone.cpp


	#include <iostream>
	#include "stonewt.h"
	using std::cout;
	void display(const Stonewt & st, int n);
	
	int main()
	{
		Stonewt incognito = 275;
		Stonewt wolfe(287.5);
		Stonewt taft(21, 8);
		
		cout << "The celebrity weighed ";
		incognito.show_stn();
		cout << "The detective weighed ";
		wolfe.show_stn();
		cout << "The President weighed ";
		taft.show_lbs();
		incognito = 276.8;
		taft = 325;
		cout << "After dinner, the celebrity weighed ";
		incognito.show_stn();
		cout << "After dinner , the President weighed ";
		taft.show_lbs();
		display(taft, 2);
		cout << "The wrestler weighed even more.\n";
		display(422, 2);
		cout << "No stone left unearned\n";
		return 0;
	}
	
	void display(const Stonewt & st, int n)
	{
		for (int i = 0; i < n; i++)
		{
			cout << "Wow! ";
			st.show_stn();
		}
	}


输出：

	The celebrity weighed 19 stone, 9 pounds
	The detective weighed 20 stone, 7 pounds
	The President weighed 302 pounds
	After dinner, the celebrity weighed 19 stone, 10 pounds
	After dinner , the President weighed 325 pounds
	Wow! 23 stone, 3 pounds
	Wow! 23 stone, 3 pounds
	The wrestler weighed even more.
	Wow! 30 stone, 2 pounds
	Wow! 30 stone, 2 pounds
	No stone left unearned

__程序说明__

当构造函数只接受一个参数是，可以使用下面的格式来初始类对象：

	Stonewt incognito = 275;

这等价于前面介绍过的另外两种格式：

	Stonewt incognito(275);
	Stonewe incognito = Stonewt(275);

然而，后面两种格式可用于接受多个参数的构造函数。

接下来，注意下面两条赋值语句：

	incognito = 276.8;
	taft = 325;

第一条赋值语句使用接受double参数的构造函数，将276.8转换为一个Stonewt值，这将把incognito的pounds成员设置为276.8。因为该条语句使用了构造函数，所以还将设置stone和pds_left成员。同样，第二条赋值语句将一个int值转换为double类型，然后使用Stonewt(double)来设置全部3个成员。

最后，注意下面的函数调用：

	display(422, 2);

display()的原型表明，第一个参数应是Stonewt对象(Stonewt和Stonewt&形参都与Stonewt实参匹配)。遇到int参数时，编译器查找构造函数Stonewt(int)，以便将该int转换为Stonewt类型。由于没有找到这样的构造函数，因此编译器寻找接受其他内置类型的构造函数。Stonewt(double)构造函数满足这种要求，因此编译器将int转换为double，然后使用Stonewt(double)将其转换为一个Stonewt对象。

### 11.6.1 转换函数

程序是将数字转换为Stonewt对象。可以做相反的转换吗？即，是否可以将Stonewt对象转换为double值。如下：

	Stonewt wolfe(285.7);
	double host = wolfe;

可以这样做，但不是使用构造函数。__构造函数只用于从某种类型到类类型的转换。__要进行相反的转换，必须使用特殊的C++运算符函数---转换函数。

转换函数是用户定义的强制类型转换，可以像使用强制类型转换那样使用它。例如，如果定义了从Stonewt到double的转换函数，就可以使用如下的转换：

	Stonewt wolfe(285.7);
	double host = double (wolfe);
	double thinker = (double) wolfe;

也可以让编译器来决定如何做：

	Stonewt wells(20, 3);
	double star = wells;

编译器发现，右侧是Stonewt类型，而左侧是double类型，因此它将查看程序员是否定义了于此匹配的转换函数。

创建转换函数，要转换为typeName类型，需要使用如下形式的转换函数：

	operator typeName();

注意以下几点：

- 转换函数必须是类方法；
- 转换函数不能指定返回类型；
- 转换函数不能有参数；


例如，转换为double类型的函数的原型如下：

	operator double();

要添加将Stonewt转换为int类型和double类型的函数，需要将下面的原型添加到类声明中：

	operator int();
	operator double();

程序11.19列出了修改后的类声明：

程序11.19 stonewt1.h

	#ifndef STONEWT1_H_
	#define STONEWT1_H_
	
	class Stonewt
	{
	private:
		enum {Lbs_per_stn = 14};
		int stone;
		double pds_left;
		double pounds;
	public:
		Stonewt(double lbs);
		Stonewt(int stn, double lbs);
		Stonewt();
		~Stonewt();
		void show_lbs() const;
		void show_stn() const;
		operator int();
		operator double();
	};
	
	#endif

程序11.20对两个转换函数进行了定义。注意，虽然没有声明返回类型，这两个函数也就返回所需的值。另外，int转换将带转换的值进行四舍五入为最接近的整数，而不是去掉小鼠部分。例如，如果pounds为114.4，则pounds+0.5等于114.9，int(114.9)等于114。但是，如果pounds为114.6，则pounds+0.5是115.1，而int(115.1)为115。

程序11.20 stonewt1.cpp

	#include <iostream>
	#include "stonewt1.h"
	using std::cout;
	
	Stonewt::Stonewt(double lbs)
	{
		stone = int (lbs) / Lbs_per_stn;
		pds_left = int (lbs) % Lbs_per_stn;
		pounds = lbs;
	}
	
	Stonewt::Stonewt(int stn, double lbs)
	{
		stone = stn;
		pds_left = lbs;
		pounds = stn * Lbs_per_stn + lbs;
	}
	
	Stonewt::Stonewt()
	{
		stone = pounds = pds_left = 0;
	}
	
	Stonewt::~Stonewt()
	{}
	
	void Stonewt::show_stn() const
	{
		cout << stone << " stone, " << pds_left << " pounds\n";
	}
	
	void Stonewt::show_lbs() const
	{
		cout << pounds << " pounds\n";
	}
	
	Stonewt::operator int()
	{
		return int (pounds + 0.5);
	}
	
	Stonewt::operator double()
	{
		return pounds;
	}

程序11.21对新的转换函数进行测试。

程序11.21 stone1.cpp

	#include <iostream>
	#include "stonewt1.h"
	
	int main()
	{
		using std::cout;
		Stonewt poppins(9, 2.8);
		double p_wt = poppins;
		cout << "Convert to double = ";
		cout << "Poppins: " << p_wt << " pounds.\n";
		cout << "Convert to int = ";
		cout << "Poppins: " << int(poppins) << " pounds.\n";
		return 0;
	}

输出：

	Convert to double = Poppins: 128.8 pounds.
	Convert to int = Poppins: 129 pounds.


__自动应用类型转换__

程序11.21中将int(poppins)和cout一起使用。假设省略了显式强制类型转换：

	cout << "Poppins: " << poppins << " pounds.\n";

程序将无法进行隐式转换。因为没有指出应转换为int类型还是doubl类型。在缺少信息时，编译器将指出，程序中使用了二义性转换。

如果类只定义了double转换函数，则编译器将接受该语句。这是因为只有一种转换可能，因此不存在二义性。

负责的情况与此类似。对于当前的类深埋来说，编译器将认为下面的语句有二义性而拒绝它：

	long gone = poppins; //存在二义性

在C++中，int和double值都可以给赋值给long变量，所以编译器使用任意一个转换函数都是合法的。然而，如果删除了这两个转换函数之一，编译器将接受这条语句。

当类定义了两种或多种的转换是，仍可以使用显式强制类型转换来指出使用哪个转换函数。可以使用下面任何一种强制类型转换表示法：

	long gone = (double) poppins;
	long gone = int (poppins);

和转换构造函数一样，转换函数也有其优缺点。提供自动、隐式转换的函数所存在的问题是：在用户不希望进行转换时，转换函数也可能进行转换。如下：

	int ar[20];
	Stonewt temp(14, 4);
	int Temp = 1;
	cout << ar[temp] << endl;

原则上，最后使用显式转换，而避免隐式转换。在C++11中，可以使用explicit来关闭隐式转换：

	class stonewt	
	{
	...
		explicit operator int() const;
		explicit operator double() const;
	};

有了上述声明，需要强制转换时将调用这些运算符。

另一种方法是，用一个功能相同的非转换函数替换该转换函数即可，但仅在被显式地调用时，该函数才会执行。即：

	Stonewt::operator int(0 {return int (pounds + 0.5);}
	
可以替换为：

	int Stonewt::Stone_to_int() {return int (pounds + 0.5);}

这样，下面的语句是非法的：

	int plb = poppins;

但如果确实需要这种转换，可以这样做：

	int plb = poppins.Stone_to_int();

总之，C++为类提供了下面的类型转换：

- 只有一个参数的类构造函数用于将类型与该参数相同的值转换为类类型。
- 被成为转换函数的特殊成员运算符函数，用于将类对象转换为其他类型。

__注意__：explicit关键字可以关闭以上两种隐私类型转换，但仍可以进行显式强制类型转换。

### 11.6.2 转换函数和友元函数

在讨论Time类时指出过，可以使用成员函数或友元函数来重载加法。处于简化的目的，假设没有定义operator double()转换函数，可以使用下面的成员函数实现加法：

	Stonewt Stonewt::operator+(const Stonewt & st) const
	{
		double pds = pounds + st.pounds;
		Stonewt sum(pds);
		return sum;
	}

也可以将加法作为友元函数来实现，如下：

	Stonewt operator+(const stonewt & st1, const stonewt & st2)
	{
		double pds = st1.pounds + st2.pounds;
		Stonewt sum(pds);
		return sum;
	}

注意，可以提供友元函数定义或成员函数定义，但不能都提供。上面任何一种格式都允许如下操作：

	Stonewt jennySt(9, 12);
	Stonewt bennySt(12, 8);
	Stonewt total = jennySt + bennySt;

另外，如果提供了Stonewt(double)构造函数，则可以这样做：

	Stonewt jennySt(9, 12);
	double kennyD = 176.0;
	Stonewt total = jennySt + kennyD;

但只有友元函数才允许如下这样做：

	Stonewt jenneySt(9, 12);
	double pennyD = 146.0;
	Stonewt total = pennyD + jennySt;

这样因为：

	Stonewt total = pennyD + jennySt;

只能被转换为：

	total = operator+(pennyD, jennySt);

第一个参数pennyD为double类型，因此调用构造函数Stonewt(double)，将它转换为Stonewt对象。

然而不能调用成员函数将jennySt和peenyD相加。将加法语法转换为函数将类似于下面这样：

	total = pennyD.operator+(jennySt);

这没有意义，因为只有类对象才可以调用成员函数。C++不会试图将pennyD转换为Stonewt对象。

__将对成员函数参数进行转换，而不是调用成员函数的对象。__

__经验告诉我们，将加法定义为友元可以让程序更容易适应自动类型转换。原因在于，两个操作数都成为函数参数，因此与函数原型匹配。__

__实现加法时的选择__


要将double量和Stonewt量相加，有两种选择。第一种方法是将下面的的函数定义为友元函数，让Stonewt(double)构造函数将double类型的参数转换为Stonewt类型的参数：

	operator+(const Stonewt &, const Stonewt &);

第二种方法是，将加法运算符重载为一个显式使用doulbe类型参数的函数：

	Stonewt operator+(double x);
	friend Stonewt operator+(double x, Stonewt & s);

这样，下面的语句将与成员函数operator+(doulbe)完全匹配：

	total = jennySt + kennyD;

而下面的语句将于友元函数operator+(double, Stonewt &)完全匹配：

	total = kennyD + jennySt;

每次中方法都有其优点。第一种方法使程序更简洁，因为定义的函数较少。缺点是，每次需要转换时，都将调用转换构造函数，这增加时间和内存开销。第二种方法刚好相反，使得程序较长，但运行速度快。


## 11.7 总结

访问私有成员：

- 友元函数： friend
- 成员函数

运算符重载： operator+()

类型转换：

- 自动类型转换
- 强制类型转换

关闭隐私类型转换：explicit

转换构造函数: 只有一个参数的构造函数，或者有默认值参数的构造函数
转换函数： operator int()


	






	