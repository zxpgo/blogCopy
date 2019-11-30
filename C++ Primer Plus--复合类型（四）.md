---

title:  C++ Primer Plus--复合类型（四）
date: 2018-09-12 14:23:14
tags: C++
categories: "C++ Primer Plus学习笔记"
mathjax: true

---

复合类型介绍<!--more-->

## 4.1 数组

数组是一种数据格式，能过存储多个同类型的值。例如，数组可以存储60个int类型的值。

创建数组，可使用声明语句，声明输入应指出以下三点：

- 存储在每个元素种的值的类型
- 数组名
- 数组种的元素数

C++中，可以通过修改简单变量的声明，添加中括号来完成数组声明。例如，下面的声明创建一个名为months的数组，该数组有12个元素，每个元素都可以存储一个short类型的值:

	short months[12]

事实上，可以将数组中的每个元素看作一个简单变量。声明数组的通用格式如下：

	typename arrayName[arraySize];

表达式arraySize指定元素数目，它必须是整型或const值，也可以是常量表达式（如：8*sizeof(int)）,即其中所有的值在编译时都是已知的。具体说，arraySize不能时变量，变量的值时程序运行时设置。稍后，介绍如何使用new运算符来避免这种限制。

数组之所以称之为复合类型，是因为它是可以使用其他类型来创建的。不能仅仅将某种东西声明为数组，它必须是特定类型的数组。没有通用的数组类型，当存在很多特定的数组类型，如char类型或long数组。例如：

	float loans[20];

loans的类型不是“数组”，而是“float数组”。这强调loans数组是使用float类型创建的。

数组的很多用途都是基于这样一个事实：可以单独访问数组的元素。方法是使用下标或索引来对元素进行编号。C++数组从0开始编号。C++使用带索引的括号表示法来指定数组的元素。例如，months[0]是months数组的第一个元素，months[11]是最后一个元素。注意最后一个元素的索引比数组长度小1。因此，数组声明能过使用一个声明创建大量的变量，然后便可以用索引来标识和访问各个元素。

有效下标的重要性：编译器不会检查使用的下标是否有效。但是程序运行后，这种赋值可能引发问题，他可能破坏数据或代码，也可能导致程序异常终止。

数组初始化：

	int y[3] = {0,1,2};


sizeof运算符返回类型或数据对象的长度（单位为字节）。

	sizeof(y);


### 4.1.1 数组初始化

只有定义数组时才能初始化，此后就不能使用了，也不能将一个数组赋给另一个数组：

	int cards[5] = {3,6,8,10}; 
	int hand[4];
	hand[4] = {5,6,7,9}; //错误做法，hand[4] = 5;这样是替换低4个元素的值为5
	hand = cards; //错误语法

然而，可以使用下标分别给数组中的元素赋值。

初始化数组时，提供的值可以少于数组的元素数目。例如，下面的语句只初始化hotelTips的前两个元素：

	float hotelTips[5] = {5.0, 2.5};
	
如果只对数组的一部分进行初始化，则编译器将把其他元素设置为0。将数组中所有的元素初始化为0非常简单，只要显式地将第一个元素初始化0。然后编译器将其他元素都初始化为0即可：

	long totals[500] = {0};

如果初始化数组方括号内([])为空，C++编译器将计算元素个数，例如，对于下面的声明：

	short things[] = {1,5,3,8};

编译器将使things数组包含4个元素。

### 4.1.2 C++11数组初始化方法

初始化时可以省略等号：

	double earning[3] {100.1, 112.2,133.2};

可以在花括号中不包括任何内容，这将把所有元素都设置为零：

	float blances[100] {}; 

列表初始化禁止缩窄转换：

	long plifs[] = {25, 3.0}; //错误语法，3.0为float无法转换为long
	char slifs[] = {'h', 'i', 1122011}; //最后一个元素太大，char无法容纳，char变量的长度为8位
	char tilfs[4] = {'h', 'i', 24}; //正确做法

## 4.2 字符串

字符串是存储在内存的连续字节中的一系列字符。C++处理字符串的方式有两种：

- 来自C语言，常被称为C-风格字符串
- 基于string类库的方法

存储在连续字节中的一系列字符意味着可以将字符串存储在char数组中，其中每个字符都位于自己的数组元素中。C-风格字符具有一种特殊的性质：以空字符结尾，用字符被写作\0,其ASCII码为0，用来标记字符串的结尾。例如：

	char dog[4] = {'b','e','a','x'}; //不是一个字符串
	char cat[4] = {'f','s','a','\0'};  //是一个字符串

这两个数组都是char数组，但只有第二个数组是字符串。空字符对C-风格字符串而言至关重要。例如，C++有很多处理字符串的函数，其中包括cout使用的那些函数，它们都逐个地字符串中的字符，直到到达一个空字符为止。如果使用cout显式cat这样的字符串，则将前3个字符，发现空字符后停止。如果使用cout显式上面的dog数组（他不是字符串），cout将打印出数组中的4个字符，并接着将内存中随后的各个字节解释为要打印的字符，直到遇到控制符为止。由于空字符（实际上是被设置为0的字节）在内存中是常见的，因此这一过程很快就停止。

一种更好的、将字符数组数组初始化为字符串的方法--只需使用一个引号括起来的字符即可，这种字符串被称为字符串常量或字符串字面值，如下：

	char bird[11] = "Mr. Cheeps";
	char fish[] = "Bubbles";

用引号括起的字符串隐式地包括结尾的空字符，因此不用显式地包括它。

C++输入工具通过键盘输入，将字符串读入到char数组中时，将自动加上末尾的空字符。当然应确保数组足够大，能过存储字符串中的所有字符---包括空字符。

注意：在确定存储字符串所需的最短数组时，别忘了将结尾的空字符计算在内。

字符串常量（使用双引号）不能与字符常量（使用单引号）互换。字符常量（如‘S’）是字符串编码的简写表示。在ASCII系统上，‘S’只是83的另一种写法，因此，下面的语句将83赋给shirt_size:

	char shirt_size = 'S';

但"S"不是字符常量,它表示的是两个字符（字符S和\0）组成的字符串。更糟糕的是，"S"实际上表示的是字符串所在的内存地址。因此，下面的语句试图将一个内存地址赋给shirt_size:

	char shirt_size = "S";//错误的做法

由于地址在C++中是一种独立的类型，因此编译器不允许这种不合理的做法。

### 4.2.1 拼接字符串常量

但字符串很长无法放到一行时，C++允许拼接字符串字面值，即将两个用引号括起来的字符串合并为一个。 事实上，任何两个有空白（空格、制表符、换行符）分割的字符串常量都将自动拼接成一个。下面语句等价：

	cout << "I'd give my right arm to ba " "a great violinist.\n";
	cout << "I'd give my right arm to ba a great violinist.\n";
	cout << "I'd give my right ar" 
			"m to ba a great violinist.\n"

注意，拼接时不会再被连接的字符串之间添加空格，第二个字符串的第一个字符将紧接在第一个字符串的最后一个字符（不考虑\0）后面。第一个字符串中的\0会被第二个字符串中的第一个字符取代。

### 4.2.2 在数组中使用字符串

将字符串存段数组中，常用的两种方法：

- 将数组初始化为字符串常量
- 将键盘或文件输入读入到数组中

string.cpp

	#include <cstring>
	#include <iostream>
	using namespace std;
	
	int main()
	{
	        const int Size = 15;
	        char name1[Size];
	        char name2[Size] = "C++owboy";
	
	        cout << "Howdy! I'm " << name2;
	        cout << "! What's your name? \n";
	        cin >> name1;
	        cout << "Well, " << name1 << endl;
	        cout << "Your name has " << strlen(name1) << " letters and is stored" << endl;
	        cout << "Your name has " << sizeof(name1) << " bytes. \n" ;
	        name2[3] = '\0'; //第四个字符设置为空字符，打印字符串时，到此结束
	        cout << "First 3 characters of my name: " << name2 << endl;
	        return 0;
	}

