---

title: Java数组、列表、循环和自定义类
date: 2018-11-09 08:33:14
tags: APP开发
categories: APP开发

---

Java基本知识，包括：数组、数组列表，for、while循环和自定义类<!--more-->

8种基本类型：

![](https://i.imgur.com/YaVcg0c.png)


注意：String不是基本类型，它是一个字符串对象。其中第一个字母`S`需要大写。

### 数组Array

创建一个数组：

	int [] shoeSizes = new int[3];


初始化数组元素：

	shoeSize[0] = 5;
	shoeSize[1] = 7;
	shoeSize[2] = 10;

获取数组的长度：

	shoeSize.length


创建一个数组，并初始化，通过输入到日志消息查看是否实现：

        String [] words = new String[10];
        words[0] = "one";
        words[1] = "two";
        words[2] = "three";
        words[3] = "four";
        words[4] = "five";
        words[5] = "six";
        words[6] = "seven";
        words[7] = "eight";
        words[8] = "nine";
        words[9] = "ten";
        Log.v("NumbersActivity","Word at index 0: " + words[0]);


### 数组列表ArrayList

![](https://i.imgur.com/9kC91Ix.png)

- 数组的大小是固定的，而数组列表的大小可以改变；
- 数组中可以存储原始数据类型（比如int,double）和对象类型，但数组列表只能存储对象类型；
- 数组不是类，不需要方法来访问和修改元素；数组列表被定义成类，意味着数组列表的每个实例都是对象，具有方法供我们访问和修改数据。

ArrayList类继承于AbstractList类，AbstractList是一个抽象类，抽象类是部分实现的类，表示某些方法已经实现，某些方法还是空着的。

Java中有很多其他类型的列表，比如链表、堆栈和向量等。


- 具体类：具有状态和所有方法的实现，比如ArrayList
- 抽象类：部分方法实现，比如AbstractList
- 接口：所有方法都没有实现，比如List

![](https://i.imgur.com/QCdOcJR.png)

创建一个数组列表：
	
	ArrayList<String> musicLibrary = new ArrayList<String>();

对数组列表添加元素:

	musicLibrary.add(“Yellow Submarine”);
	musicLibrary.add("Thriller");
	//在特定位置添加元素
	musicLibrary.add(0,"Blue Suede Shoes");

访问数组列表中的元素:

	musicLibrary.get(0);

删除数组列表中的元素：

	musicLibrary.remove(2);
	musicLibrary.remove("Thriller");

获取数组列表的长度：

	musiciLibrary.size();

下面列出了常见的[类型参数](https://docs.oracle.com/javase/tutorial/java/generics/types.html)： E - 元素（Java Collections Framework 大量用到） K - 键 N - 数字 T - 类型 V - 值 S、U、V 等 - 第 2 种、第 3 种、第 4 种类型。

创建数组列表的例子：

       ArrayList<String> words = new ArrayList<String>();
        words.add("one");
        words.add("two");
        words.add("three");
        words.add("four");
        words.add("five");
        words.add("six");
        words.add("seven");
        words.add("eight");
        words.add("seven");
        words.add("ten");
        Log.v("NumbersActivity","Words at index 0: " + words.get(0));


### 根据列表向布局添加剂TextView

        LinearLayout rootView = (LinearLayout) findViewById(R.id.rootView);
        TextView wordView = new TextView(this);
        wordView.setText(words.get(0));
        rootView.addView(wordView);

首先获取根视图，然后创建子视图，并设置子视图中的内容，最后，将子视图添加到父视图。


### 循环


#### while循环
	
	while (条件){
		循环体
	}


#### for循环


	for(初始化count; 条件; 更新count){
		循环体
	}


### 视图回收

内存资源是有限的，应该节约使用。如果我们要显示1000个联系人，创建1000个1000TextView，这样大大浪费了内存，因为显示在屏幕中的仅仅几个。如是考虑重复使用TextView。

视图回收：表示重复使用在屏幕上不再可见的视图。这里的视图是单行布局，包括LinearLayout、RelativeLayout。

采用ListView和ArrayAdapter根据需要在屏幕上显示的内容，创建阻隔的视图，即列表项行。

![](https://i.imgur.com/QV3VRHa.png)


如果某个视图在屏幕上不再可见时，我们可以通过更改相关数据重复使用该视图，及更改视图中的数据，例如TextView中的文本或ImageView中的图片，我们不需要浪费事件从头创建整个视图。我们将这些多徐的视图放入Screap Pile中并等着回收利用。

当用户正在滚动查看屏幕，想查看列表的顶部，看看最新的推文，ListView意识到需要快速创建更多的列表项行，当用户滚动的时候，似乎列表项一直都存在，它可以从Scrap Pile中选择一个视图，（如果存在的话）重复使用，而不是从头创建。我们通过使用Scrap Pile中的视图创建了更多的视图行，然后就只需更改视图中的数据，以便为相关列表项显示正确的信息。ListView同时还很智能地知道位于屏幕底部的视图已经被滚出屏幕，这些对用户来说已经不可见，可以之间添加到Scrap Pile中。当用户往上滚动时，我们就可以使用这钱位于屏幕底部的列表项行。所有这些的目标是尽量创建很少的列表项，因为从XML向Java对象隐形膨胀视图的代价很大。

![](https://i.imgur.com/kZ7FtQP.png)

当第一次将适配器与ListView相关联时，该ListView会询问“你想显示多少个项？”。ArrayAdapter知道这一信息，所以ListView对ArrayAdapter调用方法，它将在该方法中传入一个输入参数，即用户当前正在查看的列表位置。知道这一信息后，ArrayAdapter会查看数据的内部来源，如果列表是来源数据的话，它会查看列表，并获取相关信息。ArrayAdapter具有说明（Instructionss）来告诉ArrayAdapter如何创建列表项视图并返回ListView。当屏幕占满后，ListView将停止工作向ArrayAdapter寻求更多的列表项，列表项仅在需要是才创建。当视图被滚动离开屏幕后，它们就会被添加到这个Scrap Pile。然后，当我们请求新的列表项时，我们可以通过将这些视图在返回到ArrayAdapter重复使用它们。Listview将请求列表中特定位置的视图，同时传入之前用户的视图，例如，如果用户请求位置6的视图，会将可重复使用的以前的视图传给ArrayAdapter，ArrayAdapter可以通过在回收的视图中放入数据，使用回收的视图，比如，通过调用TextView setText()方法来更改这里的内容。

![](https://i.imgur.com/YldFuZr.png)

	ArrayAdapter itemsAdapter = new ArrayAdapter(this, android.R.layout.simple_list_item_1, words);
    ListView listView = (ListView) findViewById(R.id.list);
    listView.setAdapter(itemsAdapter);  


首先，使用new关键字以及具有三个输入参数的构造函数创建了一个ArrayAdapter对象，将其存储在itemsAdapter变量中，其数据类型是ArrayAdapter。第一个参数是Context，this指代NumbersActivity；第二个参数是列表项布局文件，这里指定为android.R.layout，因为Android框架实际上预定义了一个XMK布局文件，叫做simple_list_item_1，我们可以直接使用Android预定义的这个文件。最后一个参数是对象列表，即ArrayAdapter的数据来源。


前面使用ArrayList<String>创建字符串数组列表，讨论了泛型。因为ArrayList是个泛型类，我们可以重写成ArrayList<E>，E表示它是个泛型类
，E是类型参数或占位符，可以替换为具体的对象类型。例如，当我们创建ArrayList实例时，可以替换为字符串。创建一个泛型类，可以让我们稍后判断他将存储的数据类型，而不用创建多个具体的类，例如StringList类，IntegerList类等等。

第二行，找到布局中的ListView。

第三行，setAdapter方法输入参数是一个ListAdapter，ListAdatper是一个接口，由BaseAdapter抽象类实现某些方法，同时为其他方法保留为抽象方法，这时需要ArrayAdatper，ArrayAdapter是一个具体的类，所有方法都实现了。我们可以创建一个ArrayAdapter对象实例，并在我们的应用中使用。因此，ArrayAdapter是一个listAdapter，可以将itemsAdapter变量传入为listView.setAdapter()方法调用的输入参数。

![](https://i.imgur.com/oESlPBy.png)

使用下拉菜单（Spinner）、网格视图（GridView）时，都需要使用到适配器ArrayAdapter。

![](https://i.imgur.com/XSCkvuw.png)

## 构造列表项布局

	<?xml version="1.0" encoding="utf-8"?>
	<LinearLayout
	    xmlns:android="http://schemas.android.com/apk/res/android"
	    xmlns:tools="http://schemas.android.com/tools"
	    android:layout_height="match_parent"
	    android:layout_width="match_parent"
	    android:orientation="vertical"
	    android:padding="16dp">
	
	    <TextView
	        android:id="@+id/default_text_view"
	        android:layout_width="match_parent"
	        android:layout_height="wrap_content"
	        tools:text="one"/>
	
	    <TextView
	        android:id="@+id/miwok_text_view"
	        android:layout_width="match_parent"
	        android:layout_height="wrap_content"
	        tools:text="一"/>
	
	</LinearLayout>

### tools命名空间

在XML中，命名空间使元素之间没有名称冲突。`xmlns:android`是一个命名空间，我们已经在Android中使用的属性，例如android:layout_height等等以`android:`开头。

上面代码还添加一个命名空间，叫做tools命名空间

	    xmlns:tools="http://schemas.android.com/tools"

上面这行代码表示将tools前缀映射到这个命名空间`http://schemas.android.com/tools`。任何以tools开头的元素或属性都属于tools命名空间的一部分。

	tools:text="one"

利用tools命名空间text来设置内容，在Android Studio的设计预览中将会显示此文本，但不会在运行时出现，这样为我们开发提供帮助。


## 自定义类

	package com.example.android.miwok;
	
	/**
	 * 创建了一个Wrod类，用户可以使用
	 * 其包含一个默认翻译和一个Miwok翻译对于同一个单词
	 * */
	public class Word {
	    private String mDefaultTranslation;
	    private String mMiwokTranslation;
	
	    public Word(String defaultTranslation, String miwokTranslaton)
	    {
	        mDefaultTranslation = defaultTranslation;
	        mMiwokTranslation = miwokTranslaton;
	    }
	
	    /**
	     * Get the Miwok Translation
	     * @return
	     */
	    public String getMiwokTranslation() {
	        return mMiwokTranslation;
	    }
	
	    /**
	     * Get the defualt translation
	     * @return string
	     */
	    public String getDefaultTranslation(){
	        return mDefaultTranslation;
	    }
	}

根据自定义的类Word，可以创建Word对象数组列表，并传递给ArrayAdapter。

    ArrayList<Word> words = new ArrayList<Word>();
    words.add(new Word("one","一"));
    words.add(new Word("two","二"));

## 自定义ArrayAdapter


	package com.example.android.miwok;
	
	import android.content.Context;
	import android.view.LayoutInflater;
	import android.view.View;
	import android.view.ViewGroup;
	import android.widget.ArrayAdapter;
	import android.widget.TextView;
	
	import java.util.ArrayList;
	
	public class WordAdapter extends ArrayAdapter<Word> {
		//构造函数
	    public WordAdapter(Context context, ArrayList<Word> objects) {
	        super(context, 0, objects);
	    }
	    /**@Override
	    public View getView(int position,  View convertView,  ViewGroup parent)
	    {
	        return super.getView(position, convertView, parent);
	    }*/
	    @Override
	    public View getView(int position, View convertView, ViewGroup parent) {
	        // Check if the existing view is being reused, otherwise inflate the view
	        View listItemView = convertView;
	        if(listItemView == null) {
	            listItemView = LayoutInflater.from(getContext()).inflate(
	                    R.layout.list_item, parent, false);
	        }
	
	        // Get the {@link AndroidFlavor} object located at this position in the list
	        Word currentWord = getItem(position);
	
	        // Find the TextView in the list_item.xml layout with the ID version_name
	        TextView defaultTextView = (TextView) listItemView.findViewById(R.id.default_text_view);
	        // Get the version name from the current AndroidFlavor object and
	        // set this text on the name TextView
	        defaultTextView.setText(currentWord.getDefaultTranslation());
	
	        // Find the TextView in the list_item.xml layout with the ID version_number
	        TextView miwokTextView = (TextView) listItemView.findViewById(R.id.miwok_text_view);
	        // Get the version number from the current AndroidFlavor object and
	        // set this text on the number TextView
	        miwokTextView.setText(currentWord.getMiwokTranslation());
	
	        // Return the whole list item layout (containing 2 TextViews)
	        // so that it can be shown in the List
	        return listItemView;
	    }
	
	}


在

    WordAdapter adapter = new WordAdapter(this,words);
    ListView listView = (ListView) findViewById(R.id.list);
    listView.setAdapter(adapter);





