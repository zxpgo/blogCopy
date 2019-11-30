---

title:  标准模板库STL
date: 2019-05-20 22:28:50
tags: C++
mathjax: true

---

标准模板库介绍，包括六个部件的分析。所有内容学习自侯捷老师STL模板库介绍学习教程！<!--more-->
## STL概论

C++ Standard Library (C++标准库)：可以看到所有的源代码

Standard Template Libraay(STL 标准模板库)

标准库包含标准模板库，标准模板库包含六大部件。


STL是为了建立数据结构和算法的一套标准，并且降低其间的耦合关系以及提升各自的独立性、弹性、交互操作性而诞生的。

STL是依据泛型思维架设起来的一个概念结构。


STL六大组件功能与运用：

- 容器(containers)：各种数据结构：vector、list、map、deque、set
- 算法(algorithms)：各种常用算法：sort、search、copy、erase
- 迭代器(iterators)：扮演容器和算法之间的桥梁
- 仿函式(functors)：行为类似函数，可做算法的某种策略
- 配接器(adapters)：一种用来修饰容器或仿函式或迭代器接口的东西
- 配置器(allocators)：负责空间配置和管理


![](https://i.imgur.com/8n0rfmV.png)

STL是一种模板编程。跟面对对象思想有些不同，STL将数据放在类中，而将函数独立出来。

分配器支持容器，容器是一个模板类，有些操作再容器中完成，有些独立出来成为一个个函数，组成了算法。迭代器就一种泛化的指针。仿函数作用类似于函数，类对象的相加相减都是通过仿函数实现。适配器转换一些东西。比如将容器进行转换，对仿函数进行转换，对迭代器进行转换。

六大组件使用实例：

	#include <vector>
	...
	int ia[6] = {11,22,33,44,53,61};
	vector<int, allocator<int> > vi(ia, ia+6);
	cout << count_if(vi.begin(), vi.end(), not1(bind2nd(less<int>(), 40)));//条件小于40的元素个数
	
vector是容器，allocator<int>是分配器，存在默认值。注意容器类型和分配器的类型必须相配。

count_if是一个函数（算法），计算符合给定条件的元素个数；算法操作的元素范围由迭代器指定，vi.begin()和vi.end()是迭代器。每个元素跟40比较，less是一个仿函数，即小于；bind2nd是一个适配器，绑定第二参数，即将less的第二个参数绑定为40；not1表示否定的意思，即大于等于40。 not1(bind2nd(less<int>(), 40))表示判断大于等于40的条件式。

![](https://i.imgur.com/o5j1FJO.png)

算法已经固定，没有可选的。而容器不同，可以根据实际情况，数据情况和分布，来选择适合的容器。说到容器和算法就要提到复杂度（complexity, Big-oh）。

- 常数时间o(1)或o(c)
- 线性时间o(n)
- 指数时间o(2^n)
等等。


### STL版本：

GNU（GNU is not Unix）宣言来反对封锁源码。GNU以GPL(广泛开放授权)来保护其成员：使用者可以自由阅读和修改GPL软件的源码，但如果使用者要传布借助GPL软件而完成的软件，他们必须也同意GPL规范。

- HP版本的STL:允许任何人免费使用、拷贝、修改、传布、贩卖这份软件及其说明文件，并不属于GNU GPL范畴。
- PJ版本的STL，继承自HP版本，不属于open source范畴，更不属于GNU GPL。因为HP的版本声明并发GPL，并没有强迫其衍生产品必须开放源码。PJ版本被Visual采用，所以可以在visualc++的“include”子目录下找到所有STL头文件，但是不能公开或修改它。
- RW(Rouge Wave)版本的STL(RW STL)同样继承了HP版本，也不属于open source范畴，更不是GNU GPL。RW STL被C++ Builder采用，所以在C++Builder的“include”子目录下找到所有的STL头文件。
- STLport
- SGI STL继承了HP版本，SGI版本被GCC采用，可以在GCC的“include”子目录下找到所有的STL头文件，并且获准自由公开它或修改它甚至贩卖它。__与前面所有版本相比，这个版本的可读性非常高。__


### GNU c++headers文件分布：

- C++标准规范下的C头文件（无扩展名），例如cstdio, cstirng
- C++标准链接库中不属于STL范畴者，例如，string,stream等
- STL标准头文件（无扩展名），例如vector,deque,list,map,algorithm,functional
- C++ standard定案前，HP所规范的STL头文件，例如vector.h,deque.h,list.h,map.h,function.h
- SGI STL内部文件（STL起作用的部分），例如stl_vector, stl _deque.h,stl _list.h,stl _map.h等


SGI STL准备了一个环境组态文件<stl_config.h>，其中定义许多常数，表示某些状态的成立与否。所有STL头文件都会直接或间接含入这个组态文件，并以条件式写法，让前处理器根据各个常数决定取舍哪一段程序码。

所有新式的headers内的组件被封装到namespace std。C中旧式头文件（带.h）不被封装到namespace std。


### 令人困惑的C++语法


暂时（临时）对象，就是一种无名的对象。例如，int(8)，其意义相当于使用响应的构造函数生成对象且不指定对象的名称。

静态常数整数成员在class内部直接初始化

	class testClass
	{
	public:
		static cosnt int  _datai = 5;
		static const long _datal = 3L;
		static const char _datac = 'c';
	};

静态类成员，需要在类外部初始化：

	class testClass
	{
		static int _datai;
	};

	testClass::_datai = 3;

而且对于静态类成员，所有对象都共享一种静态类成员。


#### 前闭后开区间表示法[)

任何一个STL算法，都需要获得一对迭代器所标示的区间，用以表示操作范围。这一对迭代器所表示的是个所谓的前闭后开区间，以[begin, end)表示，即，整个实际范围从begin开始，直到end-1。迭代器end所指的是最后一个元素的下一个位置。

注意，容器不一定是连续空间，比如链表，树等。

![](https://i.imgur.com/djeYwXH.png)

定义迭代器：vector<int>::iterator ite = c.being();所有指针的操作，都可以使用到迭代器上，比如++，--, *，->等。

c++11中基于范围的for循环：

	for (int i: {2,3,4,5,6,7})
		std::cout << i << std::endl;

	std::vector<double> vec;
	...
	for(auto elem: vec)
		std::cout << elem << std::endl;
	for (auto& elem : vec) //如果不使用引用，值将不会变
		elem *= 3;

auto:

	list<string> c;
		....
	lsit<string>::iterator ite;
	ite = ::find(c.begin(), c.end(), target);

    //使用auto
	list<string> c;
		...
	auto ite = ::find(c.begin, c.end, targe);

#### function call运算子

函数呼叫动作也可以被多载化。许多STL算法提供两个版本，一个用于一般请情况，例如排序时以递增方式排列；一个用于特殊状况，例如排序时有使用者指定以何种特殊关系进行排序。

像这种情况，需要使用者指定某个条件或某个策略，而条件或策略的背后由一整组动作构成，便需要某种特殊的东西来代表这一整组动作。代表这一整组动作的当然是函数。过去，C语言时代，欲将函数当作参数传递，唯有通过函数指针才能达成。但是函数指针优缺点，最重要的是它无法持有自己的状态（所谓区域状态），无法再将某些修饰条件加诸于其上而改变其状态。

为此，STL算法的特殊版本所接收的所谓条件或策略或一整组动作，都以仿函数形式呈现。所谓仿函数（functor）就是使用起来像函数一样的东西。如果你针对某个类进行operator()多载化，它就成为了一个仿函数。


## 空间配置器allocator

整个STL的操作对象都存放在容器之内，而容器一定需要配置空间以置放数据。

alocator是空间配置器而不是内存配置器。因为，空间不一定是内存，空间也可以是磁盘或其他辅助存储媒体。例如，可以写一个allocatro直接向磁盘取空间。

### 空间配置器的标准接口

### 具备次配置力(sub-allocation)的SGI空间配置器

SGI STL的配置器与众不同，也与标准规范不同，其名称为alloc而非allocator。

不能采用标准的写法：

	vector<int, std::allocator<int>> iv; //in VC or CB

必须这么写：

	vector<int, std::alloc> iv; //in GCC

但是SGI STL的每一个容器都已经指定其预设的空间配置器alloc。例如下面的vector声明：

	template<class T, classAlloc = alloc> //预设使用alloc为配置器
	class vector {...};


#### SGI标准的空间配置器std::allocator

虽然SGI也定义有一个符合部分标准、名为allocator的配置器，但SGI自己从未使用过它，也不建议使用。主要原因是效率太差，只把C++的::operator new和::operator delete做一层薄薄的包装而已。。

#### SGI特殊的空间配置器std::alloc

前面所讲的allocator只是基层内存配置/释放行为（也就是说::operator new和::operator delete）的一层薄薄包装，并没有考虑任何效率上的强化。

一般而言，C++内存配置和释放动作是这样：

	class Foo{...};
	Foo * pf = new Foo;
	delete pf;

这其中的new算式内含两阶段动作：调用::operator new配置内存； 调用Foo::Foo()构建对象内容。delete算式也内含两阶段动作：调用Foo::~FOO()将对象析构；调用::operator delete释放内存。

为了精密分工，STL allocator决定将这两阶段动作区分开来。内存配置动作由alloc::alllocate()负责，内存释放动作由alloc::deallocate()负责；对象构造动作由::construct()负责，对象析构动作由::destroy()负责。

STL标准规格告诉我们，配置器定义于<memory>之中，SGI<memeory>内含一下两个文件：

	#include<stl_alloc.h> //负责内存空间的配置和释放
	#include<stl_construct.h> //负责对象内容的构造和析构

 ![](https://i.imgur.com/mT8eu7w.png)



## 迭代器概念与traits编程技法

STL的中心思想在于，将数据容器和算法分开，彼此独立设计，最后再以一帖胶着剂将他们撮合在一起。容器和算法的泛型并不困难，C++的类模板和函数模板课分别达成目标。设计两者之间的胶着剂才是难题。

### 迭代器是一种smart pointer

迭代器是一种行为类似指针的对象，而指针的各种行为中最常见的也最重要的就是解除引用(dereference)和成员提取（member access）。因此迭代器最重要的编程工作就是对operator *和operator->进行重载。


### 容器分类

- 序列式容器： 固定数组Array, 可变数组Vector, 双队列Deque， 双向链表List， 单向链表Forward-List。 

分配器负责vector的自动扩充。

- 关联式容器： Set:红黑树, Map  ;适用于需要大量查找的数据结构

红黑树一种特殊的二叉树，高度平横的二叉树，左右差不多，自动完成。并没有要求使用哪个

- 不定序容器：就是一种关联式容器 



lsit使用示例：


	namespace STL_List{
		woid test_list(long value)
		{
			list<string> c;
			char buf[10];
			for (long i = 0; i < value; i++)
			{
				try{
					snprintf(buf, 10, "%d", rand());
					c.push_back(string(buf));
				}
				catch(exception &p)
				{
					cout << "i=" << i << " " << p.what() << endl;
					abort();
 				}
			}
			cout << "list.size()=" << c.size() << endl;
			cout << "list.max_size()" << c.max_size() << endl;
			cout << "list.front()"  << c.front() << endl;
			cout << "list.back()" << c.back() << endl;

			string target = c[value/2];
			auto pItem = find(c.begin(), c.end(), target); //查找
			if (pItem != c.end())
				cout << "found, " << *pItem << endl;;
			else
				cout << "not found" << endl; 

			c.sort();  //list自带sort，所以使用自带的sort，而不使用全局sort
			
		}
		
	}


froward_list使用示例：

	namespace STL_forward_list{
		void test_forward_list(long value)
		{
			forward_list<string> c;
			char buf[10];
			for (long i = 0; i < value; i++)
			try{
					snprintf(buf, 10, "%d", rand());
					c.push_front(string(buf));  //注意单向链表没有push_back
				}
				catch(exception &p)
				{
					cout << "i=" << i << " " << p.what() << endl;
					abort();
 				}

			//cout << "forward_list.size()=" << c.size() << endl;  //不提供
			cout << "forward_list.max_size()" << c.max_size() << endl; 
			//cout << "forward_list.front()"  << c.front() << endl;  
			cout << "forward_list.back()" << c.back() << endl;  //不提供back()
			
			string target = c[value/2];
			auto pItem = find(c.begin(), c.end(), target); //查找
			if (pItem != c.end())
				cout << "found, " << *pItem << endl;;
			else
				cout << "not found" << endl; 

			c.sort();  //排序
			
		}
	}


slist使用示例：

slist是GNU g++自带的，是一种非标准的。

用法跟forward_list相同。

![](https://i.imgur.com/5aYs6Lj.png)

deque使用示例：

deque是分段连续的，但是对用户来说是完全连续的。如果空间不足，deque在两端都可以扩充一个buffer，这样就实现了双向队列。

![](https://i.imgur.com/u82baSj.png)
	
	namespace STL_deque
	{
		void test_deque(long value)
		{
			deque<string> c;
			char buf[10];
			for (long i = 0; i < value; i++)
				{
					try{
						snprintf(buf, 10, "%d", rand());
						c.push_back(string(buf));
					}
					catch(exception &p)
					{
						cout << "i=" << i << " " << p.what() << endl;
						abort();
	 				}
				}
		
			cout << "deque.size()=" << c.size() << endl;
			cout << "deque.max_size()" << c.max_size() << endl;
			cout << "deque.front()"  << c.front() << endl;
			cout << "deque.back()" << c.back() << endl;
	
			string target = c[value/2];
			auto pItem = find(c.begin(), c.end(), target); //查找
			if (pItem != c.end())
				cout << "found, " << *pItem << endl;;
			else
				cout << "not found" << endl; 
	
			sort(c.begin(), c.end());  //deque不自带排序函数，所以需要调用全局的sort
		}
	}

stack使用示例:

![](https://i.imgur.com/7u4ols8.png)

一个deque涵盖了stack的功能，stack内部使用deque实现。

	namespace STL_stack
	{
		void test_stack(long value)
		{
			stack<string> c;
			char buf[10];
			for (long i = 0; i < value; i++)
				{
					try{
						snprintf(buf, 10, "%d", rand());
						c.push(string(buf));
					}
					catch(exception &p)
					{
						cout << "i=" << i << " " << p.what() << endl;
						abort();
	 				}
				}
		
			cout << "stack.size()=" << c.size() << endl;
			cout << "stack.top()" << c.top() << endl;
			c.pop();
			cout << "stack.size()"  <<  << endl;
			cout << "stack.top()" << c.top() << endl;
		}
	}

queue使用示例：

![](https://i.imgur.com/yi0GvGY.png)

一个deque涵盖了queue的功能，queue内部使用deque实现。


	namespace STL_queue
	{
		void test_queue(long value)
		{
			queue<string> c;
			char buf[10];
			for (long i = 0; i < value; i++)
				{
					try{
						snprintf(buf, 10, "%d", rand());
						c.push(string(buf));
					}
					catch(exception &p)
					{
						cout << "i=" << i << " " << p.what() << endl;
						abort();
	 				}
				}
		
			cout << "queue.size()=" << c.size() << endl;
			cout << "queue.front()" << c.front() << endl;
			cout << "queue.back()" << c.back() << endl;
			c.pop();
			cout << "queue.size()"  <<  << endl;
			cout << "queue.front()" << c.front() << endl;
			cout << "queue.back()" << c.back() << endl;
	
		}
	}


## 分配器

这里仅仅适用于GNU C编译器。

![](https://i.imgur.com/dcMvf1W.png)

默认都是使用std::allocator分配器。

其他分配器使用方法（仅适用于GNU C）：

![](https://i.imgur.com/ZeUNAi7.png)

以上都是在在容器上使用分配器来分配内存。也可以单独使用分配器，但是没有必要。

	int *p;
	allocator<int> alloc1;
	p = alloc1.allocate(1); //1表示分配一个int元素
	alloc1.deallocate(p,1); //释放内存，1表示释放内存的大小

	__gnu_cxx::malloc_allocator<int> alloc2;
	p = alloc2.allocate(1);
	alloc2.deallocate(p,1);

一般使用new，delete或malloc或free。new和delete最终也是调用malloc和free。

malloc分配内存，使用free释放内存时，不需要告诉free需要释放的内存大小。而分配器释放内存时，需要告知其需要释放的内存大小，这样太麻烦。




## 体系结构与内核分析

源码之前，了无秘密

GNU C 2.9

标准库主要使泛型编程（Generic Programming, GP）实现的。

OOP概念：将datas和methods关联在一起

	template <class T, class Alloc = alloc>
	class list{
		...
		void sort();
	};

list内部定义了一个sort函数，而不是用全局的sort函数。

![](https://i.imgur.com/ZNzJ5BI.png)

list所具备的迭代器，只能逐个访问，而不能向vector等其他容器一样直接进行跳转。全局sort需要的迭代器要满足一定的条件：迭代器是可以连续访问的，而list无法满足，list是双向链表，在内存中存储是不连续的。所以需要定义私有的sort函数。

GP概念：将datas和methods分开来

数据结构（容器）:

	template<class T, class Alloc = alloc>
	class vector{
	...
	};

中间通过迭代器将容器和算法连起来。

	sort(c.being(),c.end());

算法：

	template<typename _RandomAccessIterator>
	inline void
	sort(_RandomAccessIterator __first,
			_RandomAccessIterator __last)
	{
		...
	}


min和max的源代码：
	
	template<class T>
	inline const T & min(const T & a, const T &  b){
		return b < a ? b:a;
	}

	template<class T>
	inline const T & max(cosnt T & a, const T & b){
		return a < b ? b:a;
	}


所有算法，其实最终涉及元素本身的操作，无非就是比大小。

	bool strLonger(const string & s1, const string & s2)
	{ return s1.size() > s2.size(); }

	cout << "max of zoo and hello: " << max(string("zoo"), string("hello")) << endl; //zoo
	cout << "max of zoo and hello: " << max(string("zoo"), string("hello"), strLonger) << endl; //hello

	template<class T>
	inline const T & max(const T & a, const T & b)
	{ return a > b ? a : b; }
	
	template<class T, class Compare>
	inline const T & max(const T & a, const T & b, Compare comp)
	{ return comp(a, b) ? a : b; }
 

模板：

- 类模板
- 函数模板
- 成员模板：比较复杂

泛化与特化：

对于一类问题，提供一种泛化的做法，但是对于某些特定的问题，有更优的做法，所以特定为其再定义一种做法。

![](https://i.imgur.com/HfCnlFQ.png)

template<>的出现，就表示特化。

![](https://i.imgur.com/blTyLj2.png)

特化（全特化），偏特化（局部特化）

![](https://i.imgur.com/GUDOeEX.png)

其中类型具体化位bool，但是分配器还是模板参数。

左侧是个数的偏特化。

右侧是范围的偏特化，第一种偏特化是指向一个任意类型的指针，第二种偏特化是指向一个常量指针。


### 分配器allocators


operator new()和malloc()

![](https://i.imgur.com/4wvYbhI.png)

operator new()会调用malloc()，malloc所申请的内存比你提供的size大，需要附加一些内容（开销）。小块内存，开销比例大，大块内存，开销比例小。

- VC版本：

![](https://i.imgur.com/8AjRbsM.png)

申请512的内存：

	int *p = allocator<int>().allocate(512, (int*)0); //allocator<int>() 创建一个临时对象，没有名称
	allocator<int>().deallocate(p, 512); //释放内存

分配器最终调用malloc和free申请和释放内存。

- BC5版本

![](https://i.imgur.com/Vmta3PO.png)

申请512内存：

	int *p = allocator<int>().allocate(512);
	allocator<int>().deallocate(p,512);

- GNU C2.9版本

![](https://i.imgur.com/rZDymea.png)

三者都是一样，使用malloc和free来申请和释放内存，这样会带来很大的开销，因为每申请一个内存需要附带一些内容，而申请的内存都比较小，会导致附带的开销比例大，不能接收。

GNU C上述标准分配器没有被使用到任何容器中。而是使用别的分配器alloc。

![](https://i.imgur.com/Zduzw3S.png)

具体形式如下：

![](https://i.imgur.com/rUBbzqR.png)

alloc尽量减少调用malloc的次数，这样可以降低额外开销。malloc每次申请时，需要在内存中记录申请的内存块大小。容器中的元素大小是一样的，所以没必要每次申请都记录内存块大小，不要cookies。

alloc设计了16条链表，每条链表负责某一种特定大小的内存块，用链表串起来。

0号链表负责8字节的大小，7号链表负责64个字节的大小，15号链表负责128个字节的内存大小。

所有的容器当它需要内存的时候，容器元素的大小会调整到8的倍数，如50调整到56，然后在链表中查找。找到对应编号的链表。如果链表中挂有内存，则直接使用。如果没有，再跟操作系统malloc要一大块来做切割，然后用链表串起来。其中每一块不用记录内存块大小，即不要带cookies。注意，每次用malloc去申请一大块内存时，还是需要cookies，只是每一个小块不需要。

比如，如果容器申请100 0000个元素，则可以省掉800 0000个字节，这只是一个概念上的估计，实际上大块内存需要附带记录信息cookies。


- GNU C4.9版本

4.9版不再使用分配器alloc，而是使用allocator。

![](https://i.imgur.com/A2V4kSc.png)

这种没有特殊设计，没有使用优化的alloc。但是alloc还在，只是更改了名字`__pool_alloc`。

![](https://i.imgur.com/7wIs9CV.png)

使用方法： 

	vector<string, __gnu_cxx::__pool_alloc<string> > vec; 



### 容器


![](https://i.imgur.com/WyLgOgE.png)

容器之间没有继承关系。

heap是一个特殊的容器，heap里面有一个vector做底部支撑。

rb_tree是红黑树。set里面有一个红黑树做底部的支撑。

sizeof()表示控制一个容器的所需的大小。


#### list

![](https://i.imgur.com/ytXA96s.png)

list_node是一个节点，node就是一个指向节点的指针。list _node中的指针指向void，后面需要进行转型，2.9版就是如此，但是4.9中进行了改善。放入1000个元素，所耗费的内存，不仅仅是1000个元素的大小，因为每个元素还要包括两个指针的大小。

除了vector和array外，所有的容器有一个iterator类。list中，iterator类：对应`__list_iterator`

iterator类里面有大量的操作符重载，iterator模拟指针，实现指针的各种操作。

![](https://i.imgur.com/kJpqeJx.png)

所有的迭代器开始有一堆的typedef(至少5个)，然后一堆function实现操作符重载。

list中迭代器的++实现：iterator中有两个++，一个带参数一个不带参数。

为了区分前++和后++，前++没有参数，后++带参数，但是参数没有意义，只是形式上的。

前++的实现：

	node = (link_type)((*node).next);
	return *this;

后++的实现：

后++需要把原来的内容记录下来，然后再++。比如： a = i++，首先将原值记录，进行前++操作，然后将原值返回给a。

	self tmp = *this;  //记录原值
	++*this;           //进行操作
	returm tmp;        //原值返回

![](https://i.imgur.com/LIrHFZG.png)

$\ast$this不会调用`operator*()`，在`*`之前有一个等于号，表示调用拷贝构造函数，所以`*this`变成了拷贝构造函数的参数。

还需要注意，前++和后++重载函数的返回值。前++的返回值是self &，而后++的返回值为self。

	int i(6);
	+++++i; -> (++)++i;  //这是允许的
	i+++++; -> (i++)++; //这是不允许的，不准后加加两次

所以后++的返回值是self，没有引用，这样就不允许后++两次。而前++允许两次，所以前++的返回值为self &。

解引用，即提取值。包括两种操作：*和->

![](https://i.imgur.com/9G6tbBw.png)

list容器的迭代器使用实例：

	list<Foo>::iterator ite;
	*ite; //获得一个Foo对象
	ite->method();


2.9版和4.9版对比：

![](https://i.imgur.com/5Jw7QaT.png)


4.9版本对2.9版本进行了改进，迭代器类中模板传递的类型有3个变成了1个，原来是T，T&和T*，4.9版本变成了一个T，然后在迭代器类中实现了T&，T$\ast$。

还有一个点在2.9中，节点的设计开始是void类型的指针，而在4.9版本中，节点中的指针的类型是节点本身。

4.9版本中，list继承了`_List_base`父类，父类内含`_List_impl`类，....

而在2.9版本中，只有一个类list。

2.9版中只有一个指针，而4.9版本中有两个指针。

![](https://i.imgur.com/vRizhOP.png)


Iterator需要遵循的原则：


rotate函数调用`__rotate`函数。

iterator_category:表示迭代器的移动性质，有些迭代器只能向前移动，有些迭代器可以先后移动，有些迭代器只能移动一步，有些迭代器可以跳着走，一次条多步。

iterator的value_type: iterator所指的元素类型

iteartor的difference_type:两个iterator之间的距离，用什么类型表示，比如unsigned int的距离不超过2^32，如果一个迭代器的范围不超过2^32，就可以用unsinged int来表示两iterator之间的距离。

![](https://i.imgur.com/K1p13tR.png)

算法提出问题，迭代器能够回答问题。图中提出了3个问题。一共有5种问题。其中还有reference和pointer，但是在所有算法中从来没有使用过。

Iterator一定需要写出来的5个assocated types:

![](https://i.imgur.com/JZTRbwL.png)


算法中提问的方式：

	I::iterator_category;
	I::pointer;
	...

iterator中必须声明出5个associated types。对于difference_type，Iterator直接使用的某个文件中的`ptrdiff_t`。`bidirectional_iterator_tag`表示双向。

如果iterator并不是class类？只有class可以做typedef。可以使用指针，指针是一个中退化的iterator。在调用算法是可能传入的是一个指针，而不是迭代器iterator。这个时候算法提问，获取某些属性，就无法成功。

这个时候需要Traits。

Traits能够区分class形式的iterator和普通指针。

![](https://i.imgur.com/pUULLlS.png)

trats的具体做法：

![](https://i.imgur.com/bDel2pd.png)

算法想要直到I的5种相关类型，算法向`iterator_traits`提问，`iterator_traits`转问I。设置一个中间层`iterator_traits`，如果传入的是指针，调用分出的两种偏特化。如果是class iterator，则会进入`iterator_traits`。

不是采用直接的问法，而是间接的：

	typename iterator_traits<I>::value_type v1;

完整的iterator_traits：

![](https://i.imgur.com/e3NhvJi.png)

各式各样的Traits:

- type traits
- iterator traits
- char traits
- allocator traits
- pointer traits
- array traits


### 容器vector

vector中有3个指针：start指向开头，finish表示存储元素的结尾，`end_of_storage`表示容器的结尾。

![](https://i.imgur.com/5FVTJ6L.png)

如果存储的元素个数超过capacity，vector需要在内存中找一个块两倍的空间。

GNU C 2.9版本中，vector使用默认的分配器alloc。vector的大小实际是3个指针的大小(12字节)：start,finish,`end_of_storage`。

vector提供begin()和end()两个函数，以及size()函数，capacity()函数，empty()函数，operator[]重载，front()函数和back()函数。

提供连续空间的容器，都会提供operator[]重载。


二倍增长的过程：

![](https://i.imgur.com/6w1P7tN.png)

`push_back()`向容器末尾添加元素。首先，通过finish和`end_of_storage`判断是否有足够的空间存放元素，如果有直接添加即可。如果没有，调用`insert_aus`辅助函数。`insert_aus`再做一次检查，主要原因是其他函数也调用它，所以需要做检查。

如果没有空间存储元素，则执行下面的代码：

![](https://i.imgur.com/cLnpSRz.png)

首先判断原来的vector大小，如果开始是0，就分配一个元素大小的空间。如果不等于是，则直接是原来的两倍。然后是绿色allocate()分配内存。分配足够空间后，在try模块中，将原来的vector内容拷贝到新vector中，为新的元素设置初值（即将新来的元素插入），然后安插点后的内容拷贝过来（这是由于insert也需要调用该函数，insert可以在中间插入，这样需要将插入点之后的元素通用拷贝到新的vector）。  最后，释放原来的vecotr。


vector的iterator

list的迭代器是一个类，而vector的迭代器是一个指针。

![](https://i.imgur.com/56qkS1u.png)

使用vector的迭代器：

	vector<int> vec;
	vector<int>::iterator ite = vec.begin();

	iterator_traits<ite>::iterator_category; //ite给iterator_traits，由于ite是一个指针，则会调用偏特化的iterator_traits<T *>，对于指针，指针指向T，所以value_type是T，ponter是T *，reference是T &。

G4.9版本中，vector变得非常复杂。存在类的继承关系：

![](https://i.imgur.com/DfDex3b.png)

vector继承`_Vector_base`父类，父类包含`_Vector_impl`，而`Vector_impl`包含三个指针，`_Vector_impl`继承`std::allocator`父类。`_Vector_base`中有一个数据成员，该数据成员包含三个指针，所以vector的大小为12。

4.9版本中，迭代器采用类实现，变得非常复杂，但是最终目的跟2.9相同。

![](https://i.imgur.com/fJVDezc.png)

抽象后，变成：

![](https://i.imgur.com/t7DCZxm.png)


### 容器array

array比vector更简单。array在C++本来就存在，将其封装成容器，以便于可以使用算法和仿函数等。

TR1（技术报告1）是介于C++1.0和C+++2.0之间一个过度版本。C++1.0是1998版本，C++2.0是2011版本。由于TR1技术比较容器读。

![](https://i.imgur.com/ZL75d18.png)

struct和class地位等同，不过有些一些小差别。

	_M_instance[_Nm, ? _Nm : 1]; //声明一个数组，如果_Nm为0，则表示申请一个元素。

array没有析构函数和构造函数。

array的迭代器是一个指针，G2.9版中的vector也是如此。此时调用`iterator_traits`偏特化类。

array使用：

	array<int, 10> myArray; //必须指定大小
	auto ite = myArray.begin();

G4.9版本中的array:


![](https://i.imgur.com/GZdWQCa.png)

声明数组的方法：

	int c[100];   //OK
	typedef int T[100];
	T c;           //OK

这样变得复杂，但是作用跟G2.9一样。

### 容器forward_list

![](https://i.imgur.com/qilVlQV.png)

跟list相似，只不过list是双向的，forward_list是单向的。


### 容器deque

deque采用分段的形式，

![](https://i.imgur.com/d35IkhG.png)

迭代器包含4个元素：cur,first,last,node。

![](https://i.imgur.com/LDqwLqy.png)

map指向的内容是控制中心，控制中心按二倍成长。如果当前map指向的控制中心内存已经使用完，则map在内存中申请二倍的内存，并将当前的内容复制到新内存中。

对于一个缓冲区，其默认

map的类型是指向指针的指针。deque的大小为40字节。包含两个迭代器，一个指针和一个sizetype(4个字节)。

deque的迭代器包含4个指针，大小为16个字节。

![](https://i.imgur.com/HonJ1I4.png)

deque的insert()

有两个参数：迭代器，新元素值

如果插在偏前面的位置，则将元素往前面推；反之往后面推。

![](https://i.imgur.com/SXotVqV.png)

如果在前端，调用`push_front()`函数，如果在尾端，调用`push_back()`函数。如果都不是，则调用`insert_aux()`函数。

![](https://i.imgur.com/ohKVzGF.png)

`insert_aux()`首先判断插入的位置跟中点的位置关系，如果位于中点左侧，将前面的元素往前推动一个位置；如果位于右侧，执行，将后面的庸俗往后推动一个位置。

deque如何模拟连续空间的？

![](https://i.imgur.com/xpmIWTE.png)

start指向最头的元素，finish指向最后一个元素的下一个位置。front()返回start，back()返回finish后退一个位置的元素。

![](https://i.imgur.com/xVq9yHr.png)

实现连续空间都是迭代器的功能，通过迭代器操作符重载实现的。

++和--运算符重载：

![](https://i.imgur.com/10GFtHl.png)

cur++后，看是否到达一个缓冲区的边界，如果到达边界，则跳到下一个缓冲区（`set_node(node+1)`），并指向最开头的元素。

cur--后，看是否到达一个缓冲区的开头边界，如果到达开头，则调跳到前一个缓冲区(`set_node(node-1)`)，并指向最后一个元素。

+=和+运算符重载：

![](https://i.imgur.com/0IN7A0X.png)

对于-=和+运算符重载，直接利用+=和+来表现。

![](https://i.imgur.com/7QMJXfs.png)

G4.9版本

![](https://i.imgur.com/YeWcXEQ.png)

### 容器queue

queue底层实现是deque，只是将deque的某些功能关闭，就得到了queue。

![](https://i.imgur.com/BBAWOlE.png)

### 容器stack

跟queue一样，stack内含一个deque，全部功能都是转调用。

![](https://i.imgur.com/f1DHLxT.png)

stack和queue都不允许遍历，也不提供iterator。

stack和queue都可选择list或deque做为底部结构。但是deque做底部结构速度更快。

![](https://i.imgur.com/7p04AGw.png)

queue不可选择vector做为底层结构，但是stack可以选择vector做为底部结构。

![](https://i.imgur.com/Epmcpqo.png)

stack和queue都不可选择set或map做为底部结构。

![](https://i.imgur.com/7CisDIQ.png)

### 容器 rb_tree

Red-Black tree(红黑树)是平衡二叉搜索树中常被使用的一种。平衡二叉搜索树的特性：排列规则有利于search和insert，并保持高度平衡——无任何节点特深。

红黑树提供遍历操作及iterators，按照正常规则（++ite）遍历，便能获得排序的序列。

我们__不应该__使用红黑树的iterators改变元素值（因为元素有其严谨的排列规则）。编程层面并未阻止此事。如此设计是正确的，因为红黑树将为set和map服务（做为其底部数据结构），而map允许元素的data被修改，只有元素的key才是不可被修改的。

红黑树提供两种插入操作：`insert_unique()`和`insert_equal()`。前者表示节点的key一定在整个tree中独一无二，否则安插失败；后者表示节点的key可重复。


![](https://i.imgur.com/B1cokni.png)

红黑树实现：

![](https://i.imgur.com/bG7DYOd.png)

第一个模板参数是Key的类型，第二个模板参数是（key，value）整体的类型，第三个模板参数告诉怎么从（key,value）整体中取出key，第四个参数是告诉两个Key如何比较大小。第五个参数是分配器。

黑红树有三个数据成员，node_count表示节点数量（4个字节），header指向红黑树的头节点（4个字节）。`key_compare`是一个类（这个类没有数据成员，所以默认是1个字节）。9个接字，按照内存对齐规则，必须是最大数据成员的整数倍，即12。

红黑树中，为了实现的方便，在头节点之上添加了一个空节点，然后header指向该节点，这样实现上方便很多。在，双向链表中也有同样的设计。

红黑树使用：

	rb_tree<int, //Key的类型
			int, //表示只有Key，没有data
			identity<int>, //采用标准库的identity类，传入一个参数，返回该参数 
			less<int>,  //key怎么比较大小，使用标准库提供的仿函数less 
			alloc> myTree;
			

![](https://i.imgur.com/yJdMP6r.png)

红黑树用例 ：

![](https://i.imgur.com/kaM9dyg.png)


G4.9版本中红黑树的用例：

![](https://i.imgur.com/dbvDQsW.png)

![](https://i.imgur.com/hu3rVwL.png)

`_Rb_tree`类中内含一个`_Rb_tree_impl`，`_Rb_tree_impl`继承`_Node_allocator`，并内含`_Rb_tree_node_base`。

采用这种做法，为了遵循handle和body规则（OO思想中的概念）。`_Rb_tree`中有一个指针（`_M_impl`,即handle）来表示它的实现手法。

### 容器set和mulitset

set/mulitset以红黑树为底层结构，因此有元素自动排序的特性。排序的依据是key，而set/multiset元素的value和key相同，value就是key。

set/multiset提供遍历的操作，及iterators。按正常规则（++ite）遍历，便能获得排序后的状态。

我们无法使用set和multiset的迭代器改变元素的值。set/multiset的iterator是其底部的红黑树的const-iterator，就是为了禁止用户对元素赋值。

set元素的key必须独一无二，因此其insert()用的是红黑树的insert_unique()。

multiset元素的key可以重复，因此其insert()用的是红黑树的insert_equal()。

set的源代码：

![](https://i.imgur.com/uBqyuzZ.png)

set包含3个模板参数，第一个参数是Key的类型，第二参数告诉怎么比较key的大小。在使用的时候，只需要指定一个key的类型，后面两个参数都要默认值：

	set<int> iset;

set中内含一个红黑树。set的迭代器实际上是红黑树的迭代器：`const_iterator`，而这个迭代器是一个常量迭代器，无法修改对应的值。

set的所有操作，都是转调用底层红黑树t的操作。set可以当作一个容器的适配器，其不做任何操作，全部都是转调用红黑树的操作。跟stack和queue一样，这两个都是转调用deque的操作。

GNU下有identity函数，但是在VC下没有，所以VC6在内部定义了一个类实现相同的功能，该类重载operator()，所以可以当作函数使用。

![](https://i.imgur.com/CMKbIHy.png)



### 容器map和multimap

map/multimap也是以红黑树为底部数据结构，因此有元素自动排序特性，排序的依据是key。

map/multimap提供遍历的操作及iterator。按正常规则（++ite）遍历，便能获得排序状态的序列。

我们无法使用map/multimap的iterators改变元素的key，但可以十三亿它来改变元素的data。因此map/multimap内部自动将用户指定的key类型为const，如果便能禁止用户对元素的key赋值。

map元素的key必须独一无二，因此insert()用的是红黑树的insert_unique()。

multimap元素的key可以重复，因此其insert()用的是红黑树的insert_equal()。

![](https://i.imgur.com/s0dD3I5.png)

map包含4个模板参数，后两个有默认值。前两个是指定key和data的类型。map的迭代器就是红黑树的迭代器，并没有规定key不能赋值，而是在包装成pair时，指定key的类型为const，即`pair<const Key, T>`。

select1st取出map元素的第一个值，即key。select1st是GNU C特有的，所以对于其他版本，它们必须自己定义一个功能相同的函数。在map类中，定义了内部类，并给类重载()运算符操作，这样类可以当作函数使用。

![](https://i.imgur.com/oa73mmd.png)

pair是标准库的结构，pair<fisrtType, secondType> p; p.first即p中的第一个元素，p.seconde即第二个元素。


map独有的operator[]:

	map<int, string> m;
	m[1] = "abc";

[]返回key对应的value，如果key不存在，就会创建一个元素<key, value>放入map中。

![](https://i.imgur.com/QkE4qoi.png)

`_k`表示key，`lower_bound`标准库中的一个算法，该算法的作用是：从排序好中序列中查找对应的key，如果找到，返回迭代器指向其中的第一个车元素。如果没有找到，就会传会最适合插入查找的key的位置。如果即没有找到，就调用insert函数。

使用[]和insert两个进行元素插入，insert的效率更高，因为[]还需要调用`lower_bound`函数进行判断。


### 容器hashtable

散列表或者哈希表。

unsigned int有2^32种变化，如果每个情况都申请内存，由于内存大小有限，内存是无法存储所有情况。

![](https://i.imgur.com/9cb0AP0.png)

但内存不足时，将编号除以空间的大小，得到余数。将元素放到余数对应的位置。

比如内存大小为100，5存放在5的位置，8存放在8的位置，105存放在5的位置。这样就存在碰撞。

防止碰撞的方法有多种：一次函数和二次函数分开。

还有就是拉链法（sepearate chaining）:

![](https://i.imgur.com/WlFgFZm.png)

一般篮子的数量都是素数。如果篮子数量小于元素数量，找到篮子数量离翻倍后最近的素数，将其当作更新的篮子数量，重新打散所有元素重新分配到篮子中。

GNU C中将篮子的数量提前分配好，存放在一个数组。

GNU C中hashtable内部实现:

![](https://i.imgur.com/ADifuYu.png)

6个模板参数：Value和Key分别表示两者的类型，HashFcn是一个函数：将元素映射到一个编号，ExtractKey是提取key的函数，EqualKey是判断两个key相等的函数，最后是一个分配器。

一个hashtable包含5个数据成员，前三个是函数对象（每一个函数对象实际上的大小是0，但是实际上无法做到0，而是做成1个字节），第四个是一个vector容器（即篮子）（一个vector里面有3个指针，所以是12字节），最后一个是元素的数量（4个字节）。大小为19，由于对齐规则，所以调整为20个字节。

一个hashtable迭代器包含两个指针，cur指向链表中的某个元素，而ht指向某个篮子。

hashtable的使用：

![](https://i.imgur.com/fAhxVsU.png)

hash-function传回的值就是hash-code。

![](https://i.imgur.com/uGwXOX4.png)

![](https://i.imgur.com/EmwGr8v.png)

hashtable中许多函数最后会归结到`hash(key)%n`：

![](https://i.imgur.com/UZXpXCZ.png)


### unordered

![](https://i.imgur.com/RZjy5kb.png)


## 算法

容器、迭代器、仿函数、适配器和分配器都是类模板（class template)；

算法是函数模板（function template）。

算法的形式：

	template<typename Iterator>
	Algorithm(Iteraotor ite1, Iterator ite2)
	{...}

	template<typename Iterator, typename Cmp>
	Algorithm(Iterator itr1, Iterator itr2, Cmp comp)
	{...}

算法看不见容器，对容器一无所知，算法只能看到迭代器，算法所需的一切信息都必须从Iterators取得，而迭代器必须能够回答算法的所有提问，才能搭配该算法的所有操作。


### 迭代器分类

5种迭代器分类：

	struct input_iterator_tag{};
	struct output_iterator_tag{};
	struct farward_iterator_tag : public input_iterator_tag{};
	struct bidirectional_iterator_tag : public forward_iterator_tag {};
	struct random_access_iterator_tag : public bidirectional_iterator_tag{};

![](https://i.imgur.com/qV7XdQs.png)

Array、Vector、Deque：在用户层面都是连续的，可以随机跳(`random_access_iterator_tag`)

list：双向(`random_access_iterator_tag`)

Forward=list：单向(`farword_iterator_tag`)

红黑树：双向（`bidirectional_iterator_tag`）

Hashtable: 单向或者双向（取决于底部链表是双向还是单向）


打印各种容器的迭代器：

![](https://i.imgur.com/zDvQ7MH.png)

typename()这样生成一个临时对象，比如`int()`。

`istream_iterator`和`ostream_iterator`分别是`input_iterator`和`output_isterator`。

![](https://i.imgur.com/Ujzp3RV.png)

typeid(itr).name()：itr是一个类型，返回类型的名字。

istream_iterator的iterator类型

![](https://i.imgur.com/q6lDN4Z.png)

所有版本接口都一样，虽然模板参数不同，但是不同的部分都给出了默认的值。所有版本都是规定`istream_iterator`的类型为`input_iterator`。

ostream_iterator的iterator类型

![](https://i.imgur.com/eXd5ABX.png)

其与istream_iterator类似。


#### 迭代器分类对算法的影响

__distance__有两个版本：一个对于连续空间，直接用尾指针减去头指针（效率高）；另一个对于非连续空间，通过将指针往后移动，并进行计数（效率低）。

distance的返回类型：两个指针的距离类型，通过`iterator_traits`获取迭代器的difference_type。

![](https://i.imgur.com/M9daKWJ.png)

首先将迭代器给`iterator_traits`得到迭代器的类型，然后利用迭代器类型名建立临时对象，放入`_distance`，根据临时对象调用不同的版本。

很多算法都会有一个主函数，然后根据迭代器类型，调用不同的次函数，类似于distance。

__advance__有三个版本：一个是前进，另一个是后退，最后一个是可前进也可以后退（双向迭代器）。

前进和移动可以是一步一步移动（效率低），也可以直接跳转（效率高）。

![](https://i.imgur.com/xneK9aO.png)

`iterator_category(i)`根据迭代器i获取迭代器的类型（分类），并以此类型创建一个临时对象返回。

迭代器有5种类型，但是为什么这里只考虑了两种情况或三种情况呢？

如果类型是`farward_iterator_tag`，distance会调用`input_iterator_tag`版本，因为`farward_iterator_tag`是一个种`input_iterator_tag`类型，这是根据类的继承是一个种is-a的关系。

__copy__：复制一个容器中的所有元素到一个新容器中。

![](https://i.imgur.com/pQma4mi.png)

copy不断的做迭代器类型判断，对不同的迭代器采用不同的做法。

首先，如果迭代器类型是`const char *`和`const wchar_t*`，则调用mememove()(是一个底层的系统函数)；如果是迭代器类型不是上述两种类型，调用一个泛化版本`_copy_dispatch()`函数。然后，`_copy_dispatch()`在进行迭代器类型判断，如果是指针，则调用`_copy_t()`函数，如果不是指针，则调用泛化版本`__copy()`。最后，`__copy()`判断迭代器类型，如果`input_iterator`类型调用for-loop，这样从头指针开始一个一个复制；如果是`random_access_iterator`，调用`_copy_d()`函数，直接last-first得到循环的次数，这样速度更快。


注意，`__copy_t()`函数会使用type traits来判断执行一个动作重不重要（比如拷贝赋值重不重要，对于一个含有指针的类是重要的），如果它是不重要的（trivial），就会调用`memmove()`函数，如果它重要，就会调用`__copy_d()`一个一个进行赋值，就会调用拷贝赋值函数。比如，如果一个对象里面没有指针，调用复制构造函数和拷贝赋值函数是不重要的。

type traits不属于STL，但属于标准库。


__destroy__：调用析构函数

![](https://i.imgur.com/5cxT7al.png)

其也要调用type traits，如果摧毁的元素的析构函数不重要，就不需要调用析构函数；如果析构函数重要，就调用析构函数destroy函数。

destroy的详细实现：

![](https://i.imgur.com/TEucOpM.png)

__算法的效率主要看算法能不能判断迭代器的分类。__

`unique_copy()`只复制不重复的元素

![](https://i.imgur.com/k8l0J5i.png)

如果目的端是一个outputiterator，不能读取其中的值(`*result`)，所以不能采用forwarditerator。

算法的接口不会规定只接受某种迭代器，而是会接收所有迭代器。因为使用的是模板函数，所以对迭代器类型没有规定。但是如果传入的迭代器类型不符合算法要求的类型，到迭代器跳转的时候会失败。

![](https://i.imgur.com/whWRE1K.png)

distance接收的迭代器类型是input iterator，所以除了output iterator，其他迭代器都可以正确放入distance函数。

比如，sort中的模板类型名称是randomAccessIterator，告诉我们迭代器类型是`randmo_access_iterator`，如果是其他类型，执行到迭代器跳转的地方会失败。

#### 具体算法分析

C函数：

- qsort(c.data, ASIZE, sizeof(long), compareLongs)
- bsearch(&target, c.data(), ASIZE, sizeof(long), compareLongs)


标准库提供的算法：

- count_if(vi.begin(), vi.end(), not1(bind2nd(less<int>(),40)))
- find(c.begin(), c.end(), target)
- sort(c.begin(), c.end())

标准库的算法需要符合：

	template <typename Iterator>
	std::Algorithm(Iterator itr1, Iterator itr2 ..)
	{}


- 算法accumulate:

有两个版本：第一个版本接收头尾两个指针和一个初值；另一个版本接收头尾两个指针，一个初值和一个函数（操作）。

![](https://i.imgur.com/nrX44WF.png)

第一个版本，执行累加操作。而第二个版本，要根据给定的函数才能知道执行了什么操作。

右侧示例中，在accumulate中给出不同的函数，得到了不同的结果。不同的函数是可被调用的对象包括：函数和函数对象。


- 算法for_each

对每个元素执行相同的操作。

接收头尾两个迭代器，和一个函数对象。

![](https://i.imgur.com/cfgsCxs.png)


基于范围的for循环：

	for(decl : coll)
		statement;

	for (int i : {1,3,4,6,5,7})
		cout << i << endl;

- 算法replace, replace_ if, replace_copy

![](https://i.imgur.com/TMwqGJh.png)

replace：如果元素跟旧值(`old_value`)相同，就替换为新值(`new_value`)。

replace_if：满足某条件（pred）的元素，替换为新值。

replace_copy: 如果元素跟旧值相同，不是取代，而是复制到内的地方并替换为新值。

- 算法count，count_if

![](https://i.imgur.com/EQUojmx.png)

count：对所有元素进行计数

count_if: 对满足某个条件的元素，进行计数

带成员函数count()的容器：set/mulitset，map/multimap...。这些容器调用count()时，调用其成员函数速度将更快。这8个容器中有key，有其更快速的做法，所以这8个容器每个都定义了一个count成员函数。

但是对于容器不带成员函数count()，那么只能调用标准库中的算法count()。

- 算法find，find_if

![](https://i.imgur.com/9Np9TVU.png)

跟count类似。

- 算法sort

![](https://i.imgur.com/zkkUMzU.png)


	bool myfunc(int i, int j) { return (i < j);}
	struct myclass{
		bool operator()(int i, int j) {return (i < j);}
	} myobj;
	
	int myints[] = {32, 17, 12, 45, 26, 80, 53, 33};
	vector<int> myvec(myints, myints+8);

	sort(myvec.begin(), myvec.begin()+4);

	sort(myvec.begin()+4, myvec.end(), myfunc);

	sort(myvec.begin(), myvec.end(), myobj); 

	sort(myvec.rbegin(), myvec.rend());  //rbegin()=end(), rend()=begin()  得到的排序跟前面相反


8种关联式容器中所有元素已经排序好，所有不再需要使用sort进行排序，所以其没有定义成员函数sort。

list和forward_list无法调用sort，因为这两者的迭代器无法跳转，所以其自身带有sort成员函数。

rbegin()和rend()

![](https://i.imgur.com/VRQMFTt.png)

reverset_iterator是一个适配器，将end()的取值方式反过来。

- 算法binary_search

二分查找：前提条件是，容器已经排序好

![](https://i.imgur.com/B1ry9gk.png)

`lower_bound`返回能够插入某个元素的最低点，`upper_bound`返回能够插入某个元素的最高点。前提条件是元素已经排序好。

## 仿函数

仿函数，又称函数对象

仿函数根据用户的要求进行设计，然后使用到算法中。

![](https://i.imgur.com/nKgp29E.png)

仿函数三大类：

- 算术类

计算两个数的加减乘除等。

	template <class T>	
	struct plus : public binary_function<T,T,T>
	{ T operator()(const T&x, const T& y) const
		{return x + y; }
	};

	template<class T>
	struct minus : public binary_function<T,T,T>
	{ T operator()(const T&x, const T& y) const
		{return x - y; }
	};

- 逻辑运算类

计算两个元素的或、与。

	template<class T>
	struct logical_and : public binary_function<T,T,bool>{
		bool operator()(const T& x, const T& y) const
		{ return x && y;}
	};

- 相对关系类

比较两个元素的大小关系

	template<class T>
	struct equal_to : pulbic binary_function<T, T, bool>
	{	
		bool operator()(const T& x, const T& y)const	
		{	return x == y; }
	};


GNU C++独有的仿函数，非标准的


	template <class T>
	struct identity : public unary_function<T,T>
	{
		const T& operator()(const T& x) const 
		{ return x;}
	}

	template<class Pair>
	struct select1st: public unary_fucntion<Pair, typenmae Pair::first_type>{
		const typename Pair::first_type & operator()(const Pair & x) const
		{ return x.first; }
	}

	...


sort中仿函数的使用：

![](https://i.imgur.com/sGaXjR7.png)

标准库提供的仿函数继承自`binary_function`或`unary_function`。而我们写的继承关系，可以运行，但是这样就没有容器STL中，在执行某些动作时可能会失败。

仿函数可适配的条件是：仿函数需要继承`binary_function`或`unary_function`。这是因为适配器可能需要访问仿函数的一些参数（即问问题），如果不继承自这两个函数，将无法进行回答。

![](https://i.imgur.com/JtGlxi0.png)

`unary_function`针对于运算符!(not)等,只有一个操作数。`binary_function`针对两个操作数的运算符。

这两个类都没有数据成员和成员函数，大小理论值为0，实际上大小为1。如果有子类继承自它，那它的大小是0。所以less的大小，理论上为0，实际上为1。


## 适配器adapter

多种适配器： 

- 容器适配器
- 迭代器适配器
- 仿函数适配器

![](https://i.imgur.com/bWw6TUP.png)

A改造了B，就可以用A代替B，用户是使用A，但幕后还是B在起作用。

适配器都是内含的方式，而不是继承。

容器适配器就是内含一个容器，迭代器适配器就是内含一个迭代器，仿函数适配器内含一个适配器。

算法不知道容器，只能看到迭代器，而迭代器有容器提供。迭代器必须拥有5个typedef。

仿函数主要是跟算法合作，比如算法比较大小的准则等都是由仿函数提供，可以把仿函数包成一个适配器，然后给算法使用。算法可能需要直到某些信息，或者适配器需要直到仿函数的性质，通过3个或2个typedef实现。

### 容器适配器 stack, queue

![](https://i.imgur.com/XSM9tgN.png)

Stack内含一个容器deque，对其进行了改造，其开放了deque的某些函数。

queue内含一个容器deque，对其进行了改造，函数的实现都是转调用deque的函数。


### 函数适配器

binder2nd：将仿函数绑定第二参数。

	count_if(vi.begin(), vi.end, 
				not1(bind2nd(less<int>(), 40)));

![](https://i.imgur.com/JsCMx7x.png)

bind2nd只是一个接口，内部实现交给binder2nd。bind2nd会根据仿函数，得到仿函数的类型，然后当作binder2nd的模板参数。比如，less<int>放入的时候，编译器会启动推导出类型。

binder2nd类中有两个数据成员op和value，一个表示函数，另一个表示需要绑定的参数。

binder2nd重载了()运算符，所以binder2nd是一个函数对象，可以像函数一样使用它。


`operation::second_argument_type`得到less的第二个参数的类型，然后在调用binder2nd时进行强制类型转换(`arg2_type(x)`)。

在binder2nd中，`operation::second_argument_type`得到less的第二个参数的类型，第二个参数的类型应该是该类型；`operation::result_type`得到less的返回类型，bind2nd的返回类型应该也是该类型。

在`count_if`中，放入的参数是第一实参，第二个参数已经绑定为40。

typename的作用：在编译时，Operation的类型无法知道，也无法知道Operation能否回答这个问题，这是编译器可能无法通过编译；使用typenam就可以让编译器通过编译。

binder2nd再一次继承了`unary_function`，使其变成可适配的，可以被其他适配器改造。

__适配器就是将参数的类型记录下来，以便后面使用。__

not1: 取否定，`bind2nd(less<int>(), 40)`表示大于40的条件，加上not1修饰，表示小于40的条件。

![](https://i.imgur.com/cv6PyrY.png)

not1同样有一个主函数和一个次函数，主函数相当于一个接口，主要的内容有次函数完成。主函数完成实参推导，即得到pred的类型Predicate。然后，利用该类型创建临时对象`unary_negate`，调用构造函数。

在`count_if`中，利用创建的临时对象`unary_negate`，调用重载的()运算符。


### 新型适配器 bind

![](https://i.imgur.com/UzzPyDH.png)

表明binder1st,bind1st,binder2st,bind2st等都是过时的，已经替换成bind。

![](https://i.imgur.com/QDbh0Z9.png)

bind可以绑定：

- 函数
- 函数对象
- 成员函数
- 数据成员 

绑定成员函数和数据成员时，`_1`必须是某个对象地址。

绑定函数：

`_1`表示占位符，即空着这个位。必须添加命名空间：`using namespace std::placeholders;`

	auto fn_invert = bind(my_divide,_2,_1);
	cout << fn_invert(10,2) << endl; // 输出0.2

其中`_1`绑定fn_invert(10,2)的第一个实参10，`_2`绑定第二实参2。

bind可以指定一个模板参数，绑定的是函数的返回类型。

	auto fn_rounding = bind<int>(my_divide, _1,_2);
	cout << fn_rounding(10, 3) << endl; //输出3

由于指定返回类型为3，会将原来的返回类型double覆盖，所以返回值是3。

绑定函数对象：

跟绑定函数一样，只是将函数声明换出了函数对象：

	先定义divides对象，并重载operator()
	std::divides<double> my_divide;

绑定成员函数：

	MyPair ten_two {10,2}; //创建对象，并给数据成员给初值
	
	auto bound_memfn = bind(&MyPair::multiply, _1); //_1表示对象的this指针 
	cout << bound_memfn(ten_two);   
	
绑定数据成员：

	auto bound_memdata = bind(&MyPair::a, ten_two);
	cout << bound_memdata() << endl;

	auto bound_memdata2 = bind(&MyPair::b, _1);
	cout << bound_memedata2(ten_two) << endl;

bind的使用：

	vector<int> v {15,23,56,34,67,24, 14};
	int n = count_if(v.cbegin(), v.cend(), not1(bind2nd(less<int>(), 30)));
	cout << "n = " << n << endl;

	auto fn_ = bind(less<int>(), _10, 30);
	cout << count_if(v.cbegin(), v.cend(), fn_) << endl;
	cout << count_if(v.begin(), v.end(), bind(less<int>(), _1, 30)) << endl;

cbegin()和cend()表示迭代器是const类型，无法修改它。


### 迭代器适配器

对迭代器进行改造。

reverse_iterator:

排序的默认情况，从小到大。如果传给其的迭代器是两个逆向迭代器（rbegin()和rend()），得到的是从大到小。

![](https://i.imgur.com/4M3nfs8.png)

`reverse_iterator`逆向迭代器也需要5个typedef，跟正向迭代器一致。

关键的取值动作，逆向迭代器是反向取值，逆向迭代器取值，就是对正向迭代器退一位取值。逆向的++就是正向的--，所有操作都是逆向的。

迭代器适配器inserter

![](https://i.imgur.com/r2vwbc4.png)

复制：

	int myints[] = {}10,20,30,40,50,60,70};
	vector<int> myvec(7); //如果给出vector的大小不足，复制的时候，如果构造这个范围，vector不会自动增长
	copy(myints, myints+7, myvec.begin());

正常的迭代器，copy会进行赋值动作，会见后面的内容覆盖。

而如果想在中间插入多个元素，而不覆盖后面的元素，可以使用inserter实现。

inserter每插入一个元素，会在迭代器后向安排一个新的空间以便元素插入。

但是copy已经写死，无法修改，就考虑对赋值=进行重载。就是对copy中的第三参数进行=操作符重载。

执行`*restult=*first;`，会进入`insert_iterator`中的重载operator=函数中，执行插入操作。

inserter

	list<int> foo, bar;
	for (int i = 1; i <= 5; i++)
	{
		foo.push_back(i);
		bar.push_back(i*10);
	}

	list<int>::iterator it = foo.begin();
	advance(it, 3);
	copy(bar.begin(), bar.end(), inserter(foo, it));



### X适配器： ostream_iterator

![](https://i.imgur.com/oGTFvBG.png)

ostream_iterator使用：

	std::ostream_iterator<int> out_it(std::cout, ",");
	std::copy(myvector.begin(), myvector.end(), out_it);


`out_it(std::cout,",")`调用`ostrem_iterator`的构造函数，在copy函数中执行`*result=*first;`是调用`ostream_iterator`重载的=运算符。

### x适配器：istream_iterator

![](https://i.imgur.com/nfx5Fql.png)

如果`istream_iterator`不带参数，就是一个`end-of-stream`迭代器，代表一个标兵。

`istream_iterator`重载++操作符，可以读取值到value，然后通过重载*运算符来返回value值。对`istream_iterator`进行++，就是进行读取值操作。

![](https://i.imgur.com/ryOpEZT.png)

先创造两个`istream_iterator`迭代器，然后将这两个迭代器当作copy的来源端，inserter()将复制操作变成插入操作。


## 标准库

勿在浮沙筑高台
### hash function

为自己的hashtable写一个hash function，来设置每个元素的位置。

写一个hash function的形式：

如果将Cunstomer放入hashtable，需要为其设置一个hash function（Customerhash）。

![](https://i.imgur.com/dL9mDGz.png)

有两种定义hash function的形式：

- 函数对象
- 普通函数

两种形式使用的方式不同：

	unordered_set<Customer, CustomerHash> custset;
	unordered_set<Customer, size_t(*)(const Customer&)> custset(20, customer_hash_func);
						 //指明函数返回类型和参数类型                   //实际的函数地址



![](https://i.imgur.com/lE9sm91.jpg)

对于直接用类的所有成员的哈希值相加当作函数函数，这样太天真。实际上可以运行，但是出现碰撞的几率高，导致查找速度慢。

TR1给出了hash function实现，比直接相加好。

直接调用`hash_val`函数，将类的所有成员当作参数。第一个版本的`hash_val`，template可以接收任意个数的模板参数；第二版本的`hash_val`，第一个参数是`size_t`，模板参数个数是任意多个；第三个版本的`hash_val`，只有两个模板参数。

首先调用的第一个版本`hash_val`，然后第一个版本`hash_val`调用第二个版本的`hash_val`，第二个版本里面有递归，直到递归到最后一个时(只有两个参数)，调用第三个版本的`hash_val`。


除了前面两种形式,还有另一种形式,写一个偏特化的hash版本.

![](https://i.imgur.com/Rr0Su8s.png)

### tuple

tuple在TR1开始加入的.

在声明的时候,允许放入的任意的元素类型和个数.

使用示例:

	cout << "string sizeof=" << sizeof(string) << endl;
	cout << "double sizeof=" << sizeof(double) << endl;
	cout << "float sizeof=" << sizefo(float) << endl;
	cout << "int sizeof=" << sizeof(int) << enld;
	cout << "complex<double> sizeof=" << sizeof(complex<double>) << endl;

	tuple<string, int, int, complex<double> > t;
	cout << "sizeof=" << sizeof(t) << endl;   //32

	tuple<int, float, string> t1(41, 6.3, "nico");
	cout << "tuple<int, float, string> sizeof=" << sizeof(t1) << endl; //12
	 
	cout << "t1: " << get<0>(t1) << " " << get<1>(t1) << " " << get<2>(t1) << endl;
	
	auto t2 = make_tuple(22, 444, "stacy");
	get<1>(t1) = get<1>(t2);

	if (t1 < t2)
		cout << "t1 < t2" << endl;
	else
		cout << "t1 >= t2" << endl;
	t1 = t2;
	cout << "t1: " << t1 << endl;

	tuple<int, float, string> t3(77, 1.1, "more light");
	int i1;
	float f1;
	string s1;
	tie(s1, f1, s1) = t3;  //分配值给值给i1,f1,s1


	typedef tuple<int, float, string> TuepleType;
	cout << tuple_size<TupleType>::value << endl;
	tuple_element<1, TupleType>::type fl = 1.0;
	typedef tuple_element<1, TupleType>::type T;

tuple实现:

![](https://i.imgur.com/Q4eIMBy.png)

如果tuple包含n个元素,则它会继承一个n-1个元素的tuple。 第一个元素成为类中的一个变量。如果到达0个元素时，调用偏特化偏特化版本（即一个空的模板类）。

tuple有两个函数head()和tail()。head()传回第一个值，tail()获得父类的成员起点。


### type traits

![](https://i.imgur.com/VZ6ouo0.png)

默认情况下，默认构造函数、拷贝构造函数，拷贝赋值函数，析构函数是重要的，那就需要在实现这些函数时，需要将对象一个一个赋值。

对于基本类型，这些函数都是不重要的。也对可以对自己定义的类，设置特化版本。

	template<> struct __tyep_traits<Foo>{
		teypdef __true_type has_trivial_default_constructor; //这些值可以设定
		....
	};

如果不给自己定义的类设置特化版本，将会使用默认的版本。普通的类都没有必要设置特化版本，除非类中使用了指针，文件等，这样使用特化版本来设置是有必要的。

POD是C中的struct，只有数据成员，没有函数成员。

c++11里面的type_traits:

![](https://i.imgur.com/UPvk6hh.png)

![](https://i.imgur.com/x78fuvh.png)

type_traits使用方式：

	template<typename T>
	void type_traits_output(const T& x)	
	{
		cout << is_void<T>::value << endl;
		cout << is_integrial<T>::value << endl;
	....
	}

类中指针时，需要析构函数。类当成是父类时，才有必要将析构函数写成虚函数。

![](https://i.imgur.com/wH0tPav.png)

string不能被当作父类，所以它的析构函数不是虚函数。

对于自己定义的类，分析type_traits:

![](https://i.imgur.com/n7nd4I1.png)
 
自己定义的类Zoo:

![](https://i.imgur.com/IbdF21a.png)

	Zoo(const Zoo &)=delete; //表示不要拷贝构造函数，编译器不要生成默认的拷贝构造函数
	Zoo(Zoo &&) = default; //移动构造函数，使用编译器给的默认版本

complex复数：没有指针，没有构造函数函数和析构函数，但是编译器会给出默认的模板。

![](https://i.imgur.com/zD09TGE.png)

c++11 type_traits实现：

is_void实现：

![](https://i.imgur.com/6LfmiHD.png)

`is_void`首先调用`remove_cv`去掉变量的const和volatile，然后调用`__is_void_helper`来得到是否为void。这里，每一个步骤都是通过声明泛化和偏特化来实现功能。

is_integral实现：

integral包括：int,long,long long, char，bool,signed char, unsigned char, unsigned int, signed int, ...

![](https://i.imgur.com/U537C8c.png)

首先去掉const和voliate，然后给`__is_ingegral_helper`去实现，实现也是采用泛化和偏特化实现的。


`is_class`,`is_union`,`is_enum`,`is_pod`的实现：

![](https://i.imgur.com/vziWV98.png)

这些实现都找不到具体的原始实现，认为是交给编译器实现的，所以看不到源码。

`is_move_assignable`:同样找不到源代码

### cout

cout是标准库的一部分，cout是一个对象，而不是类，类不能被直接使用。

![](https://i.imgur.com/PsU6tUq.png)

extern表示cout可以被其他文件使用。

![](https://i.imgur.com/kfjp9d9.png)