---

title: Android开发实践
date: 2018-10-30 19:40:14
tags: APP开发
categories: APP开发

---
Anddroid Studio(AS)安装和使用教程。<!--more-->

## Android Studio安装

分为两步：

- 安装JDK(Java Development Kit（Jave开发工具包）)：JDK让电脑读取并运行Java语言
- 安装Android Studio

Android Studio是开发APP的工作环境，没有JDK的支持，它是无效的。

## 制作卡片

分为三个步骤：

- 选择合适的视图
- 确定视图的位置
- 选择合适的风格


代码：
	
	<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
	    xmlns:tools="http://schemas.android.com/tools"
	    android:layout_width="match_parent"
	    android:layout_height="match_parent"
	    tools:context=".MainActivity">
	
	    <ImageView
	        android:layout_width="match_parent"
	        android:layout_height="match_parent"
	        android:src="@drawable/androidparty"
	        android:scaleType="centerCrop"/>
	    <TextView
	        android:text="Happy Birthday Ben!"
	        android:layout_width="wrap_content"
	        android:layout_height="wrap_content"
	        android:layout_marginTop="16dp"
	        android:layout_marginLeft="16dp"
	        android:textSize="36sp"
	        android:textColor="@android:color/white"
	        android:fontFamily="san-serif-light" />
	    <TextView
	        android:text="From Layin"
	        android:layout_width="wrap_content"
	        android:layout_height="wrap_content"
	        android:layout_alignParentBottom="true"
	        android:layout_alignParentRight="true"
	        android:paddingBottom="16dp"
	        android:paddingRight="16dp"
	        android:textSize="36dp"
	        android:textColor="@android:color/white"
	        android:fontFamily="san-serif-light"/>
	</RelativeLayout>

注意：

- 视图的顺序，决定了视图放置的位置。如果，图片视图放置在最后，文字视图将会被覆盖，无法看到。
- 图片等比放大，充满整个屏幕，使用`centerCrop`值

### 公司简介

	<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
	    xmlns:tools="http://schemas.android.com/tools"
	    android:layout_width="match_parent"
	    android:layout_height="match_parent"
	    tools:context=".MainActivity">
	
	    <RelativeLayout
	        android:layout_width="match_parent"
	        android:layout_height="200dp"
	        android:id="@+id/Image_Relative">
	
	
	        <ImageView
	            android:layout_width="match_parent"
	            android:layout_height="200dp"
	            android:src="@drawable/androidparty"
	            android:scaleType="centerCrop"/>
	        <TextView
	            android:text="南京维达科技有限公司"
	            android:layout_width="wrap_content"
	            android:layout_height="wrap_content"
	            android:layout_marginTop="16dp"
	            android:layout_centerHorizontal="true"
	            android:layout_centerVertical="true"
	            android:textSize="46sp"
	            android:textColor="@android:color/white"
	            android:fontFamily="san-serif-light" />
	
	
	    </RelativeLayout>
	
	    <RelativeLayout
	        android:layout_width="match_parent"
	        android:layout_height="wrap_content"
	        android:layout_below="@id/Image_Relative"
	        android:layout_margin="16dp">
	        <TextView
	            android:id="@+id/address_text"
	            android:text="地址：南京市江宁区东南大学"
	            android:layout_width="wrap_content"
	            android:layout_height="wrap_content"
	            android:paddingBottom="16dp"
	            android:paddingRight="16dp"
	            android:textSize="36dp"
	            android:fontFamily="san-serif-light"/>
	        <TextView
	            android:id="@+id/phone_text"
	            android:layout_below="@id/address_text"
	            android:text="电话：18212345678"
	            android:layout_width="wrap_content"
	            android:layout_height="wrap_content"
	            android:textSize="36dp"
	            android:paddingBottom="16dp"
	            android:fontFamily="san-serif-light"/>
	        <TextView
	            android:id="@+id/introduction_text"
	            android:layout_below="@id/phone_text"
	            android:text="公司介绍：上海若讷信息科技有限公司是一家专注于云计算实用化的科技公司，公司成员大部分来自华为、阿里巴巴等知名科技公司。下一个十年将是云计算不断深入社会各行各业的过程，若讷信息技术瞄准这个目标，与其他同行一起将推动云计算的应用，提升云计算在政府、企事业单位、厂矿、小微企业、甚至是个人用户客户的渗透率。"
	            android:layout_width="wrap_content"
	            android:layout_height="wrap_content"
	            android:textSize="28dp"
	            android:fontFamily="san-serif-light"/>
	
	        </RelativeLayout>
	
	
	</RelativeLayout>

