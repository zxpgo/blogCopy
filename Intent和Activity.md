---

title: Intent和Activity
date: 2018-11-08 09:40:14
tags: APP开发
categories: APP开发

---

介绍如何使用intent来切换Activity。<!--more-->

### 打开一个项目遇到的问题

如果拿到一个项目，在本地打开查看，出现如下错误：

![](https://i.imgur.com/eQSdGyk.png)

解决办法：

报错的原因是：在SDK中没有找到Android API 23的信息。在项目中需要使用到Android API 23，但是本地没有下载。

在File->Setting,进入如下界面：

![](https://i.imgur.com/fTEuUzD.png)

错误二：

![](https://i.imgur.com/lPJoa4B.png)

解决方案：

![](https://i.imgur.com/gxyinmP.gif)

在此已经下载好，如果没有下载，点击23.0.2进行下载，然后应用。


问题三:

![](https://i.imgur.com/M4W42bS.png)


android studio 升级到了3.0 取消了apt 报了这个错

在最上级的builde.gralde增加谷歌库 解决问题 

	allprojects {
	    repositories {
	        google()//新增的
	        jcenter()
	    }
	}


如图操作：

![](https://i.imgur.com/lULXsSM.png)

### 新建Activity

新建Activity

![](https://i.imgur.com/Iu9UULj.png)

当我们创建新的Activity时，AndroidManifest.xml文件中，会自动添加我们创建的Activity,如下：

![](https://i.imgur.com/OZfTiB9.png)

Manifest文件相当于一个目录，总结了应用中重要的信息。当在设备上安装应用时，Android系统首先会查看此文件，然后再运行应用中的任何代码。

Manifest文件包含的内容：

- Java文件包名称（应用的唯一标识符）
- 应用组件
- 设置最低Android API:即指出该应用与哪些版本的Android平台兼容


### Intent

创建一个打开该应用内的Activity:

    public void openNumbersList(View View)
    {
        Intent numbersList = new Intent(this, NumbersActivity.class);
        startActivity(numbersList);
    }

    <TextView
        android:id="@+id/numbers"
        style="@style/CategoryStyle"
        android:background="@color/category_numbers"
        android:text="@string/category_numbers"
        android:onClick="openNumbersList"/>


创建一个新的intent消息，需要输入两个参数。第一个参数context,我们传入this，表示当前的Activity，通常Context将帮助intent更详细地了解应用的当前状态，这样，可以将第二个Activity放在当前Activity前面。第二个参数是传入一个类组件，这里是我们需要打开的Activity的名称，最后要加上.class扩展内容，以表明这是一个特定的Java类。

Intent有两种类型：

- [隐式intent](http://developer.android.youdaxue.com/guide/components/intents-filters#ExampleSend)
- [显式intent](http://developer.android.youdaxue.com/guide/components/intents-filters#ExampleExplicit)

当不知道用户再被设备上安装了哪些应用时，隐式intent带来这种灵活性，比如当打开应用中一个网站，可以先浏览器应用发送一个intent来显示相关网站，无论哪个浏览器处理该intent都没关系，它们可以显示网站。

有时候，我们需要完全确定当我我们发送一个intent时，特定的组件会收到该intent，例如上述代码，点击Numbers，会发送一个显式intent，要求打开性的Activity并显示该类别中的内容。

显式应用通常用在同一个应用中，尽量避免用显式intent来打开第三方应用，因为我们不清楚用户是否安装该应用。

显式intent内容包括：

- context
- Component(一般为Class或Activity)
- 可选URI,某些情况下，我们可能需要设置数据URI，以便其他Activity能够接收某些信息

隐式intent内容包括：

- Action
- Data URI
- 可选：Category\Components\Extras


![](https://i.imgur.com/WiFwIMb.png)

注意两种intent都可以处理intent Extrea。

### 修改Activity名称

当进入另一个Activity时，左上角的标题没有改变，如果先从左图变成右图，需要在AndroidManifest.xml文件中对应的Activity中添加label标签：
	
	<activity android:name=".NumbersActivity"
            android:label="@string/category_numbers"></activity>

在string.xml文件中已经定义了category_numbers字符串：

    <!-- Category name for the vocabulary words for numbers [CHAR LIMIT=20] -->
    <string name="category_numbers">Numbers</string>

![](https://i.imgur.com/Jvqpkrs.png)  ![](https://i.imgur.com/OQGYYS7.png)


### Android中的事件监听器

在XML文件中，我们定义了按钮点击行为。在XML布局中的视图添加android:onClick属性:

        android:onClick="openNumbersList"


我们进一步讲解如何在应用中运用到按钮代码逻辑。在后面我们仅修改Java代码，不对XML文件做出任何更改，来实现相同的按钮点击行为。

我们可以选在当特定视图上，发送特定的事件时，获得通知。在代码中，当这个特定事件发送时，我们可以知道。在某种程度上，可以将应用看成一只耳朵，可以监听感兴趣的信号，这些信号表示的就是特定的用户事件。一旦知道该事件发送了，就可以编写当用户与UI的特定部分互动时应该发生的情况。

为了事件这一流程，可以为视图创建一个事件监听器对象，指定该事件发送时应该出现什么情况。如果我们关心点击事件，就创建一个OnClickListener，并写调用onClick方法时应该发生的情况说明，这些说明可以是任何想要的内容。比如，当用户点击视图时，打开新的屏幕、更改视图的外观等等。创建OnClickListener后，最后一步将其附在我们想要监听点击操作的视图上。

在开发时，不需要知道具体细节，只要为我们关心的事件设置监听器，Android将复杂运行指定的代码。

每个监听器都提供了[单个回调方法](http://developer.android.youdaxue.com/guide/topics/ui/ui-events)，为该方法编写代码。

在`View.OnClickListener`中。 当用户触摸项目（处于触摸模式下）时，或者使用导航键或轨迹球聚焦于项目，然后按适用的“Enter”键或按下轨迹球时，将调用`onClick()`方法。


### 接口

类，其中包含完全实现的状态和方法。比如，TextView，具有状态：字体大小、颜色、位置；方法：获取内容、设置颜色等等。

接口，其中不含状态，但包含方法。比如：OnClick()，该方法的内部逻辑需要用户编写。


### 创建监听器

完成三步操作：

- 定义事件监听器以及事件发生时会出现的自定义行为
- 通过调用构造函数为事件监听器创建新的对象示例
- 为想要在其上面监听事件的视图附加该监听器

定义NumbersClickListener类
![](https://i.imgur.com/6SjS7DH.png)
创建对象示例
![](https://i.imgur.com/wDuTp44.png)
附到监听点击行为的视图上
![](https://i.imgur.com/jf65alk.png)

创建一个类，类文件名为：NumberClickListener.java：

	package com.example.android.miwok;
	
	import android.view.View;
	import android.widget.Toast;
	
	public class NumberClickListener implements View.OnClickListener{
	    @Override
	    public void onClick(View View) {
	        Toast.makeText(View.getContext(), "Open the list of numbers", Toast.LENGTH_SHORT).show();
	    }
	}


在MainActivity中使用：

        //创建一个实例
        NumberClickListener  clickListner = new NumberClickListener();

        //找到number目录对应的View
        TextView numbers = (TextView) findViewById(R.id.numbers);

        //将创建的实例附加到View
        numbers.setOnClickListener(clickListner);

一种简洁的方法创建监听器：

 	    //找到number目录对应的View
        TextView numbers = (TextView) findViewById(R.id.numbers);

        //将创建的实例附加到View
        numbers.setOnClickListener(new View.OnClickListener(){
            @Override
            public void onClick(View View) {
                //创建一个intent来打开numberActivity
                Intent numberIntent = new Intent(MainActivity.this, NumbersActivity.class);
                //启动新创建的activity
                startActivity(numberIntent);
                }
        });


如上，将定义类，实例化以及将实例化类附到View上三个步骤整合到一起。