结果：Howdy! I'm C++owboy! What's your name? </br>
zxphello</br>
Well, zxphello</br>
Your name has 8 letters and is stored</br>
Your name has 15 bytes. </br>
First 3 characters of my name: C++</br>


sizeof运算符指出整个数组的长度：15字节；但strlen()函数返回的是存储在数组中的字符串的长度，而不是数组本身的长度。另外，strlen()只计算可见的字符，空字符不计算在内。如果存储字符串cosmic,数组的长度不能短于strlen(comisc) + 1。

注意：使用符合常量表示数组长度，当修改程序以使用不同数组长度时，工作变得非常简单。

### 4.2.3 字符串输入

程序inst1.cpp

	#include <iostream>
	using namespace std;
	
	int main()
	{
	        const int Size = 20;
	        char name[Size];
	        char dessert[Size];
	
	        cout << "Enter your name: \n";
	        cin >> name;
	        cout << "Enter your favorite dessert: \n";
	        cin >> dessert;
	        cout <<  name << " like "<< dessert << endl;
	        return 0;
	}


结果：</br>

	[root@localhost ~]# ./a.out</br>
	Enter your name: </br>
	zxp</br>
	Enter your favorite dessert:</br> 
	kk</br> 
	zxp like kk</br> 
	[root@localhost ~]# ./a.out</br> 
	Enter your name: </br> 
	zxp zxp1</br> 
	Enter your favorite dessert: </br> 
	zxp like zxp1</br> 

对于第二种情况，我们还没有对“输入甜点的提示”做出反应，程序便他把显示出来。

由于不能通过键盘输入空字符，因此cin需要用别的方法来字符串的结尾位置。cin使用空白（空格、制表符和换行符）来确定字符串的结束位置，这意味着cin在获取字符数组输入时只读取一个单词。读取该单词后，cin将该字符串放到数组中，并自动在结尾添加空字符。

这个例子的实际结果是，cin把zxp作为第一个字符串，并将它放到name数组中。把zxp1留在队列中，当cin在输入队列中搜索用户喜欢的甜点时，它发现了zxp1,因此cin读取zxp1,并将它放到dessert数组中。

### 4.2.4 每次读取一行字符串输入

当程序要求用户输入城市名，用户输入New York,希望完整的存储城市名，而不仅仅是New。

istream中的类（如cin）提供了面向行的类成员：getline()和get()。这两个函数都读取一行输入，直到到达换行符。然而，随后getline()将丢弃换行符，而get()将换行符保留在输入序列中。

**1、getline()**

使用cin.getline()调用。该函数有两个参数：

- 用来存储输入行的数组的名称参数
- 要读取字符数的参数，getline()成员函数，在读取指定数目的字符或遇到换行符时停止读取。

例如，使用getline()将姓名读取到一个包含20个元素的name数组中：

	cin.getline(name, 20);

如果一行读入不超过19个字符，将全部读取到name数组中。

将instr1.cpp程序修改为使用cin.getline(),而不是简单的cin。

	#include <iostream>
	using namespace std;
	
	int main()
	{
	        const int Size = 20;
	        char name[Size];
	        char dessert[Size];
	
	        cout << "Enter your name: \n";
	        cin.getline(name, Size);
	        cout << "Enter your favorite dessert: \n";
	        cin.getline(dessert, Size);
	        cout <<  name << " like "<< dessert << endl;
	        return 0;
	}
	
结果：

	[root@localhost ~]# ./a.out
	Enter your name: 
	zxp zxp1
	Enter your favorite dessert: 
	kk dd
	zxp zxp1 like kk dd


该程序可以读取完整的姓名和用户喜欢的甜点。getline()函数每次读取一行，通过换行符来确定行尾，但不保存换行符。


**2、get()**

get()函数的参数跟getline()相同，解释参数的方式也相同，并且都读取到行尾。但get()不丢弃换行符，而是将其留在输入队列中。假设，连续两次调用get():

	cin.get(name, Arsize);
	cin.get(dessert, Arsize);

由于第一次调用后，换行符还在输入队列中，因此第二次调用时看到的第一个字符便是换行符。因此get()默认已到达行尾，而没有读取到任何内容。

一种方法是通过使用get()的变体，使用不带参数的cin.get()调用可读取下一个字符（即使换行符），因此可以用它来处理换行符：

	cin.get(name, Arsize);
	cin.get()
	cin.get(dessert, Arsize);

另一种使用get()的方式是将两个类成员函数拼接起来（合并）：

	cin.get(name, Arsize).get();

这样做，是由于cin.get(name, Arsize)返回一个cin对象，该对象随后调用get()函数。

下面语句跟两次调用getline()效果相同:

	cin.getline(name1, Arsize).getline(name1,Arsize);	

使用get()使输入更仔细。例如，假设用get()将一行读入到数组中，如何直到停止的原因是由于已经读取了整行，而不是由于数组已填满。查看下一个输入字符，如果是换行符，说面已读取了整行，否则，说明该行中还有其他输入。

**3、空行和其他问题**

当getline()和get()读取空行时，最初的做法，下一条输入语句将在前一条getline()或get()结束读取的位置开始读取。当前做法，当get()（而不是getline()）读取空行后，将设置失效位（failbit）,这意味着接下来的输入被阻断，但可以使用如下命令来恢复输入：

	cin.clear()

另一个问题是：输入字符串可能比分配的空间长。如果输入行包含的字符数比指定的多，则getline()和get()将把余下的字符保留在队列中，而getline()还会设置失效位，并关闭后面的输入。

### 4.2.5 混合输入字符串和数字


numstr.cpp

	#include <iostream>
	using namespace std;
	
	int main()
	{
	        int year;
	        char address[80];
	        cout << "Enter year:\n" ;
	        cin >> year;
	        cout << "Enter address:\n";
	//      cin >> address;
	        cin.getline(address,80);
	        cout << "Year: " << year <<endl;
	        cout << "Address: " << address << endl;
	        return 0;
	}

结果：  Enter year:</br>
1991</br>
Enter address:</br>
Year: 1991</br>
Address: </br>

用户根本没有输入地址。问题在于cin读取年份，将回车生成的换行符留在的输入队列中。后面的cin.getline()看到换行符后，将认为是一个空行，并将一个空字符串赋给address数组。解决的办法是：在读取地址之前先读取并丢弃换行符。具体方法是：

	cin.get();//调用一个没有参数的get() 
	或者 cin.get(ch); //调用一个接受参数的get()
	或者 (cin >> year).get();
	或者 (cin >> year).get(ch); 

## 4.3 string类简介

string类型的变量可以存储字符串，不是使用字符数组的方式存储。string类使用起来比数组简单，同时提供了将字符串作为一种数据类型的表达方式。

使用string类，必须在程序中包含头文件string。string类位于命名空间std中，因此必须通告一条using编译指令，或者使用std::string来引用它。string来定义隐藏了字符串的数组特性。

strtype1.cpp

	#include <iostream>
	#include <string>
	using namespace std;
	
	int main()
	{
	        char ch1[20];
	        char ch2[20] = "jaguar";
	        string str1;
	        string str2 = "panther";
	
	        cout << "Enter a king of feline:\n";
	        cin >> ch1;
	        cout << "Enter another king of  faline: \n";
	        cin >> str1;
	
	        cout << "ch1: " <<  ch1 << endl;
	        cout << "ch2: " <<  ch2 << endl;
	        cout << "str1: " << str1 << endl;
	        cout << "str2: " << str2 << endl;
	        return 0;
	}

结果：Enter a king of feline:</br>
ocelot</br>
Enter another king of  faline: </br>
tiger</br>
ch1: ocelot</br>
ch2: jaguar</br>
str1: tiger</br>
str2: panther</br>

string类设计让程序能够处理string的大小。例如：str1的声明创建一个长度为0的string对象，当程序将输入读取到str1中时，将自动调整str1的长度。这跟数组相比，使用string更安全方便。

