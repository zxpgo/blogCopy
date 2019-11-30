---

title:  C++ Primer Plus--循环和关系表达式（五）
date: 2018-09-27 10:23:14
tags: C++
categories: "C++ Primer Plus学习笔记"
mathjax: true

---

C++提供三种循环：for循环、while循环和do while循环。<!--more-->

## 5.1 for循环

	int i;
	for (i = 0; i < 5; i++)
		cout << "C++ knows loops.\n";

该循环首先将整数变量i设置为0：
	
	i = 0

这是循环的初始化部分，然后，循环测试部分检查i是否小于5：

	i < 5

如果确实小于5，则程序执行接下来的语句--循环体：

	cout << "C++ knows loops.\n";

然后程序使用循环更新部分将i加1：

	i++

接下来，循环开始了新的周期，将新的i值与5比较。

### 5.1.1 for循环组成部分

for循环的组成部分完成步骤：

1、设置初始值；
2、执行测试，判断循环是否应当继续进行；
3、执行循环操作；
4、更新用于测试的值。


**1.表达式和语句**

for语句的控制部分使用了3个表达式。

	maids = (cooks = 4) + 3;

表达式cooks=4的值为4，因此maids的值为7。下面的语句由也是允许的：

	x = y = z = 0;

这种方法可以快速地将若干个变量设置为相同的值。优先级表表明，赋值运算符是从右向左结合的，因此首先将0赋给z,然后将z=0赋给y，依次类推。

从表达式到语句的转换很容易，只要加上分号即可，因此下面是一个表达式：

	age = 100

而下面一条语句：

	age = 100;

更准确地说，这是一条表达式语句。只要加上分号，所有的表达式都可以成为语句，但不一定编程有意义。例如：

	roents + 6;

编译器允许这样的语句，但它没有完成任何有用的工作。程序仅仅计算和，而没有使用得到的结果。

**2.非表达式和语句**

下面语句是一个表达式：

	int toad;

而int toad不是表达式，因为它没有值。因此，下面的代码非法：

	eggs = int toad * 1000;

同样不能把for循环赋给变量，for循环不是表达式，因此没有值，也不能给它赋值。

**3、修改规则**

C++对C循环的基础上添加了一项特性，要求对for循环句法做一些微妙的调整：

	for (int i = 0; i < 5; i++)

也就是说，可以在for循环的初始化部分中声明变量。

### 5.1.2 回到for循环

使用for循环计算并存储前n个阶乘:

formore.cpp

#include <iostream>
	const int ArSize = 16;
	int main()
	{
		using namespace std;
		long long f[ArSize];
		f[0] = f[1] = 1;
		for (int i = 2; i < ArSize; i++)
			f[i] = i * f[i-1];
		for (int i = 0; i < ArSize; i++)
			cout << i << "! = " << f[i] << endl;
		return 0;
	}

结果：

	0! = 1
	1! = 1
	2! = 2
	3! = 6
	4! = 24
	5! = 120
	6! = 720
	7! = 5040
	8! = 40320
	9! = 362880
	10! = 3628800
	11! = 39916800
	12! = 479001600
	13! = 6227020800
	14! = 87178291200
	15! = 1307674368000


定义一个const值来表示数组中的元素个数是个好办法。如果要扩展处理20个阶乘，则只需要修改ArSize的值为20，而不需要在程序将16修改为20。

### 5.1.3 修改步长

到现在为止，循环示例每一轮循环计数加1或减1。可以通过修改更新表达式来修改步长：

	int by = 3;
	for (int i = 0; i < 9; i = i + by)
		cout << i << endl;


### 5.1.4 使用for循环访问字符串

for循环提供了一种依次访问字符串随的每个字符的方式。

forstr1.cpp

	#include <iostream>
	#include <cstring>
	using namespace std;
	int main()
	{
		cout << "Enter a word:\n";
		string word;
		cin >> word;
		for (int i = word.size() - 1; i >= 0; i--)
			cout << word[i];
		cout << "\nBye.\n";
		return 0;
	}

结果：

	Enter a word:
	animal
	lamina
	Bye.

在本例子中，可以使用string对象，也可以使用char数组，因为它们都可以使用数组表示法来访问字符串中的字符。

### 5.1.5 递增运算符(++)和递减运算符(--)

两个运算符都要两种变体，前缀版本位于操作数前面，如++x；后缀版本位于操作数后面，如x++，两个版本对操作数的影响是相同的，但是影响的时间不同。

