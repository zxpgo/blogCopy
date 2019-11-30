---

title: 使用CurosrLoader加载数据
date: 2018-12-09 18:52:14
tags: APP开发
categories: APP开发

---

使用CursorLoader在一个后台线程上从数据库中加载数据，这将保持我们应用的响应性。<!--more-->


### CursorAdapter简介

前面了解使用ListView可以很方便地显示一列信息。而要在ListView中填充信息，需要使用到适配器adapte，所以基本上来说适配器负责创建数据源中每只宠物的列表项试图，并将它们交给ListView以显示在屏幕上。


CursorAdapter是一个抽象类，我们定义一个类并继承该类。

扩展类包含一个构造函数，该构造函数使用超类构造函数来初始化自己。

作为开发者，我们需要填入newView()方法，为游标所指的当前行创建并返回一个空白的列表项。

同时，我们还需要填入bindView()方法，以从游标中的当前行读取数据，将它设置到这个列表项试图中的试图上。

具体函数如下：

![](https://i.imgur.com/amG3Am5.png)

CursorAdapter类会帮我们处理一些逻辑，例如将游标移动正确的位置。
