---
title: Java语法基础（二）
date: 2015-07-29 09:14:18
categories: Java基础
tags:
 - Java 基础
---

![coffee](http://oslynwh8c.bkt.clouddn.com/image/hexo/coffee.jpg)

### 前言

Java 语法基础第二部分，包括程序流程控制、函数、数组。

这里有许多转载的部分，如果侵权，请告知。

<!-- more -->



### 程序流程控制

#### 1.判断结构

if 语句：

```java
	//格式一：
	if ( 条件表达式 ) {
          执行语句;
    }

	//格式二：
    if ( 条件表达式 ) {
         执行语句;
    } else {
         执行语句;
    }

	//格式三：
  	if(条件表达式) {
         执行语句；
    } else if (条件表达式) {
         执行语句；
    }
    ……
    else {
        执行语句；
    }
```



#### 2.选择结构

 switch语句:

```java
    switch(表达式)
    {
          case 取值1:
               执行语句；
               break；
          case 取值2:
               执行语句；
               break；
          …...
          default:
               执行语句；
               break；
    }
```

​    switch语句特点：

1. switch语句选择的类型只有四种：byte，short，int，char，在JDK1.7之后增加了String类型，一共五种。
2. case与default没有顺序。先执行第一个case，没有匹配的case执行default。
3. 结束switch语句的两种情况：①遇到break，②执行到switch语句结束。
4. 如果匹配的case或者default没有对应的break，那么程序会继续向下执行，运行可以执行的语句，直到遇到break或者switch结尾结束。
5. 进入switch语句后，执行顺序是先执行case，然后从上到下，最后再执行default。即使default放在case上面，执行顺序也不变。



#### 3.循环结构

##### for 循环语句：

```java
/*　　for(初始化表达式;条件表达式;循环后的操作表达式) {
　　　　　循环体;   
　　  }
*/	
			//for 循环
           for(int x = 0; x < 3; x++){
                 for(int y = 0; y < 4; y++){
                       System.out.println("ok" );
                 }
           }
```



##### while 循环语句：

```java
/*    　　while(判断条件语句) {
 　　　　　　循环体语句;
 　　　　　　控制条件语句;
   　　　}
*/
		//while 循环
        int sum = 0;                    //定义初始和为0
        int i = 1;                        //定义开始求和的第一个数
        while (i <= 100) {                //判断条件语句
            sum += i;                    //sum = sum + i;
            i++;                        //让变量i自增
        }
```



##### do....while 循环语句：

```java
/*　　　　do {
　　　　　　循环体语句;
　　　　　　控制条件语句;
　　　　}while(判断条件语句);
*/
		//do ... while 循环语句
       int sum = 0;                                //定义变量sum,用于存储求和的值
       int i = 1;                                    //定义变量i
       do {                                         // do  就是 干
           //System.out.println("i = " + i);             //循环体语句
           sum +=i;
           i++;
       }
       while (i <= 100); 
```



#### 其他流程控制

##### break语句

 break 语句应用范围：选择结构和循环结构。

break 语句的作用：跳出此次流程。

```java
class BreakDemo {
       public static void main(String[] args){
         for(int x = 0; x < 3; x++){
            for(int y = 0; y < 4; y++){
                  System.out.println("x = " + x);
                  break;
            }     
         }
      }
}
```



##### continue语句

continue 语句应用范围：循环结构。

continue 语句的作用：结束本次循环继续下次循环。

```java
class ContinueDemo {
       public static void main(String[] args){
         for(int x = 0; x < 11; x++){
                   if(x % 2 == 0)
                         continue;
                  System.out.println("x = " + x);
         }
      }
}
```





### 函数

#### 函数的定义

函数就是定义在类中的具有特定功能的一段独立小程序，函数也称为方法。

 函数的格式：

```java
/*  修饰符 返回值类型 函数名(参数类型 形式参数1，参数类型 形式参数2，...) {
          执行语句;
          return 返回值;
    }

    返回值类型：函数运行后的结果的数据类型。
    参数类型：是形式参数的数据类型。
    形式参数：是一个变量，用于存储调用函数时传递给函数的实际参数。
    实际参数：传递给形式参数的具体数值。
    return：用于结束函数。
    返回值：该函数运算后的结果，该结果会返回给调用者。
*/    

class FunctionDemo{
       public static void main(String[] args){
            int c = add(3,4);
            System.out.println("c = " + c);
      }
	 	//函数示例，此函数用于求和 ，在 main 函数中被调用
      public static int add(int a, int b){
             return a + b;
      }
}
 
```



#### 函数的特点

1. 定义函数可以将功能代码进行封装。
2. 便于对该功能进行复用。
3. 函数只有被调用才会被执行。
4. 函数的出现提高了代码的复用性。

> 1. 对于函数没有具体返回值的情况，返回值类型用关键字void表示，那么该函数中的return语句如果在最后一行可以省略不写，或者写上return;
> 2. 函数中只能调用函数，不可以在函数内部定义函数。否则，编译时期就会报错。
> 3. 定义函数时，函数的结果应该返回给调用者，交由调用者处理。



  写函数时要有两个明确：

1. 明确要定义的功能最后的结果是什么？
2. 明确在定义该功能的过程中，是否需要未知内容参与运算。

#### 函数的重载

​    重载的定义：
​    在同一个类中，允许存在一个以上的同名函数，只要它们的参数个数或者参数类型不同即可。

​    重载的作用：
​    方便于阅读，优化了程序设计。

```java
class FunctionDemo{
       public static void main(String[] args){
             System.out.println(add(3,4));
             System.out.println(add(3.0,4.0));
             System.out.println(add(3,4,5));
      }

       //加法运算，两个整数的和
       public static int add(int a, int b){
             return a + b;
      }

       //加法运算，两个小数的和
       public static double add(double a, double b){
             return a + b;
      }

       //加法运算，三个整数的和
       public static int add(int a, int b,int c){
             return add(a,b) + c;
      }
}
```

函数的功能一样，仅仅是参与运算的未知内容不同时，可以定义多函数，却使用统一函数名称，这样方便阅读。在调用时，虚拟机通过参数列表的不同来区分同名函数。

> 1. 重载与返回值类型无关，只看参数列表。
> 2. java是严谨性语言，如果函数调用出现不确定性，会编译失败。



### 数组

#### 定义

​    数组就是 同一种类型数据的集合。其实，数组就是一个容器。

​    数组的作用：
​    可以自动给数组中的元素从0开始编号，方便操作这些元素



```java
//格式一
元素类型[] 数组名 = new 元素类型[元素个数或数组长度];

int[] arr = new int[5];

//格式二
元素类型[] 数组名 = new 元素类型[]{元素，元素，……};

int[] arr = new int[]{3,5,1,7};
int[] arr = {3,5,1,7}; //简写形式
```



#### 内存分配

Java程序在运行时，需要在内存中分配空间。为了提高运算效率，又对空间进行了不同区域的划分，因为每一片区域都有特定的处理数据方式和内存管理方式。

内存的划分：

1. 寄存器

   JVM内部虚拟寄存器，存取速度非常快，程序不可控制。

2. 本地方法区

3. 方法区

   用于存储已经被虚拟机加载的类信息，常量，静态变量和即时编译器（JIT）编译后的代码等数据。

4. 栈内存

   用于存储局部变量，当变量所属的作用域一旦结束，所占空间会自动释放。

5. 堆内存(包含常量池)

   数组和对象，通过 new 建立的实例都存放在堆内存中。每一个实例都有内存地址值。 

   实例中的变量都有默认初始化值，根据类型的不同而不同。整数类型是0，小数类型是0.0或0.0f，boolean类型是false，char类型是'\u0000'。
   如果将数组的引用实例设置为null，也就是实例不再被使用，那么会在不确定的时间内被垃圾回收器回收。

示例：

 `int[] arr = new int[4];`

内存分配图：

![数组内存分配](http://oslynwh8c.bkt.clouddn.com/image/hexo/%E6%95%B0%E7%BB%84%E5%86%85%E5%AD%98%E5%88%86%E9%85%8D.jpg)



#### 数组操作常见操作

​    对数组操作最基本的动作就是存和取。
​    核心思想：就是对角标的操作。

##### 1.获取最大值（最小值）

```java
class ArrayDemo{
       public static void main(String[] args) {
             int[] arr= {89,34,-270,17,3,100};
             int max = getMax(arr);
             System.out.println("max = " + max);
       }

       public static int getMax(int[] arr){
             int maxElement = arr[0];//定义变量记录住每次比较后较大的值。
             //对数组中的元素进行遍历取出，和变量中记录的元素进行比较。
         	 for(int x = 1; x < arr.length; x++){	
                   if(arr[x] > maxElement)
                        maxElement = arr[x];//遍历结果，该变量记录就是最大值
            }
            return maxElement;
      }
}
```



##### 2.排序（选择、冒泡排序）

```java
class ArrayDemo{
       public static void main(String[] args) {
             int[] arr= {89,34,-270,17,3,100};
             System.out.print("排序前数组：" );
             printArray(arr);
             selectSort(arr); //选择排序
         	 //bubbleSort(arr); //冒泡排序
             System.out.print("排序后数组：" );
             printArray(arr);
      }

  		//选择排序
       public static void selectSort(int[] arr){
             for(int x = 0; x < arr.length - 1; x++){
                   int num = arr[x];
                   int index = x;
                   for(int y = x + 1; y < arr.length; y++){
                         if(num > arr[y]){
                              num = arr[y];
                              index = y;
                        }
                  }
                  //如果最小的就是自己，就没有必要执行swap操作
                  if(index != x)
                        swap(arr,x,index);
            }
      }
      
      public static void swap(int[] arr, int a,int b){
            int temp = arr[a];
            arr[a] = arr[b];
            arr[b] = temp;
      }
      
  		//冒泡排序
        public static void bubbleSort(int[] arr){
             for(int x = 0; x < arr.length - 1; x++){
                   for(int y = 0; y < arr.length - 1 -x; y++){
                         if(arr[y] > arr[y+1]){
                              int temp = arr[y];
                              arr[y] = arr[y+1];
                              arr[y+1] = temp;
                        }
                  }
            }
      }
  
       public static void printArray(int[] arr){
            System.out.print("[" );
            for(int x = 0; x < arr.length; x++){
                   if(x != arr.length - 1)
                        System.out.print(arr[x] + "," );
                   else
                        System.out.println(arr[x] + "]" );
            }
      }
}  
```



##### 3.折半查找（二分查找）

 如果一个数组是无序的，那么可以通过简单遍历查找的方式查找到某个元素所在的角标。但是如果一个数组是有序的，那么就可以通过一种更高效的方式达到相同的目的，也就是二分查找。

```java
class ArrayDemo{
       public static void main(String[] args) {
         int[] arr= {13,15,19,28,33,45,78,106};
         int index = binarySearch(arr,78);
         System.out.println("index = " + index);
      }
     
      public static int binarySearch(int[] arr, int key){
            int max,min,mid;
            min = 0;
            max =arr. length - 1;
            mid = (max + min)/2;

             while(arr[mid] !=key){
                   if(key > arr[mid])
                        min = mid + 1;
                   else if (key < arr[mid])
                        max = mid - 1;
                   if(max < min)
                         return -1;
                  mid = (max + min)/2;
            }
            return mid;
     }
}
```





#### 数组操作常见问题

1. 数组脚标越界异常(ArrayIndexOutOfBoundsException)：访问到了数组中的不存在的脚标时发生

```java
            int[] arr = new int[3];
            System. out.println(arr[3]);//运行此，会抛出数组脚本越界的异常
```

2. 空指针异常(NullPointerException)：当引用型变量没有指向任何实体时，用其操作实体，就会发生该异常

```java
            int[] arr = null;
            System. out.println(arr[0]);//运行此，会抛出空指针异常
```

​