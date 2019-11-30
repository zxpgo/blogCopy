---

title: android绘图系列之三(Paint常用函数介绍)
date: 2018-12-15 20:57:14
tags: APP开发
categories: APP开发

---

前面介绍了基本的paint和canvas的基本用法，这里汇总paint常用函数。<!--more-->

### 基本用法

paint基本设置的函数：

- reset() ：重置画笔 
- setColor(int color) ：给画笔设置颜色值 
- setARGB(int a, int r, int g, int b) ：同样是设置颜色，但是利用ARGB分开设置 
- setAlpha(int a) ：设置画笔透明度 
- setStyle(Paint.Style style) ：设置画笔样式，取值有 Paint.Style.FILL ：填充内部 Paint.Style.FILL_AND_STROKE ：填充内部和描边、Paint.Style.STROKE ：仅描边 
- setStrokeWidth(float width) ：设置画笔宽度 
- setAntiAlias(boolean aa) ：设置画笔是否抗锯齿


### 字体相关

- setTextSize(float textSize) ：设置文字大小
- setFakeBoldText(boolean fakeBoldText) ：设置是否为粗体文字
- setStrikeThruText(boolean strikeThruText) ：设置带有删除线效果
- setUnderlineText(boolean underlineText) ：设置下划线
- setTextAlign(Paint.Align align) ：设置开始绘图点位置
- setTextScaleX(float scaleX) ：水平拉伸设置
- setTextSkewX(float skewX) ：设置字体水平倾斜度，普通斜体字是-0.25，可见往右斜
- setTypeface(Typeface typeface) ：字体样式
