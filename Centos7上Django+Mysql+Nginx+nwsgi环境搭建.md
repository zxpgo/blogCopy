---

title: Centos7上Django+Mysql+Nginx+nwsgi环境搭建
date: 2018-12-21 20:57:14
tags: APP开发
categories: APP开发

---

前面用Appache+mod_wsgi搭建时，遇到问题没有解决，所以转移到Nginx+nwsgi。这里主要介绍如何配置nginx。<!--more-->


## 一、更新系统软件包

	yum update -y

## 二、安装软件管理包和可能使用的依赖

	yum -y groupinstall "Development tools"
	yum install openssl-devel bzip2-devel expat-devel gdbm-devel readline-devel sqlite-devel

## 三、下载Pyhton3到/usr/local 目录 

	cd /usr/local
	wget https://www.python.org/ftp/python/3.6.6/Python-3.6.6.tgz

解压

	tar -zxvf Python-3.6.6.tgz

进入 Python-3.6.6路径

	cd Python-3.6.6

编译安装到指定路径

	./configure --prefix=/usr/local/python3

注意：/usr/local/python3 路径可以自己指定，自己记着就行，下边要用到。

安装python3

	make
	make install

安装完成之后 建立软链接 添加变量 方便在终端中直接使用python3

	ln -s /usr/local/python3/bin/python3.6 /usr/bin/python3

Python3安装完成之后pip3也一块安装完成，不需要再单独安装
同样给pip3建立软链接

	ln -s /usr/local/python3/bin/pip3.6 /usr/bin/pip3

## 四、查看Python3和pip3安装情况

