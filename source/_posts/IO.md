---
title: IO
date: 2015-08-12 21:10:03
categories: Java基础
tags:
 - Java 基础
---

![stream](http://oslynwh8c.bkt.clouddn.com/image/hexo/stream.jpg)

### IO流

流是一组有顺序的，有起点和终点的字节集合，是对数据传输的总称或抽象。

IO流用来处理设备之间的数据传输。

Java对数据的操作是通过流的方式。Java用于操作流的对象都在IO包中。

<!-- more -->

流按方向分为：输入流、输出流，输入流和输出流相对于内存设备而言的。

- 将外设中的数据读取到内存中：输入。
- 将内存的数写入到外设中：输出。

流按操作数据分为两种：字节流与字符流。

![IO流](http://oslynwh8c.bkt.clouddn.com/image/hexo/IO%E6%B5%81.png)



字节流的抽象基类：InputStream，OutputStream。

字符流的抽象基类：Reader，Writer。

由这四个类派生出来的子类名称都是以其父类名作为子类名的后缀。



### 字符流

#### 字符流的由来

其实就是：字节流读取文字字节数据后，不直接操作而是先查指定的编码表，获取对应的文字。再对这个文字进行操作。

简单说：字节流+编码表。



```java
import java.io.FileWriter;
import java.io.IOException;

public class FileWriterDemo{
       public static void main(String[] args) throws IOException{
             //创建一个可以往文件中写入字符数据的字符输出流对象
             //既然是往一个文件中写入文字数据，那么在创建对象时就必须明确该文件（用于存储数据的目的地）
             //如果文件不存在，则会自动创建
             //如果文件存在，则会被覆盖
            FileWriter fw = new FileWriter("demo.txt" );
         
         	//构造函数中加入true，可以实现对文件进行续写
          	//FileWriter fw = new FileWriter("demo.txt" ,true);
            
             //调用Writer对象中的write(string)方法，写入数据
             //其实数据写入到临时存储缓冲区中
            fw.write( "abcde");
            
             //进行刷新，将数据直接写入到目的地中
            fw.flush();

             //关闭流，关闭资源，在关闭前会先调用flush刷新缓冲中的数据到目的地。
            fw.close();//close方法只能用一次。
         	//流关闭以后不能，不能再调用write方法，否则会报异常
      }
}

public class IOExceptionDemo{
       public static void main(String[] args){
            FileWriter fw  = null;
             try{
                  fw = new FileWriter("demo.txt" );
                  fw.write( "abcde");//此处有异常
            } catch(IOException e){
                  System.out.println(e.toString());
            } finally{//为防止代码异常导致流无法关闭，因此在finally中对流进行关闭
                   try{
                        fw.close();
                  } catch(IOException e){
                         throw new RuntimeException("关闭失败");
                  }
            }
      }
}
```



#### 读取一个文本文件(两种方式)

```java
import java.io.FileReader;
import java.io.IOException;

public class FileReaderDemo{
       public static void main(String[] args) throws IOException{

            FileReader fr = new FileReader("demo.txt" );
			//第一种读取方式：使用read()方法读取单个字符，到达末尾返回-1
             int ch = 0;
             while((ch = fr.read()) != -1){
                  System.out.println(( char)ch);
            }

             //第二种读取方式：使用read(char[])读取多个字符。
             //先创建字符数组
             //char[] buf = new char[3];
             //int len = 0;
             //while((len = fr.read(buf)) != -1){
             //     System.out.println( new String(buf,0,len));
             //}
            fr.close();
      }
}
```

#### 字符流的缓冲区

缓冲区的出现提高了对数据的读写效率。

缓冲区要结合流才可以使用。

作用：在流的基础上对流的功能进行了增强。



对应类：

- BufferedWriter
- BufferedReader

提高读写效率，使用缓冲区

```java
import java.io.BufferedWriter;
import java.io.FileWriter;
import java.io.IOException;

public class BufferedWriterDemo{
       public static void main(String[] args) throws IOException{
            FileWriter fw = new FileWriter("buf.txt" );

             //写入
             //创建了一个字符写入流的缓冲区对象，并且指定与要被缓冲的流对象相关联
            BufferedWriter bufw = new BufferedWriter(fw);
             for(int x = 1; x <= 4; x++){
                   //使用缓冲区的写入方法将数据先写入到缓冲区中
                  bufw.write( "abcdef" + x);
                   //写入内容换行方法：newLine();
                  bufw.newLine();
            }
             //使用缓冲区的刷新方法将数据刷目的地中
            bufw.flush();
             //关闭缓冲区，其实关闭的就是被缓冲的流对象
            fw.close();
         
         	//读取
            FileReader fr = new FileReader("buf.txt" );
         	//创建字符读取流的缓冲区对象
            BufferedReader bufr = new BufferedReader(fr);
            String line = null;
         	//读取一行
             while((line = bufr.readLine()) != null){
                  System.out.println(line);           
            }
         	fr.close();
      }
}
```

写入换行使用 BufferedWriter 类中的 newLine() 方法。
读取一行数据使用 BufferedReader 类中的 readLine() 方法。



### 字节流

基本操作与字符流类相同。但它不仅可以操作字符，还可以操作其他媒体文件。

```java
import java.io.FileOutputStream;
import java.io.IOException;

public class ByteStreamDemo{
       public static void main(String[] args) throws IOException {
            demo_write();
      }

       public static void demo_write() throws IOException {
             //1、创建字节输出流对象，用于操作文件
            FileOutputStream fos = new FileOutputStream( "bytedemo.txt");

             //2、写数据，直接写入到了目的地中
            fos.write( "abcdefg".getBytes());
      
             //关闭资源动作要完成
            fos.close();
      }
}
```

#### 字节流的缓冲区

 字节流的缓冲区：同样是提高了字节流的读写效率



使用缓冲，拷贝MP3 文件

```java
import java.io.BufferedInputStream;
import java.io.BufferedOutputStream;
import java.io.FileInputStream;
import java.io.FileOutputStream;
import java.io.IOException;

public class CopyMp3Test{
       public static void main(String[] args) throws IOException {
            copy_1();
            copy_2();
      }

       public static void copy_1() throws IOException {
            FileInputStream fis = new FileInputStream("0.mp3" );
            FileOutputStream fos = new FileOutputStream("1.mp3" );
      
             byte[] buf = new byte[1024];
             int len = 0;
             while((len = fis.read(buf)) != -1){
                  fos.write(buf,0,len);
            }
            fis.close();
            fos.close();
      }

       public static void copy_2() throws IOException {
            FileInputStream fis = new FileInputStream("0.mp3" );
            BufferedInputStream bufis = new BufferedInputStream(fis);

            FileOutputStream fos = new FileOutputStream("2.mp3" );
            BufferedOutputStream bufos = new BufferedOutputStream(fos);

             int ch = 0;
             while((ch = bufis.read()) != -1){
                  bufos.write(ch);
            }

            bufis.close();
            bufos.close();
      }
}
```





### 转换流

#### 转换流的由来

字符流与字节流之间的桥梁
方便了字符流与字节流之间的操作



转换流的应用：
字节流中的数据都是字符时，转成字符流操作更高效。



转换流：
InputStreamReader：字节到字符的桥梁，解码。
OutputStreamWriter：字符到字节的桥梁，编码。



InputStreamReader是字节流通向字符流的桥梁。

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStream;
import java.io.InputStreamReader;

public class TransStreamDemo{
      
       public static void main(String[] args) throws IOException {
             //字节流,控制台输入
            InputStream in = System.in;
             //将字节转成字符的桥梁，转换流
            InputStreamReader isr = new InputStreamReader(in);
             //对字符流进行高效装饰，缓冲区
            BufferedReader bufr = new BufferedReader(isr);           
            String line = null;
             //读取到了字符串数据
             while((line = bufr.readLine()) != null){
                   if("over" .equals(line))
                         break;
                  System.out.println(line.toUpperCase());//转换成大写
            }
      }
}
```

将文本复制到另一个文本中

```java
import java.io.BufferedReader;
import java.io.BufferedWriter;
import java.io.FileInputStream;
import java.io.FileOutputStream;
import java.io.IOException;
import java.io.InputStreamReader;
import java.io.OutputStreamWriter;

public class TransStreamDemo{
      
       public static void main(String[] args) throws IOException {

            BufferedReader bufr = new BufferedReader(new InputStreamReader(new FileInputStream("a.txt")));
            
            BufferedWriter bufw = new BufferedWriter(new OutputStreamWriter(new FileOutputStream("b.txt")));

            String line = null;

             while((line = bufr.readLine()) != null){
                   if("over" .equals(line))
                         break;

                  bufw.write(line.toUpperCase());
                  bufw.newLine();
                  bufw.flush();
            }
      }
}
```



### 流的操作规律

要弄清楚这个规律，是因为流对象太多，开发时不知道用哪个对象合适。
想要知道对象的开发时用到哪些对象，只要通过四个明确即可。

1、明确源和目的
​         源：InputStream Reader
​         目的：OutputStream Writer

 2、明确数据是否是纯文本数据
​         源：是纯文本：Reader
​                否：InputStream
​         目的：是纯文本：Writer
​                否：OutputStream 
​         到这里，就可以明确需求中具体要使用哪个体系。

3、明确具体的设备
​         源设备：
​         硬盘：File
​         键盘：System.in
​         内存：数组
​         网络：Socket流
​         目的设备：
​         硬盘：File
​         控制台：System.out
​         内存：数组
​         网络：Socket流

4、是否需要其他额外功能
​         是否需要高效（缓冲区）：
​         是，就加上buffer



### File类

 File类用来将文件或者文件夹封装成对象，方便对文件与文件夹的属性信息进行操作。

File对象可以作为参数传递给流的构造函数。

流只能操作数据，不能操作文件。

```java
import java.io.File;

public class FileDemo{
       public static void main(String[] args){
            constructorDemo();
      }

       public static void constructorDemo(){
             //可以将一个已存在的，或者不存在的文件或者目录封装成file对象
             //方式一
            File f1 = new File("d:\\demo\\a.txt" );

             //方式二
            File f2 = new File("d:\\demo" ,"a.txt" );
      
             //方式三
            File f = new File("d:\\demo" );
            File f3 = new File(f,"a.txt" );

             //考虑到Windows和Linux系统通用
            File f4 = new File("d:" + File.separator + "demo" + File.separator + "a.txt" );
      }
}
```

> File.separator是与系统有关的默认名称分隔符。在 UNIX 系统上，此字段的值为 '/'；在 Microsoft Windows 系统上，它为 '\\'。 



#### File的常用操作

##### 1、获取

获取文件名称
获取文件路径
获取文件大小
获取文件修改时间

```java
import java.io.File;
import java.text.DateFormat;
import java.util.Date;

public class FileMethodDemo{
       public static void main(String[] args){
            getDemo();
      }

       public static void getDemo(){
            File file1 = new File("a.txt" );
            File file2 = new File("d:\\demo\\a.txt" );

            String name = file2.getName();
            String absPath = file2.getAbsolutePath(); //绝对路径
            String path1 = file1.getPath();
            String path2 = file2.getPath();
             long len = file2.length();
             long time = file2.lastModified();
             //相对路径不同，父目录不同
             //如果此路径名没有指定父目录，则返回 null
            String parent1 = file1.getParent();
            String parent2 = file2.getParent();
                  
            Date date = new Date(time);
            DateFormat df = DateFormat.getDateTimeInstance(DateFormat.LONG,DateFormat.LONG);
            String str_time = df.format(date);

            System.out.println( "name:" + name);
            System.out.println( "absPath:" + absPath);
            System.out.println( "path1:" + path1);
            System.out.println( "path2:" + path2);
            System.out.println( "len:" + len);
            System.out.println( "str_time:" + str_time);
            System.out.println( "parent1:" + parent1);
            System.out.println( "parent2:" + parent2);
      }
}
```



##### 2、创建和删除

```java
import java.io.File;
import java.io.IOException;

public class FileMethodDemo{
       public static void main(String[] args) throws IOException {
            createAndDeleteDemo();
      }

       public static void createAndDeleteDemo() throws IOException {
            File file = new File("file.txt" );

             //和输出流不一样，如果文件不存在，则创建，如果文件存在，则不创建
             boolean b1 = file.createNewFile();
            System.out.println( "b1 = " + b1);

             //使用delete方法删除文件夹的时候，如果文件夹中有文件，则会删除失败
             boolean b2 = file.delete();
            System.out.println( "b2 = " + b2);

            File dir = new File("abc\\ab" );
             //使用mkdir可以创建多级目录
             boolean b3 = dir.mkdir();//make directory
            System.out.println( "b3 = " + b3);

             //最里层目录被干掉，dir代表的是最里层的目录
             boolean b4 = dir.delete();
      }
}
```

##### 3、判断

```java
import java.io.File;
import java.io.IOException;

public class FileMethodDemo{
       public static void main(String[] args) throws IOException {
            isDemo();
      }

       public static void isDemo() throws IOException {
            File f = new File("aaa.txt" );
            
             boolean b = f.exists();
            System.out.println( "b = " + b);

             if(!f.exists()){
                  f.createNewFile();
            }

             //最好先判断是否存在
             if(f.exists()){
                  System.out.println(f.isFile());
                  System.out.println(f.isDirectory());
            }

            f = new File("aa\\bb" );

            f.mkdirs();
             if(f.exists()){
                  System.out.println(f.isFile());
                  System.out.println(f.isDirectory());
            }
      }
}
```

##### 4、重命名

```java
import java.io.File;
import java.io.IOException;

public class FileMethodDemo{
       public static void main(String[] args) throws IOException {
            renameToDemo();
      }

       public static void renameToDemo() throws IOException {
            File f1 = new File("d:\\code\\day21\\0.mp3" );
            File f2 = new File("d:\\code\\day21\\1.mp3" );

             boolean b = f1.renameTo(f2);

            System.out.println( "b = " + b);
      }
}
```

##### 5、系统根目录和容量获取

```java
import java.io.File;
import java.io.IOException;

public class FileMethodDemo{
       public static void main(String[] args) throws IOException {
            listRootsDemo();
      }

       public static void listRootsDemo() throws IOException {
            File[] files = File.listRoots();

             for(File file : files){
                  System.out.println(file);
            }

            File file = new File("d:\\" );

            System.out.println( "getFreeSpace:" + file.getFreeSpace());
            System.out.println( "getTotalSpace:" + file.getTotalSpace());
            System.out.println( "getUsableSpace:" + file.getUsableSpace());
      }
}
```