char数组视为一组用于存储一个字符串的char存储单元，而string类变量是一个表示字符串的实体。

### C++11字符串初始化

C++11也允许将列表初始化用于字符串和string对象：

	char ch1[] = {"aaaa, 11"};
	string str1 = {"bbb, 22"}

### 赋值、拼接和附加

使用string类时，一些操作比数组更简单。比如：不能将一个数组赋给另一个数组，但可以将一个string对象赋给另一个string对象。

	char ch1[20];
	char ch2[20] = "jaguar";
    string str1;
    string str2 = "panther";
	ch1  = ch2;  
	str1 = str2;

	#include <iostream>
	#include <string>
	using namespace std;
	
	int main()
	{
	        char ch1[20];
	        char ch2[20] = "jaguar";
	        string str1;
	        string str2 = "panther";
	        ch1 = ch2;//语法错误
	        str1 = str2;

	        cout << "ch1: " <<  ch1 << endl;
	        cout << "ch2: " <<  ch2 << endl;
	        cout << "str1: " << str1 << endl;
	        cout << "str2: " << str2 << endl;
	        return 0;
	}

报错：
strtype1.cpp: 在函数‘int main()’中:</br>
strtype1.cpp:14:6: 错误：无效的数组赋值</br>
  ch1 = ch2;

string类简化了字符串的合并操作。可以使用+运算符将两个string对象合并，还可以将字符串附加到string对象的末尾。即：

	string str3;
	str3 = str1 + str2;
	str1 += str2;

### string类的其他操作

C-风格字符串一些常用函数（包含在cstring头文件中）：
		
- 字符串赋值到字符数组中：使用strcpy()函数
- 将字符串附加到字符数组末尾：使用strcat()函数
- 获取数组字符串长度：strlen(ch1)

获取string类字符串长度： str1.size()，size()是一个类方法，只能通过所属类的对象进行调用。

strtype3.cpp

	#include <iostream>
	#include <string>
	#include <cstring>
	using namespace std;
	
	int main()
	{
	        char ch1[20];
	        char ch2[20] = "jaguar";
	        int len1 = strlen(ch2);
	        string str1 = "12345";
	        int len2 = str1.size();
	        strcpy(ch1, ch2 );
	        strcat(ch1, "ccc");
	        cout << "ch1: " <<  ch1 << endl;
	        cout << "ch2: " <<  ch2 << endl;
	        cout << "len1: " << len1 << endl;
	        cout << "len2: " <<  len2 << endl;
	        return 0;
	}


结果：

	ch1: jaguarccc
	ch2: jaguar
	len1: 6
	len2: 5

### 4.3.4 string类I/O

strtype2.cpp

	#include <iostream>
	#include <string>
	#include <cstring>
	using namespace std;
	
	int main()
	{
	        char ch1[20];
	        string str1;
	
	        cout << "ch1's length before input: " <<  strlen(ch1) << endl;
	        cout << "str's length before input: " << str1.size() << endl;
	        cout << "Enter a line of text: \n";
	        cin.getline(ch1,20);
	        cout << "Enter another line of text: \n";
	        getline(cin,str1);
	        cout << "ch1: " << ch1 << " it's length: " << strlen(ch1) << endl;
	        cout << "str1: " << str1 << " it's length: " << str1.size() << endl;
	        return 0;
	}


结果：

	ch1's length before input: 6
	str's length before input: 0
	Enter a line of text: 
	abc
	Enter another line of text: 
	kkkk
	ch1: abc it's length: 3
	str1: kkkk it's length: 4

用户输入之前，指定了ch1的长度为20，而输出为6，这是因为：

- 初始化的数组的内容未定义
- 函数strlen()从数组的第一个元素开始计算字节数，直到遇到空字符

在本例中，在数组中第6个字节遇到空字符。对于未被初始化的数据，第一个空字符出现的位置是随机的，也可能出现数组规定字节外，这样数组的长度大于20。

getline(ch1, 20)是一个istream类的一个类方法，第一个参数为目标数组，第二参数为数组长度。

getline(cin, str1)表明getline（）不是类方法(类方法使用句点表示法),它将cin作为参数，指出从哪里查找输入。另外，没有规定字符串的长度，string对象会根据字符串的长度自动调整大小。

getline()一个是istream的类方法，而另一个不是。在引入string之前，C++就有istream类，因此istrem设计考虑了int、double等数据类型，但没有考虑string类型，所以没有处理string对象的方法。但处理string对象的代码使用string类的一个友元函数。

### 4.3.5 其他形式的字符串字面值

除char类型外，C++还有类型wchar_t，C++新增了char16_t和char32_t。可创建这些类型的数组和这些类型的字符串字面值。对于这些类型的字符串字面值，C++分布使用前缀L、u和U来表示，如下：

	wchar_t a[] = L"aaaa";
	char16_t b[] = u"bbbb";
	char32_t c[] = U"cccc";

C++11还支持Unicode字符编码方案UTF-8。在这种方案中，根据编码的数字值，字符可能存储为1~4个八位组。C++使用前缀u8来表示这种类型的字符串字面值。

C++11还增加了另一种新类型是**原始(raw)字符串**。在原始字符串中，字符表示的就是自己。例如：\n不表示换行符，而是两个常规的字符--斜杠和n。还有在字符中使用",不用\"来表示。原始字符串使用"(和)"来做定界符，并使用前缀R来标识。

	cout << R"(Jim "King" \n instead of endl.)" << '\n';

输出为：

	Jim "King" \n instead of endl.

原始字符串的界定符还可以自己设定，比如：有时候需要在字符串中输入"(或者)",这是需要自定义界定符。可以在"和(之间添加任意符号，这样在字符串结尾的)和"之间也要添加这些字符。比如：使用R"+#(标识字符串的开头，必须使用)(+#"作为原始字符串的结尾。因此由：
	
	cout << R"+#(Jim Keing"(hello world)")+=" << endl;

## 4.4 结构简介

结构是一种比较灵活的数据格式，同一个结构中可以存储多种类型的数据。结构也是C++面向对象(类)的基石。结构是用户自定义的类型，而结构声明定义了这种类型的数据属性。定义了类型后，便可以创建这种类型的变量。创建一个结构包括两步：定义结构描述；按描述创建结构变量。

结构描述如下：

	struct inflatable
	{
		char name[20];
		float volume;
		double price;
	}

其中关键字struct表明，这些代码定义的是一个结构的布局。标识符inflatable是这种数据格式的名称，因此新类型的名称为inflatable。定义结构后，便可以创建这种类型的变量：

	inflatable hat;
	inflatable mainframe;

C中要求添加struct关键字,如下：

	struct inflatable hat;

因为hat的类型为inflatable，因此可以是一个.操作符访问各个成员。比如：hat.volume指的是结构得volume成员。

### 4.4.1 程序中使用结构体

structur.cpp

	#include <iostream>
	using namespace std;
	struct inflatable
	{
	        char name[20];
	        float volume;
	        double price;
	};
	
	int main()
	{
	        inflatable guest = {"gloria", 1.88, 29.99};
	        inflatable pal = {"Arthur", 3.12, 32.99};
	
	        cout << "Guest: " << guest.name << " " << guest.volume
	        << " " << guest.price << endl;
	        cout << pal.price + guest.price << endl;
	        return 0;
	}

结果：Guest: gloria 1.88 29.99</br>
62.98

结果声明声明得位置有两种选择，第一，放在main()函数中；第二，放在main()函数的前面，其他函数也可以访问。变量也可以在函数内部和外部定义，外部定义由所有函数共享。

### C++结构初始化

与数组踹坏，C++也支持列表初始化用于结构，且等号是可选的：

	inflatable duck {"Daphe", 0.12, 9,89};

其次，如果大括号内为空，各个成员初始化为零。如下：

	inflatable mayor {};

最后，不允许缩窄转换。

### 结构可以使用string类o成员
	
	struct inflatable
	{
	        std::string name;
	        float volume;
	        double price;
	};
	

