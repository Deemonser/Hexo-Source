---
title: Java概述
date: 2015-07-23 16:15:52
categories: Java基础
tags: 
 - Java基础
---

![java](http://oslynwh8c.bkt.clouddn.com/image/hexo/java2.jpeg)

### 前言

以前记录的 **Java** 学习笔记，现在被翻出来了，所以就想着把这些 **Java** 基础重新归档整理，搬到博客上。 

<!-- more -->



### Java 简介

1995 年 SUN（StanfordUniversity Network）公司推出了 Java 这一门高级编程语言，最大特点是跨平台。

Java 是一门面向 Internet 的编程语言，目前已经成为 Web 应用程序的首选开发语言，具有简单易学，完全面向对象，安全可靠，跨平台等特点。



### Java 的三种技术架构

目前，java主要有三种技术架构：

* JAVAEE（Java Platform Enterprise Edition）企业版

  它是为开发企业环境下的应用程序提供的一套解决方案。该技术体系中包含的技术如：Servlet Jsp等，主要针对于Web应用程序开发。

* JAVASE（java Platform Standard Edition）标准版

  它是为开发普通桌面和商务应用程序提供的解决方案。该技术体系是其他两者的基础，可以完成一些桌面应用程序的开发，比如Java版的扫雷、QQ等。

* JAVAME（Java Platform Micro Edition）小型版

  它是为开发电子消费产品和嵌入式设备提供的解决方案。该技术体系主要应用于小型电子消费产品，如手机中的应用程序等。






### Java 最大特性：跨平台

跨平台性是指，通过 **Java** 语言编写的应用程序在**不同的操作系统**平台中都可以运行。

它实现的原理是 在需要运行 Java 应用程序的操作系统中，先安装一个其系统对应的 **Java 虚拟机** （JVM：Java Virtual Machine），由 JVM 来调用系统底层指令，去解析、执行 Java 程序，从而在该操作系统中运行。

![Java 虚拟机机制](http://oslynwh8c.bkt.clouddn.com/image/hexo/java%20%E8%99%9A%E6%8B%9F%E6%9C%BA%E6%9C%BA%E5%88%B6.png)

所以同一个 Java 程序在各个不同的操作系统中都可以执行，这样就实现了 Java 程序的跨平台性。



### 开发环境和工具

使用任何一门语言之前，都必须首先搭建环境。

![JDK 与JRE](https://pic4.zhimg.com/0cc3f4a15d3184391a98a7b1c58f6e5f_b.jpg)



#### JRE

JRE 全称是 Java Runtime Environment： **Java 运行环境**。

包括 **Java 虚拟机（ JVM ）**和 **Java 程序所需的核心类库** 等，是运行 Java 程序所必须的环境。



#### JDK

JDK 全称是 Java Development Kit：**Java 开发工具包** 。

JDK 不仅包含了 **Java 的开发工具**，也包括了 **JRE**。 JDK 是提供给 Java 开发人员使用的。

**Java 的开发工具** 有 **编译工具（ javac.exe ）、打包工具 ( jar.exe )** 等。



所以，我们只需要安装 JDK 即可。 

[下载地址](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)



#### 配置环境变量

下载安装 JDK 后，在DOS命令行窗口中，可以在其bin目录下执行 javac 命令，但这样非常不方便。

为了在DOS命令行窗口中，在任何目录下都能够执行javac命令，就需要将javac.exe命令文件所在目录的
路径放入path环境变量中，这就是配置环境变量。

> 原理:
>
> 在DOS命令提示符窗口中输入某个命令后，Windows系统会首先在当前目录下查找是否存在该命令文件可以执行。如果没有，Windows系统就会在path环境变量路径中查找。如果查找到，就会执行该命令。如果还没有找到，那么就会提示如下信息。



配置方法如下：

1. 右击 我的电脑 ->属性 ->高级 ->环境变量 -> 在 **系统变量** 中找到 `path`.
2. 点击新建 -> 变量名：`JAVA_HOME`，变量值：你的java所在路径，如：C:\Java\jdk1.7.0_21，这个值只要到这目录就差不多了，不要在后面加\bin等东西了。
3. 然后找到 path -> 双击，值里面别去改它， 在最前面加 `%JAVA_HOME%\bin;` ( 注意 英文分号）。
4. 设置环境变量 **classpath** ，值：`.;%JAVA_HOME%\lib\dt.jar;%JAVA_HOME%\lib\tools.jar `最前面有个点，代表的是当前路径，最后面最好不加分号。



为了验证是否配置成功，在任意位置新开一个 DOS 命令窗口，再输入 **java -version** ，如果能正常显示 java 的版本号，就说明配置成功了。 

​			

#### javac 命令和 java 命令

java运行分两部分：一个是编译，一个是运行。

* javac：负责的是编译部分，当执行javac时，会启动java的编译器程序，对指定扩展名的 .java 文件进行编译。生成了 JVM 可以识别的字节码文件，也就是class文件，java的运行程序。
* java：负责运行的部分。会启动 JVM 加载运行时所需要的类库，并对 class 文件进行执行。

![java 编译运行](http://oslynwh8c.bkt.clouddn.com/image/hexo/java%20%E7%BC%96%E8%AF%91%E8%BF%90%E8%A1%8C.png)



### Hello World！

`Hello World` 代表学习计算机语言的第一个入门小程序。现在泛指接触任何新事物的第一步。

学习 java，第一步就是著名的 `Hello World` ，现在就写第一个 Java 程序。

1. 新建一个文件 `Hello.java` (.java 是后缀名)，打开后编辑如下：

   ```java
   class Hello{
   	
   	public static void main(String[] args){
   		System.out.println("hello world!");
   	}
   }
   ```

2. 保存后，打开 DOS 命令，cd到文件目录下，首先编译: `javac  Hello.java`

3. 然后运行：`java Hello`

 ​![hello world](http://oslynwh8c.bkt.clouddn.com/image/hexo/Hello%20world.png)

可以看到 DOS 窗口里面，打印了 「hello world！」，这句是程序里面写的。

至此，完成了第一个 Java 程序。



### 常见的 DOS 命令

DOS命令窗口的启动：`win+R` 唤出 Window 运行命令，输入`cmd`  运行 DOS 窗口。

e:	e(盘符)+" : " 转到E盘

ipconfig：	查看IP配置

dir	：	列出当前目录下的文件以及文件夹

md	：	创建目录（文件夹）

rd	：	删除目录

cd	：	进入指定目录

cd..	：	退出当前目录，返回到上一级目录

cd\	：	退回到根目录

del	：	删除文件

del*：	删除所有文件

del*.文件扩展名：	删除同一类型的文件

echo：	创建文件。

cls：	清屏

help：	显示所有命令及描述

exit：	退出dos命令行		
​	