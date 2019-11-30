---

title: QT教程
date: 2019-07-08 13:44:14
tags: C++
mathjax: true

---



## QT概述

### 什么是QT

QT是一个款平台的C++图形用户界面应用程序框架。它为应用程序开发者提供建立艺术级图形界面所需的所有功能。它是完全面向对象的，很容易扩展，并且允许真正的组件编程。

 <!--more-->

1991年，QT最早由奇趣科技开发。

Qt实现了对Windows、Linux、Mac os、IOS、Android、WP等个平台的开发。

QT分为商业版和开源版。

Qt的优点：

- 跨平台，几乎支持所有的平台
- 接口简单，容易上手
- 一定程度上简化了内存回收机制
- 开发效率高
- 有很好的社区氛围，市场份额在缓慢上升
- 可以进行嵌入式开发

QT成功案例：

- WPS Office办公软件
- Linux桌面环境KDE
- Skype网络电话
- 谷歌地图
- VLC多媒体播放器
- VirtualBox虚拟机软件

## 第一个项目

- 点击新建
- 项目名称：不能有中文，不能有空格
- 项目路径：不能有中文
- 第一个窗口类名称(MyWiget)
	- 默认的三个基类(我们选择QWiget)
		- QWiget:干净的，什么都没有
		- QMainWindow：有一个窗口
		- QDialog：有一个对话框
- 默认创建的文件
	- .pro 工程文件
	- main.cpp 入口函数
	- MyWidget.h
	- MyWidget.cpp


### 默认生成的代码

#### .pro工程文件：

QT包含的模块，现在包含两个模块core,gui

	QT       += core gui
	//