plus_one.cpp

	#include <iostream>
	int main()
	{
		using namespace std;
		int a = 20;
		int b = 20;
		cout << "a  = " <<  a << "; b = " << b << endl;
		cout << "a++ = " << a++ << "; ++b = " << ++b << endl;	
		cout << "a  = " <<  a << "; b = " << b << endl;
		return 0;
	}

结果：

	a  = 20; b = 20
	a++ = 20; ++b = 21
	a  = 21; b = 21

a++意味着使用a当前值计算，然后将a的值加一；而b++的意思是先将b的值加1，然后使用新的值来计算表达式。例如：

	int x = 5;
	int y = ++x; //y的值为6

	int z = 5;
	int y = z++; //y的值为5

递增和递减运算符都是漂亮的小型运算符，不过千万不要失去控制，在同一条语句对同一个值递增或递减多次。问题在于，规则“使用后修改”和“修改后使用”可能变得模糊不清。下面的语句在不同的系统中将生成不同的结果：

	x = 2 * x++ * (3 - ++x);

对于这种语句，C++没有定义正确的行为。

### 5.1.6 副作用和顺序点

副作用指的是在计算表达式时对某些东西（如存储在变量中的值）进行了修改；顺序点是程序执行过程中的一个点。在C++中，语句中的分号就是一个顺序点，这意味着程序处理下一条语句之前，赋值运算符、递增运算符和递减运算符执行所有的修改都必须完成。

完整的表达式：不另一个更大表达式的子表达式。完整的表达式例子有：表达式语句中的表达式部分以及用作while循环中检测条件的表达式。

	while (guest++ < 10)
		cout << guest << endl;

在这里，可以认为“使用值，然后递增”，意味着先在cout语句中使用guest的值，再将其值加1。然而，表达式guest++ < 10是一个完整的表达式，因为它是一个while循环的测试条件，因此该表达式的末尾是一个顺序点。所以，C++确保副作用（将guest加1）在程序进入cout之前完成。然而，通过使用后缀格式，可确保将guest同10进行比较后再将其值加1。

现在看如下语句：

	y = (4 + x++) + (6 + x++);

表达式4 + x++不是一个完整的表达式，因此C++不保证x的值在计算子表达式4+x++后立刻增加1。在这个例子中，整条赋值语句是一个完整表达式，而分号表示了顺序点，因此C++只保证程序执行到下一条语句之前，x的值将被递增两次。

C++中，没有规定是在计算每个子表达式之后将x的值递增，还是整个表达式计算完毕才将x的值递增，鉴于此，应避免使用这样的表达式。

在C++11文档中，不再使用术语”顺序点“，因为这个概念难以用于讨论多线程执行。反而，使用了术语”顺序“，他表示有些事情在其他事件前发生。

### 5.1.7 前缀格式和后缀格式

	x++;
	++x;

从逻辑上说，上述情形下，使用前缀和后缀表达式没有区别，即当表达式的值未被使用时，因此只存在副作用。

C++允许针对类定义这些运算符，在这种情况下，用户定义前缀函数：将值加1，然后返回结果；但后缀版本首先复制一个副本，将其加去，然后将复制的副本返回。因此，对于类而言，前缀版本的效率比后缀版本的效率高。

总之，对于内置类型，采用哪种格式都不会有差别；但对于用户定义的类型，如果有用户定义的递增和递减运算符，作为前缀格式的效率高。


### 5.1.8 递增/递减运算符和指针

将递增运算符用户指针时，将把指针的值增加其指向的数据类型占用的字节数，这种规则适用于指针递增和递减：

	double arr[5] = {1.1, 2.2, 3.3, 4.4, 5.5};
	double *pt = arr;
	++pt;

也可以结合使用这些运算符和*运算符来修改指针所指向的值。

前缀运算符的从右到左结合规则意味着$\ast$++pt的含义如下：现将++应用于pt（因为++位于$\ast$的右边），然后将$\ast$应用于被递增后的pt:

	double x = *++pt; //指向arr[2],值为23.4

另一方面，++*pt意味着先取pt所指向的值，然后将这个值加1：

	++*pt; //指向arr[2],值为24.4 

接下来，看看下面的组合：

	(*pt)++;

圆括号指出，首先对指针解除引用，得到24.4，然后，运算符将这个值递增到25.4，pt仍指向arr[2]。最后，看下面组合：

	x = *pt++;

