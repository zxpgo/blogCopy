---

title: C语言实现发送邮件
date: 2019-03-31 11:44:14
tags: socket
mathjax: true

---

telent模拟发送邮件，并利用C语言进行实现。<!--more-->

## 邮件协议SMTP

SMTP称为简单Mail传输协议，目标时向用户提供高效、可靠的邮件传输。SMTP的一个重要特点是它能够在传送中接力传送邮件，即邮件可以通过不同网络上的主机接力方式传送。工作在两种情况下：一是电子邮件从客户机到服务器；而是从某一个服务器到另一个服务器。SMTP是个请求/响应协议，它监听25号端口，用于接收用户的Mail请求，并与远程Mail服务器建立SMTP连接。


### telnet模拟

首先，需要开启telnet服务，具体百度查询即可。

cmd输入

	telnet

首先开启回显

	set localecho

连接qq服务器：

	open smtp.qq.com               //连接qq邮箱服务器，百度可查询到，网易SMTP等

界面输入：
 
	
	helo qq                        //打招呼
	auth login                     //登陆请求
	邮箱                           //发送加密后邮箱地址
	邮箱密码                       //发送加密的授权码，记住不是邮箱密码
	mail from:<1165772354@qq.com> //发送邮箱     
	rcpt to:<1692957004@qq.com>   //接收方有效
	data                          //告诉服务器开始发送数据了
	subject:zxpblog               //标题
	                              //标题后空一行
	www.zxpblog.www               //正文内容
	.                             //正文内容后输入一个句号


示例：

![](https://i.imgur.com/yyACfDa.png)

邮箱和密码BASE64加密：[http://old.tool.chinaz.com/Tools/Base64.aspx](http://old.tool.chinaz.com/Tools/Base64.aspx)，也可以自己百度BASE64加密找一个在线工具进行加密

邮箱授权码获取自己百度

### C++实现邮件发送

代码：

[https://github.com/zxpgo/socket/tree/master/email](https://github.com/zxpgo/socket/tree/master/email)