![](https://i.imgur.com/4MmMccS.png)

core就是核心模块，GUI就是一些图形模块，Widgets是控件按钮模块，NetWork网络模块，Mulitmedia多媒体模块，SQL数据库模块，WebKit浏览器模块。

4版本以上加入widgets模块，低版本不加入：

	greaterThan(QT_MAJOR_VERSION, 4): QT += widgets
	
目标，生成.exe程序的名称：

	TARGET = 01_QTFirst

模板：application应用程序模板

	TEMPLATE = app

其中模板：
- app -建立一个应用程序的makefile。这是默认值，所以如果模板没有被指定，这个将被使用。
- lib - 建立一个库的makefile。
- vcapp - 建立一个应用程序的VisualStudio项目文件。
- vclib - 建立一个库的VisualStudio项目文件。
- subdirs -这是一个特殊的模板，它可以创建一个能够进入特定目录并且为一个项目文件生成makefile并且为它调用make的makefile。
 
DEFINES应用程序所需的额外的预处理程序定义的列表；在.h文件中就可以使用：#ifdefine xx_xx_xxx

	DEFINES += QT_DEPRECATED_WARNINGS
	
源文件：
	
	SOURCES += main.cpp\
	        mywidget.cpp

头文件：

	HEADERS  += mywidget.h


#### main入口函数

	#include "mywidget.h"  //包含mywidget.h头文件
	#include <QApplication> //应用程序类
	
	
	//mian函数程序入口
	//argc命令行变量的数量，argv命令行变量数组
	int main(int argc, char *argv[])
	{
	    QApplication a(argc, argv);//应用程序对象，QT中有且仅有一个应用程序对象
	    MyWidget w; //创建一个自定义的窗口对象
	    //w.show();//窗口对象默认不会弹出，需要调用show函数进行显示
	
	    return a.exec(); //a.exec()进入消息循环机制，让程序阻塞在当前
	//    exec()的伪代码：
	//    while(True){
	//        if (点击退出)
	//           break;
	//    }
	}

#### MyWidget.h

	#ifndef MYWIDGET_H  //防止头文件重复包含
	#define MYWIDGET_H
	
	#include <QWidget> //QWidget是当前的基类
	
	class MyWidget : public QWidget
	{
	    Q_OBJECT //Q_OBJECT宏，提供QT中的信号和槽的机制
	
	public:
	    MyWidget(QWidget *parent = 0);
	    ~MyWidget();
	};
	
	#endif // MYWIDGET_H

### 命名规范

- 类名： 首字母大写，单词和单词之间，首字母大写
- 函数名、变量: 首先字母小写，单词和单词之间，首字母大写


### 常用快捷键

- 帮助文档： F1
- 注释： ctrl + /(加注释和取消注释)
- 字体缩放： ctrl+鼠标滚轮
- 运行ctrl+r
- 整行代码移动：ctrl+shift+↑/↓
- 查找：ctrl + f
- 自动对齐：ctrl + i
- 同名之间.h和.cpp切换： ctrl+F4


### 基本控件接口

按钮 `QPushButton * btn = new QPushButton;`

设置父窗口： `btn->setParent(this);`

显示文本： `btn->setText("aa");`

移动 `btn->move(x,y);`

重新制定窗口大小 `this->resize(height, width);`

设置固定大小 `setFixedSize(height, width);`

设置窗口标题 `setWindowTitle("标题名")`

注意我们new一个指针，但是没有使用delete，因为我们继承的父类，系统会自动回收。 


### 对象模型（对象树）

![](https://i.imgur.com/S9lVX4p.png)

在创建对象时候，如果父类是QObject或者QOBject派生的类，这个对象可以不用去管理释放，会放入到一个对象树上，系统会自动管理释放。

构造的顺序和析构的顺序是相反的。


### QT坐标系

左上角是(0,0)点，x向右为正方向，y向下为正方向。


## 信号槽

信号槽的四个参数：

- 信号发送者
- 发送的信号
- 信号接收者
- 处理信号的槽函数


信号槽的优点：松散耦合，将两个不相关的内容连接到一起

利用connect进行链接，参数1：信号发送者(指针)，参数2：发送的信号(信号的地址)，参数3：信号的接收者(指针)，参数4：处理信号的槽函数(函数地址)

    connect(mybtn, &MyPushButton::clicked, this, &MyWidget::close);

信号槽示意图：

![](https://i.imgur.com/IKYjYd1.png)

### 自定义信号与槽

需求：
- Teacher类， student类
- ClassIsOver下课，老师发送自定义信号，饿了
- 学生响应信号，并且请老师吃饭


#### 自定义信号

自定义写好在signals下

自定义信号写法

- 1、返回void
- 2、信号只需要声明，不需要实现
- 3、自定义信号可以被重载
	
范例：

	signals:	   
	    void hungry();

#### 自定义槽函数

自定义槽函数，写到public slots下，或者全局函数，或者public下，或者是lambad表达式

自定义槽函数写法：

- 1、返回void
- 2、需要声明，也需要实现
- 3、可以被重载

范例：

	public slots:
	    void treat();

	void Student::treat()
	{
	    qDebug() << "请老师吃饭" << endl;
	}

QString转为`char *`: 首先使用.toUtf8()转换成QByteArray类型，然后再调用.data()转成`char *`。

	QString food = "鱼香肉丝";
	char * c = food.toUtf8().data() 

#### 触发自定义信号

利用emit来触发信号，具体范例：

	void Widget::classIsOver(){
	    //自定义信号触发 emit
	    emit zt->hungry();
	}


整体流程：

- 声明两个类，Student类和Teacher类
- Teacher类中定义信号，Student类定义槽函数
- 定义classIsOver函数来触发信号
- 声明Teacher和Student，并使用connect连接，以及调用classIsOver函数触发行行好

第四步的代码如下：

	class Widget : public QWidget
	{
	    Q_OBJECT
	
	public:
	    Widget(QWidget *parent = 0);
	    ~Widget();
	
	    Teacher * zt;
	    Student * st;
	
	    //下课函数
	    void classIsOver();
	
	};

	Widget::Widget(QWidget *parent)
	    : QWidget(parent)
	{
	    //创建老师和学生的对象
	    zt = new Teacher(this); //指定this就等于设置了父窗口，不要进行释放，会自动管理释放
	    st = new Student(this);
	
	    //连接信号与槽
	    connect(zt, &Teacher::hungry, st, &Student::treat);
	
	    classIsOver();
	}


当自定义的信号和槽被重载之后，需要利用函数指针，明确指出函数地址。

	public slots:
	    void treat();
	    void treat(QString food);

	void Student::treat()
	{
	    qDebug() << "请老师吃饭" << endl;
	}
	
	void Student::treat(QString food)
	{
	    //将QString转化为char *
	    //先调用.toUtf8()转化为QByteArray类型，再调用.data()转化为char *
	    qDebug() << "请老师吃饭，老师想吃： " << food.toUtf8().data() << endl;
	}

	signals:
	    void hungry(QString food);
	    void hungry();

	void Widget::classIsOver(){
	    //自定义信号触发 emit
	    //emit zt->hungry();
	    emit zt->hungry("鱼香肉丝");
	}

    void(Teacher:: *teacherSignals)(QString) = &Teacher::hungry; //函数指针明确指向有参数的版本
    void(Student:: *studentSlot)(QString) = &Student::treat;
    connect(zt, teacherSignals, st, studentSlot);
    classIsOver();

    //信号和槽可以断开连接
    disconnect(zt, teacherSignals, st, studentSlot);


扩展：

- 信号可以连接信号
- 一个信号可以连接多个槽函数
- 多个信号可以连接同一个槽函数
- 信号和槽的参数必须一一对应
- 槽函数参数的个数可以少于信号函数参数的个数，但是类型也要一一对应
- 信号和槽是可以断开连接的

#### Qt4版本信号与槽的写法

	connect(zt, SIGNAL(hungry(QString)), st, SLOT(treat(QString)));

缺陷，参数的类型不做检测。原因在于SIGNAL和SLOT括号下的内容转为字符串SIGNAL("hungry(QString)")。

优点：参数类型比较值观。


#### Lambda表达式


C++11中的Lambda表达式用于定义并创建匿名的函数对象，以简化编程工作。首先看一下Lambda表达式的基本过程

	[capture](parameters) mutable->return-type{
		statement
	}

	[函数对象参数](操作符重载函数参数)mutable->返回值{函数体}

（1）函数对象参数：

[]，标识一个Lambad的开始，这部分必须存在，不能省略。函数对象参数是传递给编译器自动生成的函数对象类的构造函数的。函数对象参数智能使用那些到定义Lambda为止时，Lambda所在作用范围内可见的局部变量（包括Lambda所在类的this）。函数对象参数有以下形式：

- 空。没有使用任何函数对象参数
- =。函数体可以使用Lambda所在作用范围内所有可见的局部变量（包括Lambda所在类的this）,并且是__值传递方式__（相等于编译器自动为我们按值传递了所有局部变量）。 `[=](){btn->setText("aaa");}`（推荐使用）
- &。函数体可以使用Lambda所在作用范围内所有可见的局部变量（包括Lambda所在类的this）,并且是__引用传递方法__（相等于编译器自动为我们按值传递了所有局部变量）。（不建议使用，有时候出现问题！）
- this。函数体内可以使用Lambda所在类中的成员变量。
- a。将a按值进行传递，其他变量不可见。按值进行传递时，函数体内不能修改传递进行的a的拷贝，因为默认情况下函数是const。要修改传递进来的a的拷贝，可以添加mutable修饰符。
- &a。将a按引用传递，其他不可见。
- a,&b。将a按值传递，b按引用传递。
- =，&a,&b。除a和b按引用进行传递外，其他参数都按值进行传递。
- &,a,b。除a和b按值进行传递外，其他参数都按引用进行传递。


（2）操作符重载函数参数

标识重载的()操作符的参数，没有参数时，这部分可以省略。参数可以通过按值（如(a,b)）和按引用（如(&a,&b)）两种方式进行传递。

（3） 可修饰标示符

mutable声明，这部分可以省略。按值传递函数对象参数时，加上mutable修饰后，可以修改按值传递进来的拷贝（注意是能修改拷贝，而不是值本身）。

（4） 函数返回值类型

->返回值类型，标识函数返回值的类型，当返回值为void，或者函数体中只有一处return的地方（此时编译器可以自动推断出返回值类型）时，这部分可以省略。

	int = n;
	n = []()->int{return 100;}();  //最后的()表示函数调用
	qDebug() <<  n << endl;

(5)函数体；

{}，标识函数的实现，这部分不能省略，但函数体可以为空。

范例：

	connect(mybtn, &QPushButton::clicked, this, [=](){
	        this->close();
	        //st->treat("鱼香肉丝");
	    });

如果现在低版本（QT4版本之前的）中使用Lambda表达式。必须在工程文件（.pro）中添加如下代码：

	CONFIG += c++11

因为这是Lambda是c++11的特性。

### 信号与槽总复习

![](https://i.imgur.com/AgbmKH0.png)


## QMainWindow

QMainWindow是QWidget的子类，提供了更多的内容。

QMainWindow是一个为用户提供主窗口程序的类，包含一个菜单栏（menu bar）、多个工具栏(tool bars)、多个锚接部件(dock widgets)、一个状态栏(status bar)及一个中心部件(central widget)，是许多应用程序的基础，如文本编辑器，图片编辑器等。

### 菜单栏

菜单栏只能创建一个。创建的方法：

	QMenuBar * bar =  MenuBar()

将菜单栏设置到窗口中：

	setMenuBar(bar);

利用菜单栏添加菜单：

	QMenu * fileMenu = bar->addMenu("文件");

菜单中添加菜单项：

	QAction * newAction = fileMenu->addAction("新建");

添加分割线：

	fileMenu->addSeparator();

### 工具栏

工具栏可以有多个。

创建工具栏：

	QToolBar * toolbar = new QToolBar(this);

添加到窗口中，并设置默认停靠位置 ：

	addToolBar(QT::LeftToolBarArea, toolbar);


设置停靠：

	toolbar->setAllowedAreas(Qt::LeftToolBarArea | Qt::RightToolBarArea);

设置浮动：

	toolbar->setFloatable(false);

设置移动：

	toolbar->setMovable(false);

添加小控件：

	//添加按钮
    QPushButton * btn = new QPushButton("按钮",this);
    toolbar->addWidget(btn);
    //工具栏中添加菜单项
    toolbar->addAction(newAction);
    //添加分割线
    toolbar->addSeparator();
    toolbar->addAction(openAction);

### 状态栏

状态栏只能有一个，可以放左侧或者右侧信息提示：

创建状态栏：

    QStatusBar * status =  statusBar();

将状态栏放到窗口中

    setStatusBar(status);

创建标签
    QLabel  * label = new QLabel("左侧信息",this);

将标签放入状态栏

    status->addWidget(label);

创建标签并放到状态栏右侧：

    QLabel * label2 = new QLabel("右侧信息", this);
    status->addPermanentWidget(label2);


### 铆接部件

铆接部件可以有多个。

创建铆接部件：

 	QDockWidget * dock = new QDockWidget("铆接部件",this);
 
添加到窗口中：

	addDockWidget(Qt::BottomDockWidgetArea,dock);

设置停靠范围

    dock->setAllowedAreas(Qt::TopDockWidgetArea);


### 核心部件

核心部件只能有一个。

设置核心部件：

	QTextEdit * edit = new QTextEdit(this);//创建一个文本框，并设置为核心部件
    setCentralWidget(edit);


### 资源文件

第一步：将资源导入项目根目录下中

第二步： 添加文件 -> QT -> QT Resource File -> 名称(res) 

第三步： 生成一个res.qrc文件， 通过右键-> open in edit 用编辑的方法打开该文件

第四步： 添加前缀名或者`/`

第五步： 添加文件

第六步： 使用资源文件： 使用方式： ";+前缀名+文件名"

    ui->actionnew->setIcon( QIcon(":/new/prefix1/Image/new.png"));
    ui->actionopen->setIcon(QIcon(":/new/prefix1/Image/open.png"));


### 对话框

分类：

- 模态对话框：打开该对话框，无法对其他窗口进行操作
- 非模态对话框：打开该对话框，还可以对其他窗口进行操作

模态对话框使用：

     QDialog dlg(this);
     dlg.resize(120, 30);
     dlg.exec(); //阻塞

非模态对话框使用：

 	QDialog * dlg2 = new QDialog(this);
    dlg2->resize(120,30);
    dlg2->show();

    dlg2->setAttribute(Qt::WA_DeleteOnClose);//关闭非模态对话框时，自动释放对应的内存

将对话框跟菜单栏连接:

    connect(ui->actionnew, &QAction::triggered, this, [=](){

        //创建模态对话框
       QDialog dlg(this);
       dlg.resize(120, 30);
       dlg.exec(); //阻塞

       qDebug() << "弹出对话框";

        //创建非模态对话框
        QDialog * dlg2 = new QDialog(this);
        dlg2->resize(120,30);
        dlg2->show();

        dlg2->setAttribute(Qt::WA_DeleteOnClose);//关闭非模态对话框时，自动释放对应的内存
     });


为什么创建模态对话框时，可以使用匿名的方式创建，而创建非模态对话框不行？

这是因为Lambda表达式一个匿名函数，执行完成后会释放内存，而模态对话框有exec()进行阻塞，所以不会退出。而非模态对话框通过show()来显示，如果通过a(value)的方式创建，会自动释放，所以必须通过new的方式将该对象放到堆中。

创建对象的三种方式：

    A a(1);  //栈中分配 
    A b = A(1);  //栈中分配 
    A* c = new A(1);  //堆中分配 

第一种和第二种没什么区别，一个隐式调用，一个显式调用，两者都是在进程虚拟地址空间中的栈中分配内存，而第三种使用了new，在堆中分配了内存，而栈中内存的分配和释放是由系统管理，而堆中内存的分配和释放必须由程序员手动释放。采用第三种方式时，必须注意一下几点问题：

- new创建类对象需要指针接收，一处初始化，多处使用
- new创建类对象使用完需delete销毁
- new创建对象直接使用堆空间，而局部不用new定义类对象则使用栈空间
- new对象指针用途广泛，比如作为函数返回值、函数参数等
- 频繁调用场合并不适合new，就像new申请和释放内存一样
- 栈的大小远小于堆的大小
- 栈是机器系统提供的数据结构，计算机会在底层对栈提供支持：分配专门的寄存器存放栈的地址，压栈出栈都有专门的指令执行，这就决定了栈的效率 比较高。堆则是C/C++函数库提供的，它的机制是很复杂的，例如为了分配一块内存，库函数会按照一定的算法（具体的算法可以参考数据结构/操作系统）在 堆内存中搜索可用的足够大小的空间，如果没有足够大小的空间（可能是由于内存碎片太多），就有可能调用系统功能去增加程序数据段的内存空间，这样就有机会 分 到足够大小的内存，然后进行返回。显然，堆的效率比栈要低得多


#### 标准对话框

所谓标准对话框，是 Qt 内置的一系列对话框，用于简化开发。事实上，有很多对话框都是通用的，比如打开文件、设置颜色、打印设置等。这些对话框在所有程序中几乎相同，因此没有必要在每一个程序中都自己实现这么一个对话框。Qt 的内置对话框大致分为以下几类：

- QColorDialog：		选择颜色；
- 
- ialog：			选择文件或者目录；
- QFontDialog：			选择字体；
- QInputDialog：		允许用户输入一个值，并将其值返回；
- QMessageBox：			模态对话框，用于显示信息、询问问题等；
- QPageSetupDialog：	为打印机提供纸张相关的选项；
- QPrintDialog：		打印机配置；
- PrintPreviewDialog：打印预览；
- QProgressDialog：		显示操作过程。

##### QMessageBox

这里主要介绍QMessageBox对话框。QMessageBox对话框主要用于显示一些标准信息，比如提示信息，错误信息，警告信息等等。

QMessageBox弹出的所有对话框都是模态对话框，弹出不同的对话框是通过其不同的静态成员函数实现的。

弹出QMessageBox对话框(模态对话框)

- 错误提示对话框

        QMessageBox::critical(this, "错误","critiacl");

- 信息提示对话框

        QMessageBox::information(this, "信息","information");

- 提问对话框

参数1：父窗口，参数2：窗口标题，参数3：提示信息，参数4：按钮类型，参数5：默认选中按键。

但设置不同的按钮时，还可以设置不同按钮的响应情况。

        if (QMessageBox::Save == QMessageBox::question(this,"提问","question",QMessageBox::Save | QMessageBox::Cancel, QMessageBox::Cancel))
        {
            qDebug() << "点击的是保存";
        }
        else
        {
            qDebug() << "点击的是取消";
        }

- 警告对话框

        QMessageBox::warning(this,"警告","Warning");


##### QFileDialog

文件对话框 

参数1：父窗口，参数2：标题，参数3：默认打开路径，参数4：过滤后缀名

        QString fileName = QFileDialog::getOpenFileName(this, "打开文件","C:/Users/zxp/Desktop","(*.doc)");
        qDebug()<< "打开的文件是： " << fileName;


##### QColorDialog 

颜色对话框

        QColor color = QColorDialog::getColor(QColor(255,0,0));
        qDebug() << color.red() << color.green() << color.blue();

##### 字体对话框

        bool ok;
        QFont font = QFontDialog::getFont(&ok, QFont("华文彩云", 36));
        qDebug() << "字体" << font.family().toUtf8().data() << "字号" << font.pointSize() << "是否倾斜" << font.italic()
                 << "是否加粗" << font.bold();


### 界面布局

利用widget做控件容器，在容器中可以进行水平布局、垂直布局和栅格布局。

widget：如果想修改垂直大小，sizePolicy下的垂直策略改为fixed。

可以修改widget和控件之间的间隙，默认为9xp。

利用弹簧将界面撑起。

如果想固定窗口大小，可以设置最大窗口的尺寸和最小窗口的尺寸和当前窗口大小一样。

### 常用控件

#### 按钮组Buttons

- QPushButton 添加图片
- QToolButton 默认只显示图片，可以设置图片大小，如果想显示文字，需要设置toolButtonStyle的属性；设置凸起效果，设置autoRaise。
- QRaidoButton 单选按钮
	- 需要设置成一个GroupBox
	- 设置默认选择`ui->radio_man->setChecked(true);`
- QCheckBox 复选按钮
	- 0表示未选中，1表示半选中，2表示选中（默认情况下，只有选中和未选中两种状态，需要设置tristate属性才会出现半选中状态）
	- 复选按钮状态获取：`connect(ui->checkBox, &QCheckBox::stateChanged, this, [=](int state){ qDebug() << "state = " << state << endl; });`
	-  同样需要设置成一个GroupBox

####  QListWiget

每个项都称为QListWidgetItem，创建一个项的代码如下：

    QListWidgetItem * item = new QListWidgetItem("锄禾日当午");

将该项放入到QListWiget:

    ui->listWidget->addItem(item);

设置对齐：

    item->setTextAlignment(Qt::AlignHCenter);

一次性加入所有数据，通过一个QStringList实现：

创建一个QStringList对象，并初始化：

    //QStringList == QList<QString>
    QStringList list;
    list << "锄禾日当午" << "汗滴禾下土" << "谁知盘中餐" << "粒粒皆辛苦";

将QStringList放入listWidget:

    ui->listWidget->addItems(list); //无法设置对齐

此种方法无法设置对齐。

#### QTreeWidget

treeWidget 树控件的使用

设置头
    ui->treeWidget->setHeaderLabels(QStringList() << "英雄" << "英雄介绍");
   
TreeWiget中每个项目都称为QTreeWidgetItem:

    QTreeWidgetItem * Litem = new QTreeWidgetItem(QStringList() << "力量");
    QTreeWidgetItem * Mitem = new QTreeWidgetItem(QStringList() << "敏捷");
    QTreeWidgetItem * Zitem = new QTreeWidgetItem(QStringList() << "智力");

添加根节点

    ui->treeWidget->addTopLevelItem(Litem);
    ui->treeWidget->addTopLevelItem(Mitem);
    ui->treeWidget->addTopLevelItem(Zitem);

在每个根节点之下都可以添加子节点，添加子节点

    QStringList heroL1, heroL2;
    heroL1 << "刚被猪" << "前排坦克，能在吸收伤害的同时造成可观的范围输出";
    heroL2 << "船长" << "前排坦克，能肉能输出能控场的全能英雄";

    QTreeWidgetItem * L1 = new QTreeWidgetItem(heroL1);
    QTreeWidgetItem * L2 = new QTreeWidgetItem(heroL2);
    Litem->addChild(L1);
    Litem->addChild(L2);

#### QTableWidget

设置列数：

    ui->tableWidget->setColumnCount(3);

设置水平表头

    ui->tableWidget->setHorizontalHeaderLabels(QStringList() << "姓名" << "性别" << "年龄");

设置行数

    ui->tableWidget->setRowCount(5);

设置具体内容：

    //ui->tableWidget->setItem(0,0,new QTableWidgetItem("亚瑟"));

    QList<QString> nameList;
    nameList << "亚瑟" << "妲己" << "安琪拉" << "赵云" << "关羽";
    QStringList sexList;
    sexList << "男" << "女" << "女" << "男" << "男";
    for (int i = 0; i < 5; i++)
    {
        int col = 0;
        ui->tableWidget->setItem(i,col++, new QTableWidgetItem(nameList[i]));
        ui->tableWidget->setItem(i,col++, new QTableWidgetItem(sexList.at(i)));
        ui->tableWidget->setItem(i,col++, new QTableWidgetItem(QString::number(18+i)));
    }


#### 其他常用控件介绍

##### 栈控件 stackedWidget

	//设置默认索引位置
    ui->stackedWidget->setCurrentIndex(0);
    //点击Scroll Area做切换
    connect(ui->btnScroll, &QPushButton::clicked, [=](){
        ui->stackedWidget->setCurrentIndex(0);
    } );
    connect(ui->btnToolBar, &QPushButton::clicked, [=](){
        ui->stackedWidget->setCurrentIndex(1);
    } );
    connect(ui->btnTabWidget, &QPushButton::clicked, [=](){
        ui->stackedWidget->setCurrentIndex(2);
    } );

##### 下拉框 comboBox


    //下拉框
    ui->comboBox->addItem("奔驰");
    ui->comboBox->addItem("宝马");
    ui->comboBox->addItem("大众");

    //点击按钮定位到大众
    connect(ui->btn_select, &QPushButton::clicked, [=](){
        //ui->comboBox->setCurrentIndex(2);
        ui->comboBox->setCurrentText("大众");
    });

#####  QLabel显示图片

    //利用label显示图片
    ui->label_image->setPixmap(QPixmap(":/Image/open.gif"));

##### QLabel显示动图

利用label显示动图

    QMovie * movie = new QMovie(":/Image/timg.gif");
    ui->label_gif->setMovie(movie);
    
默认情况下，不会播放，需要利用start播放movie图片
    movie->start();



### 自定义按钮封装

第一步：添加一个类，QT下面的的设计师界面类 包含三个文件：.h,.cpp,.ui

第二步： 提升操作： 将基类的名称填写正确，然后点击添加，点击提升

第三步： 使用自定义控件，封装信号和槽，QSpinBox移动QSlider响应，QSlider移动QSpinBox响应

  	//QSpinBox移动 QSlider跟着移动
    void(QSpinBox:: * spinSignal)(int) = &QSpinBox::valueChanged; //该函数指针有两个版本
    connect(ui->spinBox, spinSignal, ui->horizontalSlider, &QSlider::setValue);
    connect(ui->horizontalSlider, &QSlider::valueChanged, ui->spinBox, &QSpinBox::setValue);

第四步：对外接口

	//对外接口
	void SmallWidget::setData(int value)
	{
	    ui->spinBox->setValue(value);
	}
	
	int SmallWidget::getData()
	{
	    return ui->spinBox->value();
	}

	//接口使用
    //获取按钮
    connect(ui->pushButton, &QPushButton::clicked, [=](){
        qDebug()<<ui->widget->getData() << endl;
    });

    //设置按钮
    connect(ui->pushButton_2, &QPushButton::clicked, [=](){
       ui->widget->setData(50);
    });


### QT中的事件处理

QLabel中得鼠标事件：

- enterMouse鼠标进入
- leaveMouse鼠标离开
- mousePressEvent(QMouseEvent * ev)鼠标按下，可以通过ev获取鼠标的坐标，还可以判断是左键还是右键（ev->button()）。
- mouseReleaseEvent鼠标释放
- mouseMoveEvent鼠标移动，移动状态下通过ev->buttons()来判断是在移动。

QString格式化字符串：

        QString str = QString("鼠标按下 x = %1  y = %2").arg(ev->x()).arg(ev->y());
        qDebug() <<str << endl;

设置鼠标追踪状态，默认false：
    
	this->setMouseTracking(true);



### 定时器

定时器名称为：void timerEvent(QTimerEvent *e)

通过e可以获取定时器的id，e->timerId()。

启动定时器:  startTimer(毫秒); 返回一个定时器的id。

	Widget::Widget(QWidget *parent) :
	    QWidget(parent),
	    ui(new Ui::Widget)
	{
	    ui->setupUi(this);
	    //启动定时器
	    id1 = startTimer(1000); //参数为间隔，单位毫秒
	    id2 = startTimer(2000); //2s中启动一个定时器
	}
	
	
	void Widget::timerEvent(QTimerEvent *e)
	{
	    if (e->timerId() == id1)
	    {
	        static int num = 1;
	        ui->label_2->setText(QString::number(num++));
	    }
	    if (e->timerId() == id2)
	    {
	        static int num2 = 1;
	        ui->label_3->setText(QString::number(num2++));
	    }
	}


定时器的第二种使用方式：

QTimer类，首先实例化一个QTimer对象

	QTimer * timer = new QTimer(this);

启动定时器： start

发送信号： timeout

    //利用QTimer定时器类，实现定时功能
    QTimer * timer = new QTimer(this);
    //启动定时器
    timer->start(500);
    connect(timer, &QTimer::timeout, [=](){
        static int num = 1;
        ui->label_4->setText(QString::number(num++));
    });


按钮实现定时器暂停：

    connect(ui->btn, &QPushButton::clicked, [=](){
        timer->stop();
    });



### 事件分发器



事件分发器函数：bool event(QEvent *e);该函数返回bool类型，如果用户想在事件分发前捕获事件，不向下分发，可以return ture代表自定义处理。

e->type()所有Qt中的事情

事件分发器中可以做事件拦截操作，如果用户进行了拦截，在对应的事件

	
	bool MyLabel::event(QEvent *e)
	{
	    if (e->type() == QEvent::MouseButtonPress)
	    {
	        QMouseEvent * ev = static_cast<QMouseEvent *>(e);
	        QString str = QString("event事件中的鼠标被按下了 x = %1,  y = %2 ").arg(ev->x()).arg(ev->y());
	        qDebug() << str << endl;
	        return true; //代表如果是鼠标按下，用户自己处理
	    }
	    //其他事件，交给父类处理，默认处理
	    return QLabel::event(e);
	}


### 事件过滤器

利用事件过滤器，可以做更高级拦截。

利用事件过滤器有两个步骤：

- 给对应的控件安装事件过滤器
- 重写eventFilter事件

	//利用事件过滤器，给label做更高级事件拦截
	//步骤1：给label安装事件过滤器
	ui->label->installEventFilter(this);
	
	//步骤2:重写eventFilter事件
	//参数1：对比控件使用， 参数2：对比事件
	bool Widget::eventFilter(QObject *watched, QEvent *event)
	{
	    if (watched == ui->label)
	    {
	        if(event->type() == QEvent::MouseButtonPress)
	        {
	            QMouseEvent * ev = static_cast<QMouseEvent *>(event);
	            QString str = QString("eventFilter事件中的鼠标被按下了 x = %1 y = %2").arg(ev->x()).arg(ev->y());
	            qDebug() << str;
	            return true;
	        }
	    }
	
	    //默认处理
	    return QWidget::eventFilter(watched, event);
	}


### 绘图事件


绘图事件： paintEvent，由程序自动执行。

声明画家： 

	QPainter painter(this);

画各种图形。

可以设置画笔，画刷。比如画笔的宽度，分隔，画刷的分隔等等。

	void Widget::paintEvent(QPaintEvent *)
	{
	    //声明一个画家对象，在窗口设备中绘制图片
	    QPainter painter(this);
	
	    //指定画笔
	    QPen pen(QColor(255,0,0));
	    //设置笔宽
	    pen.setWidth(3);
	    //设置画笔分隔
	    pen.setStyle(Qt::DotLine);
	    //让画家拿起这跟笔
	    painter.setPen(pen);
	
	    //画刷
	    QBrush brush(Qt::cyan);
	    //设置画刷分隔
	    brush.setStyle(Qt::Dense6Pattern);
	    //让画家使用画刷
	    painter.setBrush(brush);
	
	
	    //画线
	    painter.drawLine(QPoint(0,0),QPoint(100,100));
	    //画圆,椭圆
	    painter.drawEllipse(QPoint(100,100),50,50);
	    //画矩形
	    painter.drawRect(QRectF(20,20,50,50));
	    //画文字
	    painter.drawText(QRectF(10,200,120,100),"好好学习，天天向上");
	
	}


### 高级绘图事件

设置抗锯齿。

  	//声明一个画家对象，在窗口设备中绘制图片
    QPainter painter(this);

    painter.drawEllipse(QPoint(100,100),50,50);
    //设置抗锯齿,效率比较低
    painter.setRenderHint(QPainter::Antialiasing);

    painter.drawEllipse(QPoint(250,100),50,50);

保存画家的状态和还原画家的状态。
 	
	painter.drawRect(QRect(20,20,50,50));
    //将画家移动
    painter.translate(QPoint(100,0));
    //保存画家的状态
    painter.save();
    painter.drawRect(QRect(20,20,50,50));
    painter.translate(QPoint(100,0));
    //还原画家状态
    painter.restore();
    painter.drawRect(QRect(20,20,50,50));

画图片，以及图片的移动。
			   
	int posX = 10;

	//点击按钮，实现图片移动
    connect(ui->pushButton, &QPushButton::clicked, [=]{
        posX +=20;
        update();
    });

    //利用画家，绘制图片
    //如果图片超出屏幕宽度，将posX设置回来
    if (posX > this->width())
        posX = 10;
    painter.drawPixmap(posX,160, QPixmap(":/images/jj.png"));


### 绘图设备

绘图设备是指继承QPainterDevice的子类。Qt一共提供了四个这样的类，分别是QPixmap、QBitmap、QImage和 QPicture。其中，

- QPixmap专门为图像在屏幕上的显示做了优化
- QBitmap是QPixmap的一个子类，它的色深限定为1（表示颜色只有黑白），可以使用 QPixmap的isQBitmap()函数来确定这个QPixmap是不是一个QBitmap。
- QImage专门为图像的像素级访问做了优化。 
- QPicture则可以记录和重现QPainter的各条命令。

其实QWidget也是一个绘图设备，继承自QPainterDevices。

QPixmap的使用：

    //QPixmap做绘图设备,QPixmap对不同的平台做了显示的优化
    QPixmap pix(300,300);
    pix.fill(Qt::white);
    QPainter painter(&pix);
    painter.setPen(QPen(Qt::red));
    painter.drawEllipse(QPoint(150,150),100,100);
    //保存
    pix.save("C:/Users/zxp/Desktop/pix.png");

QImage的使用：

    //QImage 做绘图设备,QImage可以对像素进行访问
    QImage img(300, 300, QImage::Format_RGB32);
    img.fill(Qt::white);
    QPainter painter(&img);
    painter.setPen(QPen(Qt::green));
    painter.drawEllipse(QPoint(150,150),100,100);
    //保存
    img.save("C:/Users/zxp/Desktop/img.png");


	//在paintEvent事件中修改QImage图片的像素
	void Widget::paintEvent(QPaintEvent *event)
	{
	    QImage img;
	    img.load(":/images/qq.png");
	    QPainter painter(this);
	    //修改像素点
	    for(int i = 10;  i< 30; i++)
	    {
	        for(int j = 10; j < 30; j++)
	        {
	            QRgb value = qRgb(255,0,0);
	            img.setPixel(i,j,value);
	        }
	    }
	
	    painter.drawImage(0,0,img);
	
	    //重现
	    QPicture pic;
	    pic.load("C:/Users/zxp/Desktop/pic.zxp");
	    QPainter painter(this);
	    painter.drawPicture(0,0,pic);
	}

QPicture使用：

    //QPicture记录和重现QPainter的各条各条命令
    QPicture pic;
    QPainter painter;
    painter.begin(&pic);

    painter.setPen(QPen(Qt::green));
    painter.drawEllipse(QPoint(150,150),100,100);

    painter.end();

    pic.save("C:/Users/zxp/Desktop/pic.zxp");//该文件无论什么后缀名，都无法打开成图片


	//在paintEvent事件中画pic.zxp图片
	void Widget::paintEvent(QPaintEvent *event)
	{
	
	    //重现Picture的图片
	    QPicture pic;
	    pic.load("C:/Users/zxp/Desktop/pic.zxp");
	    QPainter painter(this);
	    painter.drawPicture(0,0,pic);
	}


### QFile文件读写


设置按钮，打开文件，并读取文件路径。

 	//点击按钮选取文件
    connect(ui->pushButton, &QPushButton::clicked, [=](){
       QString filePath = QFileDialog::getOpenFileName(this, "选择文件", "C:/Users/zxp/Documents/QT_HM/13_QFile","(*.txt)");
       qDebug() << filePath;
       //将文件路径放入lineEdit中
       if (filePath.isEmpty())
       {
           QMessageBox::warning(this, "打开失败","文件路径为空");
           return;
       }
       ui->lineEdit->setText(filePath);

读取文件： open(QIODevice::ReadOnly)

读取文件的时候可以单行读取，也可以读取所有内容。

编码格式执行： QTextCodec

       //读文件
       //将文件中的内容放入到textEidt中
       QFile file(filePath); //默认支持的格式为utf-8
       //设置打开方式
       file.open(QIODevice::ReadOnly); //只读的方式打开文件
       QByteArray arr;
       //arr = file.readAll(); //读取文件所有内容
       while (!file.atEnd())
           arr +=  file.readLine(); //按行读取数据

编码格式指定（默认utf-8）：

       QTextCodec * codec = QTextCodec::codecForName("gbk"); //指定文件格式
       ui->textEdit->setText(codec->toUnicode(arr)); //读取gbk文件

       ui->textEdit->setText(arr);

       //关闭文件对象
       file.close();

写文件：

       //写文件
       file.open(QIODevice::Append); //利用追加的方式写
       file.write("oooooooooo");
       file.close();


### 文件信息

创建文件信息对象：

	QFileInfo info(filePath); //filePath为完整的文件路径

读取文件信息：

       
       qDebug() << "大小" << info.size() << "后缀名" << info.suffix() << "文件名" << info.fileName()
                << "文件路径" << info.filePath();
       QString str = info.created().toString("yyyy/MM/dd hh:mm:ss");
       qDebug() << str;

       QString str1 = info.lastModified().toString("yyyy/MM/dd hh:mm:ss");
       qDebug() << str1;


QDateTime格式化输出：

       QString str1 = info.lastModified().toString("yyyy/MM/dd hh:mm:ss");
       qDebug() << str1;	


学习视频：[哔哩哔哩](https://www.bilibili.com/video/av41374178/?p=41)