后缀用算符的优先级高，这意味着将运算符用户pt，而不是$\ast$pt，因此对指针递增。然而，后缀运算符意味着将对原来的地址（&arr[2]）而不是递增后的新地址解除引用，因此$\ast$pt++的值为arr[2],即25.4，当该语句执行完毕后，pt的值将为arr[3]。

### 5.1.9 组合赋值运算符

C++有一种合并加法和赋值的运算符：

	i += by;
	int pa[3] = {1,2,3};
	pa[1] += 1;
	*(pa + 1) += 2;
	pa += 1;

组合赋值运算符

| 操作符 | 作用(L为左操作数，R为右操作数)|
|---|
| += | L+R赋给L |
| -= | L-R赋给L |
| $\ast$= | L$\ast$R赋给L |
| /= | L/R赋给L |
| %= | L%R赋给L |

### 5.1.10 复合语句(语句块)

	int sum = 0;
	int number;
	for (int i =1; i <= 5; i++)
	{
		cout << "Value: " << i << ": ";
		cin >> number;
		sum += number;
	}

编译器将忽略缩进，因此需要使用花括号来说明是for中的语句块。如果在语句块中定义一个新的变量，则仅当程序执行该语句块中的语句时，该变量存在。执行完该语句块，变量将被释放。

注意，在外部定义的变量，在语句块内部也是被定义了。

### 5.1.11 逗号运算符

语句块允许把两条或更多条语句放到按C++句法只能放到一条语句的地方。逗号运算符对表达式完成同样的任务，允许将两个表达式放到C++句法只允许放一个表达式的地方。例如：假设有一个循环，每轮都将一个变量加1，而另一个变量减1：

	++j,--i

逗号并不总是逗号运算符，例如，下面这个声明中的逗号将变量列表中的相邻的名称分开：

	int i, j;

实现将一个string类对象的内容反转。

forstr2.cpp	

	#include <iostream>
	#include <cstring>
	using namespace std;
	int main()
	{
		cout << "Enter a word: ";
		string word;
		cin >> word;
		char temp;
		int i,j;
		for(j = 0, i = word.size() - 1; j < i; --i, ++j)
		{
			temp =  word[i];
			word[i] = word[j];	
			word[j] = temp;
		}
		cout << word << endl;
		return 0;
	}

结果：

	Enter a word:
	animal
	lamina


注意声明i,j的位置在循环之前，因为不能用逗号运算符将两个声明组合起来。这是因为声明已经将逗号用于其他用途--风格列表中的变量。也可以使用一个声明语句表达式来创建并初始化两个变量，但这样看起来有点乱：

	int j = 0, i = word.size() - 1;

在这种情况下，逗号只是一个分隔符。

如果在for内部声明temp:
	
	char temp = word[i];

这样，temp在每轮循环中都将被分配和释放，这比在循环外声明temp的速度慢一些。另一方面，如果在循环内部声明，则它将在循环结束后释放。

**逗号运算符其他用途**

	i =  20, j = 2 * i //其中i=20,j=40

首先，它确保先计算第一个表达式，然后计算第二个表达式（换句话说，逗号运算符是一个顺序点）。其次C++规定，逗号表达式的值是第二部分的值。例如，上面表达式的值为40，因为j=2$\ast$i的值为40。

在所有的运算符中，**逗号运算符的优先级最低**。例如，下面的语句：

	cata = 12, 120;

被解释为：

	(cata = 12), 120;

也就是说cata为12，120不起作用。然而，由于括号的优先级最高，下面的表达式：

	cats = (12, 120);

将cats设置为120---逗号右侧的表达式值。


### 5.1.12 关系表达式

不等于: !=
等于： ==
小于等于： <=
大于等于： >=

关系运算符的优先级比算术运算符地：

	x + 2 > y -2

### 5.1.13 赋值、比较和可能犯的错误

观察下面两者的输出：

	itn A[5] = {20, 20, 10, 20 ,1}
	for (int i = 0 ; A[i] == 20; i++) //输出前两个20
		cout << i << endl;
	for (int i = 0; A[i] = 20; i++) //因为这里使用赋值，所有程序会一直输出20，导致程序崩溃
		cout << i << endl;

第二个循环，一直输出20，直到程序崩溃，电脑死机。


### 5.1.14 C-风格字符串比较

