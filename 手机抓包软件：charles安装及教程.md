---
title: 手机抓包软件：charles安装及教程
date: 2018-08-13 17:24:24
tags: 
	 - 手机抓包
	 - 教程
categories: "教程" #文章分類目錄 可以省略
---
手机app或微信小程序开发过程中，解决数据传输过程中的问题，抓包软件必不可少<!--more-->
# 0、准备：
电脑关闭防火墙、其他代理或者翻墙软件
# 1、安装

最新版地址：[charles](https://www.charlesproxy.com/latest-release/download.do#)

本教程使用v4.2.6

安装一路next即可
# 2、配置

**第一步**： 查看本机IP

  方法一：运行->输入cmd->在命令行中输入：ipconfig</br>
  方法二：在charles中点击Help->Local IP Adress

![](https://i.imgur.com/8cxacvx.png)
</br>第一行为本地IP,后面两行为虚拟IP

**第二步**： 查看端口

![](https://i.imgur.com/vZ8LWDH.png)

**第三步**：手机上设置代理

安装证书

 复制http://charlesproxy.com/getssl或http://www.charlesproxy.com/ssl.zip 到手机浏览器打开，点击 here 下载，安装，信任；

手机和电脑连接到同一局域网，在wifi中设置代理，ios如下：

![](https://i.imgur.com/FVD46mi.png)  ![](https://i.imgur.com/YS5YY45.png)

电脑端弹出connction from "IP",点击allow

**第四步**： 电脑端安装证书

点击help->SSL Proxying-> install Charles root certificate

![](https://i.imgur.com/A1lKaZK.png)

一路next即可安装

第五步：设置SSL代理

点击proxying->ssl proxying settings

![](https://i.imgur.com/Y2fKZVp.png)

点击Add

 ![](https://i.imgur.com/2TIvQNX.png)

在host中填入：*通配符
在port中填入：443

# 字段说明

- Structure：树状结构显示，将网络请求按访问的域名分类；
- Sequence：水平结构显示，将网络请求按访问的时间排序
- 垃圾桶图标 ： 功能是clear，清理掉所有请求显示信息
- Filter ： 过滤，可以输入关键字来快速筛选出 URL 中带指定关键字的网络请求
- Overview ： 查看这次请求的详细内容，例如耗时详细列车了请求开始时间、结束时间，响应开始时间、结束时间，总耗时、DNS耗时、网络延时等。 
- 对于Size也详细列出了请求头大小、响应头大小、压缩比例等内容。 

- URL：进行网络请求的链接；
- Status：当前状态，complete表示请求完成；
- Responce Code：返回码。不同的接口，不同的请求结果，返回码都不同；
- Protocol：使用的协议；
- Method：请求方式，如GET请求，POST请求等；
- Kept Alive：判断当前是否正在链接（活跃）；
- Content-Type:发送的内容类型，如这里用的是XML文本，以UTF8的方式发送；
- Client Address：客户端的IP地址；
- Remote Address：远程服务器的IP；
- Timing: 
 - Request Start Time：请求开始的时间；
 - Request End Time：请求结束的时间；
 - Response Start Time：返回开始的时间；
 - Response End Time : 返回结束的时间；
 - Duration : 总时间；
- Size: 
 - Request Header ：请求的头部大小；
 - Response Header：返回的头部大小；
 - Request : 请求发送的大小；
 - Response：返回数据的大小；
 - Total：所有数据大小；
 - Request Compression : 请求压缩；
 - Response Compression : 返回压缩；
- Request ： 查看请求内容（底下的Headers，Query String， Cookies，Raw。） 
 - Headers：发送请求的头部信息； 
 - Query String : 发送参数列表；
 - Cookies： 浏览器缓存；
 - Raw：发送的原生数据，包括了头部和参数；
- Reponse : 查看响应内容 
 - Headers：是返回的头部信息；
 - Text：返回信息（除去头部）后的文本； 
 - Hex：返回信息的16进制表示；
 - XML：我返回的数据是XML。如果你返回的是JSON，这里就会显示JSON；
 - XML Text：如果你返回JSON，这里会显示JSON Text；
 - Raw：返回的所有原生数据，包括头部；
- Summary: 查看发送数据的一些简要信息（主机，状态码，数据的类型，header和body大下，加载时间，总时间）
- Chart: Summary中简要信息以图表形式展示
- Notes: 其他信息

# 问题解决

## 问题一

You may need to configure your browser or application to trust the Charles Root Certificate</br>
由于手机虽然安装了证书，但是未信任

解决方案：</br>
设置->通用->关于本机->证书信任设置-> 找到charles proxy custom root certificate然后信任该证书即可.

转自：[更多详细内容见此](https://blog.csdn.net/zhangxiang_1102/article/details/77855548)