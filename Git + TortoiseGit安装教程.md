---
title: Git + TortoiseGit安装教程和环境部署
date: 2018-08-15 22:28:50
tags:  Git

---
对于程序员而言，版本管理是不可缺少的一部分，利用Gitlab或Github对代码进行管理大大提高的效率，本文介绍如何搭建环境。<!--more-->

工作模式： 生成公私钥对，将公钥部署到github上，然后利用保存在本地的私钥去连接github

第一步: 下载Git: [https://git-scm.com/downloads](https://git-scm.com/downloads "https://git-scm.com/downloads")
		

第二步： 下载TortoiseGit: [https://tortoisegit.org/download/](https://tortoisegit.org/download/)

第三步： 安装以上两个软件，如果英语不ok的，可以安装tortoisegit语言包（[简体中文语言包](https://download.tortoisegit.org/tgit/2.6.0.0/TortoiseGit-LanguagePack-2.6.0.0-64bit-zh_CN.msi)）

在安装TortoiseGit后，第一次配置时，选择语言

![](https://i.imgur.com/9eXOV7H.png)

或者在TortoiseGit的设置进行设置，如下：</br>
首先鼠标右键，移到TortoiseGit上，点击Setting,进入如下页面，即可进行设置

![](https://i.imgur.com/x8bqPCu.png)

第三步： 密钥生成及部署

在电脑搜索栏输入PuTTygen，打开软件

![](https://i.imgur.com/bpnbfmw.png)

点击Generate

![](https://i.imgur.com/DuP2F0a.png)

在红色方框内不停移动鼠标，10秒左右，进入如下页面：

![](https://i.imgur.com/DXOtW6Q.png)

点击Save private key,将私钥保存到本地：

![](https://i.imgur.com/hFAjehH.png)

点击是，并输入私钥文件名

接着将公钥填到Github或Gitlab中，这里以github为例，gitLab相同：
 首页点击右上角的头像，并选择点击setting

![](https://i.imgur.com/NIuRTsI.png)

然后点击 SSH Keys

![](https://i.imgur.com/FcQBKu3.png)

接着点击创建一个ssh Key, 并将复制的公钥填入Key一栏中：

![](https://i.imgur.com/Hef7aPM.png)

第四步： 利用本地的私钥去连接Github，并克隆（clone）代码包到本地

在需要存放代码的目录下，点击右键，并点击 git clone（git 克隆），进入如下页面：

![](https://i.imgur.com/2LnKwQI.png)

根据图中指示填好，然后点击ok，即可将代码克隆到本地

其中的URL在Github上保存的代码包顶部地址

## 提交代码

第一步： 右键点击 git commit 

![](https://i.imgur.com/O4HrZkB.png)

第二步： 右键点击 TortoiseGit,并点击push（推送）：

![](https://i.imgur.com/oP3J76W.png)

点击ok即可



