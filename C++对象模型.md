---
title: C++对象模型
date: 2019-08-06 11:44:14
tags: C++
mathjax: true
---



泛型编程，深入探索面向对象之继承关系所形成的对象模型，包含this指针，虚指针，虚表，虚极至以及虚函数造成的多样效果<!--more-->

# 类型转换

## 转换函数

转换函数没有返回类型；转换函数声明为const。

```C++
class Fraction {
public:
	Fraction(int num, int den = 1) 
		: m_numerator(num), m_denominator(den){}
	operator double() const {  //转换函数
		return (double)m_numerator / m_denominator;
	}
private:
	int m_numerator;
	int m_denominator;
};

Fraction f(3, 5);
double  d = 4 + f; //调用operator double()将f转为0.6
```

对于4+f，首先去是否存在全局函数，operator+，第一个参数为int，第二个参数为Fraction。如果没有就去调用转换函数operator double()。

如果Fraction类如下，则不会调用转换函数operator double()，而是调用`operator+(const int i, const Fraction & frac)`函数。

```c++
class Fraction {
public:
	Fraction(int num, int den = 1)
		: m_numerator(num), m_denominator(den) {}
	operator double() const {  //转换函数
		return (double)m_numerator / m_denominator;
	}
	friend double operator+(const int i, const Fraction & frac);
private:
	int m_numerator;
	int m_denominator;
};

double operator+(const int i, const Fraction & frac) {
	return i + (double)frac.m_numerator / frac.m_denominator;
}

```

## non-explicit-one-argument ctor

允许一个实参的非explicit构造函数。将其他类型转换成类的类型。

```C++
class Fraction {
public:
	Fraction(int num, int den = 1)
		: m_numerator(num), m_denominator(den) {}
//	operator double() const {  //转换函数
//		return (double)m_numerator / m_denominator;
//	} //该函数存在时，会存在二义性
	Fraction operator+(const Fraction& f) {
		return Fraction(this->m_numerator * f.m_denominator + f.m_numerator * this->m_denominator, 
			this->m_denominator * f.m_denominator);
	}
private:
	int m_numerator;
	int m_denominator;
};


int main()
{
	Fraction f(3, 5);
	Fraction  d =  f + 4;
}
```

会将4转成为Fraction类型，因为会调用构造函数进行转换。

如果operator double() 存在时，会存在二义性，因为4可以转成Fraction，f可以转成double。

## explicit-one-argument ctor

```C++
class Fraction {
public:
	explicit Fraction(int num, int den = 1)
		: m_numerator(num), m_denominator(den) {}
	operator double() const {  //转换函数
		return (double)m_numerator / m_denominator;
	} 
	Fraction operator+(const Fraction& f) {
		return Fraction(this->m_numerator * f.m_denominator + f.m_numerator * this->m_denominator, 
			this->m_denominator * f.m_denominator);
	}
private:
	int m_numerator;
	int m_denominator;
};


int main()
{
	Fraction f(3, 5);
	Fraction  d =  f + 4; //不会将4转成Fraction，所以会报错
}
```

标准库中的转换函数：

```c++
template<class Alloc>
class vector<bool, Alloc>
{
public:
	typedef __bit_reference reference;
protected:
	reference operator[](size_type n){
		return *(begin() + difference_type(n));
	}
};

struct __bit_reference{
    unsigned int * p;
    unsigned int mask;
public:
    operator bool() const { return !(!(*p & mask)); }
};
```

# pointer-liker classes

## 智能指针

```c++
template<class T>
class shared_ptr {
public:
	T & operator*() const {
		return *px;
	}
	T * operator->() const {
		return px;
	}
	shared_ptr(T * p) : px(p) {}
private:
	T * px;
	long * pn; 
};

struct Foo {
//...
public:
	void method(void){//..}
};

shared_ptr<Foo> sp(new Foo);
Foo f(*sp);
sp->method(); //等价于px->method();
```

## 迭代器

```C++
template <class T, class Ref, class Ptr>
struct __list_iterator {
	typedef __list_node<T>* link_type;
	link_type node;
	reference operator*() const { return (*node).data; }
	pointer operator->() const { return &(operator*());  }
	self& operator++() {}
	self& operator--() {}
	self& operator++(int) {}
	self& operator--(int) {//...
	}
};

list<Foo>::iterator ite;
*ite;
ite->method(); //相等于(&(*ite))->method();
```

# function-like classes仿函数

类中重载operator ()函数。

