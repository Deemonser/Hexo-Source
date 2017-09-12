---
title: Mac 环境变量配置
date: 2017-09-12 22:15:59
categories: Mac 相关
tags: 
 - Linux 
 - 环境变量
 - Shell
---

![mac enviroment variable](http://oslynwh8c.bkt.clouddn.com/image/hexo/mac%20Environment%20variable.jpg)

mac os是 Unix 内核的桌面版操作系统，Linux 是类 Unix 操作系统。

因为 Unix 与 Linux 的 Shell 一般都是 bash，所以，Mac 下的环境变量设置和 Linux 差不多。

<!-- more -->

想要理解这些，必须先弄懂几个概念。

<br>

<br>

### 相关概念

#### Shell

操作系统可以分成核心 **kernel** (内核)和**Shell**（外壳）两部分，其中，Shell 是操作系统与外部的主要接口，位于操作系统的外层，为用户提供与操作系统核心沟通的途径。

**Shell** 俗称壳（用来区别于核 kernel），是一种「命令解析器」。

**Shell**  是指一种应用程序，这个应用程序提供了一个界面，用户通过这个界面访问操作系统内核的服务。

**Shell** 管理用户与操作系统之间的交互：等待你输入，向操作系统解释你的输入，并且处理各种各样的操作系统的输出结果。

<br>

#### Shell 脚本

Shell 脚本（shell script），是一种为 shell 编写的脚本程序。

我们所说的 shell 通常都是指 shell 脚本，但shell 和 shell script 是两个不同的概念。

由于习惯的原因，简洁起见，本文出现的 "shell编程" 都是指 shell 脚本编程，不是指开发 shell 自身

<br>

#### Shell 种类

不同系统有不同的 **Shell**，如 **bash、C shell、windows power shell** 等等；

如果简单分类的话，可以分为两类，分别是**图形化 Shell** 与**命令行 Shell** 。

比如说，在windows系统中见到的桌面即explorer.exe（资源管理器）是图形 shell ，而 cmd ( win+R 输入 cmd 可以打开cmd.exe，进行命令行操作) 就是命令行 shell。

> cmd 与 dos 
>
> cmd 属于 windows 系统的一部分，dos 本身就是一个系统，在 dos 系统下可以删除，修复windows系统，而在cmd下则不行。只是 cmd 中的某些命令和 dos 中的命令相似，因此很多人把二者混为一谈。

<br>

图形 Shell 非常简单，封装了许多常用操作，对普通用户非常友好，但面对复杂的、不常用的操作却无能为力。

在 window 中，其 命令行 Shell 的cmd 功能有限，相对于 Linux 的 Shell 有很大差距。在 Linux 系统中，Shell 脚本中可以有循环，不仅可以执行基本的条件语句，很多在编程语言中才有的功能它都具备，所以 Linux 下的 Shell 功能非常强大，可以高效率的完成很多复杂的事情。

我们一般所说的 Shell 都是 Linux 系统下的 Shell。

Linux 系统下的 Shell 种类众多，不同的 shell 具备不同的功能，shell 还决定了脚本中函数的语法，Linux中默认的 Shell是 bash(/bin/bash)。

<br>

mac 查看系统使用的Shell ：

`echo $SHELL`

mac 一般使用bash作为默认shell。

<br>

##### Bourne Shell（/bin/sh）

sh(全称 Bourne Shell) 是UNIX最初使用的 shell，而且在每种 UNIX 上都可以使用。
Bourne Shell 在 shell 编程方面相当优秀，但在处理与用户的交互方面做得不如其他几种 shell。

<br>

##### Bourne Again Shell (/bin/bash)

bash（全称 Bourne Again Shell）是 Linux 默认的 Shell ，它是 Bourne Shell 的扩展，与 Bourne Shell 完全兼容，并且在 Bourne Shell 的基础上增加了很多特性。可以提供命令补全，命令编辑和命令历史等功能。它还包含了很多 C Shell 和 Korn Shell 中的优点，有灵活和强大的编辑接口，同时又很友好的用户界面。

<br>

##### C Shell（/bin/csh）

csh(全称 C Shell) 用的是“类C”语法,csh是具有C语言风格的一种shell，其内部命令有52个，较为庞大。目前使用的并不多，已经被/bin/tcsh所取代。

<br>

##### K Shell (/bin/ksh)

ksh (全称 Korn Shell) 集合了 C Shell 和 Bourne Shell 的优点并且和 Bourne Shell 完全兼容。

<br>

##### TC shell (/bin/tcsh)

tcsh是csh的增强版，与 C shell 完全兼容

<br>

##### Z Shell (/bin/zsh )

Z shell是Korn shell的一个增强版本，具备bash shell的许多特色，是目前Linux里最庞大的一种shell。它有84个内部命令，使用起来也比较复杂。

<br>

<br>



### 配置的位置

Mac系统的环境变量，加载顺序为：

1、系统级别的

**/etc/profile**   不建议修改此文件，全局配置。

**/etc/bashrc**  一般在此文件添加系统级环境变量，bash Shell执行时，都会读取此文件。

**/etc/paths**  许多博文是在这个文件里面添加全局环境变量的。

2、用户级别的

**~/.bash_profile**  一般在此文件中添加用户级环境变量，用户登录时，仅执行一次。

**~/.bash_login** 

**~/.profile**  可以设定本用户专有的路径，环境变量，等，它只能登入的时候执行一次

**~/.bashrc** 也是某用户专有设定文档，可以设定路径，命令别名，每次shell script的执行都会使用它一次

<br>

etc目录下面的是系统级的配置，~目录下的是用户配置，用户配置文件只会加载一个（.bashrc除外）

根据系统的环境变量加载顺序，我们一般有两个地方可以编辑环境变量：**/etc/bashrc** 和 **~/.bash_profile** ，区别就是 **/etc/bashrc**  是全局系统的环境变量，**~/.bash_profile** 是当前用户的环境变量。

<br>

<br>

### Vim 编辑器

在 linux 家族中，vim编辑器是系统自带的文本编辑器，在配置环境变量时，可以会直接使用vim编辑器，所以要先学下Vim编辑器。

Vim编辑器中内设有的三种模式——**命令模式、末行模式和编辑模式**，每种模式分别又支持多种不同的命令快捷键组合，大大的提高了工作效率。

![vim编辑器三种模式](http://oslynwh8c.bkt.clouddn.com/image/hexo/vim%E7%BC%96%E8%BE%91%E5%99%A8%E6%A8%A1%E5%BC%8F.png)

- 命令模式：控制光标移动，可对文本进行删除、复制、粘贴和查找等工作。
- 输入模式：正常的文本录入。
- 末行模式：保存、退出与设置编辑环境。

每次运行vim编辑器后都默认会是“**命令模式**”，需要先进入到“输入模式”后再进行编写文档的工作，而每次编辑完成需先返回到“**命令模式**”后再进入“**末行模式**”中执行对文本的保存或退出操作，并不能直接从“输入模式”切换到“**末行模式**”。

在命令模式和末行模式中最常用的一些快捷键命令，如下表所示：

| 命令   | 作用                          |
| ---- | --------------------------- |
| dd   | 删除(剪切)光标所在整行。               |
| 5dd  | 删除(剪切)从光标处开始的5行。            |
| yy   | 复制光标所在整行。                   |
| 5yy  | 复制从光标处开始的5行。                |
| n    | 显示搜索命令定位到的下一个字符串。           |
| N    | 显示搜索命令定位到的上一个字符串。           |
| u    | 撤销上一步的操作                    |
| p    | 将之前删除（dd）或复制（yy）过的数据粘贴到光标后。 |
| ?字符串 | 在文本中从下至上搜索该字符串。             |
| /字符串 | 在文本中从上至下搜索该字符串。             |

<br>

末行模式主要用于保存或退出文件，能够设置vim编辑器的整体使用环境，还可以让用户执行外部[Linux命令](http://www.linuxprobe.com/)或跳转到特定的行数，切换到末行模式的方式就是在命令模式中输入一个冒号就可以的，末行模式中可用的命令如下表：

| 命令            | 作用                    |
| ------------- | --------------------- |
| :w            | 保存                    |
| :q            | 退出                    |
| :q!           | 强制退出（放弃对文本的修改内容）      |
| :wq!          | 强制保存退出                |
| :set nu       | 显示行号                  |
| :set nonu     | 不显示行号                 |
| :命令           | 执行该命令                 |
| :整数           | 跳转到该行                 |
| :s/one/two    | 将当前光标所在行的第一个one替换成two |
| :s/one/two/g  | 将当前光标所在行的所有one替换成two  |
| :%s/one/two/g | 将全文中的所有one替换成two      |

在末行模式下，使用最多的是 `wq` 保存退出。

<br>

<br>

### 环境变量

这里以配置Java环境变量为例，来说明Mac下配置环境变量的步骤。

1. 查看系统的环境变量：

   `echo $PATH `


1. 更新或者新建配置文件。

   `touch ~/.bash_profile`

   这里，Mac一般选择 「~/.bash_profile」这个文件来配置环境变量。

   `touch`这个命令有两个作用，如果这个文件存在，则更新修改时间，如果不存在，则创建这个文件。

2. 使用vim编辑器，打开配置文件

   `vim ~/.bash_profile `

   如果提示没权限的话，输入

   `sudo vim ~/.bash_profile `

   然后输入密码后，使用 root 权限操作。

   如果不喜欢使用 vim 编辑器，也可使用 Mac 默认的文本编辑器打开

   `open ~/.bash_profile`

3. 添加环境变量

   如果是第一次打开，这个文件应该是空的，添加环境变量如下：

   ```shell
   JAVA_HOME=/Library/Java/JavaVirtualMachines/jdk1.7.0_55.jdk/Contents/Home
   CLASSPAHT=.:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar
   PATH=$JAVA_HOME/bin:$PATH:
   export JAVA_HOME
   export CLASSPATH
   export PATH
   ```

   完成后，保存并退出编辑器。

4. 更新环境变量 , 使其生效，在终端输入

   ` source ~/.bash_profile`

   这样就完成了 Java 环境变量的配置，其他环境变量的配置类似。



<br>

<br>

### 问题

如果你的命令行安装了`zsh`和`oh-my-zsh`，修改环境变量时有些小的变化。

比如，修改的是`~/.bash_profile`，那么必须`source ~/.bash_profile`才能生效，而且重启后继续无效，因为 zsh ！我的终端是iTerm2，它很炫酷，但就是环境变量搞不成。

Google 找到了问题所在，安装 zsh后，`~/.bash_profile`并没有被执行，所以需要编辑 zsh 的配置文件`~/.zshrc`，在该文件里加入`source ~/.bash_profile` 来更新配置文件。

具体操作是，在终端输入`vim ~/.zshrc`

在文件末尾加一句 `source ~/.bash_profile`

然后保存退出。

<br>

<br>

### 参考

[第4章 Vim编辑器与Shell命令脚本。](http://www.linuxprobe.com/chapter-04.html)

[Linux 下 Shell 命令的分类及用法](http://www.linuxprobe.com/linux-shell.html)

[Mac OSX 添加环境变量的三种方法](http://yijiebuyi.com/blog/41ee3bab0c5bf1d43c7a8ccc7f0fe44e.html)

[Mac 设置环境变量](http://jingyan.baidu.com/article/ff411625c82a0312e4823799.html)

[MacOS设置环境变量path的完全总结](http://www.zhimengzhe.com/mac/111650.html)