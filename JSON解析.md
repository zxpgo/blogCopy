---

title: JSON解析
date: 2018-11-23 08:40:14
tags: APP开发
categories: APP开发

---

Json解析，以及字符串处理，日期和时间转换，小数点处理，switch语句等等<!--more-->

### JSON概述

[JSON](http://www.json.org/)是JavaScript Object Notation的缩写。最初设计JSON是为了促进网络的有效沟通，因此以JavaScript开头，与JavaScritp并无关系。它是一组用户组织数据的规则，是一种完全独立的编程语言，因此可以选择任何编程语言来解释JSON，包括用于构建Android应用的Java。

JSON语法结构的核心是：键值对

![](https://i.imgur.com/Voulkxh.png)

- 大括号: 你会发现，JSON 示例中的所有内容都封闭在 一组大括号内，这表示可将整个内容视为一个 对象。

- 逗号: 键和值以冒号分隔，同时每个键/值对以逗号分隔。 JSON 支持可在大多数编程语言中找到的基本数据类型， 如，数字、字符串、布尔值、数组和对象。 可在以下示例中看到其中几种数据类型：

- Json解析
	- 第一个 "size" 键的值为 9.5，由此可以判断出 该值属于数值类型，因为值周围没有引号。

	- 第二个 "wide" 键的值为 true，不带引号。这 表示该值属于布尔值。如果数字周围有引号，则表示其为字符串。也就是我们在第三个键值对中找到的字符串。

	- 第三个 "country-of-origin"键的值为 "usa"。

	- 第四个键是 "style"，其值表示为对象，因为两侧有大括号。在 style 对象内部，我们可以找到两个嵌套键/值对：一个为 catergories，一个为color。其实 catergories 的值是一个数组，“boot” 和 “winklepicker”被包含在中括号内组成一个数组。 按照这种方式，通过使用对象和数组，可以嵌套几组值， 随后便可在这些容器中放入不同的数据类型。


键可以引用原始数据类型，如：布尔，字符串，数字。它们也能以对象和数组形式表示更复杂的数据。键可以指向其他嵌套对象或其他对象的数组。

GeoJson是Json的一个特殊格式，进过定制可显示地理信息。 

JSON代码格式化网站：[jsonprettyprint](http://jsonprettyprint.com/) ，[jsonformatter,推荐](https://jsonformatter.curiousconcept.com/)

地震数据API：[开发者文档](https://earthquake.usgs.gov/fdsnws/event/1/)


### JSON解析

从Json中获取感兴趣的内容：[教程](http://www.tutorialspoint.com/android/android_json_parser.htm)



### 字符串分割

在 String 对象上调用的其他方法：

- length() - 返回字符串中的 字符数

- contains(CharSequence cs) - 根据 原始字符串中是否包含输入 CharSequence （或输入 String）返回 true 或 false

- indexOf(String string) - 返回输入 String 首次在原始 String 中出现的位置的索引，如果 原始 String 中不存在输入 String，则返回 -1

- split(String string) - 根据输入 String 中 指定的位置对原始 String 进行拆分， 并返回包含拆分后 String 部分的数组。

- substring(int start, int end) - 返回 一个新的 String，该 String 为从开始索引到结束索引 之间的内容（包括开始索引但不包括结束索引）。

### 日期和时间转换


。有 一个很奇妙的名为 SimpleDateFormat 的类，知晓所有关于时区的信息， 以及知晓如何按照世界的不同地区书写日期，它会 帮你处理这些难题。

提供一种所需的时间格式，如："yyyy-MM-dd HH:mm:ss"

在时间格式语法中，字符具有特殊含义，这一点 在此表中进行了详尽阐述。

"y" 代表年，"yyyy" 代表 4 位数的年份，如 2016。

"M" 代表月，"MM" 代表 2 位数的月份，如 03。

"d" 代表日，"dd" 代表 2 位数的日期，如 10。

"H" 代表时。

"m" 代表分。

"s" 代表秒。

所有未在特殊字符表中列出的字符 都将在输出字符串中直接使用。例如，如果时间格式字符串 包含冒号、短划线或逗号，则输出字符串 也将在相应位置直接包含相同的标点符号。

下面是 1463159138711 毫秒的示例。我们使用 "yyyy-MM-dd HH:mm:ss a" 格式创建一个 SimpleDateFormat 对象，然后 传入一个以时间 1463159138711 毫秒初始化的 Date 对象，随后该日期的格式便会被设置为 "2016-05-13 12:07:46 PM"。

代码如下。首先通过调用 Date 构造函数将 以毫秒为单位的时间转换为 Date 对象。

	long timeInMilliseconds = 1454124312220L;
	Date dateObject = new Date(timeInMilliseconds);
然后便可初始化 SimpleDateFormat 实例，并将其配置为 根据指定格式提供更易懂的表示。

	SimpleDateFormat dateFormatter = new SimpleDateFormat("MMM DD, yyyy");
	String dateToDisplay = dateFormatter.format(dateObject);

执行完这 4 行代码后，dateToDisplay 的值 便会呈现为格式更为美观的日期："Jan 29, 2016"。

如果 Android Studio 不识别这些类，你可能需要 在文件顶部手动导入它们。

	import java.text.SimpleDateFormat;
	import java.util.Date;


### DecimalFormat

如果我们想把数字设置为统一格式，比如都保留两位小数。

Android 中有一个类可以帮到我们！这个类叫做 DecimalFormat 类。顺便提一句， 还有 NumberFormat 类可用于处理所有 类型的数字的格式，但这是一个抽象类， 而 DecimalFormat 是一个具体类，我们可以与之交互。

要初始化 DecimalFormat 对象，你要传入一个 与已定义模式相匹配的格式字符串（与 SimpleDateFormat 类 的 year、month、date 符号类似）。特殊符号具有 特殊的意义（如 0 表示数字的占位符、# 也表示 数字，但是不显示前导零）。在 DecimalFormat 文档页面的表中有完整的列表。

以下是我们使用 DecimalFormat 格式方法的示例。

	 DecimalFormat formatter = new DecimalFormat("0.00");
	 String output = formatter.format(2.3234);//传入的是double类型

由于我们指定 DecimalFormat 显示三位数字 ("0.00")， 因此，对 2.3234 这样的数字进行格式化会将其缩短到 两位小数，从而输出字符串值 "2.32"。

如果未自动导入此类，你可能需要在文件顶部 导入这个类。

	 import java.text.DecimalFormat;


### Switch语句

示例1：

![](https://i.imgur.com/LWe4q89.png)

示例2：

![](https://i.imgur.com/B2qMM0A.png)


如果进入case 'D',则会运行到case 'F'中的内容，这是因为只有遇到break，程序才会停止。


### URL处理

提取Json中的URL，并点击列表通过浏览器打开链接。[教程](https://classroom.udacity.com/courses/ud843/lessons/1335cf7d-bb4f-48c6-8503-f14b127d2abc/concepts/9e3938ea-8018-42ba-8753-1f4b4f5f29c8)


	  // 创建一个将地震列表视为输入的新适配器
	  final EarthquakeAdapter adapter = new EarthquakeAdapter(this, earthquakes);
	
	  // 在 {@link ListView} 上设置适配器
	  // 以便可以在用户界面中填充列表
	  earthquakeListView.setAdapter(adapter);
	
	  earthquakeListView.setOnItemClickListener(new AdapterView.OnItemClickListener() {
	     @Override
	     public void onItemClick(AdapterView<?> adapterView, View view, int position, long l) {
	         // 查找单击的当前地震
	         Earthquake currentEarthquake = adapter.getItem(position);
	
	         // 将字符串 URL 转换为 URI 对象（以传递至 Intent 中 constructor)
	         Uri earthquakeUri = Uri.parse(currentEarthquake.getUrl());
	
	         // 创建一个新的 Intent 以查看地震 URI
	         Intent websiteIntent = new Intent(Intent.ACTION_VIEW, earthquakeUri);
	
	         // 发送 Intent 以启动新活动
	         startActivity(websiteIntent);
	     }
	  });


[Google API](https://developers.google.com/apis-explorer/#p/)