由于C++将C-风格字符串视为地址，因此如果使用关系运算符来比较它们，将无法得到满意的结果。相反，应使用C-风格字符串库的strcmp()函数来比较。该函数接受两个字符串地址作为参数。这意味着参数可以是指针、字符串常量或字符数组名。如果两个字符串相同，则函数返回0；如果第一个字符串按字母排在第二个字符串前面，则函数返回一个负数；如果第一个字符串按字幕顺序排在第二个字符串之后，则函数返回一个正数。

实际上，”按系统排序顺序“比”按字母顺序“更准确，这意味着字符根据字符的系统编码来进行比较。例如：使用ASCII码时，所有大写字母的编码都要小于小写字母，所以按排序顺序，大写字母将位于小写字母前面。因此，字符串”Zoo“在字符串”aviary“之前。根据编码进行比较还意味着大写字母和小写字母是不同的。

虽然不能用关系运算符来比较字符串，但可以用来比较字符，因为字符实际上是整型。

	for (ch = 'a'; ch <= 'z'; ch++)
		cout <<ch;

compstr1.cpp

	#include <iostream>
	#include <cstring>
	int main()
	{
		using namespace std;
		char word[5] = "?ate";
		for (char ch =  'a'; strcmp(word, "mate"); ch++)
		{
			cout << word << endl;
			word[0] = ch;
		}
		cout << "After loop ends, word is :" << word << endl;
		return 0;
	}

结果：

?ate
aate
bate
cate
date
eate
fate
gate
hate
iate
jate
kate
late
After loop ends, word is :mate

如果str1和str2相等，作则下面的表达式为true:

	strcmp(str1,str2) == 0

如果str1和str2不相等，则下面两个表达式都是true:

	strcmp(str1,str2) != 0
	strcmp(str1,str2)
	
如果str1在str2的前面，则下面表达式为true:

	strcmp(str1,str2) < 0;

如果str在str2的后面，则下面表达式为true:

	strcmp(str1,str2) > 0;


### 5.1.15 比较string类字符串

如果使用sting类字符串而不是C-字符串，比较起来简单些。

compstr2.cpp

	#include <iostream>
	int main()
	{
	        using namespace std;
	        string word = "?ate";
	        for (char ch =  'a'; word != "mate"; ch++)
	        {
	                cout << word << endl;
	                word[0] = ch;
	        }
	        cout << "After loop ends, word is :" << word << endl;
	        return 0;
	}

string类重载运算符！=的方式可以在下面条件下使用它：至少一个操作数为string对象，另一个操作数可以是string对象，也可以是C-风格字符串。

## 5.2 while循环

while循环是没有初始化和更新部分的for循环，它只有测试条件和循环体：

	while (test-condition)
		body

首先，程序计算圆括号内的测试条件表达式，如果该表达式为true，则执行循环体中的语句。如果希望循环最终能够结束，循环体中的代码必须完成某种影响测试条件表达式的操作。

while.cpp

	#include <iostream>
	const int ArSize = 20;
	int main()
	{
		using namespace std;
		char name[ArSize];
		cout << "Your first name:  ";
		cin >> name;
		cout << "Here is your name, verticalized and ASCIIized:\n";
		int i = 0;
		while(name[i] != '\0')
		{
			cout << name[i] << " : " << int(name[i]) << endl;
			i++;
		}
		return 0;
	}


结果：

	Your first name:  zxp
	Here is your name, verticalized and ASCIIized:
	z : 122
	x : 120
	p : 112

如果没有循环体中的i++来更新测试表达式的值，循环会一直停留在第一个数组元素上，导致死循环。测试条件还可以修改为：

	while(name[i])

程序的工作方式不变，对于编译器生成代码的速度将更快。由于name[i]是常规字符，其值为该字符的编码--非零值或true，当name[i]为空值时，其编码值为0或false。

打印字符的ASCII码，必须通过强制类型转换将name[i]转换为整型。

### 5.2.1 for与while

在C++中，for和while循环本质上是相同的，例如:

	for (init-expression; test-expression; update-expression)
	{
		statements
	}

可以改写成:

	init-expression;
	while (test-expression)
	{
		statements
		update-expression
	}


两者区别：

- for循环中省略测试条件时，将认为条件为true;
- 在for循环中，可使用初始化语句声明一个局部变量，但在while循环中不能这样做；
- 如果循环体中包括
- 
- 
- 
- 
- 
- 
- 
- 
- 
- 
- 
- 
- 
- continus语句，情况将稍有不同，后续讨论。

