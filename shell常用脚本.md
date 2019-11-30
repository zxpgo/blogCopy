---
title: shell脚本
date: 2019-09-27 11:44:14
tags: shell
mathjax: true
---

一些经常用到的脚本，小工具。

<!--more-->

## 文件复制

从一台linux主机将文件复制到另一台linux主机。

```shell
#第一步生成ssh证书
ssh-keygen #在root/.ssh目录下生成公私钥对
#第二步将公钥复制到目标linux服务器上
ssh-copy-id root@192.168.80.11 #输入需要登陆的linux服务器密码授权
#第三步免密码登陆
ssh root@192.168.80.111
#实现文件传输
scp /opt/k8s/bin/  root@192.168.80.111:/opt/k8s/bin
```

注意两个命令，主要用来免密登陆。

## 文件追加

将一台linux主机的文件追加到另一台linux主机的文件中。

```shell
#!/bin/bash

sh  #重新打开一个shell来编辑文件
scp /root/test1  root@192.168.80.110:/root/download/ #文件复制
ssh root@192.168.80.110 'cat /root/download/test1 > /root/download/test.log' #文件追加

```

同样也可以配置公私钥对来免密登陆。

