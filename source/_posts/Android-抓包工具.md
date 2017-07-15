---
title: Android 抓包工具
date: 2017-06-24 17:37:40
categories: 工具
tags: 
    - Android
    - Fiddler
    - 工具
---



![android debug](https://media.licdn.com/mpr/mpr/AAEAAQAAAAAAAAe1AAAAJDkzODAzNGJkLTMzY2YtNDYxMi05NTBiLTQxMTQ4ZDQ2MGRmMg.jpg)

### 前言

在 Android 开发中，一般情况下不用去抓包，因为网络请求都会设置 Log 输出进行查看。

以下情况你可能需要抓包：

1. 需要了解自己应用的整体网络请求状况。
2. 第三方 SDK 会发起网络请求，在使用这些 SDK 时如果出现问题时，而恰好没有日志输出。
3. 想要了解某个 APP 使用的接口。

手机上已经有些 APP 可以进行抓包了，比如 [**Packet Capture**](https://link.zhihu.com/?target=https%3A//play.google.com/store/apps/details%3Fid%3Dapp.greyshirts.sslcapture) ，[**HTTP Injector**](https://link.zhihu.com/?target=https%3A//play.google.com/store/apps/details%3Fid%3Dcom.evozi.injector) ，GitHub 上看到这个 [**AndroidHttpCapture**](https://github.com/JZ-Darkal/AndroidHttpCapture)  APP 项目。

但这些都不如 PC 端专业抓包工具 [**Fiddler **](http://www.telerik.com/fiddler) 功能强大。

<!-- more -->







### Fiddler 简介

Fiddler是一个蛮好用的抓包工具，可以将网络传输发送与接受的数据包进行截获、重发、编辑、转存等操作。也可以用来检测网络安全。

![fiddler 工作原理](http://pic002.cnblogs.com/images/2012/263119/2012020409075327.png)



Fiddler是通过改写HTTP代理，让数据从它那通过，来监控并且截取到数据。当然Fiddler很屌，在打开它的那一瞬间，它就已经设置好了浏览器的代理了。当你关闭的时候，它又帮你把代理还原了。

当然，Fiddler 不仅能拦截 **HTTP** ，还能拦截 **HTTPS**（需要配置）。

Fiddler 可以通过伪造 CA 证书来欺骗浏览器和服务器。Fiddler 是个很会装逼的好东西，大概原理就是在浏览器面前 Fiddler 伪装成一个 HTTPS 服务器，而在真正的 HTTPS 服务器面前 Fiddler 又装成浏览器，从而实现解密HTTPS 数据包的目的。

[Fiddler官方网站](http://www.telerik.com/fiddler) 提供了大量的帮助文档和视频教程， 是学习Fiddler的最好资料。

 本文记录 Android 上使用 Fiddler 的详细配置过程。



> 注意：默认情况下Fiddler是不能监听Java HttpURLConnection上的HTTP请求的。原因容易理解，Fiddler能监听浏览器的HTTP请求是因为它启动一个代理服务器，浏览器通过这个代理服务器上网，但HttpURLConnection默认不使用该代理。在 Android 上使用了 HttpURLConnection 请求的都不会被抓取(如： Glide 图片库)，需要特殊处理（方法见下）。





### Fiddler配置

#### 一.普通 https 抓包设置

Fiddler 不做配置，是不能抓取 HTTPS 的。如要抓取 HTTPS，需要安装 CA 证书，步骤如下：

1. Tools –> Fiddler Options –>  HTTPS

   ![image](http://omrcgnd7e.bkt.clouddn.com/image/blog/fiddler%20options.png)


1. 勾选Decrypt HTTPS Traffic，点击OK，安装证书（会有弹窗提示）

   ![image](http://omrcgnd7e.bkt.clouddn.com/image/blog/Fiddler%20https.png)

   ​

#### 二.抓取Iphone / Android数据包

Fiddler 抓取移动端设备的数据包，只需要让 移动设备 的数据包 经过 Fiddler 的代理。

所以，Fiddler 抓包，需要 PC 与 手机**同在一个局域网内**（手机连接 PC 创建的热点，或 手机 PC 连接同一路由器），然后手动更改 手机的网络代理，变更为PC 上 Fiddler 的代理端口地址即可。

详细步骤如下：

1. 打开Fidder，点击菜单栏中的 [Tools] –> [Fiddler Options]，点击 [Connections] ，设置代理端口是8888， 勾选 Allow remote computers to connect， 点击OK

   ![Fiddler 设置](http://omrcgnd7e.bkt.clouddn.com/image/blog/Fiddler%20connection.png)

2. 如果手机连接 PC 创建的热点，这时在 Fiddler 可以看到自己本机无线网卡的IP了（要是没有的话，重启Fiddler）；如果手机与 PC 连接同一路由器，则在 Fiddler 中也可以查看 本机在局域网 IP 地址。（也可以通过在cmd 中 ipconfig 来查看设备的网络连接状况 ）![Fiddler 查看本机 IP 地址](http://omrcgnd7e.bkt.clouddn.com/image/blog/fiddler%20%E6%9F%A5%E7%9C%8BIP.png)

3. 设置Android设备上的代理服务器，使其连接 PC 上的 Fiddler 端口。设置 -> WLAN -> 选择网络 -> 更改代理，将上面查看的 IP 与端口 填写上后，点击确定，然后查看手机能否上网（仅限 http），如果不能上网，则检查 IP 与端口。


![小米4 设置代理](http://omrcgnd7e.bkt.clouddn.com/image/blog/%E5%B0%8F%E7%B1%B34%20%E8%AE%BE%E7%BD%AE%E4%BB%A3%E7%90%86.png)

4. 在浏览器中访问网页，输入刚刚填写的代理 IP 和 端口，下载 Fiddler 的 CA 证书

![证书下载](http://omrcgnd7e.bkt.clouddn.com/image/blog/Fiddler%20%E4%B8%8B%E8%BD%BD%E8%AF%81%E4%B9%A6.png)

5. 安装 CA 证书

   设置 -> 系统和备份 -> 更多设置 -> 系统安全 -> 凭据存储 -> 从存储设备安装，选择刚刚下载的  证书，然后会要求输入密码，**这个密码是手机的锁屏密码（如果没锁屏密码就去设置一个）**，输入密码后安装成功，这时候手机可以访问 HTTPS 了。

![安装 CA 证书](http://omrcgnd7e.bkt.clouddn.com/image/blog/%E5%B0%8F%E7%B1%B34%20%E5%AE%89%E8%A3%85%20CA%20%E8%AF%81%E4%B9%A6.png)





#### 三.抓取 HttpURLConnection

 Android 手机上一些 使用 HttpURLConnection 的数据包想要抓取，还要对 Fiddler 进行如下设置:

Fiddler -> Rules -> CustomizeRules ;

在这个函数OnBeforeResponse 的最后面添加判断

> if (oSession.oRequest["User-Agent"].indexOf("Android")> -1 && oSession.HTTPMethodIs("CONNECT")) {oSession.oResponse.headers["Connection"] = "Keep-Alive"; }

这里的" Android"可替换为User-Agent里的其它字符，确保匹配上User-Agent就行

![HttpURLConnection 抓包](http://omrcgnd7e.bkt.clouddn.com/image/blog/Fiddler%20httpurlconnection.png)



在使用 Glide 时，图片一直不能拿抓包

折腾了半天后，才想起 Glide 有缓存，所以并不会去请求网络~尴尬~  





### Fiddler使用

#### 简单使用

Fiddler想要抓到数据包，要确保Capture Traffic是开启，在File –> Capture Traffic。开启后再左下角会有显示，当然也可以直接点击左下角的图标来关闭/开启抓包功能。

![image](http://omrcgnd7e.bkt.clouddn.com/image/blog/Fiddler%20turn%20on.png)

Fiddler开始工作了，抓到的数据包就会显示在列表里面

![Fiddler](http://omrcgnd7e.bkt.clouddn.com/image/blog/Fiddler%20icon.png)

下面总结了这些都是什么意思：

|      名称      |             含义              |                    图标                    |                    含义                    |                    图标                    |           含义           |
| :----------: | :-------------------------: | :--------------------------------------: | :--------------------------------------: | :--------------------------------------: | :--------------------: |
|      #       | 抓取HTTP Request的顺序，从1开始，以此递增 | ![clip_image001](http://images2015.cnblogs.com/blog/626593/201601/626593-20160118234159140-1640771885.gif) |                请求已经发往服务器                 | ![](http://images2015.cnblogs.com/blog/626593/201601/626593-20160118234206453-344992441.gif) |        响应是脚本格式         |
|    Result    |           HTTP状态码           | ![](http://images2015.cnblogs.com/blog/626593/201601/626593-20160118234159765-1592382397.gif) |               已从服务器下载响应结果                | ![](http://images2015.cnblogs.com/blog/626593/201601/626593-20160118234207625-740567358.gif) |       响应是 CSS 格式       |
|   Protocol   |  请求使用的协议，如HTTP/HTTPS/FTP等   | ![](http://images2015.cnblogs.com/blog/626593/201601/626593-20160118234201406-1416873112.gif) |                 请求从断点处暂停                 | ![](http://images2015.cnblogs.com/blog/626593/201601/626593-20160118234208297-916097140.gif) |       响应是 XML 格式       |
|     Host     |          请求地址的主机名           | ![](http://images2015.cnblogs.com/blog/626593/201601/626593-20160118234202375-1737717316.gif) |                 响应从断点处暂停                 | ![](http://images2015.cnblogs.com/blog/626593/201601/626593-20160118234209640-1298497869.png) |      响应是 JSON 格式       |
|     URL      |           请求资源的位置           | ![](http://images2015.cnblogs.com/blog/626593/201601/626593-20160118234202812-1354392122.gif) |       请求使用 HTTP 的 HEAD 方法，即响应没有内容        | ![](http://images2015.cnblogs.com/blog/626593/201601/626593-20160118234210172-1709733575.png) |       响应是一个音频文件        |
|     Body     |           该请求的大小            | ![](http://images2015.cnblogs.com/blog/626593/201601/626593-20160118234203515-1304170577.png) |           请求使用 HTTP 的 POST 方法            | ![](http://images2015.cnblogs.com/blog/626593/201601/626593-20160118234210703-1810906238.png) |       响应是一个视频文件        |
|   Caching    |      请求的缓存过期时间或者缓存控制值       | ![](http://images2015.cnblogs.com/blog/626593/201601/626593-20160118234204531-965189067.gif) | 请求使用 HTTP 的 CONNECT 方法，使用 HTTPS 协议建立连接隧道 | ![](http://images2015.cnblogs.com/blog/626593/201601/626593-20160118234214828-810550242.gif) |         普通响应成功         |
| Content-Type |           请求响应的类型           | ![](http://images2015.cnblogs.com/blog/626593/201601/626593-20160118234205547-1927498766.gif) |               响应是 HTML 格式                | ![](http://images2015.cnblogs.com/blog/626593/201601/626593-20160118234217078-1617370921.gif) |         服务端错误          |
|   Process    |        发送此请求的进程：进程ID        | ![](http://images2015.cnblogs.com/blog/626593/201601/626593-20160118234206203-722749081.gif) |                 响应是一张图片                  | ![](http://images2015.cnblogs.com/blog/626593/201601/626593-20160119000324093-1538967179.gif) | 会话被客户端、Fiddler 或者服务端终止 |



#### 指定进程

根据需求，选择要捕获的进程

![image](http://omrcgnd7e.bkt.clouddn.com/image/blog/Fiddler%20browsers.png)



#### filters信息过滤

Fiters 是过滤请求用的，左边的窗口不断的更新，这个时候通过过滤规则，显示想要看到的请求。

![Fiters](http://omrcgnd7e.bkt.clouddn.com/image/blog/Fiddler%20filters.png)

如图所示，只显示 www.baidu.com 的数据   



### 参考

更多 Fiddler 使用，参考如下：

[Fiddler 抓包工具总结](http://www.cnblogs.com/yyhh/p/5140852.html)

[fiddler filters 使用](http://blog.csdn.net/notejs/article/details/49681479)