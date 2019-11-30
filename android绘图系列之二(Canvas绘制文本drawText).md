---

title: android绘图系列之二(Canvas绘制文本drawText)
date: 2018-12-13 14:44:14
tags: APP开发
categories: APP开发

---

系列之一介绍了绘制基本图形，比如矩形、直线、圆等。Canvas除了这些操作，还有两个重量级的绘制能力:Text(文本)和Path(路径),这里介绍Text的绘制。<!--more-->

### Paint对于Text的相关设置

普通设置：

- paint.setStrokeWidth(5)：设置画笔宽度 
- paint.setAntiAlias(true)：设置是否使用抗锯齿功能，如果使用，会导致绘图速度变慢 
- paint.setStyle(Paint.Style.FILL)：设置绘图样式，对于设置文字和几何图形都有效，可取值有三种 ：1、Paint.Style.FILL：填充内部 2、Paint.Style.FILL_AND_STROKE：填充内部和描边 3、Paint.Style.STROKE：仅描边 
- paint.setTextAlign(Align.CENTER)：设置文字对齐方式 
- paint.setTextSize(12)：设置文字大小


样式设置：

- paint.setFakeBoldText(true)：设置是否为粗体文字 
- paint.setUnderlineText(true)：设置下划线 
- paint.setTextSkewX((float) -0.25)：设置字体水平倾斜度，普通斜体字是 -0.25 
- paint.setStrikeThruText(true)：设置带有删除线效果

其他设置：

paint.setTextScaleX(2)：设置水平拉伸，高度不会变

### Canvas绘制文字

#### 普通水平绘制：

	void drawText (String text, float x, float y, Paint paint) 
	void drawText (CharSequence text, int start, int end, float x, float y, Paint paint) 
	void drawText (String text, int start, int end, float x, float y, Paint paint) 
	void drawText (char[] text, int index, int count, float x, float y, Paint paint)


说明： 

- 第一个构造函数时最简单的构造函数 
- 第三、四个构造函数：实现截取一部分字体绘图 
- 第二个构造函数最强大，因为传入的可以是 CharSequence 类型字体，所以可以实现绘制带图片的扩展文字，而且还能截取一部分绘制

onDraw方法：

  @Override
    protected void onDraw(Canvas canvas) {
        super.onDraw(canvas);
        paint.setTextSize(50);
        canvas.drawText("巴扎嘿",100,100,paint);
    }


#### 指定各个文字位置

	void drawPosText (char[] text, int index, int count, float[] pos, Paint paint) 
	void drawPosText (String text, float[] pos, Paint paint)

参数：
 
- char[] text：要绘制的文字数组 
- int index：第一个要绘制的文字的索引 
- int count：要绘制的文字的个数，用来算最后一个文字的位置，从第一个绘制的文字开始算起 
- float[] pos：每个字体的位置，两个为一组

onDraw方法：

	paint.setTextSize(50);
    canvas.drawPosText("巴扎黑", new float[]{100, 100, 100, 200, 100, 300}, paint);


#### 沿路径绘制

	void drawTextOnPath (String text, Path path, float hOffset, float vOffset, Paint paint) 
	void drawTextOnPath (char[] text, int index, int count, Path path, float hOffset, float vOffset, Paint paint)

参数：

- Path path：文字的绘制路径
- char[] text：要绘制的文字数组
- int index：第一个要绘制的文字的索引
- int count：要绘制的文字的个数，用来算最后一个文字的位置，从第一个绘制的文字开始算起
- float hOffset：与路径起始点的水平偏移距离
- float vOffset：与路径中心点的垂直偏移量

	//声明全局变量
	Path path;
 	private void init() {
        //初始化画笔
        paint = new Paint();
        //设置抗锯齿
        paint.setAntiAlias(true);
        //设置画笔宽度
        paint.setStrokeWidth(5);
        //设置画笔颜色
        paint.setColor(Color.RED);
        //设置画笔样式
        paint.setStyle(Paint.Style.STROKE);
        //设置路径，以圆作为我们文本显示的路线
        path = new Path();
        path.addCircle(300,300,200,Path.Direction.CW);//路径的绘制方式，CW;表示正序绘制，CCW表示倒数绘制

    }

    @Override
    protected void onDraw(Canvas canvas) {
        super.onDraw(canvas);
        paint.setTextSize(50);
        //绘制路径
        canvas.drawPath(path, paint);
        //把文字绘制在要显示的路径上，路径起始点偏移150，圆心偏移50
        canvas.drawTextOnPath("搞笑我们是认真的！！！", path, 300, 50, paint);

    }



### drawText绘制技巧

我们都知道绘制文本只需要调用 Canvas 的 drawText 方法就可以在任何位置绘制我们想要的文字，但是你知道你知道 drawText 方法绘制文本时是依据什么条件进行绘制的么？ 我们来看一个例子吧：


	@Override
    protected void onDraw(Canvas canvas) {
        super.onDraw(canvas);      
        canvas.drawLine(50,100,600,100,paint);
        paint.setTextSize(80);
        canvas.drawText("abcefghijlk",100,100,paint);
    }

结果：

![](https://i.imgur.com/zfbzeCE.png)

发现一个很有意思的事情，就是我们明明给我们要绘制的文本设置的位置是 （50，100），而我们绘制的线的位置 Y 坐标是在 100 的位置上，为什么我们绘制的文本的第一个字母 “g” 会出现在标准线的下方呢？

#### 四线格与基线

还记得我们小时候写拼音使用的四线格本子么？带你们回忆回忆童年，那时候我们都知道要写在四线格内：

![](https://i.imgur.com/AMMzTWT.png)

那么问题来了，其实 Canvas 在调用 drawText 方法绘制文本时，也是有规则的，这个规则就是基线：


![](https://i.imgur.com/2xPZ7Po.png)

也就是说 Canvas 在调用 drawText 绘制文本时，是根据基线的位置来确定要绘制的 Text 的位置的，要想把 Text 绘制到正确的位置上，必须知道基线的位置。