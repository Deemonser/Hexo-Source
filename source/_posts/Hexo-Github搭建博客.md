---
title: Hexo Github搭建博客
date: 2017-05-14 22:53:38
categories: 博客
tags: 
 - hexo
 - Github Pages
 - 搭建博客
---

![配图](http://oslynwh8c.bkt.clouddn.com/image/hexo/alejandro-escamilla-9.jpg)

### 前言

在 *搬砖* 的过程中，浏览了许多 别人写的博客后，自己也萌生了写博客的念头。

其一是为了系统的整理知识，方便日后回顾，

其二是记录踩过的坑，防止再次 被坑…..

这样即充实了自己，也方便了别人，何乐不为。

虽然 [简书](http://www.jianshu.com/) 很好用，但看到别人自己搭的博客后瞬间就心动了。

<!-- more -->

[Hexo](https://hexo.io/zh-cn/index.html) 是一个快速、简洁且高效的博客框架，这里就记录下利用 Github 和 [Hexo](https://hexo.io/zh-cn/index.html) 搭建博客的过程。



### 原理

**Github Pages** 存放的都是静态文件，博客存放的不只是文章内容，还有文章列表、分类、标签、翻页等动态内容。

假如每次写完一篇文章都要手动更新博文目录和相关链接信息，相信谁都会疯掉，

所以 **hexo**  所做的就是将这些 md 文件都放在本地，每次写完文章后调用写好的命令来批量完成相关页面的生成，然后再将有改动的页面提交到github。



### 准备

因为博客是利用 GitHub 及 Hexo 来搭建的，所以在此之前有一些准备工作：

1. [下载安装 Git](https://git-for-windows.github.io/)
2. [GitHub 注册账户](https://github.com/)
3. [安装 Node.js](https://nodejs.org/en/)

这些基础步骤相对简单，过程中遇到问题就问 [google](https://www.google.com./) 。

验证软件安装成功需要执行以下命令：

> git --version
> node -v
> npm -v

![验证](http://oslynwh8c.bkt.clouddn.com/image/hexo/hexo%20%E9%AA%8C%E8%AF%81%E5%AE%89%E8%A3%85.png)





### 搭建hexo

#### 安装hexo

**在命令窗口利用 npm 命令即可安装。**  

调出命令窗口（win中 shift+鼠标右键 -> 在此处打开命令窗口），输入命令：

```java
npm install -g hexo
```

安装速度可能有点慢，需要耐心等待。

如果地址被“墙”，就需要梯子翻墙了。



#### 初始化hexo

1. 创建 名为「hexo」的文件夹

2. 在hexo 文件夹下打开命令窗口（win中方法同上），输入命令：

   ```java
   hexo init
   ```

初始化的速度可能也慢。

![hexo 初始化](http://oslynwh8c.bkt.clouddn.com/image/hexo/hexo%20%E5%88%9D%E5%A7%8B%E5%8C%96%E6%88%90%E5%8A%9F.png)

hexo 初始化成功后就可以在本地运行起来了。



#### 本地运行

在命令窗口分别输入一下两个命令： 

```
hexo g 

hexo s
```

然后，在浏览器中输入 [localhost:4000](localhost:4000) ，就可以看到 搭建的博客了。

![hexo 本地运行](http://oslynwh8c.bkt.clouddn.com/image/hexo/hexo%E6%9C%AC%E5%9C%B0%E8%BF%90%E8%A1%8C.png)

这个博客里面默认有一篇 「Hello World」的博文，写了Hexo 的基础操作，更多详细操作需查看 [hexo 官网](https://hexo.io/zh-cn/)





### 配置主题

看到博客的样子，你可能会失望，太low了，你想要的可能是这样：

![示例1](http://upload-images.jianshu.io/upload_images/136678-4d36d1bd7d7c2f3c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

这样子：

![示例1](http://oslynwh8c.bkt.clouddn.com/image/hexo/hexo%20%E4%B8%BB%E9%A2%98%E7%A4%BA%E4%BE%8B2.png)

也可能是这样：

![示例1](http://upload-images.jianshu.io/upload_images/136678-030253f986bbeca7.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



以上都是hexo 的主题，如果你想使你的博客更加个性化，那就配置自己的主题吧



hexo的主题是全球的爱好者所贡献的，你可以自己挑选喜欢的主题 [更多主题](https://hexo.io/themes/) 。

建议 配置  [**hexo-theme-next**](https://github.com/iissnan/hexo-theme-next) 主题（这是[GitHub](https://github.com/) 上 获得 start 最多的hexo 的主题）， [查看 next的文档](http://theme-next.iissnan.com/)

start 多就说明文档很完善，配置主题容易，踩得坑少。

限于篇幅，此处不做 next 主题的详细配置介绍，将在下篇进行演示。



### GitHub Page

[GitHub Pages](https://pages.github.com/) 本用于介绍 那些托管在 [GitHub](https://github.com) 的项目，由于他的空间免费稳定，所以可以用来做搭建一个博客。



#### 创建仓库

前面已经注册一个[Github](https://github.com/join?source=header-home)　账号，注意你的 **username** ，你的账户名将决定你GitHub Page 的域名。

比如账户名 `Deemonser` ，那你 域名就是` deemonser.github.io` 。



现在来创建一个 仓库 ，GitHub 右上角新建仓库。

![新建仓库](http://oslynwh8c.bkt.clouddn.com/image/hexo/GitHub%20%E6%96%B0%E5%BB%BA%E4%BB%93%E5%BA%93.png)

然后需要根据你的 **username** 来填写对应的仓库名称 **username.github.io** 。

![创建仓库](http://oslynwh8c.bkt.clouddn.com/image/hexo/GitHub%20%E5%88%9B%E5%BB%BA%E4%BB%93%E5%BA%93.png)

这样 ，你的 **GitHub Page** 就创建成功了。



#### 配置 SSH Key

为什么要配置这个呢？

因为你提交代码肯定要拥有你的 Github 权限才可以，但是直接使用用户名和密码太不安全了，

所以我们使用 SSH Key 来解决本地和服务器的连接问题。



##### 1.生成SSH Key

接下来的命令需要到 **Git Bash**  中完成， cmd 是不行的。

装了 **Git**   的话，鼠标右键中 会有「 **Git Bash Here** 」这个选项

![Git bash](http://oslynwh8c.bkt.clouddn.com/image/hexo/Git%20bash.png)



点击进入后，输入命令：

```java
cd ~/.ssh
```

意思是查询本机已经存在的ssh 密钥，如果已经存在 ssh 密钥，这进入到 `~/.ssh` 目录中。

如果提示 「No such file or directory」，则说明没有密钥，需要配置一个。

输入命令：

```java
ssh-keygen -t rsa -C "注释内容，一般为邮件地址" 
```

`-C` 的意思是添加注释，生成的公钥后面会带上注释，可以不用添加注释。

回车后会有3 个输入提示：

1. Enter file in which to save the key    #密钥文件的保存路径，默认在用户目录下
2. Enter passphrase    #输入密码
3. Enter same passphrase again    #确认密码

注意，上面三个 输入提示 都 **可以不填**，直接按三次 `Enter` 就可以了。 

最终会生成一个文件在用户目录下 ` C:\Users\用户名\.ssh` ，打开目录，找到 `id_rsa.pub` 文件，这个带 `.pub` 后缀的是公钥，用记事本打开后，复制密钥内容。



##### 2.GitHub 配置SSH Key

打开进入你 **Github** 的主页 ，点击右上角头像，进入到 **setting**    -->    **SSH and GPG keys**    -->    **New SSH Key**

![Github add ssh](http://oslynwh8c.bkt.clouddn.com/image/hexo/GitHub%20%E6%B7%BB%E5%8A%A0ssh.png)



点击 **New SSH Key** 后，下面出现 输入框， 将复制的公钥内容粘贴到 **Key** ，然后 **Title ** 自己填写，最后保存。



##### 3.测试

输入下面的命令，看看设置是否成功，git@github.com 的部分不要修改：

```java
ssh -T git@github.com
```

等一会儿

如果出现 「Hi Deemonser! You've successfully authenticated, but GitHub does not provide shell access.」，说明成功了



##### 4.设置用户信息

Git 会根据用户的名字和邮箱来记录提交。GitHub 也是用这些信息来做权限的处理，输入下面的代码进行个人信息的设置，把名称和邮箱替换成你自己的。

```java
git config --global user.name "deemonser"// username 
git config --global user.email  "your email"//填写github注册邮箱
```



#### 上传到Github

在上传之前，还要修改 站点配置文件 `_config.yml` 。

hexo 有两个配置文件，一个是在根目录下的  `_config.yml` 称为 **站点配置文件** 。

还有一个是在主题里面的  `_config.yml` 文件称为 主题配置文件。



打开  `_config.yml` 文件 ，搜索定位到 `deploy:`  

```java
deploy:
  type: git
  repo: https://github.com/username/username.github.io.git    //刚创建的Github仓库
  branch: master
```

按上面替换 `username` 后保存。

然后，安装 [hexo-deployer-git](https://github.com/hexojs/hexo-deployer-git) 自动部署发布工具

```java
npm install hexo-deployer-git --save
```

安装成功后，就可以一个命令发布到Github 上了

```java
hexo clean && hexo g && hexo d
```

上传成功后，就可以在浏览器中输入 `username.github.io` 来访问你的博客了。



### 参考

[零基础免费搭建个人博客-hexo+github](http://hifor.net/2015/07/01/%E9%9B%B6%E5%9F%BA%E7%A1%80%E5%85%8D%E8%B4%B9%E6%90%AD%E5%BB%BA%E4%B8%AA%E4%BA%BA%E5%8D%9A%E5%AE%A2-hexo-github/)

[5分钟 搭建免费个人博客](http://www.jianshu.com/p/4eaddcbe4d12)

[如何搭建一个独立博客——简明Github Pages与Hexo教程](http://www.jianshu.com/p/05289a4bc8b2)