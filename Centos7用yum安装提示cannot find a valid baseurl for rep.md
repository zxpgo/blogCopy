---

title:  Centos7使用yum安装提示cannot find a valid baseurl for repo:base/7/x86_64 的解决方法
date: 2019-02-12 13:08:14
tags: Linux
mathjax: true

---


*无法联网的明显表现会有：

　　1、yum install出现 Error: cannot find a valid baseurl or repo:base

　　2、ping host会提示unknown host

<!--more--> 

方法一、

　　1、打开 vi /etc/sysconfig/network-scripts/ifcfg-eth0（每个机子都可能不一样，但格式会是“ifcfg-eth数字”，也有些时ifcfg-ens数字），把ONBOOT=no，改为ONBOOT=yes

　　2、重启网络：service network restart

方法二、

　　1、打开 vi /etc/resolv.conf，增加 nameserver 8.8.8.8

　　2、重启网络: service network restart

 