### 其他结构属性

- 结构变量之间可以使用赋值运算符;
- 结构可以作为参数传递给函数，也可以让函数返回一个结构；

assgn_st.cpp

	#include <iostream>
	using namespace std;
	struct inflatable
	{
	        char name[20];
	        float volume;
	        double price;
	};
	
	int main()
	{
	        inflatable bou = {"sun", 0.2, 12.49};
	        inflatable choice;
	        choice = bou;
	        cout << "choice: " << choice.price << endl;
	        return 0;
	}

结果： choice: 12.49

从中可见成员赋值是有效的，choice结构中的成员值与bouquet结构中存储的值相同。

可以同时完成定义结构和创建结果的工作，如下：

	struct perks
	{
		int key_num;
		char car[12];	
	}mr_smith, ms_jones;

甚至可以初始化以这种方式创建的变量：

	struct perks
	{
		int key_num;
		char car[12];	
	}mr_smith ={7, "Packard"};

还可以声明没有名称的结构体，这样以后无法创建这种类型的变量，如下：

	struct
	{
		int key_num;
		char car[12];	
	}mr_smith;

创建了一个mr_smith变量，可以访问其中的成员。

### 4.4.5 结构数组

可以创建结构数组，比如，创建一个包含100个inflatable结构的数组，如下：

	inflatable gifts[100];

这样gifts是一个inflatable数组，其中的每个元素(如gifts[0])都是inflatable对象，可以与成员运算符一起使用：

	cin >> gifts[0].volume;

gifts本身是一个数组，不是一个结构。因此gifts.price是无效的。

初始化结构数组，可以结合使用初始化数组的规则，具体如下：

	inflatable guests[2] = {
		{"zzz", 1.2, 33.4},
		{"ddd", 0.4, 33,2}
	};

### 4.4.6 结构中的位字段

C++允许指定占用特定位数的结构成员，这使得创建与某个硬件设备上的寄存器对应的数据结构非常方便。字段的类型应为整型或枚举，接下来是冒号，冒号后面是一个数字，它指定了使用的位数。可以使用没有名称的字段来提供间距。每个成员都被称为位字段(bit field)。下面是一个例子：

	struct torgle_register
	{	
		unsigned int SN : 4;
		ussigned int :4 ;
		bool goodIn : 1;
		bool goodTorgle : 1;
	}

可以先通常那样初始化这些字段，还可以使用标准的结构表示法来访问位字段：

	torgle_register tr = {14, true, false};	
	cout << tr.goodIn;

位字段一般使用在低级编程中。

## 4.5 共用体

共用体(union)是一种数据格式，它能够存储不同的数据格式，但只能同时存储其中的一种类型。即，结构体可以同时存储int、long和double,共用体只能存储int、long或double。共用体的句法与结构体相似，但含义不同。

	union one4all
	{
		int int_val;
		long long_val;
		double double_val;
	}

可以使用one4all变量来存储int、long或double，条件是在不同的时间进行：

	one4all pail;
	pail.int_val = 15;
	cout << pail.int_val;
	pail.double_val = 2.2; //int_val的值丢失
	cout << pail.double_val;

因此，pail有时可以是int变量，而有时是double类型的变量。通用体每次只能存储一个值，因此必须有足够大的空间来存储最大的成员，所以共用体的长度为其最大成员的长度。
	
共用体的用处之一是，但数据项使用两种或多种格式时，可节省空间。例如：管理一个小商品目录，其中一些商品的ID为整型，而另一些为字符串。在这种情况可以如下：

	struct widget
	{
		char brand[20];
		int tyep;
		union id
		{
			long id_num;
			char id_char[20];
		} id_val;
	};
	widget prize;
	if (prize.type == 1)
		cin >> prize.id_val.id_num;
	else
		cin >> prize.id_val.id_char;

匿名共用体没有名称，其成员将成为位于相同地址出的变量，显然，每次只有一个成员是当前的成员：

	struct widget
	{
		char brand[20];
		int tyep;
		union
		{
			long id_num;
			char id_char[20];
		} ;
	};
	widget prize;
	if (prize.type == 1)
		cin >> prize.id_num;
	else
		cin >> prize..id_char;

由于共用体是匿名的，因此id_num和id_char被视为prize的两个成员，它们的地址相同，所以不需要中间标识符id_val。共用体用于节省内存。但C++用于嵌入式编程，如控制烤箱或火星漫步者的处理器，内存非常宝贵。

## 4.6 枚举

C++的enum工具提供了另一种创建符号常量的方式，这种方式可以替代const。它还允许定义新类型，但必须按照严格的限制进行。使用enum句法与使用结构相似。例如:

	enum spectrum {red, orange, yellow, green, blue, violet, indigo ultraviolet};

该语句完成了两个工作:

- 让spectrum成为新类型的名称：spectrum被称为枚举(enumeration)
- 将red、orange等作为符号常量，它们对应整数值0~7，这些常量叫做枚举量。

利用枚举类型来声明这种类型的变量：

	spectrrm band;

对于枚举类型，只定义了赋值运算符，具体说，没有为枚举定义算术运算：

	band = orange;
	++band; //非法
	band = orange + yellow; //非法
	band = 2000; //非法，2000不是一个枚举类型
	

枚举量是整型，可被提升为int类型，但int类型不能自动转换为枚举类型：

	int color = bule;
	band = 3; //非法
	color = 3 + red; 

如果int值是有效的，则可以通过强制类型转换，将它赋值给枚举变量：

	band = spectrum(3);

如果试图对一个适当的值进行强制类型转换，结果是不确定的，不会报错：

	band = spectrum(5000);

如果只使用常量，而不创建枚举类型的变量，则可以省略枚举类型的名称，如下：
	
	enum {red, orange, yellow, green, blue, violet, indigo ultraviolet};

### 4.6.1 设置枚举量的值

可以使用赋值运算符来显示地设置枚举量的值：

	enum bits {one = 1, two = 2, four = 4, eight = 8};

指定的值必须是整数，也可以只显示地定义其中一些枚举量地值：

	enum bigstep {first, second = 100, third};

这里，first在默认情况下为0，后面没有被初始化地枚举量地值将比其前面的枚举量大1.因此third的值为101。

最火，可以创建多个值相同的枚举量：
	
	enum {zero, null = 0, one, numero_nuo = 1};

其中，zero,null的值都没零，one和numero_nuo都为1。在早期，只能将int值赋给枚举类型，但这种限制取消了，因此可以使用long甚至long long类型的值。

### 4.6.2 枚举的取值范围

对于枚举来说，只有声明中指出的那些值是有效的。然而，C++现在通过强制类型转换，增加了可赋给枚举变量的合法值。每个枚举都要取值范围，通过强制类型转换，可以将取值范围中的任何整数赋值给枚举变量，即使这个值不是枚举类型，如下:

	enum bits{one=1, two=2, four=4, eight=8};
	bits myflag;

下面的代码合理：

	myflag = bits(6);

其中6不是枚举类型，但它位于枚举定义的取值范围内。

取值的范围定义如下：首先，找出上限，需要知道枚举的最大值。找出大于这个最大值的、最小的2的幂，将它减去1，得到的便是取值范围的上限。计算下限，需要知道枚举量的最小值，如果它不小于0，则取值范围的下限为0；否则采用与寻找上限方式相同的方式，但加上负号。

例如:前面定义的bigstep的最大枚举值是101。在2的幂中，比这个数大的最小值是128，因此取值范围的上限为127。对于下限，如果最小的枚举量为-6，而比它小的、最大的2的幂是-8，因此下限为-7。


## 4.7 指针和自由存储空间

使用&地址运算符，获取变量的地址。例如，如果home是一个变量，则&home是它的地址。

	#include <iostream>
	int main()
	{
	        using namespace std;
	        int donuts = 6;
	        double cups = 4.5;
	
	        cout << "donuts's addresss: " <<  &donuts << endl;
	        cout << "cups's addresss: " << &cups << endl;
	        return 0;
	}

