---
title: Django简介（一）
date: 2018-08-16 17:24:24
tags: 
	 - Django
	 - Python
	
categories: "Python之Django"

---

Django是使用Python开发的开源Web开发框架。使用Django，你能以最小的代价构建和维护高质量的Web应用。<!--more-->

## 使用Django的原因：

- Django诞生于新闻网站的环境中，它所提供的特性非常适合开发内容类的网站（例如：博客，交流社区，图片分享站等等......）。因为Django是在快节奏的新闻编辑环境中开发的，它的设计目的是使常见的Web开发任务变得快速而简单。

- Django是开源的，不是商业项目或者科研项目，它集中力量解决Web开发中所遇到的一系列问题。因此，Django每天都在现有的基础上进步，以适应不断更迭的开发需求。这样即节省了开发时间，也提高了后期维护的效率。

## Django概览

在Django建立网站的过程中，我分为3个Python文件（models.py, urls.py, views.py） 和HTML模板文件（template.html）来讲解。

### 设计模型Model

Django无需数据库就可以使用，通过对象关系映射器（Object-relational mapping）,仅使用python代码就可以描述数据结构。

	from django import models

	class book(models.Model):
		name = models.CharField(max_length=100)
		pud_date = models.DateField()

`models.py`	文件主要用一个Python类来描述数据表。称为模型（model）。运用这个类，你可以通过简单的Python代码来创建、检索、更新、删除数据库中的记录而无需写一条由一条的SQL语句。在这里我们创建了一个book模型，并定义了name和pub_date属性。


### 设计视图views

在模型定义之后，我们便可以在视图中引用模型，通常，根据视图参数检索数据，加载一个模板，并使用检索到的数据呈现模板。

	form django.shortcuts import render
	from .models import Person

	def book_archive(request, year):
		book_list = Person.objects.filter(birth_year = year)
		content = {% raw %}{'year': year, 'book_list': book_list}{% endraw %}
		return render(request, 'books/year_archive.html', context)

`views.py`文件包含了页面的业务逻辑。 `book_archive()`函数叫做视图。 这里还用到了year_archive.html模板。


### 设计链接URLs

优雅简洁的Url解析式是衡量高品质网站的重要标准。Django鼓励使用漂亮的URL设计。使用这些优美的URL,只需要建立URL和Python回调函数简单的映射关系。

	from django.urls import path
	from . import views

	ulrpatterns = [
		path('book/<int:year>', views.year_archive)
	]

`urls.py `指出了什么样的 URL 调用什么视图。 在这个例子中` books/xxxxx `将会调用 `year_archive() `这个函数。也就是说，在进入这个链接时，会返回视图函数的结果。


### 设计模板

Django拥有强大的模板功能，使用该模板能设计处强大的web页面
	
	{% raw %}
	{%block title%}Books for {{year}}{%endblock%}
	
	{{%block content}}
	<h1> Articles for {{years}} </h1>
	
	{% for book in book_list %}
		<p>{{book.name}}</p>
		<p>Published{{book.put_date | date: "F j, Y"}}</p>
	{% endfor %}
	{% end block %}
	{% endraw %}

`year_archive.html `是 html 模板。 使用带基本逻辑声明的模板语言，如{% raw %}{% for book in book_list %}{% endraw %}，它试图将函数返回的结果显示在网页上。


Django的这种结构我们称之为MTV模式：M代表模型(Model)，T代表模板(Template)，V代表视图(View)。这三个分别代表了三种不同功能的Web应用，各司其职，又彼此合作。


转自：[https://www.shiyanlou.com/courses/1127](https://www.shiyanlou.com/courses/1127)