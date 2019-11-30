---

title: Centos7上Django+Mysql+Appach环境搭建
date: 2018-12-21 20:57:14
tags: APP开发
categories: APP开发
mathjax: true

---

网上的教程都比较乱，结合Django官方文档搭建后台，在这里总结一下以备后用！<!--more-->

## 环境

- Python 3.5
- Appache 2.4.6
- Django 2.14

[Django官方文档说明](https://docs.djangoproject.com/en/2.1/)

## Python问题

由于Centos7自带的python为2.7，在这里我们同时安装python3.5。

Python下载地址：	[python](https://www.python.org/downloads/)
镜像地址（更全）：[https://www.python.org/ftp/python/](https://www.python.org/ftp/python/)

### python下载并安装：


	[root@ansible soft]#yum install gcc gcc-c++ -y
	[root@ansible soft]# wget https://www.python.org/ftp/python/3.5.6/Python-3.5.6.tar.xz
	[root@ansible soft]# tar xvf Python-3.5.6.tar.xz
	[root@ansible soft]# cd Python-3.5.6/
	[root@ansible Python-3.6.1]# ./configure
	[root@ansible Python-3.6.1]# make && make install


### 验证

	#python -V     #一个是旧版本，一个是新版本
	Python 2.7.5
	# python3 -V
	Python 3.6.1

### 设置3.X为默认版本

查看 Python 的路径，在 /usr/bin 下面。可以看到 python 链接的是 python 2.7，所以，执行 python 就相当于执行 python 2.7。

	[root@ansible ~]# ls -al /usr/bin | grep python
	-rwxr-xr-x.   1 root root       11232 Dec  2  2016 abrt-action-analyze-python
	lrwxrwxrwx.   1 root root           7 May 26  2017 python -> python2
	lrwxrwxrwx.   1 root root           9 May 26  2017 python2 -> python2.7
	-rwxr-xr-x.   1 root root        7136 Nov  6  2016 python2.7


将原来 python 的软链接重命名：

	# mv /usr/bin/python /usr/bin/python.bak

将 python 链接至 python3：

	# ln -s /usr/local/bin/python3 /usr/bin/python


### 配置yum

升级 Python 之后，由于将默认的 python 指向了 python3，yum 不能正常使用，需要编辑 yum 的配置文件，此时：


	[root@ansible-admin Python-3.6.1]# yum list
	  File "/usr/bin/yum", line 30
	    except KeyboardInterrupt, e:
	
	SyntaxError: invalid syntax

修改/usr/bin/yum和/usr/libexec/urlgrabber-ext-down，将 #!/usr/bin/python 改为 #!/usr/bin/python2.7，保存退出即可。

注意：后面利用`python3 manage.py`

## Django安装

官方文档将其分为三步： [https://docs.djangoproject.com/en/2.1/topics/install/#installing-official-release](https://docs.djangoproject.com/en/2.1/topics/install/#installing-official-release)

### pip安装

来自官方文档：[get-pip.py](https://pip.pypa.io/en/latest/installing/#installing-with-get-pip-py)

首先下载get-pip.py:

	curl https://bootstrap.pypa.io/get-pip.py -o get-pip.py

然后运行：

	python3 get-pip.py

### Virtualenv安装

	pip install virtualenv

### Django安装

默认安装的是2.1.4版本：

 	pip install Django


## Mysql数据库安装


### Mysql密码重置问题

参考：[博客](https://www.cnblogs.com/gumuzi/p/5711495.html)

重置密码解决MySQL for Linux错误 ERROR 1045 (28000): Access denied for user 'root'@'localhost' (using password: YES)

#### 1. 重置密码的第一步就是跳过MySQL的密码认证过程，方法如下：

修改mysql配置文件：

	#vim /etc/my.cnf

在[mysqld]后面任意一行添加“skip-grant-tables”用来跳过密码验证的过程，如下图所示：

![](https://i.imgur.com/v7e9UUH.png)

#### 2. 重启Mysql

	systemctl restart mysqld


#### 3. 重启之后输入#mysql即可进入mysql

进入mysql设置密码:

	mysql> use mysql;
	mysql> update user set password=password("你的新密码") where user="root";
	mysql> flush privileges;
	mysql> quit


#### 4. 编辑my.cnf,去掉刚才添加的内容，然后重启MySQL。大功告成！

## Mysql关联Django设置


在setting.py设置文件中DATABASE参数修改为如下内容：


	DATABASES = {
    'default': {
         'ENGINE': 'django.db.backends.mysql',
         'NAME': 'mysite',
         'HOST': 'localhost',
		 'PASSWORD': '密码天这里',
         'read_default_file': '/path/to/my.cnf',
         'init_command': 'SET default_storage_engine=INNODB',
   		}
	}



注意：必须提前进入mysql，创建一个数据库名字为`mysite`

各个参数的意思[参考官方文档](https://docs.djangoproject.com/zh-hans/2.1/ref/databases/#mysql-notes)


## Appache安装





