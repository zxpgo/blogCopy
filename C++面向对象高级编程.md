---
title: C++面向对象高级编程
date: 2019-08-06 11:44:14
tags: C++
mathjax: true
---

介绍C++类之间的关系，以及C++类的一些重要知识点。<!--more-->

# C++历史

- B语言（1969）
- C语言（1972）
- C++语言（1983）

C++的演化：

- C++98(1.0)
- C++03(TR1, Technical Report 1)
- C++11(2.0)
- C++14

C++包括：C++语言和C++标准库两部分



# C++语言

C语言中：数据类型，函数来处理数据，数据是全局的

![](https://raw.githubusercontent.com/zxpgo/images/master/img/20190821212435.png)

C++的class将数据和处理数据的函数包装在一起。C++的class基本等同于struct，之间有一些微小的差别。

C++的类可以分为两类：

- 类中带有指针：string
- 类中不带指针：complex

C++程序代码基本形式：

![](https://raw.githubusercontent.com/zxpgo/images/master/img/20190821213350.png)

## 类的组成

头文件的防卫式声明：

```c++
#ifndef __COMPLEX__
#define __COMPLEX__
//....
#endif
```

成员函数在class中定义，便自动成为inline，但是最后是不是inline函数，由编译器决定。 

 构造函数一般放在public中，但是有一个种特殊情况，可以把构造函数放在private中，对应设计模式——单例模式。

```C++
class A{
public:  
    static A & getInstance();
    void setup() {...}
private:
    A();
    A(const A & rhs);
    ...
};

A & A::getInstance(){
    static A a;
    return a;
}


A::getInstance().setup();
```

## 常量成员函数

函数后面加一个const，表明这个函数不能修改类中的数据成员。比如一个函数只是返回某个数据成员，那么就不要修改数据。

```C++
double real () const {
    return re;
}
```

const出现在对象或变量之前，表明这个对象或变量的内容不能修改。

```C++
const complex c1(2,1);//调用的函数也必须是const的，否则无法调用
cout << c1.real();
cout << c2.imag();

complex c2(3,1); //可以调用const成员函数和普通函数
cout << c2.real();
cout << c2.imag();
```

## 参数传递

值传递和引用传递

```c++
complex(double r = 0, double i = 0) // passy by value
		: re(r), im(i) {}
complex & operator += (const complex &); //pass by reference to const 
ostream & operator <<(ostream &os, const complex &x); //ostream & os : pass by reference
```

值传递需要把数据放入栈中，尽量不要值传递。在C语言的时候，传地址。在C++中，用传引用更好。引用在底部就是一个指针，相等于传指针。

## 返回值传递

值传递和引用传递

函数的局部变量不能当作引用传递返回值。因为当函数结束时，局部变量的内存会释放，在函数外部的引用找不到变量所在的内存。

## friend(友元)

友元函数可以使用类中的私有成员变量。

```c++
friend complex & __doapl(complex *, const complex &);

inline complex & __doapl (comeplex * ths, const complex & r){
    ths->re += r.re;
    ths->im += r.im;
    return *ths;
}
```

相同class的各个objects互为friends(友元)。

```C++
class complex{
public:
    complex(double r = 0, double i = 0) 
		: re(r), im(i) {}
    int func(const complex & param){
        return param.re + param.im;
    }
private:
   	double re, im;
};

//测试
complex c1(2,1);
complex c2；
c2.func(c1);
```

## 操作符重载

### 成员函数

```c++
inline complex & __doapl (comeplex * ths, const complex & r){
    ths->re += r.re;
    ths->im += r.im;
    return *ths;
}
inline complex & complex::operator +=(const complex & r){
    return __doapl(this, r);
}
inline complex & complex::operator +=(this, const complex & r){//所有成员函数隐含一个this指针，位置可能是第一个参数，也可能是最后一个参数，这都是编译器的实现
    return __doapl(this, r);
}

complex c1(2,1);
complex c2(5);
c2 += c1;
```

任何成员函数都有一个隐含的this指针，指向调用该函数的对象。

操作符重载的返回值不能是void，而必须是complex &的原因是，为了进行连串操作，比如：

```C++
c3 += c2 += c1;
```

### 非成员函数

```c++
//为了应付三种可能的用法
inline complex operator + (const complex & x, const complex & y) {
	return complex(real(x) + real(y), imag(x) + imag(y));
}

inline complex	operator +(const complex & x, double y) {
	return complex(real(x) + y, imag(y));
}

inline complex operator + (double x, const complex & y) {
	return complex(x + real(y), imag(y));
}

complex c1(1,2);
complex c2(2,3);

inline complex operator + （const complex & x){
    return x;
}
//取反
inline complex operator - (const complex & x){
    return complex(-real(x), -image(x));
}

std::ostream & operator <<(std::ostream & os, const complex & x) {
	return os << "(" << real(x) << "," << imag(x) << ")";
}
```

为什么这三个重载操作符的返回值不是传引用？

因为传回的值是在函数中创建的临时变量，所以在函数结束时会自动销毁。

typename()创建临时变量。比如：complex()。

## 三大函数

如果类中带有指针，就必须定义拷贝构造、拷贝赋值和析构函数。下面以String为例：

```C++
#ifndef __MYSTRING__
#define __MYSTRING__

class String
{
public:
	String(const char * cstr = 0);
	String(const String & str);
	String & operator=(const String & str);
	char * get_c_str() const { return m_data; }
	~String();
private:
	char * m_data;
};

#endif
```



### 拷贝构造

```C++
String::String(const String & str) {
	if (this->m_data != str.m_data) {
		delete m_data;
		m_data = new char[strlen(str.m_data) + 1];
		strcpy(m_data, str.m_data);
	}
}
```

### 拷贝赋值

```C++
String & String::operator=(const String & str) {
	if (this != &str) {
		delete [] m_data;
		m_data = new char[strlen(str.m_data) + 1];
		strcpy(m_data, str.m_data);
	}
	return *this;
}
```

### 析构函数

```C++

String::String(const char * cstr = 0)
{
	if (cstr) {
		m_data = new char[strlen(cstr)+1];
		strcpy(m_data, cstr);
	}
	else {
		m_data = new char[1];
		*m_data = '\0';
	}
}
String::~String()
{
	delete[] m_data;
}
```

##  堆、栈和内存管理

Stack是存在于作用域的一块内存空间。例如当你调用函数，函数本身即会形成一个stack用来放置它所接收的参数，以及返回地址。

在函数本体内声明的任何变量其所使用的内存块都取自上述stack。

Heap，或所谓system heap，是指由操作系统提供的一块全局内存空间，通过new来获取。任何地方都可以动态获得heap空间，而且需要程序员自己去释放。

```C++
{
	Complex c1(1,2);  //c1所占用的空间来自栈
    Complex * p = new Complex(3);  //Complex(3)存储在堆中，需要通过delete才能释放
}
```

### static对象

```C++
static Complex c2(1,2);
```

c2便是所有static对象，其生命在作用域结束之后仍然存在，直到整个程序结束。

### global对象

```C++
Complex c3(1,2);
int main(){
    
}
```

c3的生命周期直到整个程序结束。

new是调用operator new分配内存，然后调用构造函数。

delete是先调用析构函数，然后再调用operator delete释放内存。

array new一定要搭配array delete。因为如果使用delete是只会调用一次析构函数，这样就只是释放了一个String中的数据。因为delete会先调用构造函数释放指针指向的内存空间，然后调用delete来释放内存指针，所以如果不使用array delete就只会调用一次析构函数，只释放第一个元素指针指向的内存空间，但是delete会把三个指针释放，这样就存在两个指针指向的内存空间没有释放，导致内存泄漏。

![](https://raw.githubusercontent.com/zxpgo/images/master/img/20190822193452.png)

## static

在非static中，函数都只有一份，处理多个对象时，通过this指针来表示处理的对象。即如下：

```c++
complex c1;
cout << c1.real();
//在C语言中等价于
cout<< complex::real(&c1); //&c1等价于this指针
```

在加上static时，静态数据和静态函数只会存在一份。比如计算创建对象的个数时，就需要采用静态数据成员。而且**静态成员函数没有this指针。静态成员函数只能处理上静态数据。**

由于static修饰的类成员属于类，不属于对象，因此static类成员函数是没有this指针的，this指针是指向本对象的指针。正因为没有this指针，所以static类成员函数不能访问非static的类成员，只能访问 static修饰的类成员。

静态数据需要在类声明外部进行初始化。

用static修饰的成员变量在对象中是不占内存的，因为他不是跟对象一起在堆或者栈中生成，用static修饰的变量在静态存储区生成的。所以用static修饰一方面的好处是可以节省对象的内存空间。

```C++
class Account{
	static double m_rate;
    static void set_rate(const double & x) { m_rate = x; }
};
double Account::m_rate = 8.0; //该行必须有，但是可以不给初值

int mian(){
    //静态函数的两种调用方式
    Account::set_rate(5.0);
    
    Account a;
    a.set_rate(7.0);
}
```

将构造函数在private中：

```C++
class A{
public:
    static A & getInstance(){
        static A a;
        return a;
    }
    void setup(){...}
private:
   	A();
    A(const A & rhs);
};
```

##  类模板

```c++
template <typename T>
class complex{
public: 
    complex(T r = 0, T i = 0): re(r), im(i){}
    T real() const { return re; }
private:
    T re, im;
};

complex<double> c1(2.5, 1.5);
complex<int> c2(2, 6);
```

## 函数模板

```C++
template<typename T>
inline const T & min (const T & a, const T& b){
	return b < a ? b : a;
}

stone r1(2,3), r2(3,3), r3;
r3 = min(r1, r2); //编译器会自动进行参数推导

class stone{
public:
    bool operator < (const stone & rhs) const{
        //....
    }
};
```

## 命名空间namespace

```C++
namespace NS{
    //...
}

using namespace NS;

using namespace std;
using std::cout;
std::cout << std::endl;
```

# 面向对象编程

## 组合(Composition)

组合表示has-a关系。

```C++
template <typename T>
class queue{
protected:
    deque<T> c;//has-a关系
public:
    bool empty() const { return c.empty(); }
    void pop() { c.pop_front(); }
    void push(const value)type & x){ c.push_back(x); }
    //...
};
```

类关系图：

![](https://raw.githubusercontent.com/zxpgo/images/master/img/20190824084359.png)

queue是一个适配器，应该deque实现了所有功能，而queue只是满足用户要求堆deque的功能进行了提炼。

### 组合下的析构和构造函数

构造由内到外，析构由外而内。

先调用deque的默认构造函数，再执行queue构造函数的内容。

```C++
queue() : deque()  {...}  //:deque()由编译器操作并执行
```

先调用queue的构造函数，执行queue构造函数的内容，然后再调用deque的构造函数。

```C++
~queue() {
    //queue的动作
    ~deque(); //由编译器自动执行
}
```

## 委托(Delegation)

```C++
class String Rep;
class String{
public:
    String();
    String(const char * s);
    String(const String & s);
    String &operator=(const String & s);
    ~String();
    //...
private:
    StringRep * rep; //指针指向实现所有功能的类，point to implement
};

class StringRep{
	friend class String;
    StringRep(const char * s);
    ~StringRep();
    int count;
    char * req;
};
```

两个类之间用指针相连。两个类不同步，比如先创建String类，只有需要动作时才去创建StringRep类。

![](https://raw.githubusercontent.com/zxpgo/images/master/img/20190824090159.png)

创建了三个String类，都指向Hello字符串，而Hello字符串只会存在一份，还有一个计数器来记录由多少个指针指向该字符串。这种方式叫做引用计数。

![](https://raw.githubusercontent.com/zxpgo/images/master/img/20190824090623.png)

## 继承(Inheritance)

public继承表示is-a关系。

```C++
struct _List_node_base{
	_List_node_base * _M_next;
    _List_node_base * _M_prev;
};

template <typename _Tp>
struct  _List_node : public _List_node_base{
	_Tp _M_data;
}
```

类关系图：

![](https://raw.githubusercontent.com/zxpgo/images/master/img/20190824092011.png)

### 继承关系下的构造和析构

构造：先调用基类的默认构造函数，再执行子类的构造函数。

```C++
Derived(...) : Base() {...}
```

析构：先执行子类的析构函数，再调用基类的析构函数。

```C++
~Derived(){
    //...
    ~Base();
}
```

__注意__，父类的析构函数必须是虚函数。

三种继承方式：

- private
- protected
- public：表示is-a

## 虚函数

非虚函数：不希望派生类重新定义它

虚函数：希望派生类重新定义它，它已有默认定义。

纯虚函数：希望派生类一定要重新定义它，在父类中它没有默认定义。

```C++
class Shape{
public:
    virtual void draw() const = 0; //纯虚函数
    virtual void error(const std::string & msg); //非纯虚函数
    int ojbectID() const; //非虚函数
};

class Rectangle : public Shape {...};
class Ellipse : public Shape {...};
```

虚函数实现设计模式**模板方法**：

```C++
class CDocument {
public:
	void OnFileOpen() {
		cout << "dialog..." << endl;
		cout << "check file status..." << endl;
		cout << "open file..." << endl;
		Serialize();
		cout << "close file..." << endl;
		cout << "update all views..." << endl;
	}
	virtual void Serialize() = 0;
};
class CMyDoc : public CDocument {
public:
	void Serialize() {
		cout << "CMyDoc::Serialize()" << endl;
	}
};

int main()
{
	CMyDoc myDoc;
	myDoc.OnFileOpen();
}

输出：
dialog...
check file status...
open file...
CMyDoc::Serialize()
close file...
update all views...
```



## 继承加组合关系下的构造和析构

对于子类继承父类，而父类中包含一个类对象，这样向调用父类中类对象的构造函数，再调用父类的构造函数，最后调用子类的狗剋函数。而对于析构函数刚好相反。

对于子类继承父类，并且子类中包含另一个类对象，这样先调用父类的构造函数，再调用类对象的构造函数，最后调用子类的构造函数。具体分析代码如下：

```C++
#include <iostream>
using namespace std;

class A {
public:
	A() {
		cout << "A()" << endl;
	}
	~A() {
		cout << "~A()" << endl;
	}
};
class B {
public:
	B() {
		cout << "B()" << endl;
	}
	~B() {
		cout << "~B()" << endl;
	}
};
class C : A {
	B b;
public:
	C() {
		cout << "C()" << endl;
	}
	~C() {
		cout << "~C()" << endl;
	}

};
int main()
{
	C c;
}

输出：
A()
B()
C()
~C()
~B()
~A()
```

## 委托+继承

委托加继承可以实现**观察者模式**：

```C++
class Observer { //数据的显示方式，派生子类来以不同的方式显示数据
public:
	virtual void update(Subject * sub, int value);
};
class Subject { //存放数据
	int m_value;
	vector<Observer*> m_views;  //委托
public:
	void attach(Observer* obs) {  
		m_views.push_back(obs);
	}
	void set_val(int value) {
		m_value = value;
		notify();
	}
	void notify() {
		for (int i = 0; i < m_views.size(); ++i) {
			m_views[i]->update(this, m_value);
		}
	}
};
```

委托加继承实现**组合(Composite)模式**：

```C++
class Component {
	int value;
public:
	Component(int val) { value = val; }
	virtual void add(Component *) {} //不能写成纯虚函数，因为如果是纯虚函数，子类就必须重写，而Primitive不能拥有该方法
};
class Primitive : public Component {
public:
	Primitive(int val): Component(val){}
};
class Composite : public Component {
	vector<Component *> c;//委托
public:
	Composite(int val) : Component(val){}
	void add(Component * elem) {
		c.push_back(elem);
	}
};

```

比如文件系统，Primitive是一个文件，而Component可以是一个目录。目录中可以添加文件和目录，但是文件不能添加文件和目录，文件是最小的单元。类图关系如下：

![](https://raw.githubusercontent.com/zxpgo/images/master/img/20190825111352.png)

委托加继承实现**原型模式**:

 为了保证框架的稳定， 我们希望现在类能够创建未来的类。思路：未来创建的类继承框架中的类，然后在子类中创建定义一个静态对象（类自身），这时会调用构造函数（将其声明为私有的），然后将创建的静态对象传递给框架中的父类(通过addPrototype函数)，最后在父类中通过传递来的子类对象（原型）调用clone函数创建一个新的子类对象（副本）。注意clone调用的构造函数与创建静态对象的构造函数是两个不同的构造函数。

![](https://raw.githubusercontent.com/zxpgo/images/master/img/20190825134409.png)

代码实现：

```C++
#include <iostream>
using namespace std;

enum imageType{ LSAT, SPOT};
class Image {
public:
	virtual void draw() = 0;
	static Image * findAndClone(imageType);
protected:
	virtual imageType returnType() = 0;
	virtual Image * clone() = 0;
	static void addPrototype(Image * image) {
		_prototypes[_nextSlot++] = image;
	}
private:
	static Image * _prototypes[10];
	static int _nextSlot;
};

int Image::_nextSlot = 0; //静态变量的定义，此时才是给内存
Image * Image::_prototypes[]; //静态变量的定义

Image * Image::findAndClone(imageType type) {
	for (int i = 0; i <= _nextSlot; i++) {
		if (_prototypes[i]->returnType() == type)
			return _prototypes[i]->clone();
	}
}

class LandSatImage : public Image {
public:
	imageType returnType() {
		return LSAT;
	}
	void draw() {
		cout << "LandSatImage::draw" << _id << endl;
	}
	Image * clone() {
		return new LandSatImage(1);
	}
protected:
	LandSatImage(int dummy) {
		_id = _count++;
	}
private:
	static LandSatImage _landSatImage;
	LandSatImage() {
		addPrototype(this);
	}
	int _id;
	static int _count;
};
int LandSatImage::_count = 0;
LandSatImage LandSatImage::_landSatImage;

class SpotImage {

};


int main()
{
	Image * image;
	image = Image::findAndClone(LSAT);
	image->draw();
}

```