![](https://i.imgur.com/QCngJnx.png)

## 五、安装virtualenv ，建议大家都安装一个virtualenv，方便不同版本项目管理。

	pip3 install virtualenv

建立软链接

	ln -s /usr/local/python3/bin/virtualenv /usr/bin/virtualenv

安装成功在根目录下建立两个文件夹，主要用于存放env和网站文件的。(个人习惯，其它人可根据自己的实际情况处理)

	mkdir -p /data/env
	mkdir -p /data/wwwroot

## 六、切换到/data/env/下，创建指定版本的虚拟环境。

	virtualenv --python=/usr/bin/python pyweb

然后进入/data/env/pyweb/bin 

启动虚拟环境：

	source activate

![](https://i.imgur.com/3VlR8Q2.png)

留意我标记的位置，出现(pyweb)，说明是成功进入虚拟环境。


## 七、虚拟环境里用python3安django和uwsgi

	pip3 install django （如果用于生产的话，则需要指定安装和你项目相同的版本）
	pip3 install uwsgi

给uwsgi建立软链接，方便使用

	ln -s /usr/local/python3/bin/uwsgi /usr/bin/uwsgi

## 八、切换到网站目录/data/wwwroot,创建Django项目

	django-admin.py startproject mysite

创建成功之后，/data/wwwroot下面就会多出一个mysite文件夹，我们的项目就在这个文件夹里。

然后新建立一个名叫blog的APP

	python3 manage.py startapp blog

进入项目文件夹/data/wwwroot/mysite,添加static和templates，分别用于存放静态文件和模板文件。
编辑项目里mysite/settings.py文件

	vim /data/wwwroot/mysite/mysite/settings.py

在INSTALLED_APPS 列表里添加'blog'，注意，后面要加上一个逗号','.

![](https://i.imgur.com/9bBwGWq.png)

修改ALLOWED_HOSTS，['*']，可以让任何IP访问:


![](https://i.imgur.com/jodboEr.png)

TEMPLATES的DIRS里添加模板路径

	os.path.join(BASE_DIR, 'templates')


![](https://i.imgur.com/8CtxZ4t.png)

尾部添加

STATICFILES_DIRS = (
    os.path.join(BASE_DIR,'static'),
    )


![](https://i.imgur.com/HpaY7y6.png)


## 九、在templates下添加index.html文件，输入下面内容。

	vim /data/wwwroot/mystie/templates/index.html	

输入内容：

	<!DOCTYPE html>
	<html>
	<head>
	<meta charset="utf-8">
	<title>我的网站</title>
	</head>
	<body>
	<h1>欢迎光临我的网站！</h1>
	</body>
	</html>

## 十、配置URL

在mysite/mysite/目录下的urls.py文件添加内容，修改后如下：

	from django.contrib import admin
	from django.urls import path, include
	
	urlpatterns = [
	    path('admin/', admin.site.urls),
		path('blog/', include('blog.urls')),
	]


在blog文件夹中创建urls.py，输入如下内容：

	from django.contrib import admin
	from django.urls import path, include
	from . import views
	urlpatterns = [
		path('', views.index),
	]


## 十一、编辑blog APP下的views.py


在blog下的views.py中添加如下内容：

	def index(request):
		return render(request, 'index.html')


## 十二、启动项目


	python3 manage.py runserver	0:8000

在浏览器内输入：

	主机IP:8000
	比如：111.230.111.82:8000


正常运行


## 十三、Django正常运行之后我们就开始配置一下uwsgi

我们网站项目路径是 /data/wwwroot/mysite/,在项目根目录下创建mysite.xml文件，输入如下内容：

	<uwsgi>
	   <socket>127.0.0.1:8997</socket> <!-- 内部端口，自定义 -->
	   <chdir>/data/wwwroot/mysite/</chdir> <!-- 项目路径 -->
	   <module>mysite.wsgi</module>  <!-- mysite为wsgi.py所在目录名-->
	   <processes>4</processes> <!-- 进程数 -->
	   <daemonize>uwsgi.log</daemonize> <!-- 日志文件 -->
    </uwsgi>



## 十四、安装nginx和配置nginx.conf文件

进入home目录，执行下面命令

	cd /home/

	wget http://nginx.org/download/nginx-1.13.7.tar.gz

下载完成后，执行解压命令：

	tar -zxvf nginx-1.13.7.tar.gz

进入解压后的nginx-1.13.7文件夹，依次执行以下命令：

	./configure
	make
	make install

nginx一般默认安装好的路径为/usr/local/nginx

在/usr/local/nginx/conf/中先备份一下nginx.conf文件，以防意外。

	cp nginx.conf nginx.conf.bak

然后打开nginx.conf，把原来的内容删除，直接加入以下内容：

	events {
	    worker_connections  1024;
	}
	http {
	    include       mime.types;
	    default_type  application/octet-stream;
	    sendfile        on;
	    server {
	        listen 80;
	        server_name 127.0.0.1:80; #改为自己的域名，没域名修改为127.0.0.1:80
	        charset utf-8;
	        location / {
	           include uwsgi_params;
	           uwsgi_pass 127.0.0.1:8997;  #端口要和uwsgi里配置的一样
	           uwsgi_param UWSGI_SCRIPT mysite.wsgi;  #wsgi.py所在的目录名+.wsgi
	           uwsgi_param UWSGI_CHDIR /data/wwwroot/mysite; #项目路径
	            
	        } 
	        location /static/ {
				alias /data/wwwroot/mysite/static/; #静态资源路径
	        }
	    }
	}

要留意备注的地方，要和UWSGI配置文件mysite.xml，还有项目路径对应上。 

进入/usr/local/nginx/sbin/目录执行`./nginx -t`命令先检查配置文件是否有错，没有错就执行以下命令：

	./nginx

终端没有任何提示就证明nginx启动成功。可以使用你的服务器地址查看，成功之后就会看到一个nginx欢迎页面。


之后，在settings.py里设置：

1、关闭DEBUG模式。

	DEBUG = False  

2、ALLOWED_HOSTS设置为* 表示任何IP都可以访问网站。

	ALLOWED_HOSTS = ['*']

## 十五、访问项目的页面


进入网站项目目录

	cd /data/wwwroot/mysite/

执行下面命令：

	uwsgi -x mysite.xml

以上步骤都没有出错的话。
进入/usr/local/nginx/sbin/目录
执行：

	cd /usr/local/nginx/sbin/
	./nginx -s reload

重启nginx 。

然后在浏览器里访问你的项目地址！

	111.230.111.81/blog/


![](https://i.imgur.com/67uezFD.png)

## Mysql数据库安装和配置

[https://www.django.cn/article/show-2.html](https://www.django.cn/article/show-2.html)

安装好数据库后还需要安装mysqlclient：

	sudo yum install python3-devel

	pip3 install mysqlclient

安装好Mysql后，进入创建数据库`mysite`

在setting.py设置文件中DATABASE参数修改为如下内容：


	DATABASES = {
    'default': {
         'ENGINE': 'django.db.backends.mysql',
         'NAME': 'mysite',
         'HOST': 'localhost',
		 'PASSWORD': '密码填这里',
         'read_default_file': '/path/to/my.cnf',
         'init_command': 'SET default_storage_engine=INNODB',
   		}
	}

注意：必须提前进入mysql，创建一个数据库名字为`mysite`

转自：[www.django.cn](https://www.django.cn/article/show-4.html)