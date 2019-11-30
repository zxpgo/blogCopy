---

title: 台式安装Android Studio后出现虚拟机无法启动
date: 2018-11-18 09:40:14
tags: APP开发
categories: APP开发

---

在台式机上安装了Android Studio，想利用虚拟机来进行安装App，启动虚拟机时，遇到该问题。<!--more-->

## 具体问题：

运行程序，在启动虚拟机时，出现如下错误：

Emulator: Process finished with exit code 1 

Emulator: ERROR: x86 emulation currently requires hardware acceleration!

## 原因：

### 1 台式没有开启Virtualizatoin Technology

进入BIOS，联想通过开机按F1进入；

有些系统是在Security选项中，有些系统是在Advance中。

将Intel (R) Virtualization Technoloyg设置为Enable。

### 2 下载HAXM并安装

[下载地址](https://software.intel.com/en-us/articles/intel-hardware-accelerated-execution-manager-intel-haxm)

解压文件，点击haxm-android.exe安装即可。

[参考博客](https://www.cnblogs.com/csulennon/p/4178404.html)