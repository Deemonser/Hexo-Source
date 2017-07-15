---
title: Hello Kotlin
date: 2017-05-24 17:37:58
categories: Kotlin学习
tags: 
 - kotlin
---

![Google I/O](https://static.oschina.net/uploads/space/2017/0518/082308_VxFU_2894582.jpg)

### 前言

今年 Google I/O 大会上，**Google 宣布，将 Kotlin 语言作为安卓开发的一级编程语言，**这消息火遍整个 Android 开发圈。不过，有些开发者似乎过度解读 Google 的意图，认为 Kotlin 要取代 Java 成为 Android 开发的标配？

从 Kotlin 官方博文中我们可以看到这么一句话：Today, at the Google I/O keynote, the Android team announced first-class support for Kotlin. 部分人产生误解是因为first-class support，这句话意思其实是提供一级支持的意思，跟 JS 中常说的函数是一等公民是一个意思，但我们知道一等公民并不止一个，**Android 的一级编程语言还包括 java 、C++。**

所以 Google 会把 Android framework 层用Kotlin 再写一遍这事儿可能性很小。

<!-- more -->



### 简介

Kotlin 是运行在 JVM 上的一门静态类型语言，它是圣彼得堡的一个小岛的名字命名的，作者是大名鼎鼎的 **Jetbrains **公司。

Jetbrains 有一系列耳熟能详的产品，诸如 Android Studio, IntelliJ IDEA, 还有前端的WebStorm, PhpStorm（在这些工具上使用 Kotlin 非常简单，只需要按装 Kotlin 插件）。

- 2011年7月，JetBrains 推出 Kotlin 项目。
- 2012年2月，JetBrains 以 Apache 2 许可证开源此项目。
- 2016年2月15日，Kotlin v1.0（第一个官方稳定版本）发布。
- 2017 Google I/O 大会，Kotlin 转正。

从 [Kotlin 官网 中文版](https://www.kotlincn.net/) 介绍来看，这近乎是一门野心勃勃的语言。

![img](http://img.blog.csdn.net/20170612135229658)

- 基于 JVM 编程，这意味着 Kotlin 可以写服务器代码；
- 基于 Android 和 Browser 编程，这意味着不仅可以写客户端编程，连前端开发都可以；
- 基于 Native 编程，直接绕过 JVM 与底层代码打交道，好彪悍；

不过 Kotlin-Native 项目还在开发阶段，不知道最终推出的项目如何，还是蛮期待的，感兴趣的朋友可以关注它的 [Github 更新](https://github.com/JetBrains/kotlin-native)



### 特性

#### 1. 语法简单，不啰嗦

```kotlin
    var currentVersionCode = 1  //变量当前的版本号，类型Int可以根据值推断出来
    var currentVersionName: String = "1.0"  //显式标明类型
    val BASEURL = "https://deemonser.github.io/"    //BASEURL 类型(String)可以根据值推断出来


    //methods
    fun ma(args: Array<String>) {
        println(args)
    }

    // class
    open class Person {
    }

    class Animal : Person() {
    }

    // data class  自动生成getter，setting，hashcode和equals等方法
    data class Book(var name: String, val price: Float, var author: String)

    //支持默认参数值，减少方法重载
    fun show(name: String, age: Int = 18, address: String = "深圳市") {

    }
```

- Kotlin 支持类型推断，没有 Java 那样的啰嗦。
- 另外用 var 表示变量，val 表示常量更加的简洁
- 方法也很简单，连 function 都缩写成了 fun。
- 类的继承和实现很简单，使用:即可
- Kotlin 每个句子都不需要加分号 (;)




#### 2. 空指针安全

空指针（NullPointerException 或 NPE）是我们使用 Java 开发程序中最常见的崩溃了。因为在 Java 中我们不得不写很多防御性的代码，比如这样：

```java
public void test(String string) {
    if (string != null) {
        char[] chars = string.toCharArray();
        if (chars.length > 10) {
            System.out.println(((Character)chars[10]).hashCode());
        }
    }
}
```

在 Kotlin 中空指针异常得到了很好的解决。

- 在类型上的处理，即在类型后面加上?，即表示这个变量或参数以及返回值可以为 null，否则不允许为变量参数赋值为 null 或者返回 null
- 对于一个可能是 null 的变量或者参数，在调用对象方法或者属性之前，需要加上?，否则编译无法通过。

如下面的代码就是 Kotlin 实现空指针安全的一个例子，而且相对 Java 实现而言，简直是一行代码搞定的。

```kotlin
    fun testNullSafeOperator(string: String?) {
        System.out.println(string?.toCharArray()?.getOrNull(10)?.hashCode())
    }

        testNullSafeOperator(null)	//null
        testNullSafeOperator("12345678901")	//49
        testNullSafeOperator("123")	// null
```



关于空指针安全的原理，可以参考这篇文章[研究学习 Kotlin 的一些方法](http://droidyue.com/newtab.html?url=http%3A%2F%2Fdroidyue.com%2Fblog%2F2017%2F05%2F08%2Fhow-to-study-kotlin%2F)。



#### 3. 支持方法扩展

很多时候，Framework 提供给我们的 API 往往都时比较原子的，调用时需要我们进行组合处理，因为就会产生了一些 Util 类，一个简单的例子，我们想要更快捷的展示 Toast 信息，在 Java 中我们可以这样做。

```java
    public static void toast(Context context, String msg) {
        Toast.makeText(context,msg,Toast.LENGTH_LONG).show();
    }
```

但是 Kotlin 的实现却让人惊奇，我们只需要重写扩展方法就可以了，比如这个 longToast 方法扩展到所有的 Context 对象中，如果不去追根溯源，可能无法区分是 Framework 提供的还是自行扩展的。

```kotlin
fun Activity.toast(string: String, duration: Int = Toast.LENGTH_LONG) {
    Toast.makeText(this, "hehe", Toast.LENGTH_LONG).show()
}
```

注意：Kotlin 的方法扩展并不是真正修改了对应的类文件，而是在编译器和 IDE 方面做得处理。使我们看起来像是扩展了方法。



#### 4. Lambda, 高阶函数，Streams API, 函数式编程支持

所谓的 Lambda 表达式是匿名函数，这使得我们的代码会更加的简单。比如下面的代码就是 lambda 的应用。

```kotlin
        findViewById(R.id.text).setOnClickListener {
            Log.d("MainActivity", "$it was clicked")
        }
```

所谓的高阶函数就是：  

- 可以接受函数作为参数
- 也可以返回函数作为结果

举一个接受函数作为参数的例子。在 Android 开发中，我们经常使用 SharedPreference 来存储数据，如果忘记调用 apply 或者 commit 则数据修改不能应用。

```java
        PreferenceManager.getDefaultSharedPreferences(this)
                .edit()
                .putString("key", "abc")
                .apply();
```

利用 Kotlin 中的高阶函数的功能，我们能更好的解决这个问题。

```kotlin
    fun SharedPreferences.editor(f: (SharedPreferences.Editor) -> Unit) {
        val editor = edit()
        f(editor)
        editor.apply()
    }

//使用
PreferenceManager.getDefaultSharedPreferences(this).editor { it.putString("key", "abc") }
```

当然这上面的例子中我们也同时使用了方法扩展这个特性。



Kotlin 支持了 Streams API 和方法引用，这样函数式编程更加方便。比如下面的代码就是我们结合 Jsoup，来抓取某个 proxy 网站的数据，代码更加简单，实现起来也快速。

![屏幕快照 2017-05-18 下午 3.49.30.png](https://images2015.cnblogs.com/news/66372/201705/66372-20170518163609150-1645171105.png)



#### 5. 字符串模板

无论是 Java 还是 Android 开发，我们都会用到字符串拼接，比如进行日志输出等等。在 Kotlin 中，字符串模板是支持的，我们可以很轻松的完成一个字符串数组的组成：

```kotlin
        val book = Book("Effective Java", 52f, "Effective Java Second Edition")
        println("${book.name} is very good! It's only ￥${book.price}")
```

注意：关于字符串拼接可以参考这篇文章 [Java 细节：字符串的拼接](http://droidyue.com/newtab.html?url=http%3A%2F%2Fdroidyue.com%2Fblog%2F2014%2F08%2F30%2Fjava-details-string-concatenation%2F)。





### 基本语法

#### 声明类型

```kotlin
var age: Int = 18 				//可简写成 var age = 18
val name: String = "Deemons"	//可简写成 val name = "Deemons"
```

在Kotlin中变量声明有两种类型，

1. **var** 声明变量， var age: Int= ""中，age是变量名，：Int,表明该变量是Int类型变量，后面就是赋值语句。我们也可以这样写var str= ""省略了声明变量类型，它可以根据赋的值而自动推断出类型。
2. **val**  声明常量，赋值后不能修改。



#### 函数

```kotlin
fun sum(a: Int, b: Int): Int {
    return a + b
}
```

可以看到函数声明 fun ，函数名称 sum ，参数是两个 Int 类型的变量 a，b，返回值是 Int 类型。

这种仅有一句表达式的函数，可以将表达式作为函数体、返回值类型自动推断进行简写：

```kotlin
fun sum(a: Int, b: Int) = a + b
public fun sum(a: Int, b: Int): Int = a + b	//如果方法是一个public的，则必须明确写出返回类型
```

如果函数无返回值

```kotlin
fun printSum(a: Int, b: Int): Unit {
    println("sum of $a and $b is ${a + b}")
}
```

`Unit` 返回类型可以省略：

```kotlin
fun printSum(a: Int, b: Int) {
    println("sum of $a and $b is ${a + b}")
}
```



#### 区间

区间表达式由具有操作符形式 `..` 的 `rangeTo` 函数辅以 *in* 和 *!in* 形成。 区间是为任何可比较类型定义的，但对于整型原生类型，它有一个优化的实现。以下是使用区间的一些示例

```kotlin
if (i in 1..10) { // 等同于 1 <= i && i <= 10
    println(i)
}


for (i in 1..4) print(i) // 输出“1234”
for (i in 4..1) print(i) // 什么都不输出
for (i in 4 downTo 1) print(i) // 输出“4321”

for (i in 1..4 step 2) print(i) // 输出“13”
for (i in 4 downTo 1 step 2) print(i) // 输出“42”

for (i in 1 until 10) {   // i in [1, 10) 排除了 10
     println(i)
}
```



#### 集合

Kotlin 区分可变集合和不可变集合

![kotlin 集合接口](https://cdn.kymjs.com/kotlin/6-1-1.png)

- 不可变集合：`List<out T>` 类型是一个提供只读操作如 `size`、`get`等的接口。和 Java 类似，它继承自`Collection<T>` 进而继承自 `Iterable<T>`。（lists、sets、maps 等）
- 可变集合：`MutableCollection<E>` 类型是一个提供写操作如`add`、`remove`等的接口。开头是 `MutableXXX` 那就是一个跟 Java 中用法一致的集合类。（mutableList、mutableSet、mutableMap等）

Kotlin 没有专门的语法结构创建 list 或 set。 要用标准库的方法，如 `listOf()`、 `mutableListOf()`、`setOf()`、 `mutableSetOf()`。

```kotlin
val mutableList = mutableListOf(0, 1, 2, 3) 

var list = listOf(0, 1, 2)	//创建一个 ArrayList<> 对象
val ss = setOf(1, 2, 4)	//创建一个 Set<> 对象
val map = mapOf('a' to "apple", 'b' to "build") //创建 一个 linkedMap<> 对象
```

有一点需要知道的是，这些方法的返回值实际上返回的是 Java 的集合对象，目前只支持返回这些对象：

```kotlin
java.util.ArrayList
java.util.LinkedHashMap
java.util.HashMap
java.util.LinkedHashSet
java.util.HashSet
java.util.SortedSet
java.util.TreeSet
```

这些类型是[协变的](https://www.kotlincn.net/docs/reference/generics.html#型变)。这意味着，你可以把一个 `List<Dog>` 赋值给 `List<Animal>`。但对于可变集合类型这是不允许的。

```kotlin
val animal: List<Animal> = listOf<Dog>(Dog()) //协变
val animal: MutableList<Animal> = mutableListOf<Dog>(Dog())	//编译报错
val animal: MutableList<Animal> = mutableListOf<Animal>(Dog()) //编译通过

val list: List<String> = mutableListOf("a","b")	// 可变集合 赋值给 不可变集合 
val list: MutableList<String> = listOf<String>("a", "b")	//编译报错
```



#### 条件语句

1. if表达式

   ```kotlin
   // 传统用法
   var max = a 
   if (a < b) max = b

   // With else 
   var max: Int
   if (a > b) {
       max = a
   } else {
       max = b
   }
    
   // 作为表达式 ,kotlin 中没有三目运算符
   val max = if (a > b) a else b 

   //if的分支可以是代码块，最后的表达式作为该块的值
   val max = if (a > b) {
       print("Choose a")
       a
   } else {
       print("Choose b")
       b
   }
   ```

   ​

2. when表达式

   ```kotlin
   //when 就是加强版的switch
      when (x) {
               1 -> println("x == 1")
               2,3 -> println("x is 2 or 3")				//多个分支条件放在一起，用逗号分隔
               in 2 .. 6 -> println("x in 2..6")			//检查一个区间或者集合中
               !in 7 until  9 -> println("x not in 7..8")
               is Int -> println("x is Int type")			//检查类型（有智能转换）
               parseInt("888") -> println("x == 888")		//任意表达式（而不只是常量）作为分支条件
               else -> {
                   println("x is else")
               }
        }

   //when 也可以用来取代 if-else if链。 如果不提供参数，所有的分支条件都是简单的布尔表达式，而当一个分支的条件为真时则执行该分支
   when {
       x.isOdd() -> print("x is odd")
       x.isEven() -> print("x is even")
       else -> print("x is funny")
   }

    //when 既可以被当做语句使用 也可以被当做表达式使用（必须覆盖所有情况，就是要有else）
     var y = when (x) {
               1 -> "this is one"
               else -> "this is other"
           }
   ```

   ​


#### 循环

*for* 循环可以对任何提供迭代器（iterator）的对象进行遍历，语法如下:

```kotlin
for (item in collection) print(item)
```

数组的 `for` 循环会被编译为并不创建迭代器的基于索引的循环,如果你想要通过索引遍历一个数组或者一个 list，你可以这么做：

```kotlin
        for (i in arrayOf('a', 'b', 'c')) {
            println(i)	// a b c
        }
    
        for (i in arrayOf('a', 'b', 'c').indices) {
            println(i)	// 0 1 2
        }
    
        for ((i,value) in arrayOf('a', 'b', 'c').withIndex()) {
            println("$i : $value") // 0 : a		1 : b		2 : c
        }
```



#### 类和继承

Kotlin 中使用关键字 *class* 声明类，类声明由类名、类头（指定其类型参数、主构造函数等）和由大括号包围的类体构成。类头和类体都是可选的； 如果一个类没有类体，可以省略花括号。

```kotlin
class Invoice public constructor(){ //标准
}

class Empty 	//省略花括号
```

在 Kotlin 中的一个类可以有一个**主构造函数**和一个或多个**次构造函数**。主构造函数是类头的一部分：它跟在类名（和可选的类型参数）后。

```kotlin
class Person constructor(firstName: String) {
}

//如果主构造函数没有任何注解或者可见性修饰符，可以省略这个 constructor 关键字
class Person(firstName: String) {
}

//主构造函数不能包含任何的代码。初始化的代码可以放到以 init 关键字作为前缀的初始化块中
class Customer(name: String) {
    init {
        logger.info("Customer initialized with value ${name}")
    }
}


// constructor 声明次构造函数
class Person {
    constructor(parent: Person) {
        parent.children.add(this)
    }
}
//如果类有一个主构造函数，每个次构造函数需要委托给主构造函数， 可以直接委托或者通过别的次构造函数间接委托。
 class Person(val name: String) {
        constructor(name: String, parent: Person) : this(name)

        constructor(name: String, age: Int, parent: Person) : this(name, parent)
  }
```



要创建一个类的实例，我们就像普通函数一样调用构造函数：

```kotlin
val invoice = Invoice()

val customer = Customer("Joe Smith")
```

注意 Kotlin 并没有 *new* 关键字。



在 Kotlin 中所有类都有一个共同的超类 `Any`，这对于没有超类型声明的类是默认超类：

```kotlin
class Example // 从 Any 隐式继承
```

`Any` 不是 `java.lang.Object`；尤其是，它除了 `equals()`、`hashCode()`和`toString()`外没有任何成员。 

要声明一个显式的超类型，我们把类型放到类头的冒号之后：

```kotlin
open class Base(p: Int)

class Derived(p: Int) : Base(p)
```

如果该类有一个主构造函数，其基类型可以（并且必须） 用（基类型的）主构造函数参数就地初始化，如上面的例子。



如果一个类 实现两个不同接口，但两接口拥有同名方法时，编译器会要求覆盖同名方法。

```kotlin
 open class A {
        open fun f() {
            print("A")
        }
    
        fun a() {
            print("a")
        }
    }
    
    interface B {
        fun f() {
            print("B")
        } // 接口成员默认就是“open”的
    
        fun b() {
            print("b")
        }
    }
    
    class C() : A(), B {
        // 编译器要求覆盖 f()：
        override fun f() {
            super<A>.f() // 调用 A.f()
            super<B>.f() // 调用 B.f()
        }
    }
```



Kotlin 的细节东西太多，以上都是基础，详情还需查看[Kotlin官网](https://kotlinlang.org/) 或 [Kotlin中文网](https://www.kotlincn.net/)。