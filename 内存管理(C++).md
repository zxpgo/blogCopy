---
title: 内存管理(C++)
date: 2019-08-06 11:44:14
tags: C++
mathjax: true
---

C++内存管理，万丈高楼平地起，源码之前，了无秘密！<!--more-->

![](https://raw.githubusercontent.com/zxpgo/images/master/img/20190806195724.png)

本课程中，只介绍CRT之上的步骤。

#  C++ primitives

---

| 分配                     | 释放                       | 类别      | 是否可重载                   |
| ------------------------ | -------------------------- | --------- | ---------------------------- |
| malloc()                 | free()                     | C函数     | 不可                         |
| new                      | delete                     | C表达式   | 不可                         |
| ::operator new()         | ::operator delete()        | C++函数   | 可以                         |
| allocator<T>::allocate() | allocator<T>::deallocate() | C++标准库 | 可自由设计并以之搭配任何容器 |

以上4各工具的使用：

```C++
void *p1 = malloc(512);
free(p1);

complex<int> * p2 = new complex<int>;
delete p2;

void *p3 = ::operator new(512);
::operator delete(p3);

//以下使用C++标准库提供的allocators
//其接口虽有标准规范，但实现厂商并未完全遵守；下面三者形式略异
# ifdef _MSC_VER
	//以下两个函数都是non-static，定要通过object调用，以下分配3个ints
	int * p4 = allocator<int>().allocate(3,(int*)0);//(int*)0没有意义，只是因为Vs编译器没有给默认值，所以必须带一个参数
	allocateror<int>().deallocate(p4, 3);
#endif 

#ifdef __BORLANDC_
	//以下两个函数都是non-static，定要通过object调用，以下分配5个ints
	int * p4 = allocator<int>().allocate(5);//其实该函数有两个参数，但是该编译器给了第二个参数默认值
	allocator<int>().deallocate(p4,5);
#endif

#ifdef __GNUC__  //GNUC2.9
	//以下两个函数都是static，可通过全名调用之，以下分配512bytes
	void * p4 = alloc::allocate(512);
	alloc::deallocate(p4, 512);
# endif

#ifdef __GNUC__ //GNU4.9
	//以下聊个函数都是non-static，定要通过object调用，以下分配7个ints
	void * p4 = allocator<int>().allocate(7);
	allocator<int>().deallocate((int*)p4, 7);
	
	//以下聊个函数都是non-static，定要通过object调用，以下分配9个ints
	void * p5 = __gnu_cxx::__pool_alloc<int>().allocate(9);
	__gnu_cxx::__pool_alloc<int>().deallocate((int*)p5, 9);
#endif
```

## new expression

new实现了两个功能：

- 分配内存
- 调用构造函数

```C++
Complex * pc = new Complex(1,2);
Complex * pc;
try{
    void * mem = operator new(sizeof(Complex)); //调用malloc
    pc = static_cast<Complex*>(mem);
    pc->Complex::Complex(1,2); //注意：只有编译器才可以这样直接调用构造函数
}catch(std::bad_alloc){
    //若分配失败就不会执行构造函数
}
```

注意：如果想直接调用构造函数，可运用placement new: `new(p)Complex(1,2);`

## delete expression

delete实现两个功能：

- 调用析构函数
- 释放内存

```C++
Complex * pc =  new Complx(1,2);

pc->~Complx();         //先析构，析构函数可以被直接调用
operator delete(pc);   //然后释放内存

void __cdecl operator delete(void *p) __THROW0(){
    free(p);
}
```

## 析构函数和构造函数的直接调用

```C++
string * pstr = new string;

pstr->String("jjhow"); //Error
```

自己定义一个类来测试：

```C++
namespace jjo2 {
	class B {
	public:
		int id;
		B(int i) : id(i) {
			cout << "ctor, this=" << this << " id = " << id << endl;
		}
		~B() {
			cout << "dtor, this=" << this << endl;
		}
	};
}


using namespace jjo2;
B *pB = new B(1);
cout << pB->id << endl;
pB->B::B(3); //在vs2017中可以通过，在有些编译器无法通过
pB->~B();
```

## Array new, Array delete

```C++
Complex * pca = new Complex[3];
//调用三次构造函数

delete[] pca; //调用三次析构函数，如果没有[]只会调用一次析构函数
```

当分配内存时，cookie用来记录长度（即内存大小）和一些其他信息。

![](https://raw.githubusercontent.com/zxpgo/images/master/img/20190807150120.png)

```C++
string * psa = new string[3];

delete pas; //只调用一次析构函数，这样会存在内存泄漏，只释放了一块内存
```

通过代码测试：

```c++
namespace jjo2 {
	class B {
	public:
		int id;
		B() : id(0) { cout << "default ctor, this = " << this << " id = " << id << endl; }
		B(int i) : id(i) {
			cout << "ctor, this=" << this << " id = " << id << endl;
		}
		~B() {
			cout << "dtor, this=" << this << endl;
		}
	};
}

using namespace jjo2;
B * buf = new B[3];
B * tmp = buf;
cout << "buf = " << buf << " tmp = " << tmp << endl;
for (int i = 0; i < 3; ++i) {
	new(tmp++)B(i);  //placement new调用构造函数
}
delete [] buf;

cout << "buf = " << buf << " tmp = " << tmp << endl;
```
输出：

```C++
ctor, this=00144D18 id = 3
dtor, this=00144D18
default ctor, this = 00144E5C id = 0
default ctor, this = 00144E60 id = 0
default ctor, this = 00144E64 id = 0
buf = 00144E5C tmp = 00144E5C
ctor, this=00144E5C id = 0
ctor, this=00144E60 id = 1
ctor, this=00144E64 id = 2
buf = 00144E5C tmp = 00144E68
dtor, this=00144E64
dtor, this=00144E60
dtor, this=00144E5C
```

从输出可以看出，`delete []buf`调用残次析构函数，并且析构的时候是从最后一个元素开始。

使用array new时，内存中后多出上下两个cookie（图中61h，表示61个bytes，1个字节用来表示on/off，这是在vc6中，满足16的倍数，所以有12bytes的填充）。

其实对于int而言，delete加不加[]无所谓，因为int的析构函数是没有意义的，什么事情都没有做。只有对析构函数中需要释放内存的对象，delete加上[]才有意义。但是我们推荐不管哪种情况都加上[]。

![](https://raw.githubusercontent.com/zxpgo/images/master/img/20190807152152.png)

```C++
string * p = new string[3];
delete p;  //运行时异常

int * p = new int[3];
delete p; //正确
```

对于利用array new创建对象时，就不一样了。

```C++ 
Demo *p = new Demo[3];  //Demo是一个简单的类
delete [] p;
```

此时内存中，会多出一个3来记录需要调用三次析构函数，而如果没有加[]，就会混乱。

![](https://raw.githubusercontent.com/zxpgo/images/master/img/20190807155242.png)

## placement new

placement new允许对象构造到一个已经分配好的内存中。

没有所谓的placement delete，因为placement new根本没有分配内存。

但是，如果需要重载placement new时，需要对应写一个placement delete。

```C++
#include <new>
char * buf = new char[sizeof(Complex)*3];
Complex * pc = new(buf) Complex(1,2);
//...
delete [] buf;
```

```C++
Complex * pc = new(buf) Complex(1,2);
//编译器转为如下
Complex * pc;
try{
    void * mem = operator new(sizeof(Complex), buf); //该函数没有申请内存，等同于什么都没做
    pc = static_cast<Complex*>(mem);
    pc->Complex::Complex(1,2); //调用构造函数
}catch(std::bad_alloc){
}

void * operator new(size_t, void * loc){
    return loc; //传入一个位置，直接返回了，没有申请内存
}
```

使用placement new就等同于调用构造函数。

placement new在代码中的表现：

```C++
new(p)
::operator new(size, void*)
```

## 四者关系

new expression, operator new, ::operator new， malloc的关系：

![](https://raw.githubusercontent.com/zxpgo/images/master/img/20190807160639.png)

`::opeartor new(size_t)`是一个全局函数。

`operator new(size)`是一个成员函数，可以被其他类重载，比如`Foo::operator new(size_t)`。

C++容器的分配途径
![](https://raw.githubusercontent.com/zxpgo/images/master/img/20190807162308.png)

## 重新定义::operator new和::operator delete

下面给出了重载的接口：

```C++
void * myAlloc(size_t size){
    return malloc(size);
}
void myFree(void * ptr){
    return free(ptr);
}

inline void * operator new(size_t size){
    cout << "global new() \n";
    return myAlloc(size);
}

inline void * operator new[](size_t size){
    cout << "global new[]()\n";
    return myAlloc(size);
}

inline void * operator delete(size_t size){
    cout << "global delete() \n";
    return myFree(size);
}

inline void * operator delete[](size_t size){
    cout << "global delete[]()\n";
    return myFree(size);
}

```

## 重载operator new和operator delete

在类中重载这两个函数更有意义。

```c++
class Foo{
public:
	static void * operator new(size_t);
    static void operator delete(void*, size_t); //size_t是可选参数，最好不写
}
```

**重载的两个函数必须是静态的**，因为调用这两个函数时，是正在创建对象的过程中，此时还没有对象，所以无法通过对象来调用这两个函数。但是编译器非常体贴，即使我们不加static，**默认会给我们加上static**。因此代码中的static是可以省略的。

我们写好的重载函数，编译器会自动调用。具体流程如下：

![](https://raw.githubusercontent.com/zxpgo/images/master/img/20190807163201.png)

我们在new一个对象时，编译器自动生成代码（即图中1，2中的代码），这些代码会调用我们类中重载的operator new和operator delete。

## 重载operator new[]和operator delete[]

实现operator new[]和operator delete[]重载跟重载operator new和operator delete一样。

![](https://raw.githubusercontent.com/zxpgo/images/master/img/20190807163846.png)

## 示例

```C++
namespace FOO
{
	class Foo {
		int id;
	public:
		Foo() :id(0){
			cout << "default ctor, this = " << this << " id = " << id << endl;
		}
		Foo(int i) : id(i) {
			cout << "ctor, this = " << this << " id = " << id << endl;
		}

		~Foo() {
			cout << "dtor, this = " << this << " id = " << id << endl;
		}

		void * operator new(size_t size) {
			cout << "operator new " <<  endl;
			Foo *p = (Foo*) malloc(size);
			return p;
		}

		void operator delete(void * ptr) {
			cout << "operator delete " << endl;
			free(ptr);
		}

		void * operator new[](size_t size) {
			cout << "operator new[] " << endl;
			Foo *p = (Foo*)malloc(size);
			return p;
		}

		void operator delete[](void * ptr) {
			cout << "operator delete[] " << endl;
			free(ptr);
		}
	};
}

	using namespace FOO;
	Foo * pp = new Foo();
	delete pp;

	Foo * pp2 = new Foo[3];
	delete[] pp2;
```

输出：

```C++
operator new
default ctor, this = 00048D20 id = 0
dtor, this = 00048D20 id = 0
operator delete
operator new[]
default ctor, this = 0004503C id = 0
default ctor, this = 00045040 id = 0
default ctor, this = 00045044 id = 0
dtor, this = 00045044 id = 0
dtor, this = 00045040 id = 0
dtor, this = 0004503C id = 0
operator delete[]
```

如果使用全局的`::operator new`和`::operator delete`，就会绕过我们重载的`operator new`和`operator delete`。

```C++
	using namespace FOO;
	Foo * pp = ::new Foo();
	cout << "sizeof(Foo) = " << sizeof(Foo) << endl;
	::delete pp;

	Foo * pp2 = ::new Foo[3];
	::delete[] pp2;

输出：
sizeof(Foo) = 4
dtor, this = 0006E3E0 id = 0
default ctor, this = 000703D4 id = 0
default ctor, this = 000703DC id = 0
default ctor, this = 000703E4 id = 0
dtor, this = 000703E4 id = 0
dtor, this = 000703DC id = 0
dtor, this = 000703D4 id = 0

```

## 重载placement new和placement delete 

即new()和delete()。括号中可以放入指针和任意类型。

我们可以重载成员函数operator new()，写出多个版本，前提是每一个版本声明都必须有独特的参数列，其中第一个参数必须是size_t（因为第一参数是（）后面对象的大小），其余参数以new所指定的placement 参数为初值。出现new(...)小括号内的的便是所有的placement参数。

我们也快成重载class number `operator delete()`，写出多个版本。但是他们绝不会被delete调用。只有当new调用的构造函数抛出exception，才会调用这些重载版本的`operator delete()`。它只可能这样被调用，主要用来归还未能完全创建成功的对象所占用的内存。（但是2017VS中没有去调用！）

```C++

namespace FOO1{
	class Bad{};

	class Foo {
	public:
		Foo() {
			cout << "Foo::Foo()" << endl;
		}
		Foo(int) {
			cout << "Foo::Foo(int)" << endl;
			//throw Bad();
		}
		//这个就是一般的operator new()的重载
		void * operator new(size_t size) {
			cout << "operator new(void *,size_t)" << endl;
			return malloc(size);
		}
		//这个就是标准库已提供的placement new()的重载
		void * operator new(size_t size, void *start) {
			cout << "operator new(void *,void *)" << endl;
			return start;
		}
		//这个才是崭新的placement new
		void * operator new(size_t size, long extra){
			cout << "operator delete(void *,long)" << endl;
			return malloc(size + extra);
		}
		//这个才是崭新的placement new
		void * operator new(size_t size, long extra, char init) {
			cout << "operator new(void *,long, char)" << endl;
			return malloc(size + extra);
		}
		/*
		//Error “operator new”的第一个形参必须为“size_t”	
		void *operator new(long extra, char init) {
			return malloc(extar);
		}*/

		void operator delete(void *, size_t) {
			cout << "operator delete(void *,size_t)" << endl;
		}
		void operator delete(void *, void *) {
			cout << "operator delete(void *,void *)" << endl;
		}
		void operator delete(void *, long) {
			cout << "operator delete(void *,long)" << endl;
		}
		void operator delete(void *, long, char) {
			cout << "operator delete(void *,long, char)" << endl;
		}
	private:
		int m_i;
	};
}

	using namespace FOO1;
	Foo start;
	Foo * pp1 = new Foo;
	Foo * pp2 = new(&start)Foo;
	Foo * pp3 = new(100)Foo;
	Foo * pp4 = new(100, 'a')Foo;
	Foo * pp5 = new(100)Foo(1);
	Foo * pp6 = new(100, 'a')Foo(1);
	Foo * pp7 = new(&start)Foo(1);
	Foo * pp8 = new Foo(1);

输出:
Foo::Foo()
operator new(void *,size_t)
Foo::Foo()
operator new(void *,void *)
Foo::Foo()
operator delete(void *,long)
Foo::Foo()
operator new(void *,long, char)
Foo::Foo()
operator delete(void *,long)
Foo::Foo(int)
operator new(void *,long, char)
Foo::Foo(int)
operator new(void *,void *)
Foo::Foo(int)
operator new(void *,size_t)
Foo::Foo(int)
```



其实在使用string，就是在使用`basic_string`，只是在标准库里面被typedef成了string。在`basic_string`里面重载placement operator new()。

```C++
template<...>
class basic_string{
private:
	inline static void * operator new(size_t, size_t);
	inline static void * operator delete(void *);
};

Rep *p = new(extra) Rep;
```

为什么要加一个size_t参数，这是因为标准库里面的字符串除了字符个数的大小外，还会额外携带一包东西，所以其实内存不止字符串的大小，于是申请内存的时候需要添加额外的大小。

## 类的内存管理

 如果需要new很多对象，就要不断的调用malloc来申请内存。于是我们想实现一次通过malloc来申请大块的内存，然后从大块内存中拿一下块来存放对象，这样就避免了不断的调用malloc。这里的管理使用了链表来使用。

**第一个版本：**

```C++

namespace SCREEN {
	#include <cstddef>
	#include <iostream>
	using namespace std;
	class Screen {
	public:
		Screen(int x) : i(x){}
		int get() { return i; }
		
		void * operator new(size_t);
		void operator delete(void *, size_t);
	private:
		Screen * next; //这种设计引发多耗用一个next指针，4个字节
		static Screen * freeStore;
		static const int screenChunk;
	private:
		int i;
	};
	Screen * Screen::freeStore = 0;
	const int Screen::screenChunk = 24;

	void * Screen::operator new(size_t size) {
		Screen * p;
		if (!freeStore) {
			//链表为空，所以申请一段块
			size_t chunk = screenChunk * size;
			freeStore = p = reinterpret_cast<Screen *>(new char[chunk]);
			//将一大块分隔成一片一片，当作链表串接起来
			for (; p != &freeStore[screenChunk - 1]; ++p)
				p->next = p + 1;
			p->next = 0;
		}
		p = freeStore;
		freeStore = freeStore->next;
		return p;
	}
	void Screen::operator delete(void * p, size_t) {
		//将delete object插回free list前端
		(static_cast<Screen*>(p))->next = freeStore;
		freeStore = static_cast<Screen*>(p);
	}
}

	using namespace SCREEN;
	cout << sizeof(Screen) << endl;
	size_t const N = 100;
	Screen *point[N];
	for (int i = 0; i < N; ++i)
		point[i] = new Screen(i);

	for (int i = 0; i < 10; ++i) {
		cout << point[i] << endl;
	}
	for (int i = 0; i < 10; ++i) {
		delete point[i];
	}
```



```C++
输出： 
8                                     
00C10588
00C10590
00C10598
00C105A0
00C105A8
00C105B0
00C105B8
00C105C0
00C105C8
00C105D0
输出： 没有重载operator new和 operator delete    
8
008DE300
008DE610
008DE290
008DE140
008DE338
008DE2C8
008DE178
008DE760
008DE370
008DE5D8
```

从输出可将，如果重载operator new，一次申请大块内存，然后每次new的时候从链表中取一小块，这样就没有cookie，两个指针之间的间隔固定为8。而对于没有重载operator new对内存进行处理的，发现指针没有规律，因为内存的原因。

**第二个版本，**可以省去一个指针的开销：

```C++
namspace AIRPLANE{
	class Airplane {
	private:
		struct AirplaneRep {
			unsigned long miles;
			char type;
		};
	private:
		union { AirplaneRep rep; 
		Airplane * next;//embedded pointer做法，将类的前4个字节当作一个指针，节省指针的开销
		};
	public:
		unsigned long getMiles() { return rep.miles; }
		char getType() { return rep.type; }
		void set(unsigned long m, char t) {
			rep.miles = m;
			rep.type = t;
		}

		static void * operator new(size_t);
		static void operator delete(void *, size_t);
	private:
		static const int BLOCK_SIZE;
		static Airplane * headOfFreeList;
	};
	Airplane* Airplane::headOfFreeList;
	const int Airplane::BLOCK_SIZE = 512;

	void * Airplane::operator new(size_t size) {
		//如果大小有无，转交给::operator new
		//大小由编译器计算给出，一般不会出错，但是当继承发生时，可能出现
		if (size != sizeof(Airplane))
			return ::operator new(size);

		Airplane * p = headOfFreeList;
		if (p) //如果p有效，就把链表头部下移一个元素
			headOfFreeList = p->next;
		else {
            //free list为空，申请一块大内存
			Airplane * newBlock = static_cast<Airplane*>(::operator new(BLOCK_SIZE * sizeof(Airplane)));
            //将大块内存切成一小块一小块，并串成一个链表，但跳过第一个即（下标为0），因为它将传回做本次结果
			for (int i = 1; i < BLOCK_SIZE - 1; ++i)
				newBlock[i].next = &newBlock[i + 1];
			newBlock[BLOCK_SIZE - 1].next = 0;
			p = newBlock;
			headOfFreeList = &newBlock[1];
		}
		return p;
	}

	void Airplane::operator delete(void * deadObject, size_t size) {
		//将delete object插回free list前端
		if (deadObject == 0) return;
		if (size != sizeof(Airplane)) {
			::operator delete(deadObject);
			return;
		}
		Airplane * carcass = static_cast<Airplane*>(deadObject);
		carcass->next = headOfFreeList;
		headOfFreeList = carcass;
	}
}

	using namespace AIRPLANE;
	cout << sizeof(Airplane) << endl;
	size_t const N_Airplane = 100;
	Airplane * p_Airplane[N_Airplane];
	for (int i = 0; i < N_Airplane; ++i)
		p_Airplane[i] = new Airplane;
	p_Airplane[1]->set(100, 'A');

	for (int i = 0; i < 10; ++i)
		cout << p_Airplane[i] << endl;
	for (int i = 0; i < N_Airplane; ++i) {
		delete p_Airplane[i];
	}

输出
8
00710A38
00710A40
00710A48
00710A50
00710A58
00710A60
00710A68
00710A70
00710A78
00710A80
```

## static allocator

当你受困于必须为不同的类重写一个几乎相同的member operator new和member operator delete时，应该;有方向间隔踹总是分配特定尺寸区块大小的memory allocator概念包装起来，使它容易被重复使用。以下展示一种做法，每个allocator objector都是一个分配器，它维护一个链表；不同的allocator objects维护不同的链表。

**第三个版本：**

```C++
namespace SA {
	class allocator {
	private:
		struct obj {
			struct obj * next; //这种做法叫做embedded pointer，借用每小块内存中的前四个字节当作一个next指针，指向下一个小内存块
		};
	public:
		void * allocate(size_t size) {
			obj * p;
			if (!freeStore) {
				//链表为空，于是申请一块大内存
				size_t chunk = CHUNK * size;
				freeStore = p = (obj*)malloc(chunk);

				//将分配得来的一大块内存分隔并串成链表
				for (int i = 0; i < CHUNK - 1; i++) {
					p->next = (obj*)((char*)p + size);
					p = p->next;
				}
				p->next = nullptr;
			}
			p = freeStore;
			freeStore = freeStore->next;
			return p;
		}

		void  deallocate(void * p, size_t size) {
			//将*p收回插入到链表(free list)前端，将原来申请的内存又重新串成一个链表，方便回收
			((obj*)p)->next = freeStore;
			freeStore = (obj*)p;
		}
	private:
		obj* freeStore = nullptr;
		const int CHUNK = 5;
	};
	class Foo {
	public:
		long L;
		string str;
		static allocator myAlloc;
	public:
		Foo(long L): L(1){}
		static void * operator new(size_t size) {
			return myAlloc.allocate(size);
		}
		static void operator delete(void* pdead, size_t size) {
			return myAlloc.deallocate(pdead, size);
		}
	};
	allocator Foo::myAlloc;

	class Goo {
	public:
		complex<double> c;
		string str;
		static allocator myAlloc;
	public:
		Goo(const complex<double> x) : c(x) {}
		static void * operator new(size_t size) {
			return myAlloc.allocate(size);
		}
		static void operator delete(void * pdead, size_t size) {
			return myAlloc.deallocate(pdead, size);
		}
	};
	allocator Goo::myAlloc;
}


	using namespace SA;
	Foo * p_Foo[100];

	cout << "sizeof(Foo) = " << sizeof(Foo) << endl;
	for (int i = 0; i < 23; ++i) {
		p_Foo[i] = new Foo(i);
		cout << p_Foo[i] << ' ' << p_Foo[i]->L << endl;
	}

	for (int i = 0; i < 23; ++i) {
		delete p_Foo[i];
	}

	Goo *p_Goo[100];
	cout << "sizeof(Goo) = " << sizeof(Goo) << endl;
	for (int i = 0; i < 17; ++i) {
		p_Goo[i] = new Goo(complex<double>(i,i));
		cout << p_Goo[i] << ' ' << p_Goo[i]->c << endl;
	}

	for (int i = 0; i < 17; ++i) {
		delete p_Goo[i];
	}

输出：

sizeof(Foo) = 32
010904F0 1
01090510 1
01090530 1
01090550 1
01090570 1
010905C0 1
010905E0 1
01090600 1
01090620 1
01090640 1
01090690 1
010906B0 1
010906D0 1
010906F0 1
01090710 1
01090760 1
01090780 1
010907A0 1
010907C0 1
010907E0 1
01090830 1
01090850 1
01090870 1
sizeof(Goo) = 48
01090900 (0,0)
01090930 (1,1)
01090960 (2,2)
01090990 (3,3)
010909C0 (4,4)
01089CA0 (5,5)
01089CD0 (6,6)
01089D00 (7,7)
01089D30 (8,8)
01089D60 (9,9)
01091540 (10,10)
01091570 (11,11)
010915A0 (12,12)
010915D0 (13,13)
01091600 (14,14)
01091660 (15,15)
01091690 (16,16)
```

注意：`struct obj {struct obj * next; };`这种做法相当于暂时的利用一小块内存的前4个字节，当成一个指针 ，而在使用这块内存来分配对象的时候，这个指针就会消失，因为往前移动了。跟之前的第二个版本一样。

## macro for static allocator

**第四版本：**对版本三做了一些改动，设计两个宏(macro)来代替重复的动作。

```c++
namespace SA {
	class allocator {
	private:
		struct obj {
			struct obj * next; //这种做法叫做embedded pointer，借用每小块内存中的前四个字节当作一个next指针，指向下一个小内存块
		};
	public:
		void * allocate(size_t size) {
			obj * p;
			if (!freeStore) {
				//链表为空，于是申请一块大内存
				size_t chunk = CHUNK * size;
				freeStore = p = (obj*)malloc(chunk);

				//将分配得来的一大块内存分隔并串成链表
				for (int i = 0; i < CHUNK - 1; i++) {
					p->next = (obj*)((char*)p + size);
					p = p->next;
				}
				p->next = nullptr;
			}
			p = freeStore;
			freeStore = freeStore->next;
			return p;
		}

		void  deallocate(void * p, size_t size) {
			//将*p收回插入到链表(free list)前端，将原来申请的内存又重新串成一个链表，方便回收
			((obj*)p)->next = freeStore;
			freeStore = (obj*)p;
		}
	private:
		obj* freeStore = nullptr;
		const int CHUNK = 5;
	};

	#define DECLARE_POOL_ALLOC() \
	public: \
		void * operator new(size_t size) { return myAlloc.allocate(size); } \
		void  operator delete(void * p) { return myAlloc.deallocate(p, 0); } \
	protected: \
		static allocator myAlloc;

	#define IMPLEMENT_POOL_ALLOC(class_name) \
	allocator class_name::myAlloc;

	class Foo {
		DECLARE_POOL_ALLOC();
	public:
		long L;
		string str;
	public:
		Foo(long l) :L(l) {}
	};
	IMPLEMENT_POOL_ALLOC(Foo);
}

	Foo * p_Foo[100];

	cout << "sizeof(Foo) = " << sizeof(Foo) << endl;
	for (int i = 0; i < 23; ++i) {
		p_Foo[i] = new Foo(i);
		cout << p_Foo[i] << ' ' << p_Foo[i]->L << endl;
	}

	for (int i = 0; i < 23; ++i) {
		delete p_Foo[i];
	}

输出：
sizeof(Foo) = 32
00F104F0 0
00F10510 1
00F10530 2
00F10550 3
00F10570 4
00F105C0 5
00F105E0 6
00F10600 7
00F10620 8
00F10640 9
00F10690 10
00F106B0 11
00F106D0 12
00F106F0 13
00F10710 14
00F10760 15
00F10780 16
00F107A0 17
00F107C0 18
00F107E0 19
00F10830 20
00F10850 21
00F10870 22
```

## global allocator

标准库中的分配器，跟版本三的实现相同，只不过标准库中具有16条链表，大小不一，可以处理16中不同的大小。标准库的分配器是全局，针对所有的类服务。具体如下图：

![](https://raw.githubusercontent.com/zxpgo/images/master/img/20190811171249.png)

## new handler

当operator new每能力为你分配出你所申请的的内存，会抛出一个std::bad_alloc exception。某些老编译器则是返回0，你也可以让编译不抛出异常，而是返回0，即如下的形式：

``` C++
new(nothrow)Foo;
```

此种为nothrow形式。

抛出异常之前会先调用一个可有用户指定的函数(handler)，以下是new handler的形式和设定方法：

```C++
typedef void(* new_handler)();
new_handler set_new_handler(new_handler p) throw();
```

设计良好的new handler只有两种选择：

- 让更多内存可用
- 调用abort()或exit()

一个例子

```C++
namespace NEWHANDLER {
#include <new>
#include <iostream>
#include <cassert>
	using namespace std;
	void noMoreMemory() {
		cerr << "out of memory";
		abort();
	}
}
void main()
{
	using namespace NEWHANDLER;
	set_new_handler(noMoreMemory);

	int * ppp = new int[100000000000000000];
	assert(ppp);
	ppp = new int[100000000000000000000];
	assert(ppp);
}
```

注意：有些编译器可能会提示数组长度过大，比如vs2017上就无法运行上述程序。

## =default，=delete

```C++
class Foo{
public:
    Foo() = default;
    Foo(const Foo &) = delete;
    Foo & operator=(const Foo &) = delete;
    ~Foo()=default;
    //....
};
```

这两个关键字不仅使用于构造函数、拷贝构造函数、赋值构造函数以及析构函数，而且适用于operator new/delete和operator new[]/delete[]以及他们的重载版本。在实际中，

![](https://raw.githubusercontent.com/zxpgo/images/master/img/20190811192619.png)

# std::allocator

## vs6中的标准分配器实现

从下图可以看出vs6的allocator什么事也没有做，只是转接调用operator new和operator delete。

![](https://raw.githubusercontent.com/zxpgo/images/master/img/20190812200010.png)

## BC5标准分配器实现

BC5跟vc6一样，没有任何特殊设计。

![](https://raw.githubusercontent.com/zxpgo/images/master/img/20190812200245.png)

## G2.9标准分配器指向

G2.9的设计跟vc6和BC5一样。

G2.9中容器使用的分配器并不是allocator，而是采用的alloc。

![](https://raw.githubusercontent.com/zxpgo/images/master/img/20190812201002.png)

## G2.9中的std::alloc vs. G4.9中的__pool_alloc

G2.9中的std::alloc和G4.9中的__pool_alloc的设计一样，只是修改了变量名。

![](https://raw.githubusercontent.com/zxpgo/images/master/img/20190812201426.png)

## G4.9标准分配器实现

G4.9版本中的标准分配器也没有特殊设计，只是转接调用::operator new和::operator delete。

G4.9版本中，__pool_alloc分配器实现了去除cookie.。



## G2.9 std::alloc运行模式

std::alloc分配的内存不带cookie，std::alloc分配内存的方式：

当一个容器使用alloc分配内存时，比如需要申请一个元素的大小为32个字节，这是alloc会申请32×20×2个字节大小的内存。乘以20是alloc的设置，就是每次申请20个元素的内存。而乘以2是了申请一个备战内存池，意思就是每次申请内存时，都会多申请一倍，以备之后申请内存用。

如果试了第一个32字节的内存，链表的指针指向下一个第二个32字节。

如果容器再一次需要申请一个元素大小为64字节的内存，此时发现存在备战内存池，就不需要再去申请内存了，直接在32字节后面备战内存池中取，即就备战内存池分为10个64字节的内存块。

如果容器再一次需要申请一个元素大小为96字节的内存，此时发现没有剩余的备战内存池，所以直接调用malloc取申请96×20×2的内存，接下来的动作跟申请32个字节内存的一样。

![](https://raw.githubusercontent.com/zxpgo/images/master/img/20190813110224.png)

## embedded pointers(嵌入式指针)

内存块将前4个字节当作一个指针，串成一个链表。归还内存的时候，重新就内存中的4个直接当作直接，重新衔接到链表中。

图中的每一个小块就是容器需要申请一个元素的大小，将前四个字节当作一个指针，如果容器申请内存时，指针往前移动一个，指向下一个可以分配给容器的内存块。如果容器归还内存，重新就前4个字节当作指针，并且拼接到链表（单向链表）中。

![](https://raw.githubusercontent.com/zxpgo/images/master/img/20190813111516.png)

商用的内存管理都是采用这种嵌入式指针。

如果对象大小小于4个字节，这种联想是对的。但是现实中基本所有对象都是大于等于4个字节的。

## std::alloc的整个过程

**第一个状态**，16根指针为空。

![](https://raw.githubusercontent.com/zxpgo/images/master/img/20190813132118.png)

**第二步**：用户申请32个字节的内存，分配器一般都是给容器使用的，而不是应用程序。如果直接使用分配器，比如记住申请了多大的内存，因为没有cookie去记住了，不然就没法回收。而对于容器，所有元素的大小都一样，通过sizeof容器元素的类型就可以知道。

容器申请32个字节的元素，由于战备内存池为空，所以必须申请大块内存，即32×20×2+RoundUp(0>>4)=1280的内存放到战备内存池中，并且出一个小块给容器，剩余19个小块内存放在链表#3上，余下的640字节的内存当作备战内存池，给下个来申请内存的容器使用。累计申请总量1280。

![](https://raw.githubusercontent.com/zxpgo/images/master/img/20190813132353.png)

图中所说的pool就是战备内存池。RoundUp是一个函数,将一个数字调整到16的倍数，0>>4将一个数字除以16。

而对于每次申请的大块内存，是通过malloc来实现的，所以这个大块内存上下都带有cookie（图中没有画出）。

**第三步**：一个新的容器申请64字节大小的内存。

容器申请64字节大小的内存时，发现#7号是空的，所以去战备内存池查看释放有余量，上次战备内存池中有640字节的大小，所以将640切割成10个小内存块，第一个小块给容器使用，剩余9个小内存块。此时备战内存池大小为0。累计申请总量1280。

![](https://raw.githubusercontent.com/zxpgo/images/master/img/20190813133638.png)

在战备内存池中切出来的小内存块数量在1到20之间。

**第四步**：又一个新的容器申请96字节大小的内存。

容器申请96字节大小的内存时，发现#11号链表是空的，并且发现战备内存池是空的。所以通过malloc申请一大块内存，大小为96×20×2+RoundUp(1280>>4)（1280是目前的累计申请总量申请的总量，>>4表示除以16）。将第一个小内存块给容器，剩余19个小内存块。备战内存池剩余2000个字节 ，累计申请量为5200字节。

![](https://raw.githubusercontent.com/zxpgo/images/master/img/20190813134223.png)

**第五步：**又一个新容器申请88字节大小的内存。

首先发现#10号链表为空，战备内存池有余量，故从战备内存池中分出20个小块，第一个小内存块分配给容器，剩余19个小内存块。战备内存池余量为240，累计申请总量：5200。

![](https://raw.githubusercontent.com/zxpgo/images/master/img/20190813135549.png)

**第六步**：容器连续三次申请88字节的内存。

发现#10号链表不为空，还有许多余量，直接分配三个容器，链表指针向前移动三次。战备内存池余量为240，累计申请总量：5200。

![](https://raw.githubusercontent.com/zxpgo/images/master/img/20190813135831.png)

**第七步：**又一个新容器申请8字节大小的内存。

首先发现#0号链表为空，而战备内存池不为空，有余量。故从战备内存池中分割20个区块，第一个小内存块给容器，剩余19个小内存块。战备内存池余量为80，累计申请总量：5200。

![](https://raw.githubusercontent.com/zxpgo/images/master/img/20190813140254.png)

**第八步：**又一个新容器申请104字节的内存。

首先发现#12链表为空，并且备战内存池的余量不足供应一个，于是先将战备内存池余量的80字节分配给#9号链表（**碎片处理**）。然后利用malloc去申请大块内存，大小为：1024×20×2+RoundUp(5200>>4)。第一个小块分配给容器，切出19个小块分配给链表，剩余2408字节。战备内存池余量为2408，累计申请总量：9688。

![](https://raw.githubusercontent.com/zxpgo/images/master/img/20190813140334.png)

**第九步：**又一次申请112字节的内存。

首先发现#13号链表为空，于是查看战备内存池，从中分割20个小内存块，第一个分配给容器，剩余19个小内存块给链表。战备内存池余量为168，累计申请总量：9688。跟之前重复，没有新内容。

![](https://raw.githubusercontent.com/zxpgo/images/master/img/20190813141153.png)

**第十步：**容器申请48字节的内存。

首先发现#5号链表为空，由于战备内存池有余量，从中分割出3个小内存块，余量为24，其中一个分配给容器，剩余2个小内存块。战备内存池余量为24，累计申请总量：9688。

![](https://raw.githubusercontent.com/zxpgo/images/master/img/20190813141605.png)

以上已经将所有的可能都整理出来的。但是还没有考虑当内存不足时的情形。下面讨论这种情况。但是现在内存基本不会出现这种情况，怎么去测试呢？我们修改源代码将系统内存改为10000。

**第十一步：**容器申请72字节的内存。

首先发现#8号链表为空，而战备内存池大小为24字节（碎片处理，会放到#2号链表）不足，于是需要通过malloc申请大块内存，大小为：72×20×2+RoundUp(9688>>4)，但是我们将system heap设为10000，目前已经用掉了9688，无法满足此次申请，于是alloc从手中资源取最接近72字节大小，即80（#9号链表）来回填战备内存池，再从中切出72字节给容器。战备内存池余量为7，累计申请总量：9688。

![](https://raw.githubusercontent.com/zxpgo/images/master/img/20190813142154.png)

**第十二步：**容器再次申请72字节的内存。

#8链表没有可用的小内存块，而战备内存池余量又不足一个，于是先将战备内存池给#0号链表（碎片处理），然后申请72×20×2+RoundUp(9688>>4)大小的内存，但是我们将system heap设为10000，目前已经用掉了9688，无法满足此次申请。于是alloc从手中字节最接近72字节链表的88字节链表，即#10号链表，回填战备内存池，再从中切除72字节内存给容器，余16个字节。战备内存池余量为16，累计申请总量：9688。

![](https://raw.githubusercontent.com/zxpgo/images/master/img/20190813144010.png)

**第十三步：**容器申请120字节的内存。

首先发现#14号链表没有为空，而备战内存池的余量不足一个，于是将备战内存池余量（16个字节）拨给#1号链表（碎片处理），然后去申请120×20×2+RoundUp(9688>>4)字节的内存，但是此时无法满足申请容量。于是alloc从手中最接近#14号的链表回填备战内存池，但是找不到，终于穷途末路山穷水尽日薄西山。战备内存池余量为0，累计申请总量：9688。

![](https://raw.githubusercontent.com/zxpgo/images/master/img/20190813143847.png)

两个问题：

- alloc手中还有多少资源？以上白色的小块借是，可不可以将白色小区快合成较大区块供应给容器，技术难度极高。因为我们无法知道链表的长度，链表长度可能达到几万，无法去计算。
- system heap手中还剩多少资源？10000-9688-312。可不可以将失败的那次申请量折半...折半...再折半，最终申请量小于等于312便能申请成功，实现难度不大。

## G2.9 std::alloc 源码分析

略

[学习视频](https://www.bilibili.com/video/av45101636/?p=27)



# malloc/free

  VC6内存分配： 学习视频



 