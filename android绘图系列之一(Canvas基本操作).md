---

title: android绘图系列之一(Canvas基本操作)
date: 2018-12-12 10:52:14
tags: APP开发
categories: APP开发

---

Canvas的基本操作以及开始绘制简单图形。<!--more-->

### Canvas和Paint

Canvas 和 Paint 之间的关系就像我们平时画画需要的画笔和画纸一样，我们画画无外乎也就需要这两个工具，而这两个工具体现在 Android 中，就是我们的 Paint（画笔）和 Canvas（画纸，通常称为画布），所以凡是跟要画的东西设置相关的，比如颜色、大小、宽度、样式、透明度等都是在 Paint 中设置的。而凡是跟要画的成品，比如想画一个矩形、圆形、文字、路径等都是通过 Canvas 操作的。

#### 基础代码

新建一个类，派生自View类，重写View的onDraw方法，在onDraw方法中通过Canvas来实现想要的效果：


	public class CustomView extends View {

	    private Paint paint;
	
	    public CustomView(Context context) {
	        super(context);
	        init();
	    }
	
	    //要实现的构造函数　
	    public CustomView(Context context, @Nullable AttributeSet attrs) {
	        super(context, attrs);
	        init();
	    }
	 	 //要实现的构造函数
	    public CustomView(Context context, @Nullable AttributeSet attrs, int defStyleAttr) {
	        super(context, attrs, defStyleAttr);
	        init();
	    }
	
	    //要实现的构造函数
	    @RequiresApi(api = Build.VERSION_CODES.LOLLIPOP)
	    public CustomView(Context context, @Nullable AttributeSet attrs, int defStyleAttr, int defStyleRes) {
	        super(context, attrs, defStyleAttr, defStyleRes);
	        init();
	    }
	
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
	    }
	
	    @Override
	    protected void onDraw(Canvas canvas) {
	        super.onDraw(canvas);
			//绘制一个圆心为(300,300),半径为300的圆
	        canvas.drawCircle(300,300, 300,  paint);
	    }
	}

一定要对所有类型的构造函数都要实现，并在每个构造函数中不要忘记了初始化。

在布局文件中使用我们定义View：

	<?xml version="1.0" encoding="utf-8"?>
	<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
	
	    android:layout_width="match_parent"
	    android:layout_height="match_parent"
	    android:orientation="vertical">
	
	    <com.example.dztx.myapplication.CustomView
	        android:layout_width="match_parent"
	        android:layout_height="match_parent"/>
	
	</LinearLayout>


### Canvas基本图形

#### 圆

	void drawCircle(float cx, float cy, float radius, Paint paint)

参数：
 
- cx：圆心点的 X 轴坐标 
- cy：圆心点的 Y 轴坐标 
- radius：圆的半径 
- paint：画笔

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
	    }
	
	    @Override
	    protected void onDraw(Canvas canvas) {
	        super.onDraw(canvas);
	        canvas.drawCircle(300,300,100,paint);
		}




#### 矩形

	void drawRect (float left, float top, float right, float bottom, Paint paint) 
	void drawRect (RectF rect, Paint paint) 
	void drawRect (Rect r, Paint paint)

参数：

- 第一种写法是直接传入矩阵的四个点的位置；
- 而第二种写法是根据传入的RectF以及Rect矩形变量来指定所画的矩形。

示例代码：


	canvas.drawRect(100, 100, 300, 300, paint);
	canvas.drawRect(new Rect(100, 100,500,500), paint);
    canvas.drawRect(new RectF(100, 100,500,500), paint);

#### 角矩形

	void drawRoundRect (RectF rect, float rx, float ry, Paint paint) 
	void drawRoundRect (float left, float top, float right, float bottom, float rx, float ry, Paint paint)

参数：

- rect:所需画的矩形
- rx:生成圆角的椭圆的X轴半径
- ry:生成圆角的椭圆的Y轴半径

onDraw中代码实现：

	canvas.drawRoundRect(100,100,300,300,50,50,paint);
    canvas.drawRoundRect(new RectF(100,400,300,600),50,50,paint);

#### 椭圆

	void drawOval (RectF oval, Paint paint)

参数： RectF oval:用来生成椭圆的矩形

onDrawd代码：

	 @Override
    protected void onDraw(Canvas canvas) {
        super.onDraw(canvas);
        canvas.drawRect(100,400,700,700,paint);
        paint.setColor(Color.BLUE);
        canvas.drawOval(new RectF(100,400,700,700),paint);
    }

#### 圆弧

圆弧是椭圆的一部分，而椭圆是根据矩形来生成的，所以圆弧当然也是根据矩形来生成的。

	void drawArc (RectF oval, float startAngle, float sweepAngle, boolean useCenter, Paint paint)

参数：
- Rect oval: 生成圆弧的矩形
- float startAngle: 圆弧开始的角度，以X轴正方向为0°
- float sweepAngle: 圆弧持续的角度
- boolean useCenter: 是否显示弧的两边，true显示，false不显示，只有一条弧线


onDraw方法：

    @Override
    protected void onDraw(Canvas canvas) {
        super.onDraw(canvas);
        canvas.drawRect(100,100,400,500,paint);
        paint.setColor(Color.BLUE);
        canvas.drawArc(new RectF(100,300,400,500),0,90,false,paint);
    }

#### 直线

	void drawLine (float startX, float startY, float stopX, float stopY, Paint paint)

参数：

- float startX：开始点 X 坐标 
- float startY：开始点 Y 坐标 
- float stopX：结束点 X 坐标 
- float stopY：结束点 Y 坐标

onDraw方法：

    @Override
    protected void onDraw(Canvas canvas) {
        super.onDraw(canvas);
        canvas.drawLine(100,100,500,900,paint);
        paint.setStrokeWidth(50);
        canvas.drawLine(200,100,600,900,paint);
    }

#### 多条直线
	
	void drawLines (float[] pts, Paint paint) 
	void drawLines (float[] pts, int offset, int count, Paint paint)

参数：  float[] pts：pts 是点的集合，每两个点形成一条连接线，100, 100, 500, 900 这四个数字其实就是确定了要连接的两个点的位置

onDraw方法：

   @Override
    protected void onDraw(Canvas canvas) {
        super.onDraw(canvas);
        float[] ps = {100,100,200,200,300,300,500,500};
        canvas.drawLines(ps,paint);
    }

转载：[AkaiC](https://blog.csdn.net/AkaiC/article/details/80010241)