---
title: Java 反射
date: 2015-09-02 12:28:19
categories: Java基础
tags:
 - Java 基础
---

![coffee](http://oslynwh8c.bkt.clouddn.com/image/hexo/coffee-bean.jpg)

反射是 Java 的高级技能，在平时开发中很少接触到。

在此做一个简单的笔记备忘。

<!-- more -->



### 反射

Java 反射机制是在运行状态中，对于任意一个类（class文件），都能够知道这个类的所有属性和方法；对于任意一个对象，都能够调用它的任意一个方法和属性；这种动态获取的信息以及动态调用对象方法的功能称为java语言的反射机制。

简单来说，反射就是通过class文件的对象Class，去使用该文件中的成员变量，构造方法，成员方法。



### Class

Java 中，所有的类文件都有共同属性，向上抽取，把这些共性内容封装成一个类，这个类就叫 **Class** （描述字节码文件的对象）。

Class类中包含属性有field（字段）、method（方法）、construction（构造函数）。



### Class 与 class 的区别

- Class：Class是Java程序中各个Java类的总称；它是反射的基石，通过Class类来使用反射
- class：Java中的类,用于描述一类事物的属性和方法。

### 获取 Class  的 3 种方式

```java
import cn.itcast.bean.Person;

//要想要对字节码文件进行解剖，必须要有字节码文件对象。
public class ReflectDemo
{
        public static void main(String[] args) throws ClassNotFoundException {
                getClassObject_1();
                System.out.println("--------------------");
                getClassObject_2();
                System.out.println("--------------------");
                getClassObject_3();
        }

   /*
        * 获取字节码对象的方式：
        * 方式一：Object类中的getClass()方法的。
        * 想要用这种方式，必须要明确具体的类，并创建对象。
        * 麻烦。
        */
        public static void getClassObject_1(){               
                Person p = new Person();
                Class clazz = p.getClass();

                Person p1 = new Person();
                Class clazz1 = p1.getClass();

                System.out.println(clazz == clazz1);
        }

        /*
        * 方式二：任何数据类型都具备一个静态的属性.class来获取其对应的Class对象。
        * 相对简单，但是还是要明确用到类中的静态成员。
        * 还是不够扩展。
        */
        public static void getClassObject_2(){              
                Class clazz = Person.class;
                Class clazz1 = Person.class;

                System.out.println(clazz == clazz1);
        }

        /*
        * 方式三：只要通过给定的类的字符串名称就可以获取该类，更为扩展。
        * 可以用Class类中的方法完成。
        * 该方法就是forName。
        * 这种方法只要有名称即可，更为方便，扩展性更强。
        */
        public static void getClassObject_3() throws ClassNotFoundException {               
                //可以把类的字符串名称写到配置文件中，然后读取出来。
                String className = "cn.itcast.bean.Person";
                Class clazz = Class.forName(className);

                System.out.println(clazz);
        }
}
```

### 获取 Class 中的构造函数

```java
import cn.itcast.bean.Person;
import java.lang.reflect.Constructor;
import java.lang.reflect.InvocationTargetException;

public class ReflectDemo
{
        public static void main(String[] args) throws Exception {
                createNewObject_1();
                System.out.println("--------------------");
                createNewObject_2();
        }

        public static void createNewObject_1() throws Exception {
                //早期：new时候，先根据被new的类的名称找寻该类的字节码文件，并加载进内存，
                //并创建该字节码文件对象，并接着创建该字节文件的对应的Person对象。
                //Person p = new Person();

                //现在：
                String name = "cn.itcast.bean.Person";
                //找寻该文件类文件，并加载进内存，并产生Class对象。
                Class clazz = Class.forName(name);
                //如何产生该类的对象呢？
                Object obj = clazz.newInstance();//调用Person的空参构造函数
        }

        public static void createNewObject_2() throws Exception {

                //Person p = new Person("小强",39);

                /*
                * 当获取指定名称对应类中的所体现的对象时。
                * 而该对象初始化不使用空参数构造函数该怎么办呢？
                * 既然是通过指定的构造函数进行对象的初始化。
                * 所以应该先获取到该构造函数，通过字节码文件对象即可完成。
                * 该方法是：getConstructor(parameterTypes);
                */

                String name = "cn.itcast.bean.Person";
                //找寻该名称类文件，并加载进内存，并产生Class对象。
                Class clazz = Class.forName(name);
                //获取到了指定的构造函数对象
                Constructor constructor = clazz.getConstructor(int.class,String.class);
                //通过该构造器对象的newInstance方法进行对象的初始化。
                Object obj = constructor.newInstance(38,"小明");
        }
}
```

### 获取 Class 中的属性

```java
import cn.itcast.bean.Person;
import java.lang.reflect.Field;

public class ReflectDemo
{
        public static void main(String[] args) throws Exception {
                getFieldDemo();
        }
        
        /*
        * 获取字节码文件中的字段。
        */
        public static void getFieldDemo() throws Exception {
                Class clazz = Class.forName("cn.itcast.bean.Person");
                
                //getField只能获取所有可访问公共字段，private获取不到。
                //Field field = claszz.getField("age");

                //getDeclaredField可以获取到公共字段，也可以获取到私有字段。
                Field field = clazz.getDeclaredField("age");

                //对私有字段的访问必须取消权限检查，进行暴力访问。
                field.setAccessible(true);

                Object obj = clazz.newInstance();
                
                //为对象的属性赋值
                field.set(obj,89);
                
                //获取某对象的某属性值
                Object o = field.get(obj);

                System.out.println(field);
        }
}
```



### 获取 Class 中的方法

```java
import cn.itcast.bean.Person;
import java.lang.reflect.Method;
import java.lang.reflect.Constructor;

public class ReflectDemo
{
        public static void main(String[] args) throws Exception {
                getMethodDemo_1();
                System.out.println("---------------------------");
                getMethodDemo_2();
                System.out.println("---------------------------");
                getMethodDemo_3();
        }
        
        /*
        * 获取指定Class中的公共函数。
        */
        public static void getMethodDemo_1() throws Exception {

                Class clazz = Class.forName("cn.itcast.bean.Person");
                
                Method[] methods = clazz.getMethods();//获取的都是公有的方法

                methods = clazz.getDeclaredMethods();//获取本类中所有方法，包括私有。

                for(Method method : methods){
                        System.out.println(method);
                }
        }

        public static void getMethodDemo_2() throws Exception {

                Class clazz = Class.forName("cn.itcast.bean.Person");
                
                Method method = clazz.getMethod("show",null);//获取空参数一般方法

                Object obj = clazz.newInstance();

                Constructor constructor = clazz.getConstructor(int.class,String.class);
                obj = constructor.newInstance(37,"小明");

                method.invoke(obj,null);
        }

        public static void getMethodDemo_3() throws Exception {

                Class clazz = Class.forName("cn.itcast.bean.Person");
                //获取非空参数一般方法
                Method method = clazz.getMethod("paramMethod",String.class,int.class);

                Object obj = clazz.newInstance();

                Constructor constructor = clazz.getConstructor();
                obj = constructor.newInstance();

                method.invoke(obj,"小强",89);
        }
}
```