通常，使用for循环来循环计数，因为for循环格式允许将所有相关的信息---初始值、终止值和更新计算的方式放在同一个地方。在无法预先直到循环执行次数时，使用while循环。

设计循环时，三条原则：

- 指定循环终止的条件；
- 在首次测试之前初始化条件；
- 在条件被再次测试之前更新条件。

注意分号使用：

	while (i < 10);
	{
		cout << i;
		i++;
	}

这将是一个空循环，分号将结束while循环。

### 5.2.2 编写延时循环

有时候，让程序等待一段时间很有用。while循环可用于这个目的。早期的技术是让计算机进行计数，以等待一段时间：
	
	long waite = 0;
	while (waite < 10000)
		waite++;

这种方法的问题是，当计算机处理的速度发生变化，必须修改计数限制。更好的办法是让系统时钟来往常这种工作。

C++库中有一个函数有助于完成这项工作，这个函数名叫clock()，返回程序开始执行后所用的系统时间。这有两个复杂的问题：首先，clock()返回时间的单位不一定是秒，其次，该函数的返回类型在某些系统可能是long，在另一些习俗可能是unsigned long或其他类型。

但头文件ctime(早期的time.h)提供了解决这些问题的解决方案。首先，定义一个符号常量---CLOCKS_PER_SEC，该常量等于每秒钟包含的系统时间单位数，因此系统时间除以这个值，可以得到秒数。或者将秒数乘以CLOCKS_PER_SEC，可以得到系统时间单位为单位的时间。其次，ctime将clock作为clock()返回类型的别名，这意味着可以将变量声明为clock_t类型，编译器将把它转换为long、unsigned int或适合系统的其他类型。

waiting.cpp

	#include <iostream>
	#include <ctime>
	int main()
	{
		using namespace std;
		cout << "Enter the delay time, in seconde: ";
		float secs;
		cin >> secs;
		clock_t delay = secs * CLOCKS_PER_SEC;
		cout << "starting\a\n";
		clock_t start = clock();
		while (clock() - start < delay);
		cout << "done \a\n";
		return 0;
	}

结果：

	Enter the delay time, in seconde: 5
	starting
	done 

该程序以系统时间单位为单位计算延迟时间，避免了在每轮循环中将系统时间转换为秒。

**类型别名**

C++为类型建立别名的方式有两种。一种是使用预处理器：

	#define BYTE char //注意没有分号

这样，预处理器将在编译程序时使用char替换所有的BYTE，从而使BYTE成为char的别名。

第二种方式是使用C++（和C）的关键字typedef来创建别名。例如，将byte作为char的别名，可以这样做：

	typedef char byte;


要让byte_pointer成为char指针的表明，可以将byte_pointer声明为char指针，然后在前面加上关键字typedef:

	typedef char * byte_pointer;

也可以使用#define，不过声明一系列变量时，这种方法不适用，例如：

	#define FLOAT_POINTER float *;
	FLOAT_POINTER pa pb;

预处理器置换该声明为这样：

	float *pa, pb;

typedef方法不会有这样的问题。它能够处理更复杂的类型别名，这使得与使用#define相比，typedef是一种更佳的选择。

注意，typedef不会创建新类型，只是为已有的类型建立一个新名称。

编写程序对比两者：

	#include <iostream>
	#define char_point char *
	typedef char * byte_pointer;
	int main()
	{
		using namespace std;
		byte_pointer pa, pb;
		cout << "typedef: \n";
		cout << sizeof(pa) << endl;
		cout << sizeof(pb) << endl;
		
		char_point pc, pd;
		cout << "#define:\n ";
		cout << sizeof(pc) << endl;
		cout << sizeof(pd) << endl;
		return 0;
	}


结果：

	typedef: 
	8
	8
	#define:
	8
	1


## 5.3 do while循环

do while循环不同于介绍过的两种循环，因为它是出口条件循环，即这种循环将首先执行循环体，然后判定测试表达式，决定是否应该继续执行循环。句法如下：

	do
		body
	while (test-expression);

通常，入口条件循环比出口条件循环好，因为入口条件循环在循环开始之前对条件进行检查。但有时do while测试更合理，例如，请求用户输入时，程序必须先获取输入然后对它进行测试。

