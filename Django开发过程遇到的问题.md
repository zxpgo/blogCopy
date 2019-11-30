---
title: Django学习过程遇到的问题
date: 2019-01-21 21:34:24
tags:  "Django"
mathjax: true

---

2019第一篇博客，最近一个月忙于考试，博客没有更新，最近想继续Android开发，先用Django把后台搭好，考试结束就继续学习django。<!--more-->

学习Django过程使用的教程：[Django中文网](https://www.django.cn/course/course-1.html)

极力推荐，学习过程中遇到的问题，博主给予了很大的帮助。


## 无法加载静态文件


	python3 manage.py collectstatic

运行上述命令，就会自动把所有静态文件全部复制到STATIC_ROOT中。如果开启了admin，这一步是很必要的，不然部署到生产环境的时候会找不到样式文件


## 数据库迁移是出现的问题

运行`python3 manage.py migrate`时出现

	django.db.utils.OperationalError: (1091, "Can't DROP 'user_id'; check that column/key exists")
	django.db.utils.OperationalError: (1060, "Duplicate column name 'category_id'")

运行： `python3 manage.py migrate blog(项目名称) --fake`


## 常用命令

关闭uwsgi：
	
	 killall -9 uwsgi

开启uwsgi： 

	uwsgi -x mysite.xml

重启nginx:

	cd /usr/local/nginx/sbin
	./nginx -s reload