结果：donuts's addresss: 0x7ffe74cc89b8</br>
cups's addresss: 0x7ffe74cc89b4


显示地址时，该实现的cout使用十六进制表示法，因为这是常用于描述内存的表示法。两个地址的差为：0x7ffe74cc89b8-0x7ffe74cc89b4（即4）,在实现中，donuts的存储位置比cups低，而这种类型使用4个字节。当然，不同的系统，存储的顺序以及字节大小都不同。

指针与C++基本原理：

面向对象编程与传统的过程性编程的区别在于，OOP强调的是在运行阶段进行决策。运行阶段指的是程序正在运行，编译阶段指的是编译器将程序组合起来。运行阶段是做决策，程序应该如何运行，而编译阶段是安全预先设定的程序运行。

运行阶段决策提供了灵活性，可以根据当时的情况进行调整。比如：考虑为数组分配内存的情况。

一种特殊的变量--指针用于存储地址的值。$\ast$运算符被称为间接值或解除引用。

pointer.cpp

	#include <iostream>
	
	int main()
	{
	        using namespace std;
	        int updates = 6;
	        int *p_updates;
	        p_updates =  &updates;
	
	        cout << "*p_updates: " << *p_updates << endl;
	        cout << "p_updates: " << p_updates << endl;
			*p_updates = 1 + *p_updates;
	        return 0;
	}

	
结果：$\ast$p_updates: 6</br>
p_updates: 0x7ffc6c803fa4</br>
$\ast$p_updates + 1: 7


从中可知，p_updates表示地址，使用$\ast$号运算符来获得值。$\ast$p_updates和updates完全等价。可以像int变量一样使用$\ast$p_updates。

### 4.7.1 声明和初始化指针

声明指针，计算机需要跟踪指针指向的值的类型。比如：char的地址和double的地址，看上去一样，但char和double使用的字节数不同，它们存储值得内部格式不同。

	int * p_updates;
	或 int *p_updates;
	或 int* p_updates; //int* 是一种类型--指向int的指针。
	或 int*p_updates;
	
这表明，$\ast$updates的类型为int。由于$\ast$运算符被用于指针，因此p_updates变量本身必须是指针。

	int* p1, p2;

注意上面的语句是创建一个指针(p1)和一个int变量(p2)。对于每个指针变量名，都需要使用一个$\ast$。

	double * tax;
	char* str;

将tax声明为一个指向double的指针，编译器知道$\ast$tax是一个double类型的值。即$\ast$tax是一个以浮点数格式存储的值，这个值占据8个字节（不同系统可能不同）。指针变量不仅仅是指针，而且是指向特定类型的指针。虽然，tax和str指向两种不同长度的叔叔类型，但这两个变量本身的长度是相同的，即char的地址和double的地址的长度相同。

可以在声明语句中初始化。

	int h = 5;
	int *ph = &h;

被初始化的是指针，而不是它指向的值，即pt的值设为&h;而不是$\ast$pt的值。

### 4.7.2 指针的危险

在C++中创建地址时，计算机分配用来存储地址的内存，但不会分配用来存储指针所指向的数据的内存。为数据提供空间是一个独立的步骤，忽略这一步是错误的，如下：

	long * fellow;
	*fellow = 222;

fellow确实是一个指针。上述代码没有将地址赋给fellow,那么222将被存放在哪里？由于fellow没有被初始化，它可能有任何值。不管值是什么，程序都将它解释为存储222的地址。

注意：一定要在对指针应用解除引用运算符（$\ast$）之前，将指针初始化为一个确定的、适当的地址。

### 4.7.3 指针和数字

指针不是整型，索然计算机通常把地址当作整数处理。指针没有加减乘除运算，指针描述的是位置。不能简单的对将整数赋给地址：

	int *pt;
	pt = 0xB8000000; 

在C++中，编译器将显示错误信息，类型不匹配。要将数字值作为地址来使用，应通过强制类型转换将数字转换为适当的地址类型：

	int *pt;
	pt = (int*) 0xB8000000;

这样，赋值语句两边都是整型的地址，因此赋值有效。pt是int值的地址，并不意味着pt本身的类型是int。

### 4.7.4 使用new来分配内存

在C语言中，可以使用库函数malloc()来分配内存；而在C++让可以这样做，但C++提供了更好的方法---new运算符。

在运行阶段为一个int值分配未命名的内存，并使用指针来访问这个值：

	int *pn = new int;

new int告诉程序，需要适合存储int的内存。new运算符根据类型确定需要多少字节的内存，然后找到这样的内存，并返回其地址，并将地址赋给pn，pn是被声明为指向int的指针。现在pn是地址，$\ast$pn存储那里的值。将这种方法于将变量的地址赋给指针进行对比:

	int h = 5;
	int *ph = &h;

在这两种情况下，都是将一个int变量的地址赋给了指针。在第二种情况，可以通过变量名了访问该int值，而第一种情况只能通过指针进行访问。

为数据对象（可以是结构，也可以是基本类型）获得并指定分配内存的通用格式：

	typeName *pointer_name = new typeName;

use_new.cpp

	#include <iostream>
	
	int main()
	{
	        using namespace std;
	        int nights = 1001;
	        int *pt = new int;
	        *pt = 1001;
	
	        cout << "*pt: " << *pt << endl;
	        cout << "pt: " << pt  << endl;
	        cout << "&nights: " << &nights << endl;
	        return 0;
	}

结果：*pt: 1001</br>
pt: 0x220c010</br>
&nights: 0x7ffc241baf94

new为int数据对象分配内存，这是在程序运行时进行的。指针必须声明所指向的类型的原因是：地址本身只指出了对象存储的地址开始，而没有指出其类型(使用的字节数)。

对于指针，new分配的内存块于常规变量声明分配的内存块不同。变量nights的值存储在被称为栈的内存区域中，new从被称为堆或自由存储区的内存区域分配内存。

### 4.7.5 使用delete释放内存

需要内存时，使用new来请求。使用完内存后，使用delete运算符将其归还给内存池。一定要配对使用new和delete，否则会发送内存泄漏，即被分配的内存再也无法使用。如果泄漏严重，则程序将由于不断寻找更多内存而终止。

	int *ps =  new int;
	delete ps;

不用使用delete释放已经释放的内存，这样做结果是不确定的。另外，不要使用delete来释放声明变量所获得的内存：

	int jugs = 5;
	int *pi = &jugs;
	delete pi; //不允许，错误的做法

注意：只能用delete释放使用new分配的内存，然后，对空指针使用delete是安全的。

使用delete的关键在于，将它用于new分配的地址，而不意味着要使用用于new的指针，而是用户new的地址：

	int *ps = new int;
	int *pq = ps;
	delete pq;

一般来说，不要创建两个指向同一个内存块的地址，因为这样增加错误地删除同一个内存块两次的可能性。但，对于返回指针的函数，使用另一个指针是有道理的。

### 4.7.6 使用new来创建动态数组

在编程时给数组分配内存被称为静态联编，意味着数组在编译时加入到程序中的。但使用new时，如果在运行阶段需要数组，则创建它，如果不需要，则不创建。还可以在程序运行时选择数组的长度，这种被称为动态联编，意味着数是在程序运行时创建的。这种数组叫作动态数组。

**1、使用new创建动态数组**

在C++中创建动态数组：只要将数组元素类型和元素数目告诉new即可。必须在类型名后加上方括号，其中包含元素的数目：

	int *psome = new int[10]; 
	delete [] psome;

创建了一个包含10个int元素的数组。并使用delete对分配的内存进行释放。释放内存时，方括号告诉程序，应该释放整个数组，而不仅仅是指针指向的元素。

程序确实跟踪了分配的内存量，以便以后使用delete []正确地释放这些内存，但这种信息是不公用的。例如:不能使用sizeof运算符来确定动态数组分配的数组包含的字节数。

**2、使用动态数组**