dowhile.cpp

	#include <iostream>
	using namespace std;
	int main()
	{
		int n;
		cout << "Enter number: ";
		do{
			cin >> n;
		} while(n != 7);
		cout << "Yes, 7 is my favorite.\n";
		return 0;
	}

结果：

	Enter number: 3
	4
	7
	Yes, 7 is my favorite.


**奇特的for循环**

	int i = 0;
	for (;;)
	{
		i++;
		if (30 >= i)
			break;
	}

另一种变体：

	int i = 0;
	for(;;i++)
	{
		if (30 >= i)
			break;
	}	

上述代码基于这样一个事实：for循环中的空测试条件被视为true。这些例子不易于阅读，也不能用作编写循环的通用模型。第一个例子的功能在do while循环中将表达得更清晰：

	int i = 0;
	do{
		i++
	} while(i <= 30);

第二个例子使用while循环可以表达得更清晰：

	while(i < 30)
	{
		i++;
	}


## 5.4 基于范围的for循环（C++11）

基于范围的for循环，简化了一种常见的循环任务：对数组或容器类（vector或array）的每个元素执行相同的操作：

	double prices[5] = {4.99, 10.99, 1.99, 7.99, 8.99};
	for (double x: price)
		cout << x << endl;

其中x最初表示数组price的第一个元素。显示第一个元素后，不断执行循环，而x依次表示数组的其他元素。因此，上述代码显示全部5个元素。

要修改数组的元素，需要使用不同的循环变量语法：

	for (double &x : prices)
		x = x * 0.80;

符号&表明x是一个引用变量。还可以结合使用基于for循环和初始化列表：

	for (int x : {3, 5, 2, 6})
		cout << x << " ";
	cout << endl;

Linux下使用C++11编译程序(rangefor.cpp):

	g++ -std=c++11 rangefor.cpp


### 5.5 循环和文本输入

cin对象支持3种不同模式的单字符输入，其用户接口各不相同。下面介绍while循环中使用这三种模式：

### 5.5.1 使用原始的cin进行输入

程序通过选择某个特殊的字符--哨兵字符，来作为停止表示，程序知道合适停止读取。例如下面程序遇到#字符时停止输入。

textcin1.cpp

	#include <iostream>
	using namespace std;
	int main()
	{
		char ch;
		int count = 0;
		cout << "Enter characters, enter # to quit: \n";
		cin >> ch;
		while (ch != '#')
		{
			cout << ch;
			++ count;
			cin >> ch;
		}
		cout << endl << count <<  " characters read. \n";
		return 0;
	}


结果：

	Enter characters, enter # to quit: 
	zxp is handsome # read here
	zxpishandsome
	13 characters read. 

程序在输出时省略了空格，原因是cin在读取char值时，与读取其他类型一样，cin将忽略空格和换行。因此输入中的空格没有被回显，也没有被包括在计数内。

只有按下回车键，用户输入的内容才会被发送给程序，这就是在运行程序时，可以在#后输入字符的原因。

### 5.5.2 使用cin.get(char)补救

通常，逐个字符读取时，程序需要检查每个字符，包括空格、制表符和换行符。cin所属的istream类中包括一个能够满足这种要求的成员函数。具体说，成员函数cin.get(char)读取输入中的下一个字符（即使它是空格），并将其赋值给变量ch。

textcin2.cpp

	#include <iostream>
	using namespace std;
	int main()
	{
		char ch;
		int count = 0;
		cout << "Enter characters, enter # to quit: \n";
		cin.get(ch);
		while (ch != '#')
		{
			cout << ch;
			++ count;
			cin.get(ch);
		}
		cout << endl << count <<  " characters read. \n";
		return 0;
	}

结果：

	Enter characters, enter # to quit: 
	zxp is handosome # read here
	zxp is handosome 
	17 characters read. 

现在程序回显了每个字符，并将全部字符计算在内，其中包括空格。

cin.get(ch)调用一个值放在ch变量中，这意味着将修改该变量的值。在C语言中，要修改变量的值，必须将变量的地址传递给函数，即cin.get(&ch)。当在C++中，只要函数将参数声明为引用即可。引用是C++在C上新增的一种类型。

### 5.5.3 使用哪一个cin.get()

前面使用过：

	cin.get(name, ArSize).get();

相当于两行：

	cin.get(name., ArSize);
	cin.get();

而本节，使用的为：

	cin.get(ch);

