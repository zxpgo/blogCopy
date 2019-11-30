---

title: Linux不同版本以及程序包安装
date: 2018-08-28 22:23:14
tags: Linux
categories: "实验楼学习笔记之Linux"

---


Linux版本介绍，以及程序包安装命令介绍<!--more-->

## Linux系统版本

- RedHat(红帽)系列： Redhat、Centos、Fedora等
- Debian(得比恩)系列： Debian、Ubuantu等

## 程序包安装

### RedHat系列

- 常见的安装包格式rpm包，安装rpm包的命令是"rpm -参数"
- 包管理工具yum
- 支持tar包

### Debian系列

- 常见的安装包格式deb包，安装deb包的命令是"dpkg -参数"
- 包管理工具apt-get
- 支持tar包

tar 只是一种压缩文件格式，所以，它只是把文件压缩打包而已。

rpm 相当于windows中的安装文件，它会自动处理软件包之间的依赖关系。优缺点来说，rpm一般都是预先编译好的文件，它可能已经绑定到某种CPU或者发行版上面了。

## yum安装命令

YUM（Yellow dog Updater, Modified ）是Yellow Dog Linux开发的。 Yellow Dog Linux原本是一套完全源于Red Hat但运作于先前使用IBM PPC平台的MAC机器，原本以为这个版本已经消失，没想到最新消息是他们推出了在Sony PS3上面运作的版本。 CentOS及Fedora的基本预设安装中即将YUM列入其内。 

使用方法：

- 编辑/etc/yum.conf档案详细设定请参考： [简易APT/YUM伺服器设定](http://linux.vbird.org/linux_server/0450apt.php)
- 基本指令： （如果yum在工作过程中需要使用者回应，可加上-y参数直接回答yes ）

| yum install softwarename1 [softwarename2.....] | 安装套件 | 
| yum update [softwarename 1 softwarename2...] | 更新套件，不指定套件名则更新所有可更新的套件 |
| yum list | 列出目前在yum server 上面有的套件 |
| yum info | 类似rpm -qi |
| yum clean | 移除下载到本机的packages 或headers |
| yum remove softwarename1 [softwarename2.....]	| 移除已经安装的套件 | 

注1：Red Hat近年来致力于将一个设定档切割成很多小设定档。 以yum.conf为例，Red Hat将其分割成xxx.repo档放置在/etc/yum.repos.d这个目录下。 并在yum.conf档里增加一行注解： PUT YOUR REPOS HERE OR IN separate files named file.repo in /etc/yum.repos.d。 个人可视喜好决定。

## apt安装命令

Debian开发，目前也有porting到其他版本，要在Red Hat系的Fedora或CentOS使用也是可以的。

使用方法：

编辑/etc/apt/sources.list ，设定所选用的版本，如stable，testing，unstable及套件来源站台或装置。 档案详细设定请参考： 了解Debian系统的哲学

基本指令：

| apt-setup	| 设定/etc/apt/souces.list | 
| apt-get update	| 软体资料库同步 | 
| apt-get install softwarename1 [softwarename2.....]	| 安装软体 | 
| apt-get remove softwarename 1 [softwarename 2...]	|  移除软体(保留设定档） | 
| apt-get --purge remove softwarename 1 [softwarename 2...]	|  移除软体(不保留设定档） | 
| apt-cache search softwarename	| 列出所有sofrwarename的套件 | 
| apt-upgrade [softwarename 1 softwarename2...]	|  更新套件，不指定套件名则更新所有可更新的套件 | 
| apt-get clean(autoclean)	| 删除系统暂存的deb(autoclean只会将比目前系统旧版的套件删除) | 
| apt-get dist-upgrade	|  转换系统的版本（需在/etc/apt/sources.list指定stable，testing或unstable） | 


转自：[张飞在线](https://www.cnblogs.com/zhangfeionline/p/5893748.html)