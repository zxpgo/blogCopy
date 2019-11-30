---
title: 利用Git提交代码至Github以及克隆
date: 2018-11-01 20:28:50
tags:  Git
---

平常写的代码，希望保存下来，放本地不安全，所以放到Github上。<!--more-->

这里默认已经安全了Git，并根Github已经建立连接。具体可以参照[博客](https://www.zxpblog.cn/2018/08/15/Git%20+%20TortoiseGit%E5%AE%89%E8%A3%85%E6%95%99%E7%A8%8B/)，只要安装Git即可，TortoiseGit可以不用安装，TortioseGit是图形化界面操作，如果不喜欢命令行操作的可以安装。

下面，以提交一个APP工程文件为例。

![](https://i.imgur.com/fkzEygE.png)

我要提交上图中的所有文件到Github上。

第一步，在Github创建一个repository，如下：

![](https://i.imgur.com/3da8eNa.gif)

第二步，将Github上的仓库克隆到本地（开始为空），如下：

![](https://i.imgur.com/u47NPDh.gif)

此时，本地多一个名为myapplication的文件夹：


![](https://i.imgur.com/8fQtFoj.png)

第三步：将除myapplication文件外的其他文件拷贝到myapplication文件中：

![](https://i.imgur.com/iZOEikZ.gif)


第四步：进入myapplication目录，并执行如下代码：

	cd myapplication
	git add.
	git commit -m "log"

![](https://i.imgur.com/ELJPe46.gif)

由于文件过大，所以没有等到提交完成。

成功后，Github会显式刚才提交的代码：

![](https://i.imgur.com/UJbxw31.png)

最后，可以给自己提交的代码添加README.md文件来描述提交的内容。


至于克隆到本地，具体如步骤二。