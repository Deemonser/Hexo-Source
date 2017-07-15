---
title: Android自定义View-Canvas 基础操作
date: 2017-07-11 23:30:50
categories: Android自定义 View
tags:
 - 自定义View
 - Android
 - Canvas

---

![canvas](http://oslynwh8c.bkt.clouddn.com/image/hexo/canvas.jpg)

### 前言

在自定义View中，**onDraw(Canvas canvas) ** 是绘制主体，其中**Canvas** 的一下基础操作非常重要。

所有，整理一下 **Canvas** 的常见的操作。

<!-- more -->

### Paint的基本操作

在这之前，先了解下 **Paint** 的基本操作。 Paint 画笔在绘制过程中必不可少，它控制绘制的颜色，线条宽度，文字大小等

- `Paint.setStyle(Style style)` 设置绘制类型

- `Paint.setColor(int color)` 设置颜色

- `Paint.setStrokeWidth(float width)` 设置线条宽度

- `Paint.setTextSize(float textSize)` 设置文字大小

- `Paint.setAntiAlias(boolean aa)` 设置抗锯齿开关

- `Paint.setStrokeCap(cap)` 设置点的形状

  `Paint.setStrokeCap(cap)` 可以设置点的形状，但这个方法并不是专门用来设置点的形状的，而是一个设置线条端点形状的方法。端点有圆头 (`ROUND`)、平头 (`BUTT`) 和方头 (`SQUARE`) 三种。

以上就是最常用的Paint 操作，更高级的操作如 阴影，填充等这里不做介绍



#### Style

绘制style 有三种：`FILL`（默认）,`STROKE` 和 `FILL_AND_STROKE` 

她们的区别如下：

![style](http://oslynwh8c.bkt.clouddn.com/image/hexo/paint%20style.jpg)



#### 抗锯齿

在绘制的时候，往往需要开启抗锯齿来让图形和文字的边缘更加平滑。

毛边或者锯齿，发生的原因并不是很多人所想象的「绘制太粗糙」「像素计算能力不足」；同样，抗锯齿的原理也并不是选择了更精细的算法来算出了更平滑的图形边缘。 

实质上，锯齿现象的发生，只是由于图形分辨率过低，导致人眼察觉出了画面中的像素颗粒而已。换句话说，就算不开启抗锯齿，图形的边缘也已经是最完美的了，而并不是一个粗略计算的粗糙版本。 

抗锯齿的原理是：修改图形边缘处的像素颜色，从而**让图形在肉眼看来具有更加平滑的感觉**。

![](http://7xn2zf.com1.z0.glb.clouddn.com/2017-07-08-14994380294186.jpg)

未开启抗锯齿的圆，所有像素都是同样的黑色，而开启了抗锯齿的圆，边缘的颜色被略微改变了。这种改变可以让人眼有边缘平滑的感觉，但从某种角度讲，它也造成了图形的颜色失真。 



### Canvas的基本操作

#### 绘制颜色

| 操作类型 | 相关API                               | 备注              |
| ---- | ----------------------------------- | --------------- |
| 绘制颜色 | drawColor，    drawRGB，     drawARGB | 在整个绘制区域统一涂上指定颜色 |

例如：

```java
canvas.drawColor(Color.BLACK)
canvas.drawRGB(100, 200, 100);  
canvas.drawARGB(100, 100, 200, 100); 
```

这类颜色填充方法一般用于在绘制之前设置底色，或者在绘制之后为界面设置半透明蒙版。



#### 绘制基本形状

| 操作类型 | 相关API                                    |
| ---- | ---------------------------------------- |
| 点    | drawPoint(float x, float y, Paint paint)，    drawPoints(float[] pts,  Paint paint) |
| 线    | drawLine(float startX, float startY, float stopX, float stopY, Paint paint) ，   drawLines(float[] pts, Paint paint) |
| 矩形   | drawRect(float left, float top, float right, float bottom, Paint paint)，drawRect(Rect r, Paint paint) |
| 圆角矩形 | drawRoundRect(RectF rect, float rx, float ry, Paint paint) |
| 椭圆   | drawOval(RectF rect,Paint paint)         |
| 圆    | drawCircle(float centerX, float centerY, float radius, Paint paint) |
| 圆弧   | drawArc(RectF oval, float startAngle, float sweepAngle, boolean useCenter,Paint paint) |



##### 绘制点（drawPoint）

```java
//drawPoint(float x, float y, Paint paint) 前两参数是 X，Y 的坐标
canvas.drawPoint(100, 100, paint);

//drawPoints(float[] pts,  Paint paint) 第一个参数是 float数组，每两个一对，代表X,Y坐标
float[] points = {10, 10, 20, 20, 30, 30, 40, 40};
canvas.drawPoints(points, paint);

//droawPoints 还有一个重载方法 drawPoints(float[] pts, int offset, int count,  Paint paint)
// 第二个参数代表跳过数组的几个数，
// 第三个参数代表坐标的个数
canvas.drawPoints(points,2,4,paint);//此处代表 跳过数组前面2个数，4个坐标数（20, 20, 30, 30），绘制两个点
```

这个简单，图就不贴了





##### 绘制线段（drawLine）

```java
//drawLine(float startX, float startY, float stopX, float stopY,Paint paint) 
//前两个参数表示起点坐标，第三、四个参数表示终点坐标
canvas.drawLine(50, 50, 300, 50, paint);

//drawLines(float[] pts,Paint paint) 第一个参数 float 数组，表示线 的坐标，4个表示一条线段
float[] lines = {100, 100, 300, 300, 300, 100, 100, 300};
canvas.drawLines(lines, paint);
```



##### 绘制矩形（drawRect）

```java
//drawRect(float left, float top, float right, float bottom,Paint paint)
//前四个参数分别代表矩形相对于父控件的 左上右下 距离，详情见下表
canvas.drawRect(100, 100, 400, 400, paint);

//Rect 对象 是对 四个参数的封装
Rect rect = new Rect(120, 120, 380, 380);
canvas.drawRect(rect, paint);

//RectF 与Rect 相似，差别就是精度不同, 提供的方法也稍微存在差别
RectF rectF = new RectF(150f, 150f, 150f, 150f);
canvas.drawRect(rectF, paint);
```

View的left，top，right，bottom距离，如下图：

![view 的坐标](http://files.jb51.net/file_images/article/201703/20170313110258.jpg)



**Rect和RectF两者最大的区别就是精度不同，Rect是int(整形)的，而RectF是float(单精度浮点型)的**。除了精度不同，两种提供的方法也稍微存在差别，在这里我们暂时无需关注，想了解更多参见官方文档 [Rect](http://developer.android.com/reference/android/graphics/Rect.html) 和[RectF](http://developer.android.com/reference/android/graphics/RectF.html)



##### 绘制圆角矩形（drawRoundRect）

```java
//drawRoundRect(RectF rect, float rx, float ry, Paint paint)
//第一个参数是RectF（对坐标参数的封装），第二参数的圆角的 x轴半径，第三参数的圆角的Y轴半径
RectF rectF = new RectF(100, 100, 300, 300);
canvas.drawRoundRect(rectF, 30, 30, paint);
```

圆角矩形示例：

![圆角矩形的圆角半径](http://oslynwh8c.bkt.clouddn.com/image/hexo/%E5%9C%86%E8%A7%92%E7%9F%A9%E5%BD%A2.png)



##### 绘制椭圆（drawCircle）

```java
// drawOval(RectF rect,Paint paint) 只需要一个矩形作为参数
RectF rectF = new RectF(100,100,800,400);
canvas.drawOval(rectF,paint);
```



##### 绘制圆（drawCircle）

```java
// drawCircle(float centerX, float centerY, float radius, Paint paint)
// 前两个参数 是圆心坐标，第三个是半径
canvas.drawCircle(500,500,400,paint)
```



##### 绘制圆弧（drawArc）

```java
//drawArc(RectF oval, float startAngle, float sweepAngle, boolean useCenter,Paint paint)
//RectF oval: 矩形参数
//float startAngle: 开始角度
//float sweepAngle: 绘制的角度
//boolean useCenter: 是否使用中心
RectF rectF = new RectF(100,100,800,400);
canvas.drawArc(rectF,0,90,false,paint);
```



#### 绘制图片

绘制图片具体细分为两种：**Picture** 和 **Bitmap** ，**Picture** 到后面详解，最常用的是 **Bitmap** 

```java
//第一种 public void drawBitmap (Bitmap bitmap, float left, float top, Paint paint)
// 第二、三个参数是图片左上角的坐标点
canvas.drawBitmap(bitmap,50f,50f, paint)

//第二种
//public void drawBitmap (Bitmap bitmap, Rect src, Rect dst, Paint paint)
//public void drawBitmap (Bitmap bitmap, Rect src, RectF dst, Paint paint) 
//第二个参数指定图片绘制区域，第三个参数指定图片显示区域
Rect src = Rect(0, 0, bitmap.width / 2, bitmap.height / 2);
Rect dst = Rect(0, 0, width / 4, height / 4);
canvas.drawBitmap(bitmap, src, dst, paint);

// 第三种  Matrix 矩阵，这个有点复杂，先不写 
public void drawBitmap (Bitmap bitmap, Matrix matrix, Paint paint)
```

第一种绘制图片效果如下：

![bitmap](http://oslynwh8c.bkt.clouddn.com/image/hexo/bitmap1.jpg)



第二种效果如下：

![bitmap](http://oslynwh8c.bkt.clouddn.com/image/hexo/bitmap22.jpg)

指定图片绘制区域为原图的1/2 \* 1/2 = 1/4，指定图片显示区域为控件的1/4 \* 1/4 = 1/16。



#### 绘制文本

```java
//指定文本基线位置 绘制文字
//public void drawText (String text, float x, float y, Paint paint)
//public void drawText (String text, int start, int end, float x, float y, Paint paint)
canvas.drawText("Hello Deemons!", 200f, 200f, paint);
canvas.drawText("Hello Deemons!",6,14, 200f, 300f, paint);//指定显示的文本角标
  
  
//Path 路径是个神器
//public void drawTextOnPath (String text, Path path, float hOffset, float vOffset, Paint paint)
//public void drawTextOnPath (char[] text, int index, int count, Path path, float hOffset, float vOffset, Paint paint)
```

效果如下：

![drawText](http://oslynwh8c.bkt.clouddn.com/image/hexo/drawText.png)



#### 绘制路径

Canvas中不仅仅是能画规则图形，图片等，最重要的是可以根据自己的需求绘制不规则的图形。

比如这样：

![心](http://oslynwh8c.bkt.clouddn.com/image/hexo/%E7%BB%98%E5%88%B6%20%E5%BF%83.png)

这一切都归功于神器 **Path** 对象。

限于篇幅，Path 很难几句描述清楚，这里就不讲了。

这些基础掌握后，一下简单的自定义就没问题咯。比如这些

![示例](http://oslynwh8c.bkt.clouddn.com/image/hexo/%E8%87%AA%E5%AE%9A%E4%B9%89view%E5%9F%BA%E7%A1%80%E5%9B%BE%E5%BD%A2.jpg)





### 参考

[安卓自定义View教程目录](http://www.gcssloop.com/customview/CustomViewIndex/)

[Android 开发进阶: 自定义 View 1-1 绘制基础](http://hencoder.com/ui-1-1/)