```C++
template<class T>
struct identity{
	const T &;
	operator()(const T & x) cosnt { return x }
};

template <class Pair>
struct select1st{
	const typename Pair::first_type & 
	operator()(const Pair& x) const {
		return x.first;
	}
};

template<class Pair>
struct select2en {
	const typename Pair::second_type &
		operator()(const Pair& x) const {
		return x.second;
	}
};
template <class T1, class T2>
struct pair {
	T1 first;
	T2 second;
	pair() : first(T1()), second(T2()) {}
	pair(const T1 & a, const T2 & b) :first(a), second(b) {}
}; 	
```

# 模板

## 类模板

```C++
template <typename T>
class complex{
private:
    T re, im;
public: 
    complex(T r = 0, T i = 0) : re(i), im(i){}
};
complex<double> c1(1.2, 1.5);
```

## 函数模板

```C++
template<class T>
inline const T& min(const T& a, const T& b){
    return b < a ? b : a;
}
```

编译器会对函数模板进行参数推导。

## 成员模板

```c++
template <class T1, class T2>
struct pair{
	typedef T1 first_type;
	typedef T2 second_type;
	
	T1 first;
	T2 second;
	
	pair() : first(T1()), second(T2()){}
	pair(const T1& a, const T2& b) : first(a), second(b)P{}
	
	template<class U1, class U2>
	pair(const pair<U1, U2>& p) : first(p.first), second(p.second){}
};

class Base1{};
class Derived1 : public Base1{}

class Base2{};
class Derived2 : public Base2{}

pair<Derived1, Derived2> p;
pair<Base1, Base2> p2(p);
```

```C++
template<typename _Tp>
class shared_ptr: public __shared_ptr<_Tp>{
	template<typename _Tp1>
	explicit shared_ptr(_Tp1 * __p) 
	: __shared_ptr<_Tp>(__p){}
};

Base1 * ptr = new Derived1;
shared_ptr<Base1> sptr(new Derived1);
```

## 模板特化

```C++
template<class key>
struct hash{};

template<>
struct hash<char>{
	size_t operator()(char x) const { return x; }
};
struct hash<int>{
	size_t operator()(int x) const { return x;}  
};
```

## 模板偏特化

```C++
//个数上的偏特化
template<typename T, typename Alloc>
class vector{
//...
};
template<typename Alloc=...>
class vector<bool, Alloc>{//偏特化，针对bool类型有专门的设计

};

//范围上的偏特化
template<typename T>
class T{};

template<typename U>
class C<U*>{};

C<string> obj1;
C<string*> obj2;
```

## 模板模板参数

```C++
template<typename T, template<typename T> class Container>
class XCls{
private:
	Container<T> c;
public:
	//...
};
template<typename T>
using Lst = list<T, allocator<T>>;

XCls<string, list> mylst1; //错误,因为需要设置第二参数，所以必须向下面那样使用
XCls<string, Lst> mylst2;
```

下面情况不是模板模板参数：

```C++
template<class T, class Sequence = deque<T>>
class stack{
protected:
	Sequence c;
	//...
};

stack<int> s1;
stack<int, list<int>> s2;
```

## 模板参数可变

```C++
void print(){}
template<typename T, typename... Types>
void print(const T& firstArg, const Types&.. args){
	cout << firstArg << endl;
	cout << sizeof...(args) << endl;
	print(args...);
}
```

# 引用

- 引用必须有初值；
- 而且设置完初值，不能改变；（指针可以改变指向其他值）

```C++
int x = 0;
int& r = x;
int x2 = 5;
r = x2; //r,x都是5
```

注意：

引用的底部就是指针，所以实际上是4个字节。但是编译器会制造假象：大小相同，地址也相同。

`sizeof(r) == sizeof(x), &r = &x`。

**Java里面的所有变量都是引用。**

引用通常不用于声明变量，而用于参数类型和返回类型的描述。

下面二者不能并存：

```C++
double imag(const double& im){}
double imag(const double im){} //存在二义性
```

这是因为两个函数的签名相同。

# 对象模型

继承下，构造函数由内而外，析构函数由外而内。

组合下，构造函数由内而外，析构函数由外而内。

继承和组合下，构造函数由父类构造函数->组合对象的构造函数->子类本身的构造函数（VS2017中，可能不同编译器不同）。析构函数刚好相反。

## 虚指针和虚表

```C++
class A {
public:
	virtual void vfunc1();
	virtual void vfunc2();
	void func1();
	void func2();

private:
	int m_data1, m_data2;
};

class B : public A {
public:
	virtual void vfunc1();
	void func2();
private:
	m_data3;
};

class C : public B {
public:
	virtual void vfunc1();
	void func2();
private:
	int m_data1,m_data4;
};
```