从上面可以看出，cin.get()的参数可以为空，可以是一个char类型的变量，甚至还可以是两个参数：一个整型，一个char型。这是因为C++支持**函数重载**。函数重载允许创建多个同名函数，条件是它们的参数列表不同。例如：如果在C++中使用cin.get(name, Arsize)，则编译器将找到使用char*和int作为参数的cin.get()版本；如果使用cin.get(ch)，则编译器将使用接受一个char参数的版本。

### 5.5.4 文件末尾

前面的输入程序通过使用#符号来表示输入结束，这样难令人满意，因为这样的符号可能就是合法的输入的组成部分。如果输入来自一个文件，则可以使用一种功能更强大的计数---检测文件末尾(EOF)。C++输入工具和操作系统协同工作，来检测文件末尾并将这种信息告知程序。

检测到EOF后，cin将两位(eofbit和failbit)都设置为1。可以通过成员函数eof()来查看eofbit是否被设置；如果检测到EOF，则cin.eof()将返回true，否则返回false。同样，如果failbit被设置为1，则fail()成员函数返回true，否则返回false。注意：eof和fail方法报告最近读取的结果；即它们事后报告，而不是预先报告，因此应当cin.eof()或cin.fail()测试应放在读取后。一般使用fail()，其可用于更多的实现中。

textcin3.cpp

	#include <iostream>
	using namespace std;
	int main()
	{
		char ch;
		int count = 0;
		cout << "Enter characters, enter # to quit: \n";
		cin.get(ch);
		while (cin.fail() == false)
		{
			cout << ch;
			++ count;
			cin.get(ch);
		}
		cout << endl << count <<  " characters read. \n";
		return 0;
	}


结果：

	Enter characters, enter # to quit: 
	yes yes it is very good
	yes yes it is very good
	no no i don't agree
	no no i don't agree
	//此处使用快捷键ctrl+d(Linux系统)，windows系统使用Ctrl+z
	44 characters read.


注意：Windows下面Ctrl+z就相当于EOFLinux下面Ctrl+d相当于EOF。

**1. EOF结束输入**

cin方法检测到EOF时，将设置cin对象中一个指示EOF条件的标记。设置这个标记后，cin将不读取输入，再次调用cin也不管用。对于文件输入是有道理，因为程序不应读取超出文件末尾的内容。

然而对于键盘输入，有可能使用模拟EOF来结束循环，但稍后要读取其他输入，cin.clear()方法可能清除EOF标记，使输入继续进行。不过在某些系统中（比如：Linux），输入快捷键将结束输入和输出，而cin.clear()将无法恢复输入和输出。

**2. 常见的字符输入做法**

	cin.get(ch);
	while (cin.fail() == false)
	{
		cout << ch;
		++ count;
		cin.get(ch);
	}

可以将上述代码使用一些简洁方式。!运算符将true切换为false或将false切换为true。比如:

	while (!cin.fail())

方法cin.get(char)的返回值是一个cin对象。然而，istream类提供了一个可以将istreamd对象(cin)转换为bool值得函数；但cin出现在需要是bool值得地方（while循环得测试条件中）时，该转换函数被调用。意味着可以改写为：

	while (cin)

这比cin.eof()和cin.fail()更通用，因为cin还可以检测到其他失败的原因，比如磁盘故障。

最后，由于cin.get(char)的返回值为cin，因此可以将循环简写为：

	while (cin.get(ch))
	{}

这样，cin.get(char)只被调用一次，而不是两次：循环前一次，循环后一次。为判断循环测试条件，程序必须先调用cin.get(ch)，如果成功，则将值放入ch中，然后，程序获得函数的返回值，即cin。接下来，程序对cin进行bool转换，如果输入成功，则结果为true，否则为false。

### 5.5.5 另一个cin.ge()版本

C语言中字符I/O函数--getchar()和putchar()，它们仍旧适用，只要包含头文件stdio.h(或cstdio)即可。也可以使用istream和iostream中类似的成员。

不接受任何参数的cin.get()成员函数将返回输入中的下一个字符：

	int ch;
	ch = cin.get();

该函数的工作方式与C语言中getchar()相似，将字符编码作为int值返回，而cin.get(ch)返回一个对象，而不是读取的字符。同样，可以使用cout.put()函数来显示字符：

	cout.put(ch);

