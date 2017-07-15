---
title: java 数字格式化
date: 2017-03-19 10:37:23
Categories: java知识整理
tags: 
 - java 
 - 数字格式化 
 - NumberFormat 
 - DecimalFormat
---

![数字格式化](http://oslynwh8c.bkt.clouddn.com/image/hexo/pexels-photo-196659.jpg)

### 前言

**数字的格式化** 是工作中不可避免要接触到的问题，我们经常碰到这些的需求：

> 1. 金额以 ”¥999,999.99“ 格式 (带金额符，逗号分组，小数点保留两位)
>
> 2. 数字以 ”999,999.99“格式 
>
> 3. 百分比 ”％“ 或 千分比 ”‰“   例：0.45 —>  45%
>
> 4. 小数点后最多保留两位小数，如果有零，则直接省略 例：3.141 —>  3.14
>
>    ​												3.100  —> 3.1
>
>    ​												3.000 —> 3        
>
> 5. 精度模式   ”舍“   ”入“   或   ”四舍五入“  



如此，就得好好整理一下。

<!-- more -->

在 java 中主要涉及两个类：`NumberFormat` 及其子类 `DecimalFormat` ，我们分别说说特点。



### NmberFormat

`NumberFormat` 常用于指定不同于本地的地区（就是国际化）。

`NumberFormat` 有三个类型格式化: **数字 **, **金额** , **百分比** 。 而且格式控制比较弱 , 所有格式化都带分组

例如:

- 数字类型 在德国 逗号`,`代表小数分隔符，点`.`代表组分隔符 即 12345678.123 —> 12.345.678,123

```java
double num = 12345678.1233;

NumberFormat format = NumberFormat.getInstance();	// 本地 
NumberFormat germanFormat = NumberFormat.getInstance(Locale.GERMAN);	//指定德国

System.out.println(format.format(num));		//12,345,678.123
System.out.println(germanFormat.format(num));	//12.345.678,123
```

- 金额类型 在中国是人民币`¥` ，在美国是美元 `$`

```java
double num = 12345678.1233;

NumberFormat format = NumberFormat.getCurrencyInstance();
NumberFormat usFormat = NumberFormat.getCurrencyInstance(Locale.US);	//指定美国

System.out.println(format.format(num));		//￥12,345,678.12
System.out.println(usFormat.format(num));	//$12,345,678.12
```

- 百分比都一样

```java
double num = 78.1233;
NumberFormat format = NumberFormat.getPercentInstance();
System.out.println(format.format(num));		//7,812%
```

> 小数位数 >= 3位的情况下，数字类型默认保留3位小数，金额则保留2位小数，百分比不保留小数
>
> 小数位数 < 3位的情况下，有几位小数就显示几位 
>
> 精度默认是`四舍六入五取偶`



整数位数和小数位数可以设置

```Java
 double num1 = 123456789;
 double num2 = 1.123456;

 NumberFormat format = NumberFormat.getInstance();

 format.setMaximumIntegerDigits(8);		//设置整数的 最大位数
 format.setMinimumIntegerDigits(2);		//设置整数的 最小位数
 format.setMaximumFractionDigits(2);		//设置小数的 最大位数
 format.setMinimumFractionDigits(1);		//设置小数的 最小位数

 System.out.println(format.format(num1));	//23,456,789.0
 System.out.println(format.format(num2));	//01.12
```



精度通过`setRoundingMode（）`设置

```java
 double num1 = 3.1114;
 double num2 = 3.1115;
 double num3 = 3.1116;

 NumberFormat format = NumberFormat.getInstance();

 format.setRoundingMode(RoundingMode.HALF_UP);  //四舍五入
 System.out.println(format.format(num1));//3.111
 System.out.println(format.format(num2));//3.112
 System.out.println(format.format(num3));//3.112

 format.setRoundingMode(RoundingMode.DOWN);		// 舍
 System.out.println(format.format(num1));//3.111
 System.out.println(format.format(num2));//3.111
 System.out.println(format.format(num3));//3.111

 format.setRoundingMode(RoundingMode.UP);		// 入
 System.out.println(format.format(num1));//3.112
 System.out.println(format.format(num2));//3.112
 System.out.println(format.format(num3));//3.112

```

关于`RoundingMode` 的值 有`UP`，`DOWN`，`CEILING`，`FLOOR`，`HALF_UP`，`HALF_DOWN`，`HALF_EVEN`，`UNNECESSARY`这些，详情见这个：[DecimalFormat对数值格式化的舍入问题——RoundingMode](http://blog.csdn.net/alanzyy/article/details/8465098)



> 常用的就是**四舍五入**`HALF_UP` 以及 **银行家舍入**（四舍六入五取偶） `HALF_EVEN`，欧洲的银行工厂都用的后种。
>
> **四舍六入五取偶**  ：取偶的意思就是遇到5**（5后面位上必须全是0）**，把前一位变成偶数  1.15->1.2   1.16->1.2   1.25->1.2   1.26->1.3
>
> **NumberFormat** 格式化都会带分组功能。如果不需要这个，而且不考虑国际化则建议使用**DecimalFormat**





### DecimalFormat

**DecimalFormat** 有很好的格式控制, 可随意定制。和 **NumberFormat**  相比，它的线程并不安全，建议配合**ThreadLocal** ，为每个线程创建一个实例。

```Java
private static ThreadLocal<DecimalFormat> threadLocal = new ThreadLocal<DecimalFormat> () {
    @Override
    protected DecimalFormat initialValue() {
        return new DecimalFormat();
    }
};

public static String format(String pattern, double v) {
    DecimalFormat decimalFormat = threadLocal.get();
    decimalFormat.applyPattern(pattern);
    return decimalFormat.format(v);
}
```



**DecimalFormat** 类主要靠 `#` 和 `0` 两种占位符号来指定数字长度。`0` 表示如果位数不足则以 0 填充，`#` 表示只要有可能就把数字拉上这个位置。

```java
double pi = 3.1415927;//圆周率  

System.out.println(new DecimalFormat("0").format(pi));      //3
System.out.println(new DecimalFormat("0.00").format(pi));   //3.14
System.out.println(new DecimalFormat("00.000").format(pi)); // 03.142
System.out.println(new DecimalFormat("#").format(pi));      //3
System.out.println(new DecimalFormat("#.##%").format(pi));  //314.16%

long c = 299792458;//光速
System.out.println(new DecimalFormat("#.#####E0").format(c));   //2.99792E8
System.out.println(new DecimalFormat("00.####E0").format(c));   //29.9792E7
System.out.println(new DecimalFormat(",###").format(c));        //299,792,458
System.out.println(new DecimalFormat("光速大小为每秒,###米。").format(c));//光速大小为每秒299,792,458米。
```



除了上面常用的符号，还有一些不常用的

[Java中DecimalFormat的用法！](http://blog.csdn.net/u013456370/article/details/52688836)