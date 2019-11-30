---

title: Java面向对象编程
date: 2018-10-31 21:40:14
tags: APP开发
categories: APP开发

---

Java一般语法，以及在Android开发中的知识点和注意事项。<!--more-->

	private int calculatePrice(int quantity)
	{}


private是访问修饰符，比如：public或private。这意味着calulatePrice方法只能被MainActivity里的其他方法调用，之外的部分无法调用它。如果声明位public，则所有代码都可以访问它。

int为返回类型。返回一个整型。

calculatePrice为函数名。

## 资源

资源放在文件`res`目录下，资源包括：图片、布局、

访问资源:

- 在Java代码中： `R.类型名.资源名`，当编译项目时，Android中有一个AAPT的工具，会产生一个R类，（即一个叫R.java的文件）。

- 在XML代码中： `@类型名/资源名`

![](https://i.imgur.com/lDwfyRH.png)

Java文件中有如下代码：

 	setContentView(R.layout.activity_main);

该行代码表示我们引用了activity_main布局文件，展示了MainActivity怎么加载XML文件。


## 对象

XML中的视图，比如TextView、imageView实际上是通过应用里的Java对象控制的。

一个TextView对象有属性:

- 字体大小
- 字体颜色
- 字体

方法：

- setText()
- getText()
- setTextSize()
- getTextSize()
- onDraw()

可以通过查看TextView.java文件，来指导TextView类有哪些属性和方法。
	

TextView类的简化版：

	/**
	 * Displays text to the user.
	 */
	public class TextView extends View {
	 
	    // String value
	    private String mText;
	 
	    // Text color of the text
	    private int mTextColor;
	    
	    // Context of the app
	    private Context mContext;
	 
	    /**
	     * Constructs a new TextView with initial values for text and text color.
	     */
	    public TextView(Context context) {
	      mText = "";
	      mTextColor = 0;
	      mContext = context;
	    }
	 
	    /**
	     * Sets the string value in the TextView.
	     *
	     * @param text is the updated string to be displayed.
	     */
	    public void setText(String text) {
	        mText = text;
	    }
	 
	    /**
	     * Sets the text color of the TextView.
	     *
	     * @param color of text to be displayed.
	     */
	    public void setTextColor(int color) {
	        mTextColor = color;
	    }
	 
	    /**
	     * Gets the string value in the TextView.
	     *
	     * @return current text in the TextView.
	     */
	    public String getText() {
	        return mText;
	    }
	 
	    /**
	     * Gets the text color of the TextView.
	     *
	     * @return current text color.
	     */
	    public int getTextColor() {
	        return mTextColor;
	    }
	}
	


### 创建对象


通过调用构造函数来初始化一个对象

	TextView priceTextView = new TextView(content)

其中TextView(context)是调用构造函数，context是一个语境，可以帮助获取应用资源以及其他与语境有关的内容。

通过工厂方法来创建一个对象

	MediaPlayer player = MediaPlayer.create(context, R.raw.song);

其中create是MediaPlayer的工厂方法，得到一个名字为player的MediaPlayer对象。

构造函数创建对象使用较多，而工厂方法创建对象使用较少。至于使用哪一种方法，通过查看开发手册得知。


### 对象调用方法

调用格式：
	
	对象名.方法名(输入参数);

	titleTextView.setText("News");
	titleTextView.setTextSize(18);
	titleTextView.setTextColor(Color.RED);


在类内部调用和类外部调用的区别：

类内部可以访问私有属性和私有方法，而外部只能访问类的公开方法和属性。另外，类内部访问不需要添加对象名，因为默认是this。而类外部访问需要添加对象名，如下：

![](https://i.imgur.com/ZmeVedu.png)

### 继承

Java代码中，开始处有如下代码：

	public class MainActivity extends AppCompatActivity {
	
	    int scoreTeamA = 0;
	    int scoreTeamB = 0;
	    @Override
	}


`extends AppCompatActivity`表明类`MainActivity`继承了`AppCompatActivity`的属性和方法。

如下方法，在MainActivity中没有定义，因此是继承自AppCompatActivity:

	 setContentView(R.layout.activity_main);
	 TextView scoreView = (TextView) findViewById(R.id.team_b_score);

下面的代码，在方法声明前添加`@override`表明这是**重载函数**，即该函数在`AppCompatActivty`类中已经定义，但是在这里重新定义，做出一些修改。

	 @Override
	    protected void onCreate(Bundle savedInstanceState) {
	        super.onCreate(savedInstanceState);
	        setContentView(R.layout.activity_main);
	    }

不同视图之间存在一些共性，比如，都具有大小、可以隐藏、位置等等。为了减少代码量，可以将这些相同的内容提出，放到一个新类View中，然后这些视图继承于View类。具体如下：

![](https://i.imgur.com/zUKDko6.png)

这样，TextView类中只要编写与View类不同的部分，以及不希望继承View的内容重新编写即可。这样，TextView、ImageView、Button继承于View。

### 通过资源编号获取视图对象

	onCreate(R.layout.activity_main)

方法，是根据XML文件中的布局建立一个视图层次结构，如下：

![](https://i.imgur.com/QCEbhdB.png)

具体原理如下：

![](https://i.imgur.com/6mbZzN8.png)

	findViewByid(int id)

会找到传入的id相对应的View。findViewByid方法编程视图结构，找到视图层次结构中的某个View。该方法的返回值是View类型的对象。

findViewByid()方法返回的View类型，通常我们要使用具体的视图比如：TextView。因此需要进行强制转换：

	   private void displayForTeamB(int score)
	    {
	        TextView scoreView = (TextView) findViewById(R.id.team_b_score);
	        scoreView.setText(String.valueOf(score));
	    }

其中`(TextView)`就是将View类型强制转换成TextView类型。如果不强制转换的话，无法调用`setText()`方法，因此View类型不具有该方法，只有TextView对象具有该方法。


### 视图中读取数据

**Getter 和 Setter 方法概述**

已经使用过方法了，例如 setText 和 setImageResource。这些叫做 setter 方法，因为它们负责修改或操控视图的某个值（例如视图存储的文本或图片）。按照惯例，它们以“set”开头。

还有一种类别的方法，叫做*getter*方法，它们的唯一目的是从视图中“获取”值，例如获取视图的当前文本。按照惯例，它们以“get”开头。在下个练习中，我们将用到一些 getter 方法。

### 日志

先Android日志中写消息，在代码中写出如下类似的语句：

	Log.i("EnterpriseActivity.java", "Captain's Log, Stardate 43125.8. We have entered a spectacular binary star system in the Kavis Alpha sector on a most critical mission of astrophysical research.");

第一个参数是日志语句来自的**类的名称**，第二个参数是想要显式地文字。


这里，我们使用了 Log.i()，表示“信息”级别的日志。其他级别的选项如下所示：

- e(String, String)（错误）
- w(String, String)（警告）
- i(String, String)（信息）
- d(String, String)（调试）
- v(String, String) (详情)


读取TextView的内容，并输入到日志中：

	 TextView textViewItem3 = (TextView) findViewById(R.id.menu_item_3);
     String menuItem3 = textViewItem3.getText().toString();
     Log.v("MainActivity", menuItem3);


![](https://i.imgur.com/OG3LyWU.png)


### 复选框

组件：

    <CheckBox
		android:id="@+id/whipped_cream_checkbox"	
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Whipped Cream"
        android:layout_marginLeft="16dp"
        android:paddingLeft="16dp"
        android:textSize="26sp"/>

Java代码中获取CheckBox的状态，并在写入日志消息中：

  		CheckBox whippedCreamCheckBox = (CheckBox) findViewById(R.id.whipped_cream_checkbox);
        boolean hasWhippedCream =  whippedCreamCheckBox.isChecked();
		Log.v("MainActivity","The checkbox is " + hasWhippedCream);

### 布尔类型

声明：

	boolean hasWhippedCream = false;

变量名开头一般为“is”或“has”，这样，看变量名就知道该变量为布尔类型。


### 滚动条


在原来的代码的基础上，最外层套一个ScrollView视图即可，具体如下：

对于线性布局

	<ScrollView
		xmlns:android="http://schemas.android.com/apk/res/android"
    	xmlns:tools="http://schemas.android.com/tools"
    	android:layout_width="match_parent"
    	android:layout_height="match_parent">
	
		<LinearLayout>
			...
		</LinearLayout>
	
	</ScrollView>

对于相对布局

	<ScrollView
		xmlns:android="http://schemas.android.com/apk/res/android"
    	xmlns:tools="http://schemas.android.com/tools"
    	android:layout_width="match_parent"
    	android:layout_height="match_parent">
	
		<RelativeLayout>
			...
		</RelativeLayout>
	
	</ScrollView>



### 输入框

XML中

	<EditText
            android:id="@+id/name_edit_view"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:hint="Name"
            android:inputType="textCapCharacters"
            android:layout_marginTop="16dp"/>


Java代码中：

    EditText nameEditText = (EditText) findViewById(R.id.name_edit_view);
	String name = nameEditText.getText().toString();

EditText继承于TextView，所以它具有getText方法。

在此，getText()返回类型为EditText，所以需要使用`.toString`将其转换为字符串类型。


### if/else

	if (numberOfGuests < 6) {
	     tip = 50;
	} else if (numberOfGuests < 20) {
	     tip = 100;
	} else {
	     tip  = 1000;
	}


### Toast

Toast用来在屏幕上显式消息。比如，如果预定的咖啡数量不能小于1，当数量等于之后，用户再点击减少时，在屏幕显示“预定咖啡数量不能小于1”。如下：

	if (quantity < 1) 
	{
    	//通过toast,给出一个错误信息
        Toast.makeText(this, "You cannot have less than 1 coffees.", Toast.LENGTH_LONG).show();
       	//退出这个方法
        return;
    }


创建Toast消息，需要调用Toast.makeText方法，并且输入三个参数，输入context指向这个activity。第二个参数是在屏幕上显示的消息内容。第三个参数指定它在屏幕显示的时间。最后这个对象调用show()方法来显示该消息。


### intent消息

intent消息包含两个主要内容：

- 动作
- 数据

还可以指定额外消息：

- 种类
- 成分
- 其他

这些内容可以让Android决定哪个应用来处理该intent消息。


比如，电话intent消息：

	ACTIO_DAIL
	tel:2125551212

注意数据格式采用：URI（uniform resource identifier），这是构建数据的标准方法。这样才可以正确处理该消息。

添加一个邮件intent:


        Intent intent = new Intent(Intent.ACTION_SENDTO);
        intent.setData(Uri.parse("mailto:"));//仅仅邮件APP处理该消息
        intent.putExtra(Intent.EXTRA_SUBJECT, "Just Java order for " + name);//邮件标题
        intent.putExtra(Intent.EXTRA_TEXT, priceMessage);//邮件内容
        if (intent.resolveActivity(getPackageManager()) != null) //只有当APP接受了该消息，才开始启动
            startActivity(intent);



### 本地化

`res->values`该目录下是默认的语言文件。如果希望能根据系统语言来适配APP的语言，我们需要添加不同语言的资源文件。具体如下：

![](https://i.imgur.com/oKFTJoU.png)

其中values-es表示西班牙语资源文件，values-fr表示法国语言资源文件。语言资源文件的格式如下：

	<resources>
	    <string name="app_name">My Application</string>
	</resources>

中间可以随机添加字符串。name表示该字符串的名字。在XML文件中，可以如下调用它：

	android:text = "@string/name"


在Java代码中，调用：

	getString(R.string.name);

这样，就不需要再XML和Java代码中出现字符串，字符串全部放在资源文件中。

在有些时候，可能不想翻译一部分文本字符串。例如，我们想要创建一个字符串，然后，留出一个空隙来添加变量。这个时候，需要使用`xliff`标签来标出不被翻译的部分。翻译用户的名字没有意义，但是翻译Name是有意义的。xliff标签给出一个名字的ID,在xliff标签中可以放入不用翻译的字符串，使用`%s`来代替，稍后会被id为name的字符串取代。具体如下：
	
	//resource
	<string name = "order_summary_name">Name: <xliff:g id="name" example"Amy">%s</xliff:g></string>
	//java
	String priceMessage = getString(R.string.order_summary_anme, name);

以表格的形式查看`resource`文件中的语言种类：

![](https://i.imgur.com/qMFudIH.gif)



### 样式

styles.xml文件中定义样式：

	<!--style for a header TextView-->
    <style name="CustomText">
        <item name="android:textSize">26sp</item>
        <item name="android:textColor">#4527A0</item>
        <item name="android:textStyle">italic</item>
    </style>

布局文件中调用styles.xml文件中的样式：

	 <TextView
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:layout_margin="16dp"
            android:text="TOPPINGS"
            style="@style/CustomText" />


调用的格式如：

	style="@style/样式名字"


### 主题

styles.xml文件中最开始的代码：

    <style name="AppTheme" parent="Theme.AppCompat.Light.DarkActionBar">
        <!-- Customize your theme here. -->
        <item name="colorPrimary">#009688</item>
        <item name="colorPrimaryDark">#00796B</item>
        <item name="colorAccent">#536DFE</item>
    </style>


这是用来设置主题的风格，所有的视图都会使用该样式。可以改变应用栏或状态栏的颜色等特征。

样式被应用于一个单一的View，而主题被应用到活动或者应用中的所有View。

Android中有一些自带的标准系统主题。

![](https://i.imgur.com/q6Bnx3C.png)

定义分别如下：

![](https://i.imgur.com/lAVMYxl.png)

在此，使用上述第二种主题。在styles.xml文件中，使其称为父主题：

   <style name="AppTheme" parent="android:Theme.Material.Light">
        <!-- Customize your theme here. -->
        <item name="android:colorPrimary">#80DEEA</item>
        <item name="android:colorPrimaryDark">#000000</item>
        <item name="android:colorAccent">#536DFE</item>
        <item name="android:windowBackground">#E0F7FA</item>
    </style>

还可以在上述代码中，设置不同区域的颜色，这样会覆盖父主题的默认值。不同区域的名称：

![](https://i.imgur.com/FWwFUDZ.png)

### 应用清单

每个应用的根目录中都必须包含一个`AndroidManifest.xml`文件（且文件名精确无误）。 清单文件向 Android 系统提供应用的必要信息，系统必须具有这些信息方可运行应用的任何代码。


 	<application
        android:allowBackup="true"
        android:icon="@mipmap/ic_launcher"
        android:label="@string/app_name"
        android:roundIcon="@mipmap/ic_launcher_round"
        android:supportsRtl="true"
        android:theme="@style/AppTheme">
        <activity android:name=".MainActivity">
            <intent-filter>
                <action android:name="android.intent.action.MAIN" />

                <category android:name="android.intent.category.LAUNCHER" />
            </intent-filter>
        </activity>
    </application>

 `android:theme="@style/AppTheme"`指出了所有活动都使用`AppTheme`主题，如果将该语句放到`MainActivity`活动中，这仅该活动使用`AppTheme`主题。由于上述代码中就一个活动，因此放在哪里都没关系。其中`AppTheme`主题风格在`styles.xml`文件的开头定义的。如下:

	<application
        android:allowBackup="true"
        android:icon="@mipmap/ic_launcher"
        android:label="@string/app_name"
        android:roundIcon="@mipmap/ic_launcher_round"
        android:supportsRtl="true"
       
        <activity 
				android:name=".MainActivity"
 				android:theme="@style/AppTheme">>
            <intent-filter>
                <action android:name="android.intent.action.MAIN" />

                <category android:name="android.intent.category.LAUNCHER" />
            </intent-filter>
        </activity>
    </application>


### Android-找不到R类问题的解决方法

![](https://i.imgur.com/ASMbTfu.png)

#### 重新检查资源文件中XML文件的有效性 

如果最近一次编译时未生成R.java文件，则会引起项目资源引用错误，通常这是由于布局文件中的拼写错误导致的。因无法校验布局文件的有效性，IDE往往无法进行输入错误警示。改正错误并保存xml文件，IDE会重新生成R.java文件。

#### 删除gen目录
 
若以上方法还不能够重新生成R.java文件，可以删除整个gen目录，IDE会重新生成R.java文件。
