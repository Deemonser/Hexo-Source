---
title: Android Library的依赖方式及发布
date: 2017-08-21 01:46:26
categories: Android知识整理
tags:
 - JCenter
 - Android依赖
---

![](http://oslynwh8c.bkt.clouddn.com/image/hexo/notebook.jpg)

最近发布一个项目，发现以前发布到 JCenter 的步骤都忘光了，又得到处翻资料，真是尴尬.....

还是那句老话，好记性不然烂笔头，在此整理 Android Studio 依赖相关 以及 如何发布项目到 JCenter

<!-- more -->



### Android Studio 添加依赖

#### Module 依赖

module 依赖是指在本地创建一个 module，然后如下步骤，依赖此模块。

![图一](http://oslynwh8c.bkt.clouddn.com/image/hexo/Android%20studio%E4%BE%9D%E8%B5%96%E6%96%B9%E5%BC%8F.jpg)

这样做的好处就是随时可以修改 module。

上图可以看到，Android Studio（以下简称 AS）提供了三种依赖方式。



#### 本地依赖

本地依赖是指，将 jar 或者 aar 直接拷贝到项目的 libs 文件夹下 ，然后对其进行依赖。

- jar 的依赖如图一，拷贝进 libs 目录后，AS 才能识别到。
- aar 的本地依赖官方没有提供解决方案，不过高手在民间。

![](http://oslynwh8c.bkt.clouddn.com/image/hexo/%E6%9C%AC%E5%9C%B0%E4%BE%9D%E8%B5%96.png)



在讲解本地 aar 依赖之前，先科普一下 aar 文件。

aar 文件是基于 jar 文件之上开发的。因为有些Android Library 需要植入一些安卓特有的文件，比如 AndroidManifest.xml，资源文件，Assets或者JNI。这些文件在 Jar 中是没有的，因此诞生了 aar 文件。

aar 文件和 jar 一样，只是普通的zip文件。不过具有不同的文件结构。jar文件以classes.jar的名字被嵌入到aar文件中。

aar文件如下：

- /AndroidManifest.xml (mandatory)
- /classes.jar (mandatory)
- /res/ (mandatory)
- /R.txt (mandatory)
- /assets/ (optional)
- /libs/*.jar (optional)
- /jni/<abi>/*.so (optional)
- /proguard.txt (optional)
- /lint.jar (optional)

在此说明一下，aar 文件的生成只能在 Android Library 中，也就是gradle脚本的声明是`apply plugin: 'com.android.library' `的 module，然后编译后就会在 **build/outputs/aar**  文件夹里生成aar文件。

![生成 aar](http://oslynwh8c.bkt.clouddn.com/image/hexo/%E7%94%9F%E6%88%90%20aar%20%E6%88%96%20jar.png)

本地 aar 依赖需要在 app 的 build.gradle 文件添加如下内容

```java
repositories {
    flatDir {
        dirs 'libs' //this way we can find the .aar file in libs folder
    }
}
```

之后再其它项目里面添加 gradle 依赖

```java
dependencies {
    compile(name:'dor', ext:'aar')
}
```

以上就是 aar 的本地依赖。



#### 远程依赖

远程依赖就是在 app 的 build.gradle 中直接添加（当然也可以通过图一搜索的方式）

```java
dependencies {
  compile 'com.squareup.picasso:picasso:2.5.2'
}
```

这样一行代码就完成了依赖，也是最常见的依赖方式。



#### 远程依赖库的来源

我刚开始也不明白AS 是怎样凭借 一行代码得到这些library的。

查阅资料才知道，AS 是从项目 build.gradle 文件里面定义的Maven 仓库服务器上下载library的。

Apache Maven 是 Apache 开发的一个工具，提供了用于贡献library的文件服务器。

总的来说，只有两个标准的 Android library 文件服务器：JCenter 和  Maven Central。

不管是 「JCenter」还是「Maven Central」 ，两者都是**Maven** 仓库，只是他们维护的服务器不同，由不同的人提供。

 

##### JCenter

JCenter 是一个由 [bintray.com](https://bintray.com) 维护的Maven仓库 ，整个仓库的内容在 [http://jcenter.bintray.com/](http://jcenter.bintray.com/)

在项目的build.gradle 文件中如下定义仓库，就能使用jcenter了

```java
allprojects {
    repositories {
        jcenter()
    }
}
```



##### Maven Centra

Maven Central 则是由 [sonatype.org](https://sonatype.org/) 维护的Maven仓库，整个仓库的内容在 [https://oss.sonatype.org/content/repositories/releases/](https://oss.sonatype.org/content/repositories/releases/)

在项目的build.gradle 文件中如下定义仓库，就能使用Maven Central了

```java
allprojects {
    repositories {
        mavenCentral()
    }
}
```



##### jcenter 与 Maven Central 的区别

事实上两个仓库都具有相同的使命：提供Java或者Android library服务。上传到哪个（或者都上传）取决于开发者。

起初，Android Studio 选择Maven Central作为默认仓库。如果你使用老版本的Android Studio创建一个新项目，mavenCentral()会自动的定义在build.gradle中。

但是Maven Central的最大问题是对开发者不够友好。上传library异常困难。上传上去的开发者都是某种程度的极客。同时还因为诸如安全方面的其他原因，Android Studio团队决定把默认的仓库替换成jcenter。正如你看到的，一旦使用最新版本的Android Studio创建一个项目，jcenter()自动被定义，而不是mavenCentral()。

以下是使用Jcenter的原因：

- jcenter通过 CDN 发送library，开发者可以享受到更快的下载体验。
- jcenter是全世界最大的Java仓库，因此在Maven Central 上有的，在jcenter上也极有可能有。
- 上传library到仓库很简单，不需要像在 Maven Central上做很多复杂的事情。 
- 友好的用户界面 
- 如果你想把library上传到 Maven Central ，你可以在bintray网站上直接点击一个按钮就能实现

基于上述原因，我们发布 Android Library 最好是发布到 Jcenter 上。



#### Gradle 下载依赖库的原理

在项目的 build.gradle 文件中加入一行代码的时候，这些库是怎样下载到我们的项目中呢？

一般来说，library 的字符串形式，包含3部分

`GROUP_ID:ARTIFACT_ID:VERSION`

以冒号「：」为分隔

- GROUP_ID

  library的group，通常以开发者包名 加 library的名称来命名group。

- ARTIFACT_ID

  library的真实名称

- VERSION

  library 的版本号

例如Android 界大名鼎鼎的 Square 公司的 Library

```java
dependencies {
  compile 'com.squareup:otto:1.3.7'
  compile 'com.squareup.picasso:picasso:2.5.2'
  compile 'com.squareup.okhttp:okhttp:2.4.0'
  compile 'com.squareup.retrofit:retrofit:1.9.0'
}
```

添加上述的依赖库后，Gradle 会根据项目的build.gradle 文件中配置的Maven 仓库，询问仓库服务器这个library是否存在，（如果配置了多个仓库，会依次询问这些仓库）如果存在，Gradle 会获得 library 的存储路径，然后 AS 将下载这些文件到我们的电脑上，与我们的项目一起编译。

一般存储路径都是这样的形式：GROUP_ID/ARTIFACT_ID/VERSION_ID。

例如：

Jcenter库的地址：[http://jcenter.bintray.com/com/squareup/otto/1.3.7](http://jcenter.bintray.com/com/squareup/otto/1.3.7) 

Maven Central 库的地址：[https://oss.sonatype.org/content/repositories/releases/com/squareup/otto/1.3.7/](https://oss.sonatype.org/content/repositories/releases/com/squareup/otto/1.3.7/)



### 上传 Library 到 JCenter

我们总是用别人的库，如果自己写一个库分享给别人用，那样不是更酷嘛！

我们可以通过 Gradle 把项目发布到 Maven 库中，至于选 Maven Central 还是 JCenter，通过前面的介绍，当然是选 JCenter 更好。

整个发布过程如下图：

![上传到 JCenter 的过程](http://oslynwh8c.bkt.clouddn.com/image/hexo/%E4%B8%8A%E4%BC%A0%20JCenter%20%E8%BF%87%E7%A8%8B.png)

可以看到整个过程还是挺清晰的，但不亲自去试试，就不知道有多少坑！



#### 1.注册 Bintray 并创建私有 Maven 仓库

 JCenter 是由 Bintray 维护的 Maven 库，所有首先得去 Bintray 网站注册一个账号。

就是因为注册这一步有个巨坑，害得自己填了好久。

打开 [Bintray官网](https://bintray.com) ，天真的我就直接点了 **START YOUR FREE TRIAL** ，然后欲哭无泪。

![Bintray 组织注册](http://oslynwh8c.bkt.clouddn.com/image/hexo/Bintray%20%E7%BB%84%E7%BB%87%E6%B3%A8%E5%86%8C.jpg)

Bintray 官网在2016年底改版了，直接点注册是组织用户，而非个人用户，这将导致最后一直提交失败。

最后只能重新注册新的个人账户。

个人账户注册在首页的底部，要把页面拉下去才看得到。

![Bintray 个人注册](http://oslynwh8c.bkt.clouddn.com/image/hexo/Bintray%20%E4%B8%AA%E4%BA%BA%E6%B3%A8%E5%86%8C.jpg)

或者直接点击这个 [注册个人账户](https://bintray.com/signup/oss)

后边注册的步骤就很简单了，有 `github `或者 `google` 账户的直接登录就行了，如果需要注册这里的邮箱不能是国内的邮箱。



登录上之后，点击右上角的小箭头 Edit Profile —> API Key —> 输入密码 —> 复制 API Key。

![获取 API Key](http://oslynwh8c.bkt.clouddn.com/image/hexo/Bintray%E8%8E%B7%E5%8F%96%20Key.png)

这个 API Key 在后面会有用到。

然后创建私有的 Maven 仓库，在个人首页点击 **Add New Repository** 

![添加仓库](http://oslynwh8c.bkt.clouddn.com/image/hexo/Bintray%20%E6%B7%BB%E5%8A%A0%E4%BB%93%E5%BA%93.png)

然后按照下图填写内容

![创建仓库](http://oslynwh8c.bkt.clouddn.com/image/hexo/Bintrary%E5%88%9B%E5%BB%BA%E4%BB%93%E5%BA%93.png)

最后点击 **Create** 创建，这样就创建了仓库。



#### 2. 配置项目

接下来就是配置要发布的项目。

首先是在项目的根目录 build.gradle 中配置如下：

```java
buildscript {
    repositories {
        jcenter()
    }
    dependencies {
        classpath 'com.android.tools.build:gradle:2.3.0'
        // NOTE: Do not place your application dependencies here; they belong
        // in the individual module build.gradle files
        //首先添加这两个路径
        classpath 'com.github.dcendents:android-maven-gradle-plugin:1.5'
        classpath 'com.jfrog.bintray.gradle:gradle-bintray-plugin:1.7'
    }
}

allprojects {
    repositories {
        jcenter()
    }
}

task clean(type: Delete) {
    delete rootProject.buildDir
}

//添加下面的配置以便在 module 中引用
ext {
    bintrayRepo = 'maven' //仓库名
    publishedGroupId = 'com.deemons.dor' //引用时 第一部分
    libraryDescription = 'android network'//描述
    siteUrl = 'https://github.com/Deemonser/Download'//项目地址
    gitUrl = 'https://github.com/Deemonser/Download.git'//gitUrl
    developerId = 'deemons'//注册的用户名
    developerName = 'deemonser'//开发者名称
    developerEmail = 'deemonser@hotmail.com'//开发者邮箱
    licenseName = 'The Apache Software License, Version 2.0'
    licenseUrl = 'http://www.apache.org/licenses/LICENSE-2.0.txt'
    allLicenses = ["Apache-2.0"]
}

//开源库中有中文注释,添加下面这些
allprojects {
    tasks.withType(Javadoc) {
        options {
            encoding "UTF-8"
            charSet 'UTF-8'
            links "http://docs.oracle.com/javase/7/docs/api"
        }
    }
}
```

然后在将要发布的 module 下的 build.gradle 中添加如下配置

```java
//for upload
ext {

    publishedGroupId = rootProject.publishedGroupId //引用时的第一部分
    artifact = 'dor'//引用时的第二部分
    libraryVersion = '0.1.0'//引用时的第三部分
    libraryName = 'dor'//本module名称
    bintrayName = 'dor'//Jcenter 上显示的项目名



    bintrayRepo = rootProject.bintrayRepo
    libraryDescription = rootProject.libraryDescription



    siteUrl =rootProject.siteUrl
    gitUrl = rootProject.gitUrl

    developerId = rootProject.developerId
    developerName =rootProject.developerName
    developerEmail = rootProject.developerEmail

    licenseName = rootProject.licenseName
    licenseUrl = rootProject.licenseUrl
    allLicenses = rootProject.allLicenses
}

apply from: 'https://raw.githubusercontent.com/nuuneoi/JCenter/master/installv1.gradle'
apply from: 'https://raw.githubusercontent.com/nuuneoi/JCenter/master/bintrayv1.gradle'
```

最后，在项目的 local.properties 文件中添加如下配置

```Java
bintray.user=YOUR_BINTRAY_USERNAME	//填写注册的用户名
bintray.apikey=YOUR_BINTRAY_API_KEY //填写前面获取的 API Key
```

 完成上面三个地方的配置后，就可以通过命令上传项目到 Bintray 。



#### 3.提交项目到 Bintray

分别执行下面两个命令

```java
./gradlew install
./gradlew bintrayUpload
```

如果两次都没报错，并且看到BUILD SUCCESS，就表示上传成功了。

这时候在自己的 Bintray 上，点击前面创建的 maven 仓库。 

![maven 仓库](http://oslynwh8c.bkt.clouddn.com/image/hexo/maven%E4%BB%93%E5%BA%93.png)

可以看到发布上来的 library 了

![上传的 library](http://oslynwh8c.bkt.clouddn.com/image/hexo/%E4%B8%8A%E4%BC%A0%E7%9A%84%20library.jpg)





#### 4.将 Bintray 的项目发布到 JCenter

点击刚刚上传的项目，看到下面的详细信息。

![发布到 JCenter](http://oslynwh8c.bkt.clouddn.com/image/hexo/%E5%8F%91%E5%B8%83%E5%88%B0%20JCenter.jpg)

这时候，点击右下角的 **Add to JCenter** 按钮后，跳转到一个提交页面。

然后什么也不做，直接点击Send。

然后就是漫长的等待Bintrary 的审核，如果请求审核通过，会收到一封邮件。

最后就是测试 **一行代码** 依赖。

如果成功了，那就大功告成啦~