该函数的工作方式与C语言中的putchar()类似，只不过其参数类型为char，而不是int。C++实现提供了三种原型：put(char),put(signed char),put(unsigned char)。给put一个int类型将导致错误类型，但可以通过强制类型转换实现：cin.put(char(ch))。

当函数到达EOF时，cin.get()将返回一个符号常量EOF表示的特殊值。该常量是在头文件iostream中定义的。通常，EOF被定义为-1，但没必要知道实际值，而必需在程序中使用EOF即可。

	cin.get(ch);
	while (ch != EOF)
	{
		cout << ch;
		++ count;
		cin.get(ch);
	}

由于EOF表示的不是有效字符编码，因此可能不与char类型兼容，例如：在有些系统中，char类型是没有符号的，因此char变量不可能为EOF值(-1)。由于这种原因，如果使用cin.get()并测试EOF，则必须将返回值赋给int类型，而不是char类型。但是，如果将ch的类型声明为int，而不是char，则必须在显示ch时强制转换为char类型。

textcin5.cpp

	#include <iostream>
	using namespace std;
	int main()
	{
		int ch;
		int count = 0;
		while ((ch = cin.get()) != EOF)
		{
			cout.put(ch);
			count++;
		}
		cout << endl  << count << " characters read.\n";
		return 0;
	}

结果:

	the smalller
	the smalller
	good boy
	good boy
	//按下快捷键ctrl+d
	22 characters read.


循环条件，如果写成如下形式：

	while ( ch = cin.get() !=  EOF)

由于！=的优先级高于=，因此程序首先对cin.get()的返回值和EOF进行比较。比较的的结果为false或true，而这些bool值被转换为0或1，并赋值给ch。

ch=cin.get()和cin.get(ch)的区别

| 属性 | cin.get(ch) | ch = cin.get() |
|---|
| 传递输入字符的方式 | 赋给参数ch | 将函数返回值赋给ch |
| 用于字符输入时函数的返回值 | istrem对象(执行bool转换后为true) | int类型的字符编码 |
| 达到EOF时函数的返回值 | istream对象(执行bool转换后为false) | EOF |

	cin.get(ch1).get(ch2)

这是可行的，因为函数cin.get(ch1)返回一个cin对象，然后便可以通过该对象调用get(ch2)。

## 5.6 嵌套循环和二维数组

二维数组的声明如下：

	int maxtemps[4][5];

该声明意味着maxtemps是一个包含4个元素的数组，其中每个元素都是一个由5个整数组成的数组。表达式maxtemps[0]是maxtemps数组e第一个元素，因此maxtemps[0]本身就是一个由5个int组成的数组。maxtemps[0]数组的第一个元素是maxtemps[0][0]，该元素是int元素。可以认为第一个下标表示行，第二个下标表示列。

假设要打数组的所有内容：

	for (int row = 0; row < 4; row++)
	{
		for (int col = 0; col < 5; col++)
			cout << maxtemps[row][col] << "\t";
		cout << endl;
	}

### 5.6.1 初始化二维数组

创建二维数组时，可以初始化其所有元素：

	int maxtemps[4][5] = 
	{
		{1,2,3,4,5},
		{2,3,4,5,6},
		{3,4,5,6,7},
		{4,5,6,7,8},
	};


### 5.6.2 使用二维数组

nested.cpp

	#include <iostream>
	using namespace std;
	const int Cities = 5;
	const int Years = 4;
	int main()
	{
		const char * cities[Cities] = 
		{
			"Griblle",
			"Gribbletown",
			"New Gribble",
			"San Gribble",
			"Gribble Vista"
		};
		int maxtemps[Years][Cities] = 
		{
			{1,2,3,4,5},
			{2,3,4,5,6},
			{3,4,5,6,7},
			{4,5,6,7,8},
		};
	
		cout << "Maximum tempeartures for 2008-2011\n";
		for (int city = 0; city < Cities; city++)
		{
			cout << cities[city] << ": \t";
			for (int year = 0; year < Years; year++)
				cout << maxtemps[year][city] << "\t";
			cout << endl;
		}
		return 0;
	}

结果：

	Maximum tempeartures for 2008-2011
	Griblle: 	1	2	3	4	
	Gribbletown: 	2	3	4	5	
	New Gribble: 	3	4	5	6	
	San Gribble: 	4	5	6	7	
	Gribble Vista: 	5	6	7	8


## 5.7 总结

C++提供三种循环：for循环、while循环和do while循环。