*psome是第1个元素的值,psome[0]同样是第一个元素的值。psome[1]是第2个元素的值，以此类推。

arraynew.cpp

	#include <iostream>
	int main()
	{
	        using namespace std;
	        double *p3 = new double [3];
	        p3[0] = 0.2;
	        p3[1] = 0.5;
	        p3[3] = 0.8;
	        cout << "p3[1]: " << p3[1] << endl;
	        p3 = p3 + 1;
	        cout << "p3+1,p3[0]: " << p3[0] << endl;
	        p3 = p3 - 1;
	        delete [] p3;
	        return 0;
	}


结果：p3[1]: 0.5
p3+1,p3[0]: 0.5

从中可知，程序将指针p3当作数组名来使用，p3[0]表示第1个元素，依次类推。不过指针和数组名之间有差别的，不能更改数组名的值，但指针是变量，因此可以修改它的值：

	p3 = p3 + 1;

将p3加1的效果，是将p3[0]指向数组中的第2个元素。将它减1后，指针将指向原来的值，这样程序可以给delete[]提供正确的地址。


## 4.8 指针、数组和指针算术

指针和数组基本等级的原因在于指针运算符和C++内部处理数组的方式。将指针加1后，增加的量等于它所指向的类型的字节数。比如：将double类型的指针加1后，如果系统double使用8个字节存储，则数值将加8。另外，C++将数组名解释为地址。

addpntrs.cpp

	#include <iostream>
	int main()
	{
	        using namespace std;
	        double wages[3] = {1000.0, 2000.0, 3000.0};
	        short stacks[3] = {3, 2, 1};
	
	        double *pw = wages;
	        short *ps = &stacks[0];
	        cout << "pw = " <<  pw << ", *pw = " << *pw << endl;
	        pw = pw + 1;
	        cout << "Add 1 to the pw pointer:\n";
	        cout << "pw = " <<  pw << ", *pw = " << *pw << endl;
	
	        cout << "ps = " <<  ps << ", *ps = " << *ps << endl;
	        ps = ps + 1;
	        cout << "Add 1 to the ps pointer:\n";
	        cout << "ps = " <<  ps << ", *ps = " << *ps << endl;
	
	        cout << "stacts[0] = " << stacks[0] << endl;
	        cout << "*(stacks + 1) = " <<  *(stacks+1) << endl;
	
	        cout << "Wages array size: " << sizeof(wages) << endl;
	        cout << "pw pointer size: " << sizeof(pw) << endl;
	        return 0;
	}


结果：

	pw = 0x7ffedfcf9060, *pw = 1000
	Add 1 to the pw pointer:
	pw = 0x7ffedfcf9068, *pw = 2000
	ps = 0x7ffedfcf9050, *ps = 3
	Add 1 to the ps pointer:
	ps = 0x7ffedfcf9052, *ps = 2
	stacts[0] = 3
	*(stacks + 1) = 2
	Wages array size: 24
	pw pointer size: 8

### 4.8.1 程序说明

在多数情况下，数组名解释为数组的第一个元素的地址。因此，下面语句将pw声明为指向double类型的指针，然后将它初始化为wages---wages数组中第一个元素的地址：

	double *pw = wages; 

和所有数组一样，有：

	wages = &wages[0]; //第一个元素的地址

程序查看了pw和$\ast$pw的值，前者是地址，后者是存储在该地址的值。pw加1，数字地址值增加8（double类型）这样pw指向数组中第二个元素。而对于ps(short类型)，ps+1,其地址值将增加2。

注意：将指针变量加1后，其增加的值等于指向的类型所占用的字节数。