![](https://raw.githubusercontent.com/zxpgo/images/master/img/20190830161353.png)

虚指针指向虚函数表。

如果虚函数没有重载，在虚表中的记录将会相同。

# this

```C++
class CDocument{
	void onFileOpen();
    virtual void Serialize();
    ...
};
CDocument::OnFileOpen(){
    ...
    Serialize();
    ...
}
virtual Serialize();

class CMyDoc : public CDocument{
    virtual Serialize(){...}
};
int main(){
    CMyDoc myDoc;
    myDoc.onFileOpen();
}
```

# const

当成员函数的const和non-const版本同时存在，const object只会(只能)调用const版本，non-const object只会(只能)调用non-const。

---

|                                                    | const object（data members 不得变动） | non-const object(data members 可变动) |
| :------------------------------------------------: | :-----------------------------------: | ------------------------------------- |
|   const member functions(保证不更改data members)   |                   √                   | √                                     |
| non-const member functions(不保证data members不变) |                   ×                   | √                                     |

```C++
const String str("hello world");
str.print();
```

如果当初设计String::print()时未指明const，那么上行便是经由const object调用non-const member functions，会出错。

标准库中class template std::basic_string<...>有如下两个member functions:

```C++
charT operator[](size_type pos) const{.../*不必考虑COW*/}
reference operator[](size_type pos) {.../*必须考虑COW*/}//COW:copy on write
```

这是因为string的设计采用了引用计数技术。假设四个string对象可以指向同一个字符串，这样字符串只会存在一份。如果其中一个string对象需要修改字符串，会拷贝出来一份给其，让其去修改，即写时复制。而对于常量string对象，就不会出现修改对象的情况，所以不必考虑写时复制（COW）。

# new delete

new先分配内存，在调用构造函数。分配内存调用operator new成员函数，然后调用malloc函数。

## 重载::operator new，::operator delete，::operator new[]，::operator delete[]

```C++
inline void * operator new(size_t size){
	return myAlloc(size);
}
inline void operator new[](size_t,size){
    return myAlloc(size);
}
inline void operator delete(void *ptr){
    myFree(ptr);
}
inline void operator delete[](void * ptr){
    myFree(ptr);
}

```

## 重载成员函数operator new/delete

```C++
class Foo{
public:
    void* operator new(size_t size);
    void operator delete(void*, size_t);
};
```

## 重载成员函数operator new[]/delete[]

```C++
class Foo{
public:
    void* operator new[](size_t size);
    void operator delete[](void*, size_t);
};

Foo * p = new Foo[N]; 
```

示例： 

```C++
#include <iostream>
#include<string>
using namespace std;
class Foo {
public:
	int _id;
	long _data;
	string _str;
public:
	Foo() : _id(0) { cout << "default ctor.this=" << this << "id=" << _id << endl; };
	Foo(int i) : _id(i) { cout << "ctor.this=" << this << "id=" << _id << endl; }
	virtual ~Foo() { cout << "dtor.this=" << this << "id=" << _id << endl; }
	static void * operator new(size_t size) {
		Foo *p = (Foo *)malloc(size);
		cout << "call operator new, size = " << size  << endl;
		return p;
	}
	static void operator delete(void * pdead, size_t size) {
		cout << "call operator delete" << endl;
		free(pdead);
	}
	static void * operator new[](size_t size) {
		Foo *p = (Foo *)malloc(size);
		cout << "call operator new[], size=" << size << endl;
		return p;
	}
	static void operator delete[](void * pdead, size_t size) {
		cout << "call operator delete[]" << endl;
		free(pdead);
	}

};
int main()
{
	cout <<" sizeof(Foo) = "<< sizeof(Foo) << endl;
	Foo * p = new Foo();
	Foo *q = new Foo[4];
	delete p;
	delete[]q;
}

```

输出结果：

```C++
 sizeof(Foo) = 40
call operator new, size = 40
default ctor.this=014A5A38id=0
call operator new[], size=164
default ctor.this=014AD92Cid=0
default ctor.this=014AD954id=0
default ctor.this=014AD97Cid=0
default ctor.this=014AD9A4id=0
dtor.this=014A5A38id=0
call operator delete
dtor.this=014AD9A4id=0
dtor.this=014AD97Cid=0
dtor.this=014AD954id=0
dtor.this=014AD92Cid=0
call operator delete[]
```

为什么调用operator new[]打印出来的size是164字节，而不是160字节呢？

这是因为需要一个计数器来记录创建了对象个元素。

如果使用全局的operator new和operator new()，就不会调用类中重载的版本。如下：

```C++
int main()
{
	cout <<" sizeof(Foo) = "<< sizeof(Foo) << endl;
	Foo * p = ::new Foo(); //调用全局::operator new
	Foo *q = ::new Foo[4];
	::delete p;
	::delete[]q;
}
```

输出：

```C++
sizeof(Foo) = 40
default ctor.this=011E4D48id=0
default ctor.this=011E98FCid=0
default ctor.this=011E9924id=0
default ctor.this=011E994Cid=0
default ctor.this=011E9974id=0
dtor.this=011E4D48id=0
dtor.this=011E9974id=0
dtor.this=011E994Cid=0
dtor.this=011E9924id=0
dtor.this=011E98FCid=0
```

## 重载new()和delete()

可以重载class member operator  new()，写出多个版本，前提是每一个版本的声明都必须有独特的参数，其中第一个参数都必须是size_t，其余参数以new所指定的placement 参数为初值。出现new()小括号内的便是居中对齐placement参数：

```C++
Foo *pf = new(300, 'c')Foo;
```

```C++
	void * operator new(size_t size, void * start) {
		return start;
	}

	void * operator new(size_t size, long extra) {
		return malloc(size + extra);
	}

	void * operator new(size_t size, long extra, char init) {
		return malloc(size + extra + sizeof(init));
	}
```



也可以重载class member operator delete()，写出多个版本。但它们绝不会被delete调用。只会当new所调用的构造函数抛出异常，才会调用这些重载的operator delete()。它只可能这样被调用，主要用来归还未能完全创建成功的对象所占用的内存。

```c++
#include <iostream>
#include<string>
using namespace std;
class Bad{};
class Foo {
public:
	int _id;
	long _data;
	string _str;
public:
	Foo() : _id(0) { cout << "default ctor.this=" << this << "id=" << _id << endl; };
	Foo(int i) : _id(i) { cout << "ctor.this=" << this << "id=" << _id << endl;  
	}
	virtual ~Foo() { cout << "dtor.this=" << this << "id=" << _id << endl; }
	static void * operator new(size_t size) {
		Foo *p = (Foo *)malloc(size);
		cout << "call operator new, size = " << size  << endl;
		return p;
	}
	static void operator delete(void * pdead, size_t size) {
		cout << "call operator delete" << endl;
		free(pdead);
	}
	static void * operator new[](size_t size) {
		Foo *p = (Foo *)malloc(size);
		cout << "call operator new[], size=" << size << endl;
		return p;
	}
	static void operator delete[](void * pdead, size_t size) {
		cout << "call operator delete[]" << endl;
		free(pdead);
	}

	void * operator new(size_t size, void * start) {
		cout << "operator new(sizt_t, void*)" << endl;
		return start;
	}

	void * operator new(size_t size, long extra) {
		cout << "operator new(size_t, long)" << endl;
		return malloc(size + extra);
	}

	void * operator new(size_t size, long extra, char init) {
		cout << "operator new(size_t, long, char)" << endl;
		return malloc(size + extra + sizeof(init));
	}

	void operator delete(void * , void *) {
		cout << "operator delete(void*, void)" << endl;
	}

	void operator delete(void * ,long) {
		cout << "operator delete(void * ,long)" << endl;
	}

	void operator delete(void *, long, char) {
		cout << "operator delete(void *, long, char)" << endl;
	}


};
int main()
{
	cout <<" sizeof(Foo) = "<< sizeof(Foo) << endl;
	Foo * p1 = new Foo;
	Foo * p2 = new(100,'c')Foo;
	Foo * p3 = new(100)Foo(1);
	Foo * p = ::new Foo();
	Foo *q = ::new Foo[4];
	::delete p;
	::delete[]q;
}
输出：
 sizeof(Foo) = 40
call operator new, size = 40
default ctor.this=00B55B18id=0
operator new(size_t, long, char)
default ctor.this=00B61838id=0
operator new(size_t, long)
ctor.this=00B63BC0id=1
default ctor.this=00B54E28id=0
default ctor.this=00B63C7Cid=0
default ctor.this=00B63CA4id=0
default ctor.this=00B63CCCid=0
default ctor.this=00B63CF4id=0
dtor.this=00B54E28id=0
dtor.this=00B63CF4id=0
dtor.this=00B63CCCid=0
dtor.this=00B63CA4id=0
dtor.this=00B63C7Cid=0
```

## basic_string使用new(extra)扩充申请量

![](https://raw.githubusercontent.com/zxpgo/images/master/img/20190901203455.png)

Rep是一个引用计数。


