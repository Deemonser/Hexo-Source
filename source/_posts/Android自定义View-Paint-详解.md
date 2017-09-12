---
title: Android自定义View-Paint 详解
date: 2017-08-30 22:40:15
categories: Android自定义 View
tags:
 - 自定义View
 - Android
 - Canvas
---

![paint](http://oslynwh8c.bkt.clouddn.com/image/hexo/paint.jpeg)

最近学习了 **扔物线**  的 [Android 开发进阶: 自定义 View 1-2 Paint 详解](http://hencoder.com/ui-1-2/) 后受益匪浅。

对 **Path** 类讲的非常详细，所以做个笔记，以便翻阅。

<!-- more -->

Path 在自定义View 中主要控制绘制的颜色以及效果。



### 颜色

Canvas 绘制内容时，有三层对颜色的处理：

![img](http://oslynwh8c.bkt.clouddn.com/image/hexo/Canvas%E9%A2%9C%E8%89%B2%E7%BB%98%E5%88%B6.jpg)

#### 基本颜色

基本颜色的绘制有三种方式

* canvas.drawColor/ARGB()  方法是填充整个画板的底色
* drawBitmap()  的颜色，是直接由 ` Bitmap` 对象来提供，用于图片
* Paint 参数控制图形和文字的颜色



Paint 参数控制基本颜色有两种方式，一种直接设置颜色，另一种间接设置颜色。

##### 1.直接设置

直接设置就非常简单，就是 Paint 的两个方法

```java
paint.setColor(Color.parseColor("#009688"));  

paint.setARGB(100, 255, 0, 0);  
```



##### 2.通过 Shader 间接设置

Shader 的中文叫做「着色器」，也是用于设置绘制颜色的。「着色器」不是 Android 独有的，它是图形领域里一个通用的概念，它和直接设置颜色的区别是，着色器设置的是一个颜色方案，或者说是一套着色规则。

当设置了 Shader 之后 Paint 在绘制图形和文字时就不使用 setColor/ARGB()  设置的颜色了，而是使用 Shader  的方案中的颜色。



在 Android 的绘制里使用 Shader ，并不直接用 Shader 这个类，而是用它的5个子类

*  `LinearGradient` 
*  `RadialGradient`
*  `SweepGradient` 
*  `BitmapShader`
*  `ComposeShader` 



###### LinearGradient 线性渐变

设置两个点和两种颜色，以这两个点作为端点，使用两种颜色的渐变来绘制颜色

```java
Shader shader = new LinearGradient(100, 100, 	//渐变的两个端点的位置 x,y
                                   500, 500, 
                                   Color.parseColor("#E91E63"),	//两端点的颜色
                                   Color.parseColor("#2196F3"), 
                                   Shader.TileMode.CLAMP	//端点范围之外的着色规则，类型是Shader.TileMode
                                  );
paint.setShader(shader);
canvas.drawCircle(300, 300, 200, paint);  
```

LinearGradient 的其他参数很简单，只是最后一个参数 tile ，是 Shader.TileMode 类型。

![LinearGradient](http://oslynwh8c.bkt.clouddn.com/image/hexo/LinearGradient.jpg)



TileMode有三个可选值：

* CLAMP 会在端点之外延续端点处的颜色
* MIRROR 是镜像模式
* REPEAT 是重复模式





###### RadialGradient 辐射渐变

辐射渐变就是从中心向周围辐射状的渐变

```java
Shader shader = new RadialGradient(300, 300, 	//辐射中心的坐标
                                   200, 		//辐射半径
                                   Color.parseColor("#E91E63"),	//辐射中心的颜色
                                   Color.parseColor("#2196F3"), //辐射边缘的颜色 
                                   Shader.TileMode.CLAMP);	//辐射范围之外的着色模式，类型是Shader.TileMode
paint.setShader(shader);
canvas.drawCircle(300, 300, 200, paint);  
```

辐射渐变的参数 也很简单，最后一个 范围之外的模式 和前面的相同。

![RadialGradient](http://oslynwh8c.bkt.clouddn.com/image/hexo/RadialGradient.jpg)



###### SweepGradient 扫描渐变

扫描渐变和雷达扫描的效果类似，以 X 轴正方向为起始位置，顺时针扫描。

```java
Shader shader = new SweepGradient(300, 300, 		//扫描的中心
                                  Color.parseColor("#E91E63"), 	//扫描的起始颜色，浅红色
                                  Color.parseColor("#2196F3"));	//扫描的终止颜色，蓝色
paint.setShader(shader);
canvas.drawCircle(300, 300, 200, paint);  
```

![SweepGradient](http://oslynwh8c.bkt.clouddn.com/image/hexo/SweepGradient.jpg)



###### BitmapShader

这个是用Bitmap 的像素来作为图形或文字的填充

```java
Bitmap bitmap = BitmapFactory.decodeResource(getResources(), R.drawable.batman);  
Shader shader = new BitmapShader(bitmap, 	//Bitmap对象
                                 Shader.TileMode.CLAMP, 	//横向的TileMode
                                 Shader.TileMode.CLAMP);  	//纵向的TileMode
paint.setShader(shader);
canvas.drawCircle(300, 300, 200, paint);  
```

![BitmapShader](http://oslynwh8c.bkt.clouddn.com/image/hexo/BitmapShader.jpg)



###### ComposeShader 混合着色器

混合就是把两个  Shader  一起使用

```java
// 第一个 Shader：头像的 Bitmap
Bitmap bitmap1 = BitmapFactory.decodeResource(getResources(), R.drawable.batman);  
Shader shader1 = new BitmapShader(bitmap1, Shader.TileMode.CLAMP, Shader.TileMode.CLAMP);

// 第二个 Shader：标识的 Bitmap
Bitmap bitmap2 = BitmapFactory.decodeResource(getResources(), R.drawable.batman_logo);  
Shader shader2 = new BitmapShader(bitmap2, Shader.TileMode.CLAMP, Shader.TileMode.CLAMP);

// ComposeShader：结合两个 Shader
Shader shader = new ComposeShader(shader1, 		//两个Shader
                                  shader2, 
                                  PorterDuff.Mode.SRC_OVER);//PorterDuff.Mode类型，叠加模式 
paint.setShader(shader);

canvas.drawCircle(300, 300, 300, paint);  
```

![ComposeShader](http://oslynwh8c.bkt.clouddn.com/image/hexo/ComposeShader.jpg)

`ComposeShader()` 在硬件加速下是不支持两个相同类型的 `Shader` 的，所以这里也需要关闭硬件加速。

ProterDuff.Mode是enum，用于两个 Shader 的叠加模式。因内容过多，所有在下面单独说明。



###### ProterDuff.Mode

PorterDuff.Mode 一共有 17 个，可以分为两类：

1. Alpha 合成 (Alpha Compositing)
2. 混合 (Blending)



第一类，Alpha 合成，其实就是 「PorterDuff」 这个词所指代的算法。 

「PorterDuff」 是两个人的姓。这两个人共同发表了一篇论文，描述了 12 种将两个图像共同绘制的操作（即算法）。而这篇论文所论述的操作，都是关于 Alpha 通道「透明度」的计算的，后来人们就把这类计算称为**Alpha 合成** ( Alpha Compositing ) 。

效果直接盗 Google 的[官方文档](https://developer.android.com/reference/android/graphics/PorterDuff.Mode.html)了。

源图像和目标图像：

![img](http://oslynwh8c.bkt.clouddn.com/image/hexo/src_dst.jpg)

Alpha 合成：

![img](http://oslynwh8c.bkt.clouddn.com/image/hexo/Alpha%20%E5%90%88%E6%88%90.jpg)

第二类，混合，是制图软件里都有的那些混合模式（`multiply` `darken` `lighten` 之类的）。这一类操作的是颜色本身而不是 `Alpha` 通道，并不属于 `Alpha` 合成，所以和 Porter 与 Duff 这两个人也没什么关系，不过为了使用的方便，它们同样也被 Google 加进了 `PorterDuff.Mode` 里。

![img](http://oslynwh8c.bkt.clouddn.com/image/hexo/%E6%B7%B7%E5%90%88.jpg)

**结论**

从效果图可以看出，Alpha 合成类的效果都比较直观，而混合类的效果就相对抽象一些，Alpha 合成类需要掌握，而混合类了解即可，需要的时候在来查找。



#### ColorFilter

除了前面 Paint 设置基本颜色， 还有一个 `setColorFilter(ColorFilter colorFilter)` 方法和颜色有关。

ColorFilter 这个类是为绘制设置颜色过滤。颜色过滤的意思，就是为绘制的内容设置一个统一的过滤策略，然后 `Canvas.drawXXX()` 方法会对每个像素都进行过滤后再绘制出来。

ColorFilter 并不直接使用，而是使用它的子类。

它共有三个子类：

* `LightingColorFilter`
* `PorterDuffColorFilter` 
* `ColorMatrixColorFilter`

```java
/*LightingColorFilter(int mul, int add)
整个计算过程如下：
R' = R * mul.R / 0xff + add.R  
G' = G * mul.G / 0xff + add.G   
B' = B * mul.B / 0xff + add.B  
*/
ColorFilter lightingColorFilter = new LightingColorFilter(0x00ffff, 0x000000);  
paint.setColorFilter(lightingColorFilter); 
```

这三个类都是对颜色的直接操作，而且比较专业，就介绍到这里了。



#### Xfermode

Xfermode 指的是你要绘制的内容和 `Canvas` 的目标位置的内容应该怎样结合计算出最终的颜色。但通俗地说，其实就是要你以绘制的内容作为源图像，以 View 中已有的内容作为目标图像，选取一个 `PorterDuff.Mode` 作为绘制内容的颜色处理方案。Xfermode 只有一个子类PorterDuffXfermode，所以直接用它。而且必须使用过离屏缓冲，不然效果会很奇特。

```java
int saved = canvas.saveLayer(null, null, Canvas.ALL_SAVE_FLAG);	//首先需要离屏缓冲

canvas.drawBitmap(rectBitmap, 0, 0, paint); // 画方
paint.setXfermode(new PorterDuffXfermode(PorterDuff.Mode.DST_IN)); // 设置 Xfermode
canvas.drawBitmap(circleBitmap, 0, 0, paint); // 画圆
paint.setXfermode(null); // 用完及时清除 Xfermode

canvas.restoreToCount(saved); //离屏缓冲后的图形，恢复到画板上
```

![Xfermode](http://oslynwh8c.bkt.clouddn.com/image/hexo/Xfermode.gif)





### 效果

#### 抗锯齿

抗锯齿默认是关闭的，如果需要抗锯齿，需要显式地打开。

```java
//两种方式
paint.setAntiAlias(true);

Paint paint = new Paint(Paint.ANTI_ALIAS_FLAG);//参数里加一个 ANTI_ALIAS_FLAG 的 flag开启抗锯齿
```



#### Style

`setStyle(style)` 用来设置图形是线条风格还是填充风格

```java
paint.setStyle(Paint.Style.FILL); // FILL 模式，填充  
canvas.drawCircle(300, 300, 200, paint);  
```

Pain.Style 有三个模式

* FILL 模式，填充  ，默认模式
* STROKE 模式，画线 
* FILL_AND_STROKE 模式，填充 + 画线




#### 线条形状

设置线条形状的一共有 4 个方法

* setStrokeWidth(float width) 

  设置线条宽度。单位为像素，默认值是 0

* setStrokeCap(Paint.Cap cap) 

  设置线头的形状。线头形状有三种：`BUTT` 平头、`ROUND` 圆头、`SQUARE` 方头。默认为 `BUTT`。

  ![StrokeCap](http://oslynwh8c.bkt.clouddn.com/image/hexo/StrokeCap.jpg)

* setStrokeJoin(Paint.Join join)

  设置拐角的形状。有三个值可以选择：`MITER` 尖角、 `BEVEL` 平角和 `ROUND` 圆角。默认为 `MITER`

  ![StrokeJoin](http://oslynwh8c.bkt.clouddn.com/image/hexo/StrokeJoin.jpg)

* setStrokeMiter(float miter)

  设置 `MITER` 型拐角的延长线的最大值。

  这个是方法是对setStrokeJoin方法的补充。  `MITER` 型连接点有一个额外的规则：当尖角过长时，自动改用 `BEVEL` 的方式来渲染连接点，而这个方法就是用来设置延长线的最大值的。拐角的大小为 θ ，那么这个比值就等于 1 / sin ( θ / 2 ) 

  ![StrokeMiter](http://oslynwh8c.bkt.clouddn.com/image/hexo/StrokeMite.jpg)




#### 色彩优化

`Paint` 的色彩优化有两个方法： `setDither(boolean dither)` 和 `setFilterBitmap(boolean filter)` 。它们的作用都是让画面颜色变得更加「顺眼」，但原理和使用场景是不同的

* setDither(boolean dither) 设置图像抖动 ，
* setFilterBitmap(boolean filter) 设置是否使用双线性过滤来绘制 `Bitmap`




#### 轮廓效果

setPathEffect(PathEffect effect) 用来给给图形的轮廓设置效果。

PathEffect 有六种

* CornerPathEffect 把所有拐角变成圆角

* DiscretePathEffect 把线条进行随机的偏离

* DashPathEffect 虚线来绘制线条

* PathDashPathEffect 根据路径绘制虚线

* SumPathEffect

  组合效果，分别按照两种 `PathEffect` 分别对目标进行绘制。

* ComposePathEffect

  组合效果，先对目标 `Path` 使用一个 `PathEffect`，然后再对这个改变后的 `Path` 使用另一个 `PathEffect`




#### 阴影效果

`setShadowLayer(float radius, float dx, float dy, int shadowColor)` 

 `radius` 是阴影的模糊范围； 

`dx` `dy` 是阴影的偏移量； 

`shadowColor` 是阴影的颜色。

如果要清除阴影层，使用 `clearShadowLayer()`

> 在硬件加速开启的情况下， `setShadowLayer()` 只支持文字的绘制，文字之外的绘制必须关闭硬件加速才能正常绘制阴影。
>
> 如果 `shadowColor` 是半透明的，阴影的透明度就使用 `shadowColor` 自己的透明度；而如果 `shadowColor`是不透明的，阴影的透明度就使用 `paint` 的透明度



#### 前景效果

`setMaskFilter(MaskFilter maskfilter)` 设置绘制层上方的附加效果。

`MaskFilter` 有两种

*  `BlurMaskFilter(float radius, BlurMaskFilter.Blur style)` 

  模糊效果，有NORMAL、SOLID、INNER、OUTER四种类型

  ![](http://oslynwh8c.bkt.clouddn.com/image/hexo/BlurMaskFilter.jpg)

* ` EmbossMaskFilter(float[] direction, float ambient, float specular, float blurRadius)`

  浮雕效果，`direction` 是一个 3 个元素的数组，指定了光源的方向； `ambient` 是环境光的强度，数值范围是 0 到 1； `specular` 是炫光的系数； `blurRadius` 是应用光线的范围




#### 获取绘制的Path

* `getFillPath(src, dst)` 方法就能获取绘制的实际 `Path`。方法的参数里，`src` 是原 `Path` ，而 `dst` 就是实际 `Path` 的保存位置。 `getFillPath(src, dst)` 会计算出实际 `Path`，然后把结果保存在 `dst` 里
* `getTextPath()` 方法，获取的就是目标文字所对应的 `Path` 。这个就是所谓「文字的 `Path`」




### 文字

`Paint` 有些设置是文字绘制相关的，即和 `drawText()` 相关的。

* 文字大小
* 文字间隔
* 文字效果（删除线、斜体、下划线）




### 初始化

这一类方法用来初始化 `Paint` 对象，或者是批量设置 `Paint` 的多个属性的方法

* rest()  重置 `Paint` 的所有属性为默认值
* set(Paint src) 把 `src`  的所有属性全部复制过来
* setFlages(int flags) 批量设置 flags。




### 参考

[Android 开发进阶: 自定义 View 1-2 Paint 详解](http://hencoder.com/ui-1-2/)