stacks[1]和$\ast$(stacks+1)等价，$\ast$(stacks+1意味着先计算数组第2个元素的地址，然后找到存储在那里的值。（运算符优先级要求使用括号，如果不使用将给$\ast$stacks的值加1）。

对于数组和指针，c++可以执行下面的转换：

	arrayname[i];  -> *(arrayname+1);
	pointername[i]; -> *(pointername+1);

数组和指针的区别在于，数组名是常量，而指针可以修改其值。如下：

	arrayname = arrayname + 1;//错误
	pointername = pointername + 1;

另一个区别，对于数组应用sizeof运算符得到的是数组的长度，而对指针应用sizeof运算符得到的指针的长度，即使指针指向一个数组。在上述程序中有体现。

**数组的地址**
数组名被解释为其第一个元素的地址，而对数组名应用地址运算符时，得到的是整个数组的地址：

	short tell[10];
	cout << tell << endl; //第一个元素的地址
	cout << &tell << endl; //整个数组的地址

从数字上说，这两个值是相等的；但概念上，tell(&tell[0])是一个2字节内存块的地址，而&tell是一个20字节的内存块地址。因此表达式tell+1将地址值加1，而表达式&tell+2将地址加20。即：tell是一个short指针($\ast$short),而&tell是一个指向包含20个元素的short数组（short($\ast$)[20]）的指针。

	short (*pas)[20] = &tell;

pas的类型为short($\ast$)[20]，由于pas被设置为&tell,因此*pas于tell等价，即($\ast$pas)[0]为tell数组的第一个元素。其中括号不能少，否则，pas是一个short指针数组，它包含20个元素。

### 4.8.2 指针小结

**1、声明指针**

	typeName * pointername;
	
	double *pn;
	char *pc;

**2、给指针赋值**

对变量使用&运算符，来获取被命名的内存的地址，new运算符返回未命名的内存的地址。

	double *pn;
	char * pc;
	couble * pa;
	double bud = 2.33;
	pn = &bud;
	pc = new char;
	pa = new double [10];
	
**3、对指针解除引用**

对指针解除引用意味着获取指针指向的值。

	cout << *pn;
	*pc = "s";
	pa[1] = 2.11;

决不要对未被初始化为适当地址的指针解除引用。

**4、区分指针和指针指向的值**

pt是指向int的指针，则$\ast$pt是指向int类型的变量的值。

	int *pt = new int;
	*pt = 3;

**5、数组名**

在多数情况下，C++将数组名视为数组第一个元素的地址。一种例外情况是，将sizeof运算符用于数组名时，此时将返回整个数组的长度。

**6、指针算术**

C++允许将指针和整数相加。还可以将一个指针减去另一个指针，获得两个指针的差，仅当两个指针指向同一个数组时，运算才有意义。
	
	int tacos[10] = {2,3,4,5,6,8,9,1,0,7};
	int *pt = tacos;
	pt = pt + 1;
	int *pe = &tacos[9];
	pe = pe - 1;
	int diff = pe - pt;

**7、数组的动态联编和静态联编**

使用数组声明来创建数组时，采用静态联编，即数组的长度在编译时给定：

	int tacos[10];

使用new[] 运算符创建数组时，将采用动态联编，即将在运行时为数组分配空间，其长度也在运行时设置：

	int size;
	cin >> size;
	int *pz = new int [size];
	delete [] pz;

**8、数组表示法和指针表示法**

	tacos[0]; 等价于 *tacos;
	tacos[3]; 等价于 *(tacos+3);

数组名和指针变量都是如此，因此对于指针和数组名，既可以使用指针表示法，也可以使用数组表示法。

### 4.8.3 指针和字符串

cout对象认为char的地址是字符串的地址，因此它打印该地址处的地址，然后继续打印后面的字符，知道遇到空字符(\0)为止。如果要获取字符串数组的地址，需要进行强制转换，如（int*）flower。而且，"are red"字符串常量，为了保持输出一致，这个引号括号起来的字符串也是一个地址。

注意：在cout和多数C++表达式中，char数组名、char指针以及引号括起来的字符串常量都被解释为字符串第一个字符的地址。

ptrstr.cpp

	#include <iostream>
	#include <cstring>
	int main()
	{
	        using namespace std;
	        char animal[20] = "bear";
	        const char *bird = "wren";
	        char *ps;
	
	        cout << animal << " and " << bird << endl;
	        //cout << ps << endl;
	        cout << "Enter a kind of animal:";
	        cin >> animal;
	
	        ps = animal;
	        cout << ps << endl;
	        cout << "Before using strcpy():\n";
	        cout << animal << " at " << (int *)animal << endl;
	        cout << ps << " at " << (int*)ps << endl;
	
	        ps = new char[strlen(animal) + 1];
	        strcpy(ps, animal);
	        cout  << "After using strcpy():\n";
	        cout << animal << " at " << (int *)animal << endl;
	        cout << ps << " at " << (int*)ps << endl;
	        return 0;
	}


结果：

	bear and wren
	Enter a kind of animal:fox
	fox
	Before using strcpy():
	fox at 0x7ffd1b868460
	fox at 0x7ffd1b868460
	After using strcpy():
	fox at 0x7ffd1b868460
	fox at 0xe91010


其中"wren"实际表示的是字符串的地址，因此"const char $\ast$bird = "wren";"语句是将"wren"的地址赋给了bird指针。程序中将bird指针声明为const，因此编译器将禁止改变bird指向的位置中的内容。

获得字符串副本，首先，需要分配内存来存储该字符串，这可以通过声明一个数组或使用new来完成。后一种方法使得能够根据字符串长度来指定所需的空间：

	ps = new char[strlen(animal) + 1];

然后，需要将animal数组中的字符串复制到新分配的空间中。将animal赋给ps是不可行的，因为这样只能修改存储在ps中的地址，从而失去程序访问新分配内存的唯一途径，需要使用库函数strcpy():

	strcpy(ps, animal);

strcpy()函数接收两个参数，第一个是目标地址，第二个是要赋值的字符串的地址。通过使用new和strcpy()，将获得"fox"两个独立的副本。

	fox at 0x7ffd1b868460
	fox at 0xe91010

经常需要将字符串放到数组中。初始化数组时，使用"="运算符；否则使用strcpy()或strncpy()。

	char food[20] = "carrots";
	strcpy(food, "flan");

	strcpy(food, "a picnic basket filled with many goodies");//导致问题，food数组比字符串小。

对于最后一种情况，函数将字符串剩余的部分复制到数组后面的内存字节中，这可能覆盖程序正在使用的其他内存。要避免这种问题，使用strncpy()。该函数接收第3个参数--要复制的最大字符数。

	strncpy(food, "a picnic basket filled with many goodies", 19);
	food[19] = '\0';

这样最多将19个字符复制到数组中，然后最后一个元素设置为空字符。如果该字符串少于19个字符，则strncpy()将在复制完成字符串之后加上空字符，以标记字符串的结尾。 

### 4.8.4 使用new创建动态结构

在运行时创建数组优于在编译时创建数组，对于结构也如此。对于new用于结构由两步组成：创建结构和访问其成员。创建结构，需要同时使用结构类型和new。如下：

	inflatable *ps = new inflatable;

这样把足以存储inflatable结构的一块可用内存的地址赋给ps。这种句法和C++内置类型完全相同。接下来是成员访问，创建动态结构时，不能使用运算符句点用于结构，因为这种结构没有名称，只知道其地址。C++专门提供了箭头成员运算符（->）。该运算符由连字符和大于号组成，可用于指向结构的指针，就像点运算符可用于结构名一样。例如：ps->price。

另一种访问结构的方法是，如果ps是指向结构的指针，则$\ast$ps就是被指向的值---结构本身。由于$\ast$ps是一个结构，因此($\ast$ps).price是该结果的price成员。C++的运算符优先级规则要求使用括号。

newstrct.cpp

	#include <iostream>
	struct inflatable
	{
	        char name[20];
	        float volume;
	        double price;
	};
	
	int main()
	{
	        using namespace std;
	        inflatable *ps = new inflatable;
	        cout << "Enter name of infaltable item: ";
	        cin.get(ps->name, 20);
	        cout << "Enter volume of inflatable item: ";
	        cin >> (*ps).volume;
	        cout << "Enter price of inflatable item: ";
	        cin >> ps->price;
	        cout << "Name: " << (*ps).name << endl;
	        cout << "Volume: " << ps->volume << endl;
	        cout << "Price: " << ps->price << endl;
	        return 0;
	}


结果：

	Enter name of infaltable item: Frodo
	Enter volume of inflatable item: 1.4
	Enter price of inflatable item: 27.99
	Name: Frodo
	Volume: 1.4
	Price: 27.99

**1. 一个使用new和delete的示例**

delete.cpp定义了一个函数getname()，该函数返回输入字符串的指针。该函数将输入读入到一个大型临时数组中，然后使用new[]来创建一个刚好存储该输入字符串的内存块，并返回一个指向该内存块的指针。对于读取大量字符串的程序，这种方法可以大大节省内存。

假设程序需要读取100个字符串，其中最大的字符串包含79个字符，而大多数字符串都短得多。如果用char数组来存储这些字符串，则需要100个数组，其中每个数组的长度为80个字符，总共需要8000多个字节，而其中大部分内存没有被使用。

另一种方法，创建一个数组，它包含100个指向char的指针，然后使用new根据字符串的需要分配相应数量的内存。还可以使用new根据需要的指针数量来分配空间。

delete.cpp

	#include <iostream>
	#include <cstring>
	using namespace std;
	char * getname(void);
	int main()
	{
	        char *name;
	        name = getname();
	        cout << name <<  " at " << (int*)name << endl;
	        delete [] name;
	        name = getname();
	        cout << name << " at " << (int*)name << endl;
	        delete [] name;
	        return 0;
	}
	
	char * getname()
	{
	        char temp[80];
	        cout << "Enter last name: ";
	        cin >> temp;
	        char *pn = new char [strlen(temp)+1];
	        strcpy(pn, temp);
	        return pn;
	}

结果：

	Enter last name: Fred
	Fred at 0x1c76010
	Enter last name: Zll
	Zll at 0x1c76010

**2. 程序说明**

getname()函数，使用cin将输入的字符串放到temp数组中，然后使用new分配行内存，以存储该字符串。程序需要strlen(temp)+1个字符（包括空字符）来存储该字符串，将这个值提供给new。获得空间后，getname()使用标准库函数strcpy()将temp中的字符串复制到新的内存块中。最后返回pn，这是字符串副本的地址。

在main()中，返回值（地址）被赋值给name。该指针是在main()中定义的，但它指向getname()函数中分配的内存块。接下来，在释放name指向的内存快后，main()再次调用getname()。C++不保证新释放的内存就是下一次使用new时选择的内存，但该例子中刚好是的。

在这个例子中，getname()分配内存，而main()释放内存，将new和delete放在不同的函数通常不是一个好办法，因为这样容易忘记使用delete。不过这样做确实可以。

### 4.8.5 自动存储、静态存储和动态存储

根据用户分配内存的方法，C++有3种管理数据内存的方式：自动存储、静态存储和动态存储（有时也叫作自由存储空间或堆）。在存储时间的长短方面，以这3种方式分配的数据对象各不相同。

**1、自动存储**

在函数内存定义的常规变量使用自动存储空间，被称为自动变量。即它们在所属的函数被调用时自动产生，在该函数结束时消亡。例如delete.cpp中的temp数组仅当getname()函数活动时存在。当程序控制权回到main()时，temp使用的内存自动释放。

实际上，自动变量是一个局部变量，其作用域为包含它的代码块。自动变量通常存储在**栈**中，这意味着执行代码块时，其中的变量将依次加入栈中，而在离开代码时，将按照相反的顺序释放这些变量。这被称为后进先出（LIFO）。

**2、静态存储**

静态存储是整个程序执行期间都存在的存储方式。使变量成为静态存储的方式有两种:

- 在函数外面定义它
- 在声明使用关键字static

	static double fee = 44.3;

自动存储和静态存储的关键在于：这些方法严格限制了变量的寿命。变量可能存在于程序的整个生命周期（静态变量），也可能只是特定函数被执行时存在（自动变量）。

**3、动态存储**

new和delete运算符提供了一种比自动变量和静态变量更灵活的方法。它们管理了一个内存池，这在C++被称为自由存储空间或**堆**。该内存池同用于静态变量和自动变量的内存是分开的。

数据的生命周期不完全受程序或函数的生存控制。与常规的变量相比，使用new和delete让程序员对程序如何使用内存有更大的控制权。在栈中，自动添加和删除机制使得占用的内存是连续，但new和delete的相互影响可能导致占用的自由存储区不连续，这使得跟踪内存的位置更困难。


**内存泄漏**

如果使用new运算符在自由存储空间（堆）上创建变量后，没有调用delete，则即使包好指针的内存由于作用域和对象生命周期的原因而被释放，在自由存储空间上动态分配的变量或结构也将继续存在。实际上，将会无法访问自由存储空间的结构，因为指向这些内存的指针无效。这将导致内存泄漏。泄漏的内存，将在程序整个生命周期内不可使用，这些内存被分配出去，无法回收。极端情况，内存泄漏可能导致程序崩溃。

## 4.9 类型组合

	struct antarctica_year_end
	{
	        int year;
	};

可以创建这种类型的变量：

	antarctica_year_end s01, s02, s03;

然后，使用成员运算符来访问其成员：

	s01.year = 1998;

可以创建指向这种结构的指针：

	antarctica_year_end *pa = &s02;

将指针设置为有效地址后，就可以使用间接成员运算符来访问成员：

	pa->year = 1999;

可创建结构数组：

	antarctica_year_end trio[3];

然后，使用成员运算符来访问元素的成员：

	trio[0].year = 2003;

由于数组名是一个指针，因此可以使用间接成员运算符：

	(trio+1)->year = 2004;

可创建指针数组：

	const antarctica_year_end *arp[3] = {&s01,&s02,&s03};

arp是一个指针数组，arp[1]就是一个指针，可将间接运算符应用于它：

	cout << arp[1]->year << endl;

可创建指向上述数组的指针：

	const antarctica_year_end **ppa = arp;

其中arp是一个数组名，因此它是第一个元素的地址。但其第一元素为指针，因此ppa是一个指针，指向const antarctica_year_end的指针。这样容易出错，比如忘记const，搞错顺序或结构类型，C++11提供了auto，编译器知道arp的类型能过正确地推断出ppb的类型：

	auto ppb = arp;

ppa是一个指向结构指针的指针，因此*ppa是一个结构指针，可间接成员运算符应用于它：

	cout << (*ppa)->year << endl;
	cout << (*(ppa+1))->year << endl;

mixtype.cpp

	#include <iostream>
	struct antarctica_year_end
	{
	        int year;
	};
	int main()
	{
	        using namespace std;
	        antarctica_year_end s01, s02, s03;
	        s01.year = 1998;
	        antarctica_year_end *pa = &s02;
	        pa->year = 1999;
	        antarctica_year_end trio[3];
	        trio[0].year = 2003;
	        cout << trio->year << endl;
	        const antarctica_year_end *arp[3] = {&s01,&s02,&s03};
	        cout << arp[1]->year << endl;
	        const antarctica_year_end **ppa = arp;
	        cout << (*ppa)->year << endl;
	        cout << (*(ppa+1))->year << endl;
	        return 0;
	}


结果：
	
	2003
	1999
	1998
	1999

## 4.10 数组的替代品 

### 4.10.1 模板类vector

模板类vector类类似于string类，也是一种动态数组。可以运行阶段设置vector对象的长度，可在末尾附加新数据，还可以在中间插入新数据。实际上，vector使用new和delete来管理内存，但这种工作是自动完成的。

首先，使用vector类，必须包含头文件vector；其次，vector包含在命名空间std中，因此可以使用using编译指令、using声明或std::vector；然后，模板使用不同的语法来指出它存储的数据类型；最后，vector类使用不同的语法来指定元素数。

	#include <vector>
	using namespace std;
	vector<int> vi; //创建一个int类型空数组
	int n;
	cin >> n;
	vector<double> vd(n); //创建一个double类型n个元素的数组

其中vi是一个vector<int>对象，vd是一个vector<double>对象。由于vector对象在插入或添加值时自动调整长度，因此可以在将vi的长度初始化为0。但要调整长度，需要使用vector包中的各种方法。

一般而言，创建一个名为vt的vector对象，它可存储n_elem个类型typeName的元素：

	vector<typeName> vt(n_elem);

###  4.10.2 模板类array(C++11)

vector类的功能比数组强大，但付出的代价是效率低。如果需要的是固定长度的数组，使用数组是更佳的选择，但代价是不那么方便。鉴于此，C++新增了模板类array，它也位于命名空间std中。与数组一样，array对象的长度也是固定，也使用栈（静态内存分配），而不是自由存储区，因此效率与数组相同，但更方便，更安全。需要包含头文件array。创建arry对象的语法：

	#include <array>
	using namespace std;
	array <int,5> ai;
	array<double,4> ad = {1.2, 1.3, 2.2, 2.3};

创建一个名为arr的array对象，它包含n_elem个类型为typeName的元素：

	array <typeName, n_elem> arr; 

### 4.10.3 数组、vector对象、array对象比较

choices.cpp

	#include <iostream>
	#include <vector>
	#include <array>
	int main()
	{
	        using namespace std;
	        double a1[4] = {1.2, 1.4, 2.2, 2.3};
	        vector<double> a2(4);
	        a2[0] = 1.0/3.0;
	        a2[1] = 1.0/5.0;
	        a2[2] = 1.0/7.0;
	        a2[3] = 1.0/9.0;
	        array<double, 4>  a3 = {3.14, 2.72, 1.62, 1.41};
	        array<double, 4>  a4;
	        a4 = a3;
	        cout << "a1[2]:" << a1[2] << " at " << &a1[2] << endl;
	        cout << "a2[2]:" << a2[2] << " at " << &a2[2] << endl;
	        cout << "a3[2]:" << a3[2] << " at " << &a3[2] << endl;
	        cout << "a4[2]:" << a4[2] << " at " << &a4[2] << endl;
	
	        a1[-2] = 20.2;
	
	        cout << "a1[-2]:" << a1[-2] << " at " << &a1[-2] << endl;
	        cout << "a3[2]:" << a3[2] << " at " << &a3[2] << endl;
	        cout << "a4[2]:" << a4[2] << " at " << &a4[2] << endl;
	        return 0;
	}


Linux下使用C++11版本运行:g++ -std=c++11 choices.cpp

结果：

	a1[2]:2.2 at 0x7ffdefd049f0
	a2[2]:0.142857 at 0x16f2020
	a3[2]:1.62 at 0x7ffdefd04a10
	a4[2]:1.62 at 0x7ffdefd04a30
	a1[-2]:20.2 at 0x7ffdefd049d0
	a3[2]:1.62 at 0x7ffdefd04a10
	a4[2]:1.62 at 0x7ffdefd04a30

**程序说明：**

首先，注意到无论数组、vector对象还是array对象，都可使用标准数组表示法来访问各个元素。从地址可知，array对象和数组存储在相同的内存区域（即栈）中，而vector对象，存储在另一个区域（自由存储区域或堆）中。

a1[-2] = 20.2;

索引-2，找出a1指向的地方，向前移两个double元素，并将20.2存储到目的地。即将信息存储到数组的外面，与C语言一样，C++也不检查这种越界错误。vector和array对象仍可以编写这种不安全的代码。

对于vector和array对象还可使用at()成员函数:

	a2.at(1) = 2.3;

使用at()时，将在运行期间捕获非法索引，而程序默认将终端。这种额外检查的代价是运行的时间更长，这就是C++允许使用任何一种表示法的原因。另外，这些类还能够降低意外超界错误的概率，例如，它们包含成员函数begin()和end()，能够确定边界，以免无意超界。

## 4.11 总结

数组、结构和指针是C++的3种复合类型。

字符串

共用体

枚举

指针

new和delete

vector和array对象
