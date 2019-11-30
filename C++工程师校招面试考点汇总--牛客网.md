---
title: C++工程师校招面试考点汇总--牛客网
date: 2019-11-25 11:44:14
tags: C++
mathjax: true
---

学习牛客网总结的C++面试考点笔记，转自： [牛客网链接](https://www.nowcoder.com/discuss/164721?type=0&order=0&pos=16&page=0)

<!--more-->

[TOC]

# 基础知识

## 基本语言

### static关键字的作用

- 变量：存储在静态区
  - static全局变量：在定义该变量源文件内有效，而普通的全局变量在各个源文件都是有效的，未初始化时，自动初始化为0
  - static局部变量：只在当前函数中起作用，未初始化时，自动初始化为0
- 函数：函数的定义和声明默认是extern，但是静态函数旨在当前的文件可见，在其他文件不可见
  - 注意：不要在头文件中声明static函数，因为这样在其他源文件中是不可见的；不要在源文件中声明非static函数，因为这样其他源文件就可见了。
- 类
  - 类成员变量
    - 所有的对象共用一份，可以用来计算对象创建的个数
    - 实现了多个对象之间的数据共享
    - 存储在静态区，sizeof()时不包含静态变量的大小
    - 静态变量只能在类外定义和初始化
  - 类成员函数
    - 不能使用this指针
    - 对静态成员变量和成员函数的引用不需要用对象名
    - 在静态成员函数的实现中不能直接引用类中的非静态成员，可以引用类中的静态成员
    - 如果要在静态成员函数中引用非静态函数，可以通过对象来引用
    - 调用静态成员函数的格式如下：<类名>::<静态成员函数名>(参数)。

### C++和C的区别

- 设计思想上，C++是面向对象的语言，C是面向过程的结构化编程语言

- 语法上

  - C++具有封装、多态、继承三种特性
  - C++相比C，增加了许多类型安全的功能，比如强制类型转换
  - C++支持繁星编程，比如模板类、函数模板等
  - C中for中不能定义变量，但是C++可以在for定义变量
  - C中只可以malloc申请内存，C++可以通过malloc和new申请
  - .....

### C++中的4中cast转换

`const_cast`：用于将const类型转换成非const类型

`static_cast`：用于各种隐式转换，比如非const转const，void *转指针等，`static_cast`能用于多态向上转换，如果向下转能成功但是不安全，结果未知。

`dynamic_cast`：用于动态类型转换，**只能用于含有虚函数的类**，用于类层次之间的向上和向下转换，只能转指针或引用，如果是非法的对于指针返回NULL，对于引用跑出异常。它通过判断在执行该语句的时候变量的**运行时**类型和要转换的类型是否相同来判断是否能够向下转换。

`reinterpret_cast`：几乎什么都可以转，比如将int转成指针，可能会出问题，尽量少用。底层是重新翻译二进制实现的。

向上转换：子类 转向 基类

向下转换：基类 转向 子类

**为什么不使用C语言的强制类型转换？**

注意：C的强制类型转换表面上看起来功能强大什么都能转，但是转化不够明确，不能进行错误检查，容易出错。

### C/C++中的指针和引用

- 指针有自己的一块空间，而引用只是一个别名
- 使用sizeof看一个指针的大小是4，而引用则是被引用对象的大小
- 指针可以被初始化为NULL，而引用必须被初始化且必须是一个已有对象的引用；
- 作为参数传递时，指针需要被解引用才可以对对象进行操作，而直接对引用的修改都会改变引用所指向的对象；
- 指针在使用中可以指向其他对象，但是引用只能只是一个对象的引用，不能被改变；
- 指针可以多多级指针(`**p`)，而引用只有一级；
- 指针和引用使用++运算符的意义不一样；
- 如果返回动态内存分配的对象或内存，必须使用指针，引用可能引起内存泄露。

### 给定三角形ABC和一点P(x,y,z)，判断点是否在ABC内，给出思路并手写代码

根据面积法，如果P在三角形内，那么三角形ABP的面积+三角形ACP的面积+三角形BCP的面积=ABC的面积。算法如下：

```c++
#include "pch.h"
#include <iostream>
const float ABC_FLOAT = 0.0001;
struct Point {
	float x;
	float y;
};
float GetTriangleArea(const Point pt0, const Point pt1, const Point pt2)
{
	float area = 0.0;
	Point AB, BC;
	AB.x = pt1.x - pt0.x;
	AB.y = pt1.y - pt0.y;
	BC.x = pt2.x - pt1.x;
	BC.y = pt2.y - pt1.y;
	area = fabs(AB.x * BC.y - AB.y * BC.x) / 2.0f;
	return area;
}

bool IsInTriangle(const Point A, const Point B, const Point C, const Point D)
{
	float SABC, SADB, SBDC, SADC;
	SABC = GetTriangleArea(A, B, C);
	SADB = GetTriangleArea(A, D, B);
	SBDC = GetTriangleArea(B, D, C);
	SADC = GetTriangleArea(A, D, C);
	float Sum =  SADB + SBDC + SADC;
	
	if (abs(Sum - SABC) < ABC_FLOAT)
		return true;
	else
		return false;
}
 
int main()
{
	Point A, B, C, D;
	A.x = 0, A.y = 6;
	B.x = 0, B.y = 0;
	C.x = 6, C.y = 0;
	D.x = 2, D.y = 2;
	std::cout << IsInTriangle(A, B, C, D) << std::endl;
}
```

### 怎么判断一个数是二的倍数，怎么求一个数中有几个1，说一下你的思路

判断一个数是不是2的倍数，即判断该数二进制末尾是不是0：
$$
a \% 2 == 0 \\
or \space a \& 0x0001 == 0
$$
求一个数中1的位数，可以直接逐位除十取余判断：

```c++
int fun(long x)
{
    int count = 0;
    while(x)
    {
        if (x % 10 == 1)
        {
            ++count;
            x /= 10;
        }
    }
    return count;
}
```

### C++中的四个智能指针

智能指针的作用是管理一个指针，因为存在以下这种情况：申请的空间在函数结束时忘记释放，造成内存泄漏。使用智能指针可以很大程度上的避免这个问题，因为智能指针就是一个类，类会自动调用析构函数，析构函数会自动释放资源。所以智能指针的作用原理就是在函数结束时自动释放内存空间，不需要手动释放内存空间。

- `auto_ptr`: 采用所有权模式。

```c++
auto_ptr<string> p1(new string("hello"));
auto_ptr<string> p2;
p2 = p1; //不会报错
```

此时不会报错，p2剥夺了p1的所有圈，但是程序运行时访问p1将会报错，所以`auto_ptr`的缺点是：存在潜在的内存崩溃问题。



- `unique_ptr`：实现独占式拥有或严格拥有概念，保证同一时间只有一个智能指针可以指向该对象。它对于避免资源写了特别有用。

采用所有权模：

```c++
unique_ptr<string> p3 (new string("auto"));
unique_ptr<string> p4;
p4 =p3; //报错
```

编译器认为p4=p3非法，避免了p3不再指向有效有数据的问题，因此`unique_ptr`比`auto_ptr`更安全。

另外`unique_ptr`还有更聪明的地方：当程序试图将一个`unique_ptr`赋值给另一个时，如果源`unique_ptr`是个临时右值，编译器允许这样做；如果源`unique_ptr`将存在一段时间，编译器禁止这么做，比如：

```c++
unique_ptr<string> p3 (new string("auto"));
unique_ptr<string> p4;
p4 =p3;                                     //#1 报错
unique_ptr<string> p2;
p2 = unique_ptr<string>(new string("unique"));  //#2 允许
```

其中#1留下悬挂的unique_ptr(pu1)，这可能导致危害。而#2不会留下悬挂的unique_ptr，因为它调用 unique_ptr 的构造函数，该构造函数创建的临时对象在其所有权让给 pu3 后就会被销毁。这种随情况而已的行为表明，unique_ptr 优于允许两种赋值的auto_ptr 。

注：如果确实想执行类似与#1的操作，要安全的重用这种指针，可给它赋新值。C++有一个标准库函数std::move()，让你能够将一个unique_ptr赋给另一个。例如：

```c++
unique_ptr<string> ps1, ps2;
ps1 = demo("hello");
ps2 = move(ps1);
ps1 = demo("alexia");
cout << *ps1 << *ps2 << endl;
```



- shared_ptr`: shared_ptr实现共享式拥有概念。多个智能指针可以指向相同对象，该对象和其相关资源会在“最后一个引用被销毁”时候释放。

从名字share就可以看出资源可以被多个指针工地，它使用计数机制来表明资源被几个指针共享。可以通过成员函数`use_count()`来查看资源的所有者个数。除了可以通过new来构造，还可以通过传入`auto_ptr,unique_ptr`,

`weak_pt`来构造。当我们调用release()时,当前指针释放资源所有权,计数减一.当计数等于0时,资源会被释放.

成员函数:

- use_count返回引用计数的个数
- unique返回释放独占所有权(use_count为1)
- swap交换两个share_ptr对象(即交换拥有的对象)
- reset放弃内部对象的所有权或拥有对象的变更,会引起原有对象的引用计数的减少
- get返回内部对象(指针),由于已经重载了()方法,因此和直接使用对象是一样的。比如`shared_ptr<int> sp(new int(1));` sp和sp.get()是等价的.



- `weak_ptr`是一种不控制对象生命周期的智能指针,它指向一个`shared_ptr`管理的对象,进行该对象的内存管理的那个强引用的`shared_ptr`,`weak_ptr`只是提供了对管理对象的一个访问手段.

weak_ptr 设计的目的是为配合 shared_ptr 而引入的一种智能指针来协助 shared_ptr 工作, 它只可以从一个 shared_ptr 或另一个 weak_ptr 对象构造, 它的构造和析构不会引起引用记数的增加或减少。weak_ptr是用来解决shared_ptr相互引用时的死锁问题,如果说两个shared_ptr相互引用,那么这两个指针的引用计数永远不可能下降为0,资源永远不会释放。它是对对象的一种弱引用，不会增加对象的引用计数，和shared_ptr之间可以相互转化，shared_ptr可以直接赋值给它，它可以通过调用lock函数来获得shared_ptr。

```c++
class B;
class A
{
public:
    shared_ptr<B> pb_;
    ~A()
    {
    	cout<<"A delete\n";
    }
};
class B
{
public:
    shared_ptr<A> pa_;
    ~B()
    {
   	 	cout<<"B delete\n";
    }
};
void fun()
{
    shared_ptr<B> pb(new B());
    shared_ptr<A> pa(new A());
    pb->pa_ = pa;
    pa->pb_ = pb;
    cout<<pb.use_count()<<endl;
    cout<<pa.use_count()<<endl;
}
int main()
{
    fun();
    return 0;
}
```

可以看到fun函数中pa ，pb之间互相引用，两个资源的引用计数为2，当要跳出函数时，智能指针pa，pb析构时两个资源引用计数会减一，但是两者引用计数还是为1，导致跳出函数时资源没有被释放（A B的析构函数没有被调用），如果把其中一个改为weak_ptr就可以了，我们把类A里面的shared_ptr pb_; 改为weak_ptr pb_; 运行结果如下，这样的话，资源B的引用开始就只有1，当pb析构时，B的计数变为0，B得到释放，B释放的同时也会使A的计数减一，同时pa析构时使A的计数减一，那么A的计数为0，A得到释放。

注意的是我们不能通过weak_ptr直接访问对象的方法，比如B对象中有一个方法print(),我们不能这样访问，pa->pb_->print(); 英文pb_是一个weak_ptr，应该先把它转化为shared_ptr,如：shared_ptr p = pa->pb_.lock(); p->print();

### 数组和指针的区别

----

|                             指针                             |                 数组                 |
| :----------------------------------------------------------: | :----------------------------------: |
|                        保存数据的地址                        |               保存数据               |
| 间接访问数据，首先获得指针的内容，然后将其作为地址，从该地址中提取数据 |             直接访问数据             |
|                    通常用于动态的数据结果                    | 通常用于固定数目且数据类型相同的元素 |
| 通过malloc分配内存，free释放内存(new，delete, new [], delete []) |           隐式的分配和删除           |
|                通常指向匿名数据，操作匿名函数                |            自身即为数据名            |

### 野指针

野指针就是指向一个已删除对象的或者未申请访问首先内存区域的指针。

摘录：https://www.cnblogs.com/asking/p/9460965.html

野指针指向了一块随机内存空间，不受程序控制。如指针指向已经被删除的对象或者指向一块没有访问权限的内存空间，之后如果对其再解引用的话，就会出现问题。

**野指针产生的原因：**

- 指针定义时未初始化：指针在被定义的时候，如果程序不对其进行初始化的化，它会指向随机区域，因为任何指针变量（除了static修饰的指针变量）在被定义的时候是不会被置空的，他的默认值是随机的。

- 指针被释放时没有被置空：在用malloc开辟内存空间时，要检查返回值是否为空，如果为空，则申请失败；如果不为空，则指针指向的是申请的内存空间的首地址。指针指向的内存空间在用free()或者delete（注意delete只是一个操作符，而free()是一个函数）释放后，如果程序员没有对其置空或者其他的赋值操作，就会使其成为一个野指针。

- 指针操作超过变量作用域：不要返回指向栈内存的指针或引用，因为栈内存在函数结束的时候会被释放。示例：

  ```c++
  class A 
  {
      public:
      void Func(void)
      { 
          cout << “Func of class A” << endl; 
      }
  };
  
  class B
  {
      public:
      A *p;
      void Test(void)
      {
          A a;
          p = &a; // 注意 a 的生命期 ，只在这个函数Test中，而不是整个class B
      }
      void Test1() 
      {
          p->Func(); // p 是“野指针”
      }
  };
  ```
  

​       函数 Test1 在执行语句 p->Func()时，p 的值还是 a 的地址，对象 a 的内容已经被清除，所以 p 就成了“野指针” 。

**野指针的危害：**

 指针指向的内存已经无效了，而指针没有被置空，解引用一个非空的无效指针是一个未被定义的行为，也就是说一定导致段错误，野指针很难定位到是哪里出问题，在哪里这个指针就失效了，不好查找出错的原因。所以调式起来就会很麻烦，有时候会需要很长的时间。

 **规避方法：**
- 初始化指针时将其置为NULL，之后再对其进行操作
- 释放指针时将其置为NULL，最好编写代码时将free()函数封装一下，在调用free()后将其指针置为NULL
- 要想彻底地避免野指针，最好的办法就是养成一个良好的编程习惯。

### 介绍一下C++中的智能指针

智能指针主要用于管理在堆上分配的内存，它将普通的指针封装成一个栈对象。当张对象的生存周期结束后，会在析构函数中释放掉申请的内存，从而防止内存泄露。

C++11中最常用的智能指针类型为`shared_ptr`，它采用引用计数的方法，记录当前内存资源被多少个智能指针引用。该引用计数的内存在堆上分配当新增一个引用计数加1，当过期时引用计数减1。只有当引用计数为0时，智能指针才会自动释放引用的内存资源。

对`shared_ptr`进行初始化时，不能将一个普通指针付给智能指针，因为一个是指针，一个是类。可以通过`make_shared`函数或通过构造函数传输普通指针。并可以通过`get`函数获得普通指针。

### 智能指针内存泄露问题

**第一个问题智能指针存在内存泄露吗？**

存在！

当两个对象相互使用一个`shared_ptr`成员变量指向对方，会造成循环引用，使引用计数失效，从而导致内存泄露。例如：

```c++
class Child;
class Parent {
private:
	std::shared_ptr<Child>  ChildPtr;
public:

	void setChild(std::shared_ptr<Child> child) {
		this->ChildPtr = child;
	}

	/*void doSomething() {
		if (this->ChildPtr.use_count()) {
			
		}
	}*/
	~Parent() {}
};

class Child {
private:
	std::shared_ptr<Parent>  ParentPtr;
public:
	void setParent(std::shared_ptr<Parent> parent) {
		this->ParentPtr = parent;
	}

	/*void doSomething() {
		if (this->ParentPtr.use_count()) {

		}
	}*/
	~Child() {}
};

int main() {
	std::weak_ptr<Parent> wpp;
	std::weak_ptr<Child> wpc;
	{
		std::shared_ptr<Parent> p(new Parent);
		std::shared_ptr<Child> c(new Child);

		p->setChild(c);
		c->setParent(p);
		wpp = p;
		wpc = c;
		std::cout << p.use_count() << std::endl;  //2
		std::cout << c.use_count() << std::endl;  //2
	}
	std::cout << wpp.use_count() << std::endl;  //1
	std::cout << wpc.use_count() << std::endl;  //1
	return 0;
}

```

上述代码中，parent有一个一个`shared_ptr`类型的成员指向孩子，而child也有一个`shared_ptr`类型成员指向父亲。然后再创建孩子和父亲对象时也使用了智能指针c和p，随后将c和p分别又赋给child的智能指针成员parent和parent的智能指针成员child，从而形成了循环引用。

**第二个问题，怎么解决智能指针的内存泄露问题？**

为了解决循环引用导致的内存泄露，引入了`weak_ptr`弱指针，`weak_ptr`的构造函数不会修改引用计数的值，从而不会对对象的内存进行管理，其类似一个普通指针，但不指向引用计数的共享内存，但是其可以检测到所管理的对象释放已经被释放，从而避免非法访问。

### C++中的引用和指针

定义：

- 引用：C++是C语言的继承，它可进行过程化程序设计，又可进行抽象数据类型为特点的基于对象的程序设计，还可以进行以继承和多态为特点的面向对象的程序设计。引用就是C++对C语言的重要扩充。引用就是某一变量的一个别名，对引用的操作与对变量直接操作完全一样。引用的声明方法：&引用名=目标变量名;引用引入了对象的一个同义词。定义引用的表示方法与定义指针的相似，只是用&替代了*。
- 指针：指针利用地址，它的值直接指向存在电脑存储器中另一个地方的值。由于通过地址能找到所需的变量单元，可以说，地址指向该变量单元。因此，将地址形象化为“指针”。意思是通过它能找到以它为地址的内存单元。

区别：

- 可以有const指针，但是没有const引用
- 指针在使用中可以指向其他对象，但是引用只能是一个对象的引用，不能被改变
- 指针和引用使用++运算符的意义不一样
- 如果返回动态内存分配的对象或内存，必须使用指针，引用可能引起内存泄露
- 指针有自己的一块空间，而引用只是一个别名
- 使用sizeof看一个指针的大小是4，而引用则是被引用对象的大小
- 指针可以被初始化为NULL，但是引用必须被初始化且必须是一个已有对象的引用
- 作为参数传递时，指针需要解引用才可以对对象进行操作，而直接对引用的修改都会改变引用所指向的对象
- 指针可以有多级指针，而引用只有一级

### 为什么析构函数必须是虚函数？为什么C++默认的析构函数不是函数

将可能会被继承的父类的析构函数声明为虚函数，可以保证当我们new一个子类的时候，然后指向基类指针指向子类（多态）对象，释放基类指针时可以释放掉子类的空间，防止内存泄露。

C++的默认析构函数不是虚函数，因为虚函数需要额外的虚函数表和徐知政，占用额外的内存。而对于不会被继承的类来说，其析构函数如果是虚函数，就会浪费内存。因此C++默认的析构函数不是虚函数，而是只有当需要当作父类时，设置为虚函数。

### 函数指针

函数指针是指向函数的指针变量。

函数指针本身首先是一个指针变量，该指针变量指向一个具体的函数。这正如用指针变量可指向整型变量、字符型、数组一样，这里是指向函数。

C在编译时，每一个函数都有一个入口地址，该入口地址就是函数指针所指向的地址。有了指向函数的指针变量后，可用该指针变量调用函数，就如同指针变量引用其他类型变量一样，在这些概念上是大体一致的。

调用函数和做函数的参数，比如回调函数。

示例：

```c++
char * fun(char *p){}   //函数fun
char * (*pf)(char * p)  //函数指针pf
pf = fun;               //函数指针pf指向函数fun
pf(p);                  //通过函数指针pdf调用函数fun
```

### fork函数

fork: 创建一个和当前进程映像一样的进程可以通过fork()系统调用：

```c++
#include <sys/types.h>
#include <unistd.h>

pid_t fork(void);
```

成功调用forK()会创建一个新的进程，他几乎与调用fork()的进程一摸一样，这两个进程都会继续运行。在子进程中，成功的fork()调用会返回0。在父进程中fork()返回子进程的pid。如果出错，fork()返回一个负值。

最常见的fork()用法是创建一个新的进程，然后使用exec()载入二进制映像，替换当前进行的映像。这种情况下，派生（fork）了新的进程，而这个子进程会执行一个新的二进制可执行文件的映像。这种**派生加执行**的方式是很常见的。

在早前的Unix系统中，创建进程比较原始。当调用fork()时，内核会把所有的内部数据结构复制一份，复制进程的页表项，然后把父进程的地址空间中的内容逐页复制到子进程的地址空间中。但从内核角度来说，逐页的复制方式是十分耗时的。

现在的Unix系统采取了更多的优点，例如Linux，采用了写时复制（Copy on Write）的方式，而不是对父进程空间进程整体复制。

### C++中析构函数的作用

析构函数与构造函数对应，当对象结束其声明周期，如对象所在的函数调用完毕时，系统会自动执行析构函数。

析构函数名也应与类名相同，只是在函数名前面加一个位取反符~，例如~stud()，以区别构造函数。它不带任何参数，也没有返回值（报考void类型）。只能有一个析构函数，不能重载。

如果用户没有编写析构函数编译系统会自动生成一个缺省的析构函数（即便自定义了析构函数，编译器总是会为我们合成一个析构函数，并且如果自定义了一个析构函数，并且编译器在执行时会先调用自定的析构函数再调用合成的析构函数），它也不进行任何操作。所以许多简单的类没有用显式的析构函数。

如果一个类中有指针，且在使用过程中动态的申请了内存，那么最好显示构造函数在销毁类之前，释放掉申请的内存空间，避免内存泄露。

类析构顺序：1）派生类本身的析构函数；2）对象成员析构函数；3）基类析构函数

类构造顺序：1）基类构造函数；2）对象成员构造函数；3）派生类本身的构造函数

注意：

- 基类构造函数如果有多个基类则构造函数的调用顺序是某类在类派生表中出现的顺序而不是它们在成员初始化表中的顺序；
- 成员类对象构造函数如果有多个成员类对象则构造函数的调用顺序是对象在类中被声明的顺序而不是它们出现在成员初始化表中的顺序。

- 父类的构造函数必须是参数列表形式的
- 多继承时，调用顺序取决于class D: public Base2, public Base1, public Base的顺序，也就是先调用Base2,再Base1，再Base。但是有虚继承的时候，虚继承的构造函数是最优先被调用的。

### 静态函数和虚函数

静态函数在编译的时候就已经确定运行时机；

虚函数在运行的时候动态绑定。

虚函数因为采用了虚函数表机制，调用的时候会增加一次内存开销。

### 重载和覆盖

重载：两个函数名相同，但是参数列表不同（个数、类型），返回值类型没有要求，在同一作用域中

重写：子类继承了父类，父类中的函数是虚函数，在子列中重新定义了这个函数，这种情况是重写

### strcpy和strlen

strcpy是字符串拷贝函数，原型：

```c++
char * strcpy(char * dest, const char * src);
```

从src逐字节拷贝到dest，直到遇到‘\0’结束。如果输入的是字符数组，并且结尾数组中没有'\0'，就会导致拷贝越界，造成缓冲区溢出漏洞。

strlen函数是计算字符串长度的函数，返回从开始到‘\0’之间的字符个数。

### 虚函数和多态

多态主要分为静态多态和动态多态。静态多态主要是重载，在编译的时候就已经确定；动态多态是用续航的机制实现的，在运行期间绑定。举个例子，一个父类类型的指针指向一个子类对象时候，使用父类的指针去调用子类中重写的父类中的虚函数的时候，会调用子类重写过后的函数，在父类中声明为加了virtual关键字的函数，在子类中重写时候不需要加virtual也是虚函数。

在有虚函数的类中，类的最开始部分是一个虚函数表的指针，这个指针指向一个虚函数表，表中放了虚函数的地址，实际的虚函数在代码段中。当子类继承了父类的时候野指继承其虚函数表，当子类重写父类中虚函数的时候会将其继承到的虚函数表表的地址替换为重新写的函数地址。使用了虚函数，会增加访问内存开销，降低效率。

### ++i和i++的区别和实现

区别：++i先增加1，然后返回；i++先返回i，然后i增加1。

实现：

++i实现：

```c++
int& int::operator++()
{
    *this = *this + 1;
    return *this;
}
```

i++实现：

```c++
const int int::operator(int)
{
    int oldValue = *this;
    ++(*this);
    return oldValue;
}
```

从效率上++i的效率高于i++，因为i++不仅调用了++i，还有其他操作。

### 写个函数在main函数之前先执行

在控制台程序中，main函数是用户定义的执行入口点，当程序编译成功之后，链接器（Linker）会将mainCRTStartup连接到exe中，exe执行时，一开始先mainCRTStartup，这是因为程序在执行时会调用各种各样的运行时库函数，因此执行前必须要初始化好运行时库，mainCRTStartup函数会负责相应的初始化工作，他会完成一些C全局变量以及C内存分配等函数的初始化工作，如果使用C++编程，还要执行全局类对象的构造函数。最后，mainCRTStartup才调用main函数。

```c++
__attribute((constructor))void before()
{
    printf("before main\n");
}
```

详细介绍见：https://blog.csdn.net/youyou519/article/details/82703448

### 有一个段代码修改其中一个字符的前提下，使得代码输出20个"Hello"

原始代码：

```c++
for (int i = 0; i < 20; i--)
    cout << "Hello" << endl;
```

答案：

```c++
for (int i = 0; i + 20; i--)
    cout << "Hello" << endl;
```

### const修饰成员函数的目的

const修饰的成员函数表明函数调用不会对对象做出任何修改，事实上，如果确认不会对对象做更改，就应该为函数加上const限定，这样无论const对象还是普通对象都可以调用该对象。

下面总结了const对象和非const对象是否可以调用const函数和非const函数：

---

|             | const对象 | 非const对象 |
| ----------- | --------- | ----------- |
| const函数   | 可以      | 可以        |
| 非const函数 | 不可以    | 可以        |

### C++里怎么定义常量？常量存放在什么位置？

常量C++里的定义就是一个top-level const加上对象类类型，常量定义必须初始化。

对于局部变量，常量存放在栈区，对于全局变量，常量存放在全局/静态存储区。对于字面值常量，常量存放在常量存储区。

### 四行代码的区别 const char * arr = "123"; char * brr = "123"; const char crr[] = "123"; char drr[] = "123";

```c++
const char * arr = "123";
//字符串123保存在常量区，const本来是修饰arr指向的值不能通过arr去修改，但是字符串“123”在常量区，本来就不能改变，所以加不加const效果一样

char * brr = "123";
//字符串123保存在常量区，跟arr指针指向的是一个地址，童话不能通过brr区修改“123”

const char crr[] = "123";
//这里123本来是存放在栈上的，但是编译器可能会做某些优化，将其放到常量区

char drr[] = "123";
//字符串123保存在战区，可以通过drr去修改
```

### 常量指针和指针常量

```c++
const char * p;  //常量指针
char const * p; //两者意义相同，表示p指向地址空间的内容不能被修改

char * const p; //指针常量，表示p所指的地址不能变
```

名字记法：哪个在前面先说谁。比如`const char * p`;` const`在前头，所以是常量指针。而对于 `char * const p;` ,`*`在前后，所以说指针常量。

### shared_ptr的实现

参考：https://github.com/anbo225/shared_ptr

核心要理解引用计数，什么时候销毁底层指针，还有赋值，拷贝构造时候的引用计数的变化，析构的时候要判断底层指针引用计数为0了才能真正释放底层指针的内存。

```C++
//SharePtr.h
#ifndef sharedPtr_h
#define sharedPtr_h

#include <iostream>
using namespace std;

template <typename T>
class SharedPtr {
private:
	T *ptr; //指向实际共享对象
	int * use_count; //引用计数，注意次数类型为int *

public:

	SharedPtr() : ptr((T*)0), use_count(0) {}

	SharedPtr(const SharedPtr<T> &orig) : ptr(orig.ptr), use_count(&(++*orig.use_count)) {
		cout << "copy constructor: " << *ptr << "\t refCount=" << *use_count << endl;
	}

	SharedPtr(T * p) : ptr(p), use_count(new int(1)) {
		cout << "create object: " << *ptr << "\t refCount=1" << endl;
	}

	SharedPtr<T>& operator=(const SharedPtr<T> &rhs) {
		if (&rhs != this) {
			++*rhs.use_count;
			if (--*use_count == 0) {
				cout << "in function operator= . delete " << *ptr << endl;
				delete ptr;
				delete use_count;
			}
			ptr = rhs.ptr;
			use_count = rhs.use_count;
			cout << "in function operator= . " << *ptr << "\t refCount=" << *use_count << endl;
			return *this;
		}
		return *this;
	}

	T operator*() {
		if (use_count == 0)
			return (T*)0;
		return *ptr;
	}

	T * operator->() {
		if (use_count == 0)
			return 0;
		return ptr;
	}

	~SharedPtr() {
		if (ptr && --*use_count == 0) {
			cout << *ptr << "\t refCount = 0. delete the ptr." << *ptr << endl;
			delete ptr;
			delete use_count;
		}
	}

	int getCount() {
		return *use_count;
	}
};

#endif
```

```c++
#include <iostream>
#include "SharedPtr.h"
#include <string>
using namespace std;

int main()
{
	SharedPtr<string> pstr(new string("first object"));
	SharedPtr<string> pstr2(pstr);
	SharedPtr<string> pstr3(new string("second object"));
	pstr3 = pstr2;
}

```

