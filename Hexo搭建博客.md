---
title: Hexo搭建属于自己的博客
date: 2018-08-11 22:28:50
tags: Hexo
---
本文将介绍利用Hexo搭建博客的教程，包括其中遇到问题的解决方案，包括：添加域名等等内容，本人亲自实践过，希望对您有所帮助，如果遇到其他问题，可联系本人。<!--more-->
# 准备：

Git：https://git-scm.com/downloads

Nodejs:https://nodejs.org/en/

Github账号，并创建新仓库

新建仓库如下：
![](https://i.imgur.com/Qt2pUjV.png)
![](https://i.imgur.com/FSsJniC.png)

# 安装Nodejs：
一路next即可

# 安装git
安装一路next，重点在配置

首先将安装目录添加进环境变量

然后设置用户名和邮箱

    $ git config --global user.name "John Doe"
    $ git config --global user.email johndoe@example.com

# 安装hexo

在所要存放博客文件的文件夹下右键，点击git bash

输入如下命令：

    npm install -g hexo-cli

# 创建hexo文件夹

    hexo init 

# 安装依赖依赖包

    npm install

# 站点配置
在博客目录下找到_config.yml,修改如下内容</br>
**在修改配置文件时，建议使用Notepad++进行编辑，原因是缩进和空格会影响代码能否成功提交**

    # Site
    title: 平步青云win
    subtitle:
    description: 建站 Linux Python C++ 编程语言
    keywords: 建站 Linux Python C++ 编程语言
    author: zxp
    language: zh-Hans
    timezone:

    
    deploy:
      type: git
      repository: https://github.com/zxpgo/blog.git# 博客仓库地址
     #repository: https://github.com/xxx/xxx.github.io.git 

博客仓库地址见本文开头第二张图片内的地址

注意：**：之后必须添加一个空格再添加内容**

否则会有如下错误：
![](https://i.imgur.com/Z9xaVfN.png)

# 发布博客

    hexo new "文章名字"

# 本地站点生成
## 生成文件

    hexo g

显示如下信息，表示成功

![](https://i.imgur.com/UpMYKme.png)

## 启动服务

    hexo s

显示如下信息，表示成功：

![](https://i.imgur.com/ENe7sIs.png)

到此本地的配置结束

打开浏览器，输入localhost:4000查看

# 部署Github
    hexo clean && hexo g && hexo d
    
    hexo clean #清除缓存文件 (db.json) 和已生成的静态文件 (public)。（个人习惯，非必需）
    hexo g #生成静态文件，每次改动之后都需要执行以下这个命令。
    hexo d #这个才是真正的部署命令，这里用的都是简写形式

## <font color=#f00>问题一</font>

运行时出错，由于还没有添加sshkey，具体教程如下：
打开Git Bash

设置Git的user name和email：（前面以及设置）

    git config --global user.name "aqiongbei" #改成你的注册Github的用户名
    git config --global user.email "aqiongbei@gmail.com" #改成你的注册Github的邮箱

## 生成SSH密钥：

生存密钥：

    ssh-keygen -t rsa -C "aqiongbei@gmail.com" #改成你注册Github的邮箱

返回提示如下：

![](https://i.imgur.com/ONTwjSc.png)


然后会提示，不用理，直接按回车，一共三次。
在C:\Users\Neckpain\.ssh得到 id_rsa 和 id_rsa.pub 文件，说明生成成功

## 添加密钥到 Github
打开 Github，登录自己的账号后
点击自己的头像->settings->SSH Keys->Add SSH key
将本地 id_rsa.pub 中的内容粘贴到 Key 文本框中，随意输入一个 title，点击 Add Key 即可。

## 测试
Git里面继续输入

    ssh -T git@github.com
返回

![](https://i.imgur.com/ztaoOqM.png)

则说明验证成功。


## <font color=#f00>问题一</font>
ERROR Deployer not found: git

![](https://i.imgur.com/g0NwgIs.png)

解决方案如下：


    npm install --save hexo-deployer-git

## 添加域名

首先购买域名，博主在阿里云购买，记得实名认证

首先在域名控制台添加记录，

![](https://i.imgur.com/KvOvo9Q.png)


记录一般添加两条，具体如下:

![](https://i.imgur.com/m3HfCIE.png)

其中的ip通过ping命令获取github服务器ip,其中仓库的URL为：
	
	github'用户名'.github.io

具体可见下图：

![](https://i.imgur.com/KiuLQXH.png)

然后在github的博客代码处添加一个CNMAE文件，不要后缀，或者在本地的source文件（所有提交的文件都放在该目录下）中添加该文件，并提交，

内容为申请的域名

    www.zxpblog.cn
    zxpblog.cn

最后还得在setting中的Custom domain处添加域名，必须加www, 如下
![](https://i.imgur.com/ZbIj49r.png)

![](https://i.imgur.com/RPLwY5K.png)

# 主题修改

在本地博客目录下打开git bash

     git clone https://github.com/iissnan/hexo-theme-next themes/next

在_config.yml中找到theme修改为下载的主题名

![](https://i.imgur.com/RPI4qld.png)

重新部署到github即可

    hexo clean && hexo g && hexo d

# 底部添加访问量统计
部分主题自带访问量统计，不用配置
打开\themes\主题名字\layout\_partials\footer.swig文件,不同具体footer文件的后缀可能不同，在顶部添加如下代码：

    <script async src="https://dn-lbstatics.qbox.me/busuanzi/2.3/busuanzi.pure.mini.js"></script>

接着在合适的地方添加需要显示的统计数字代码（同上文件）：

    <div class="powered-by">
    <i class="fa fa-user-md"></i><span id="busuanzi_container_site_uv">
      本站访客数:<span id="busuanzi_value_site_uv"></span>
    </span>
    </div>

两种统计方式：


- 同一用户连续点击n篇文章，记录n次访问量
- 
    <span id="busuanzi_container_site_pv">
    本站总访问量<span id="busuanzi_value_site_pv"></span>次
    </span>


- 同一用户连续点击n篇文章，记录1次访问量
- 
    <span id="busuanzi_container_site_uv">
      本站总访问量<span id="busuanzi_value_site_uv"></span>次
    </span>

# 添加RSS

首先安装 hexo-generator-feed包

    npm install hexo-generator-feed

在配置文件中添加如下代码：
    
    #RSS配置
    feed:
    	type: atom
    	path: atom.xml
    	limit: 20
    	hub:
    	content:
    	content_limit:
   		content_limit_delim: ' '
主题不同，添加方式可能不同，NEXT主题 _
CONFIG.YML文件中有个 RSS的配置，直接设置为 TRUE就可以了 

# Yelee主题
[http://moxfive.coding.me/yelee/](http://moxfive.coding.me/yelee/)

# Hexo中的Yelee主题，首页不显示文章摘要

转自：[https://blog.csdn.net/youshaoduo/article/details/78709160](https://blog.csdn.net/youshaoduo/article/details/78709160)

# 首页显示文章摘要

方法一：在需要显示的摘要之后添加如下代码：

	  <!--more-->

  
方法二： 自动截取方法
转自：[https://twiceyuan.com/2014/05/25/hexo%E8%87%AA%E5%8A%A8%E6%B7%BB%E5%8A%A0readmore%E6%A0%87%E8%AE%B0/](https://twiceyuan.com/2014/05/25/hexo%E8%87%AA%E5%8A%A8%E6%B7%BB%E5%8A%A0readmore%E6%A0%87%E8%AE%B0/)

# 添加文章访问量统计
themes\yelee\layout\_partial\footer.ejs中添加如下代码：

    <script async src="//dn-lbstatics.qbox.me/busuanzi/2.3/busuanzi.pure.mini.js"></script>

在article.ejs中<%- partial('post/tag') %>后添加如下代码：

    	<span id="busuanzi_container_page_pv" style ="display:block">
         	|阅读量(<span id="busuanzi_value_page_pv"></span>)
        </span>
       

在theme/source/css/style.styl文件中设置格式

    #busuanzi_container_page_pv
    {display:block  !important;
    padding-top:4px;
    color: #bebebe;
    //margin-left:200px;
    }
    
## 问题系列：

### 出现Template render error：（unknown path）


#### 问题描述
在使用hexo g生成文章的时候，报错如下:

ATAL Something's wrong. Maybe you can find the solution here: http://hexo.io/docs/troubleshooting.html
Template render error: (unknown path)
  unexpected end of file

#### 问题原因

当文章中有}}时,且这两个括号未被代码块包含，解析会出问题

#### 解决方法
	{% raw %}
	 含有双大括号的内容
	{% endraw %}

转自：[https://www.jianshu.com/p/738ebe02029b](https://www.jianshu.com/p/738ebe02029b)

