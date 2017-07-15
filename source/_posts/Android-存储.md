---
title: Android 存储
date: 2017-03-12 21:16:41
categories: Android知识整理
tags:
 - 存储
---

![test](http://img2.imgtn.bdimg.com/it/u=169468055,4200803197&fm=23&gp=0.jpg)

在工作中经常会用到存储相关的API，各种概念比较混乱， 所以整理一下，以便以后再次查看

<!-- more -->



### 概述

Android最初只支持内部存储，到后来国内ROM厂商各自改造，人为的把内部存储分为Internal,External导致开发者对Androi存储系统产生很多迷惑。同时破坏了Android原有的生态系统，Google一直对这块都没有发布更新，直到Android6.0引入了适配存储（Adoptable Storage）的方式来支持外置的SD卡。



### 分类

　Android数据存储位置分为内部存储 `Internal Storage `和外部存储 `External Storage`。

* 内部存储是系统默认的存储位置,永远可用。默认情况下，保存在内部存储内的文件是应用程序私有的，其他应用程序（或用户）是无法访问的。当用户卸载此应用程序时，内部存储的数据会一并清除。
* 外部存储可以通过物理介质提供(如SD卡)，也可以通过将内部存储中的一部分封装而成，设备可以有多个外部存储实例。可能不可用。外部存储的读写权限在不同系统版本中是不同的.




### 历史

* 从Android 1.0开始，写操作受权限`WRITE_EXTERNAL_STORAGE`保护。

* 从Android 4.1开始，读操作受权限`READ_EXTERNAL_STORAGE`保护。

* 从Android 4.4开始，应用可以管理在它外部存储上的对应目录(`SDCard/Android/data/应用包名/`)，而不用获取读写权限。程序卸载后,此目录下的数据会一并清除.

* 从Android6.0开始, 运行时权限机制开启后,读写权限分为一组被列入危险权限,从而需要用户手动确认,否则crash.当然,你也可以不兼容6.0也没关系.

  ​

  长期以来, Android手机的外部存储目录乱象横生, 各家应用在外边存储目录上随意创建文件夹及文件, 毫无规范可言, 卸载应用后文件没有清理, 导致整个外部存储不堪重负. 这其中还包括各个大厂的应用. 

  ​

### 常用目录

内部存储在Android文件系统的位置是当我们在打开DDMS下的File Explorer面板的时候，`/data`目录就是所谓的内部存储(ROM)。但是注意，当手机没有root的时候不能打开此文件夹。

外部存储可以分为两类,  一类是系统为每个应用创建的目录:`SDCard/Android/data/应用包`, 这部分存储是应用的扩展存储, 在Android4.4之后,应用不需要申请权限就能读写; 另一类,则是除`SDCard/Android`目录外的其他目录,访问这部分目录都需要权限.

在Android系统中，根据调用的系统API接口，有3种目录可以给我们写入文件：

1. **应用**私有存储

   内部存储中, 两个目录重点说下：

   1. `/data/app` : app文件夹里存放着我们所有安装的app的apk文件

   2. `/data/data`: 此目录下将每一个APP的存储内容按照包名分类存放好。包名下就是我们`应用的私有存储`.

      比如:

      * `data/data/包名/shared_prefs` 存放该APP内的SP信息
      * `data/data/包名/databases` 存放该APP的数据库信息
      * `data/data/包名/files` 通过`Context.getFileDir()`获取内置存储下的文件目录，可以用来保存不能公开给其他应用的一些敏感数据如用户个人信息
      * `data/data/包名/cache` 通过`Context.getCacheDir()`获取内置存储下的缓存目录，可以用来保存一些缓存文件如图片，当内置存储的空间不足时将系统自动被清除

      当然, 还可以在包名目录下创建文件夹.

      ​	使用文件名称和操作模式调用`openFileOutput()`得到`FileOutputStream`

      ​	调用`openFileInput()`并向其传递要读取的文件名称,将返回一个`FileInputStream`

      除此之外还有两个API用得到

      ​	`deleteFile()`删除保存在内部存储的文件。
      ​	`fileList()`返回您的应用当前保存的一系列文件。

   > 这些数据也会随着用户卸载App而被一起删除。这两个目录其实就对应着`设置->应用->你的App->存储空间`下面的`清除数据`和`清楚缓存`

   ​

2. **应用**扩展存储（SD卡）

   * 获取方式：

     * `Context.getExternalFilesDir()`：`获取SD卡上的文件目`
     * `Context.getExternalCacheDir()`：`获取SD卡上的缓存目录`

   * 绝对路径：

     * `Context.getExternalFilesDir()`：`SDCard/Android/data/应用包名/files/`
     * `Context.getExternalCacheDir()`：`SDCard/Android/data/应用包名/cache/`

   * 写权限：

     * API < 19：需要申请 
     * API >= 19：不需要申请

     > 既然是SD卡上的目录，那么是可以被其他的应用读取到的，所以这个目录下，不应该存放用户的敏感信息。同上面一样的，这里的文件会随着App卸载而被删除，也可以由用户手动在设置界面里面清除。还有, SD卡并不保证一定可用, 所以在操作SD卡是一定要先通过`getExternalStorageState()`判断SD卡状态 是否可用

     ​

3. 公共存储（SD卡）

   - 获取方式：`Environment.getExternalStorageDirectory()`
   - 绝对路径：`SDCard/你设置的文件夹名字/`
   - 写权限：需要申请

   > 如果我们的App需要存储一些公共的文件，应该将文件保存到设备上的“公共”位置，以便其他应用能够在其中访问这些文件，并且用户也能轻松地从该设备复制这些文件。 执行此操作时，应使用共享的公共目录之一，例如 `Music`、`Pictures` 和 `Ringtones`等。
   >
   > 要获取表示相应的公共目录的`File`，请调用`getExternalStoragePublicDirectory()`，向其传递您需要的目录类型，例如`DIRECTORY_MUSIC`、`DIRECTORY_PICTURES`、`DIRECTORY_RINGTONES`或其他类型。通过将您的文件保存到相应的媒体类型目录，系统的媒体扫描程序可以在系统中正确地归类您的文件.




### 总结

有了上面的介绍, 相信都明白了. 根据存储信息的安全性选择存储位置.

1. 如果是`私有的`, `重要的`用户信息,则存放在`内部存储`
2. 如果是应用的一些资源,如缓存图片, 则存放在应用的`扩展存储`
3. 如果是一些公共的资源, 则可以存放在`公共存储`



还有一个坑要填, 在Android4.4后 应用的扩展存储是不需要申请权限的, 这都知道了. 可为了适配以前的版本, 还是需要在manifest清单中注册读写权限. 那么问题来了,  Android6.0是可以手动取消存储空间的, 如下图所示，只要在manifest里面注册了，就可以动态取消之！

![app-权限](http://unclechen.github.io/content/images/app-perm-before.png)

当用户取消了权限之后，SDK接口中与`File`相关的API全部都返回空了，于是我们就没法写文件了。

将AndroidManifest.xml文件中的

```
<uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE"/>


```

改为

```
<uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE"
android:maxSdkVersion="22"/>

```

表示只在`API <= 22`时，才申请`WRITE_EXTERNAL_STORAGE`权限。这样用户就无法在Android6.0系统的设置下面看到`存储空间`权限的开关，当然也就无法关闭它了.至于为啥是22而不是18,因为flyme这伙android5.1 写getExternalFilesDir必须有权限才行.



参考文章:
[Android存储（1）-- 你还在乱用Android存储嘛！！！](https://juejin.im/post/58b557de128fe10065e93cc8)

[Android6.0权限适配之WRITE_EXTERNAL_STORAGE（SD卡写入）](http://unclechen.github.io/2016/03/06/Android6.0%E6%9D%83%E9%99%90%E9%80%82%E9%85%8D%E4%B9%8BSD%E5%8D%A1%E5%86%99%E5%85%A5/)



更多详情参考:

[Android存储（2）-- 适配器存储](https://juejin.im/post/58b66777570c350062fa2b42)

[Android存储（3）-- 设备配置](https://juejin.im/post/58b66eff61ff4b006ccdb6ee)

[Android存储（4）-- 配置示例](https://juejin.im/post/58b675a761ff4b006ccde8ed)