---

title: Socket编程
date: 2019-03-31 11:44:14
tags: socket
mathjax: true

---

socket编程用于实现两个实体之间的通信，比如客户端和服务器之间，客户端先服务器发送请求消息，服务器对请求进行相应。<!--more-->

## socket原理

Socket（套接字）只是一个概念，并不是存在实体。协议栈内部有一块用于存储控制信息的内存空间，这里记录了用于控制通信操作的控制信息，例如通信对象的IP地址、端口号、通信操作的进行状态等，这就是套接字。

Socket通信流程：

![](https://i.imgur.com/0e7ZEWB.jpg)

收发数据的操作分为如下4个阶段：

- 创建套套接字
- 将管理连接到服务器的套接字上
- 收发数据
- 断开管理并删除套接字

# C++中Socket通信函数

头文件和库加载

	#include <WinSock2.h> //socket头文件
	#pragma comment(lib, "ws2_32.lib") //socket库

## 客户端

### 第一步： 加载Socket库

WSAStartup()

使用Socket之前需要调用此函数

	int
	WSAAPI
	WSAStartup(
	    _In_ WORD wVersionRequested,
	    _Out_ LPWSADATA lpWSAData
	    );

第一个参数是：Socket版本；第二个参数：操作系统通过它返回请求Socket版本信息

MAKEEORD(a, b)是一个宏定义，第一个参数是主版本号，第二个参数是副版本号

示例代码：

	WSADATA wsaData;//获取版本信息
	WSAStartup(MAKEWORD(2, 2), &wsaData); //MAKEWORD(主版本号，副版本号)

### 第二步：创建socket

socket()
	
	SOCKET
	WSAAPI
	socket(
	    _In_ int af,
	    _In_ int type,
	    _In_ int protocol
	    );

- af: 一个地址描述符。目前仅支持AF_INET，即ARPA_Internet地址格式。
- teype: 指定socket类型。新套接口的类型描述类型，如TCP(SOCK_STREAM)和UDP(SOCK_DGRAM)。常用的socket类型有：SOCK_STREAM、SOCK_DGRAM、SOCK_RAW、SOCK_PACKET等等。
- protocol:指定协议。套接口所用的协议。如调用者不想指定，可用0。常用的协议有：IPPROTO_TCP、IPPROTO_UDP、IPPROTO_STCP、IPPROTO_TIPC等，它们分别对应TCP传输协议、UDP传输协议、STCP传输协议、TIPC传输协议。、

返回一个套接字

示例代码：

	SOCKET cliSock = socket(AF_INET, SOCK_STREAM, 0); //流式套接字,第三个参数0代表自动选择协议

### 第三步：打包服务端地址


SOCKADDR_IN结构体

	typedef struct sockaddr_in {
	
	#if(_WIN32_WINNT < 0x0600)
	    short   sin_family;
	#else //(_WIN32_WINNT < 0x0600)
	    ADDRESS_FAMILY sin_family;
	#endif //(_WIN32_WINNT < 0x0600)
	
	    USHORT sin_port;
	    IN_ADDR sin_addr;
	    CHAR sin_zero[8];
	} SOCKADDR_IN, *PSOCKADDR_IN;


- sin_port:端口
- sin_adr:IP地址
- sin_family: 协议族


示例：

	SOCKADDR_IN servAddr;
	char servIP[] = "192.168.136.1"; //服务器IP
	servAddr.sin_family = AF_INET;
	servAddr.sin_addr.S_un.S_addr = inet_addr(servIP); //服务器地址设置为回传地址
	servAddr.sin_port = htons(port); //host to net shrot, 设置端口号为port


### 第四步：连接服务端
	
connect()

	int
	WSAAPI
	connect(
	    _In_ SOCKET s,
	    _In_reads_bytes_(namelen) const struct sockaddr FAR * name,
	    _In_ int namelen
	    );

-s 客户端套接字
- name: 指向要连接套接字的sockaddr结构体指针，注意必须转换成SOCKADDR*类型。
- namelen: sockaddr结构体的字节长度

客户端程序调用，使客户的socket 与监听于name所指定的计算机特定端口上的服务socket进行连接，连接成功返回0，否则返回SOCKET_ERROR。

本函数用于创建与指定外部端口的连接。s参数指定一个未连接的数据报或流类套接字。


示例代码：

	connect(cliSock, (SOCKADDR*)&servAddr, sizeof(SOCKADDR));

### 第五步： 发送消息给服务端

连接服务端成功后，可以向服务端发送消息

send()


	int
	WSAAPI
	send(
	    _In_ SOCKET s,
	    _In_reads_bytes_(len) const char FAR * buf,
	    _In_ int len,
	    _In_ int flags
	    );

- s: 一个用于标识已连接套接口的描述字，是目的地的套接字
- buf: 需要发送的消息的缓冲区
- len: 发送消息的字节数
- flags: 调用执行方式，一般设置为0


示例：

	//发送客户端的名称给服务端
	const int NICKNAME_SIZE = 20;//名称长度
	char client_name[NICKNAME_SIZE] = "travaler"; //用户名
	send(cliSock, client_name, sizeof(client_name), 0);



### 第六步： 接收消息

服务端向客户端发送消息时，需要客户端进行消息接收

recv()

	int
	WSAAPI
	recv(
	    _In_ SOCKET s,
	    _Out_writes_bytes_to_(len, return) __out_data_source(NETWORK) char FAR * buf,
	    _In_ int len,
	    _In_ int flags
	    );

不论是客户端还是服务端应用程序都用recv函数从TCP连接的另一端接收数据。

- s: 指定接收端套接字描述符
- buf: 指明一个缓冲区，该缓冲区用来存放recv函数接收的数据；
- len: 指明buf的长度；
- flags: flags一般设置为0


示例：

	char buffer[BUFFER_SIZE] = { 0 };
	int nrecv = recv(cliSock, buffer, sizeof(buffer), 0); // nrecv是接收到的数据字节数

### 获取本地IP

自己设计的函数：

	//获取本地IP
	void getLocalIP(char localIp[], int n)
	{
		gethostname(localIp, n);
		HOSTENT *host = gethostbyname(localIp);
		in_addr PcAddr;
		for (int i = 0; ; i++)
		{
			char *p = host->h_addr_list[i];
			if (NULL == p)
			{
				break;
			}
			memcpy(&(PcAddr.S_un.S_addr), p, host->h_length);
			strcpy(localIp, inet_ntoa(PcAddr));
		}
	}

函数调用

	char localIP[BUFFER_SIZE]; //本地IP
	getLocalIP(localIP, sizeof(localIP) / sizeof(char));

# 服务端

前三步（加载socket库、创建socket和服务端地址打包）跟客户端相同，然后接收消息和发送消息也跟客户端相同。

不同的是：客户端是将客户端socket与服务端地址包连接，而服务端是将服务端的socket与服务端地址包绑定，还有服务端需要接收客户端的请求。

### 第一步：加载socket库
	
	WSADATA wsaData;//获取版本信息
	WSAStartup(MAKEWORD(2, 2), &wsaData); //MAKEWORD(主版本号，副版本号)

### 第二步： 创建socket

	SOCKET servSock = socket(AF_INET, SOCK_STREAM, 0); //流式套接字

### 第三步： 服务端地址打包

	SOCKADDR_IN servAddr;
	servAddr.sin_family = AF_INET;
	servAddr.sin_addr.S_un.S_addr = inet_addr("192.168.136.1"); //服务器地址设置为回传地址
	servAddr.sin_port = htons(port); //host to net shrot, 设置端口号为port

### 第四步： 绑定服务端的socket和打包号的地址

bind()

	int
	WSAAPI
	bind(
	    _In_ SOCKET s,
	    _In_reads_bytes_(namelen) const struct sockaddr FAR * name,
	    _In_ int namelen
	    );

- s: 指定绑定的Socket描述符
- name: 指向要绑定的套接字的sockaddr结构体指针，注意必须转换成SOCKADDR*类型。
- namelen: 指定sockaddr结构体长度

示例：

	bind(servSock, (SOCKADDR*)&servAddr, sizeof(servAddr));

### 第五步： 监听

listen()

	int
	WSAAPI
	listen(
	    _In_ SOCKET s,
	    _In_ int backlog
	    );

- s: 用于标识一个已捆绑未连接套接字的描述字。
- backlog: 等待连接队列的最大长度。

功能：监听通过socket发起的连接请求，将其加入等待队列，等待accept函数的接收。
  
示例：

	listen(socket_Server, 10);


### 第六步： 接收连接请求

accept()

	SOCKET
	WSAAPI
	accept(
	    _In_ SOCKET s,
	    _Out_writes_bytes_opt_(*addrlen) struct sockaddr FAR * addr,
	    _Inout_opt_ int FAR * addrlen
	    );

- s: 套接字描述符，该套接字在listen()后监听连接。
- addr： 指向一个缓冲区，其中接受为通讯层所知的连接实体的地址。Addr参数的实际格式由套接字创建时所产生的地址族确定。
- addrlne: 指针，输入参数，配合addr一起使用，指向存有addr地址长度的整型数。

示例：

	//接收连接的请求，服务器创建一个socket副本用来处理接收到的请求
	socket_Client = accept(socket_Server, (SOCKADDR*)&addrCli, &naddr); //指针类型参数,接收accept的返回值

### 发送消息

同客户端

	const int BUF_SIZE = 2048;//缓冲区大小
	char sendbuf[BUF_SIZE];  //发送缓冲区
	strcpy(sendbuf, "hello!"); //设置缓冲区内容
	send(socket_Client, sendbuf, sizeof(sendbuf), 0);


### 发送消息给客户端

同客户端


## 清理内存

最后都需要关闭scoket，以及清除socket库。

closesocket()

	int
	WSAAPI
	closesocket(
	    _In_ SOCKET s
	    );

关闭一个描述符为s的套接字，执行成功返回0，否则返回SOCKET_ERROR。

WSACleanup()

	int
	WSAAPI
	WSACleanup(
	    void
	    );

应用程序在完成对请求的Socket库使用之后，要调用此函数来解除与Socket库的绑定并且释放Socket库所占用的系统资源。


示例：

	closesocket(socket_Client);
	closesocket(socket_Server);
	WSACleanup();


## IP处理函数

### gethostname()

返回本地主机的标准主机名

	#include <Winsock2.h>
	int PASCAL FAR gethostname(char FAR *name, int namelne);
	name: 一个指向要存放主机名的缓冲区指针
	namelen: 缓冲区的长度


### inet_aton()


### inet_addr()

	unsigned long
	WSAAPI
	inet_addr(
	    _In_z_ const char FAR * cp
	    );

inet_ addr函数转换__网络主机__(如192.168.1.10)地址为网络字节二进制值。如果参数char $\ast$cp无效，函数返回-1（INADDR_NONE），这个函数在处理地址为255.255.255.255时也返回-1,255.255.255.255是一个有效地址，不过inet _addr无法处理。


示例：

	cliAddr.sin_addr.S_un.S_addr = inet_addr("192.168.136.1");

### inet_ntoa()

	char FAR *
	WSAAPI
	inet_ntoa(
	    _In_ struct in_addr in
	    );

inet_ ntoa函数转换__网络字节排序的地址__为__标准的ASCII以点分开的地址__。该函数返回指向点分开的字符串地址（如192.168.1.10）的指针，该字符串的空间为静态分配的，这意味着在第二次调用该函数时，上一次条用将会被重写（覆盖），所以如果需要保存该串最后复制处理自己管理。


示例：

	inet_ntoa(cliAddr[nextIndex].sin_addr);

