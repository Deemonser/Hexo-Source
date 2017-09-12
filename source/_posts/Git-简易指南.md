---
title: Git 简易指南
date: 2017-09-03 09:47:38
categories: 工具
tags: 
 - Git
---


![git logo](http://oslynwh8c.bkt.clouddn.com/image/hexo/Git-Logo.png)




用了 Git ， 就再也不想用svn 了。

早就想花时间整理下 Git 的使用，到今天行动了。

这里记录下 Git 的基本原理，以及常用的操作。

<!-- more -->

<br><br>

### Git 简介

2005 年，开发 BitKeeper 的商业公司收回了 Linux 内核开源社区免费使用 BitKeeper 的权力，于是 Linux 的缔造者 Linus Torvalds 花了两周时间自己用 C 写了一个分布式版本控制系统 Git，这就是牛人！

[Git](http://zh.wikipedia.org/wiki/Git) 已经是目前最流行的版本管理系统，学会Git 成了开发者的必备技能。

​    

### Git 下载安装

在开始前，得进行下载安装，这个可以直接去 [Git官网](https://git-scm.com/) 下载，首页就是下载地址。

Git 目前支持 Linux/Unix、Solaris、Mac和 Windows 平台上运行。

Git 是使用命令操作的，如果觉得不方便，还有一些图形化的 Git 工具，比如 Source Tree。但一般都是使用 Git 命令操作的，方便快捷。 

安装完成之后，如果是Windows，可以点击鼠标右键，看到如下菜单

![windows 鼠标右键](http://oslynwh8c.bkt.clouddn.com/image/hexo/git%E5%8F%B3%E9%94%AE%E8%8F%9C%E5%8D%95.png)

然后点击 「 **Git Bash Here** 」进入命令窗口。

在命令窗口输入 命令 `git --version` ，则可以看到 Git  的版本号。

<br>

### 仓库的创建与检出

先不解释Git 的工作流程。

Git 使用的第一步，就是本地仓库的创建，以及 仓库的检出。

<br>

#### 仓库的创建

当想要某个项目或者文件进行版本管理时，第一步就是创建这个项目的本地仓库。

创建新文件夹 「TestGit」，打开，然后执行 

`git init`

这样就创建新的 git 仓库，这个仓库可以管理「TestGit」文件夹下的所有文件。

可以看到 TestGit 下多了一个名为 「.git」的隐藏文件夹，这个文件夹就是 Git 的仓库。

Git的版本库里存了很多东西，其中最重要的就是称为stage（或者叫index）的暂存区，还有Git为我们自动创建的第一个分支`master`，以及指向`master`的一个指针叫`HEAD`。

![Git 版本库](http://oslynwh8c.bkt.clouddn.com/image/hexo/Git%20%E7%89%88%E6%9C%AC%E5%BA%93.png)

整个本地项目可以看做上区域。

第一个是你的 `工作区`，它持有实际文件，如「TestGit」目录（.git 隐藏文件不算）；

第二个是 `暂存区（stage）`，它像个缓存区域，临时保存你的改动；

最后是 `HEAD` 以及各个分支组成的本地仓库。

<br>

#### 仓库的检出

通常，我们想从远程主机（如 Github、Coding等）拉取一个项目到本地来，就要克隆仓库。

使用如下命令:

`git clone <版本库的网址>` 

或者 `git clone <版本库的网址> <本地目录名>`

例如，

```
git clone git@github.com:square/retrofit.git
```

它支持多种协议，除了HTTP(s)以外，还支持SSH、Git、本地文件协议等，Github 上就是使用的 SSH 协议。

<br><br>

### Git 的工作流程

![Git工作流](http://oslynwh8c.bkt.clouddn.com/image/hexo/Git%E5%B7%A5%E4%BD%9C%E6%B5%81.png)



这个图是在 [阮一峰的博客](http://www.ruanyifeng.com/blog/2014/06/git_remote.html) 里找的，这个图更加便于理解 Git 的整个工作流程 ，其中还有一些常用的Git 命令。

可以简单的认为 Git 由四部分构成：

- Workspace：工作区
- Index / Stage：暂存区
- Repository：本地仓库
- Remote：远程仓库


<br>

#### Workspace 工作区

工作区就是本地的文件目录，在此目录下的所有文件都可以被 Git 管理（.git 文件除外）。

平时进行开发、改动也是在这个工作区进行操作的。

工作区只会对应本地仓库的一个分支。

工作区一般同步了一个远程仓库，如果不需要联网，也可以不用远程仓库。

仓库可以有多个分支，默认在创建时，生产一个 master 分支与工作区对应。

<br>

#### Index / Stage 暂存区

「.git」隐藏目录下的index文件就是暂存区。

 暂存区会记录 `git add .` 添加文件的相关信息(文件名、大小、timestamp...)，不保存文件实体， 它会通过 id 指向每个文件实体。

可以使用`git status`查看暂存区的状态。暂存区标记了你当前工作区中，哪些内容是被git管理的，哪些文件有改动却没有添加到 暂存区。

工作区里文件的每一次改动都必须通过 `git add` 命令来添加到暂存区，然后才能`git commit `提交到本地仓库。

<br>

#### Repository 本地仓库

本地仓库保存了暂存区 `git commit` 提交过的的各个版本。

本地仓库有分支的概念，在创建仓库时，默认生产一个 `master` 分支来。

`master`分支是一条线，Git用`master`指向最新的提交，再用`HEAD`指向`master`，就能确定当前分支，以及当前分支的提交。

![master branch](http://oslynwh8c.bkt.clouddn.com/image/hexo/master%20branch.png)

每次提交，`master`分支都会向前移动一步，这样，随着你不断提交，`master`分支的线也越来越长。

可以通过`git branch <分支名称>` 手动创建其它分支，并通过 `git branch` 来查看所有本地分支，然后通过 `git checkout <分支名称>` 来切换分支。

`HEAD`始终指向当前所处分支的最新的提交点。你所处的分支变化了，或者产生了新的提交点，HEAD就会跟着改变。

通过`git push` 可以将本地仓库与远程仓库的同步。

<br>

#### Remote 远程仓库

远程仓库一般在云端，如 Github、Coding 等都属于免费的 Git 仓库。

远程仓库方便我们在不同地点协同办公。

<br><br>

### Git 的常用命令

![Git常用命令速查表](http://oslynwh8c.bkt.clouddn.com/image/hexo/Git%E5%B8%B8%E7%94%A8%E5%91%BD%E4%BB%A4%E9%80%9F%E6%9F%A5%E8%A1%A8.jpg)

这图是网上找的 Git常用命令表。

除了最开始的仓库创建与克隆，还有以下这些命令经常用到。

<br>

#### status

如果纯用命令的话，status 这个命令应该是最常用的。

它的作用就是查看 Git 的状态，它会列出在工作区中哪些文件没有被 Git 管理，哪些被管理的文件有修改却没有 `git add` 到暂存区，并根据情况给出建议。

所有，如果不用 IDE 中的插件的话，`git status` 配合 `git add .` 这两个命令应该是最常用的。但这些工作现在都由插件帮我们完成了。

| 命令         | 解释         |
| ---------- | ---------- |
| git status | 列出Git 当前状态 |

示例：（开始很想贴图，但命令窗口放在这里真的low......）

```
$ git status
On branch master

Initial commit

Untracked files:
  (use "git add <file>..." to include in what will be committed)

        retrofit/

nothing added to commit but untracked files present (use "git add" to track)
```



这里面说，有一个 retrofit 的文件夹还没有被添加到暂存区，建议使用 `git add <file>` 来添加到暂存区。

<br>

#### add 

add 命令就是将工作区修改的内容提交到暂存区，交由git管理。

| 命令                  | 解释              |
| ------------------- | --------------- |
| git add .           | 添加当前目录的所有文件到暂存区 |
| git add   &lt;file> | 添加指定文件或文件夹到暂存区  |

示例：

```
deemonser@kj-deemonser MINGW64 /d/deemonser/Desktop/TestGit (master)
$ git add .

deemonser@kj-deemonser MINGW64 /d/deemonser/Desktop/TestGit (master)
$ git status
On branch master

Initial commit

Changes to be committed:
  (use "git rm --cached <file>..." to unstage)

        new file:   retrofit
```

可以看到 使用 `git add .` 之后，再查看状态，发现添加了 retrofit 文件夹，说明已经添加到暂存区了。 

<br>

#### commit

commit 命令是将暂存区的所有内容提交到本地仓库，并使得当前分支的HEAD向后移动一个提交点，然后暂存区会清空。

| 命令                                   | 解释                          |
| ------------------------------------ | --------------------------- |
| git commit -m &lt;message>           | 提交暂存区到本地仓库,message代表提交的说明信息 |
| git commit &lt;file> -m &lt;message> | 提交暂存区的指定文件到本地               |
| git commit --amend -m &lt;message>   |                             |

其中 message 是必填的，表示本次提交的记录信息。

```
deemonser@kj-deemonser MINGW64 /d/deemonser/Desktop/TestGit (master)
$ git commit -m 'first commit, add retrofit file'
[master (root-commit) 34d93c2] first commit, add retrofit file
 1 file changed, 1 insertion(+)
 create mode 160000 retrofit
```

在提交后，可以通过 `git log` 来查看提交的历史记录，可以看到此次提交有一个很长的ID。

还可以通过 `git reflog` 来查看 Git  的所有操作记录。

<br>

#### branch

分支这个概念非常重要，在工程中经常要用到分支，即使是单人开发，也提倡使用多分支。

在实际开发中，我们应该按照几个基本原则进行分支管理：

首先，`master`分支应该是非常稳定的，也就是仅用来发布新版本，平时不能在上面干活；

那在哪干活呢？干活都在`dev`分支上，也就是说，`dev`分支是不稳定的，到某个时候，比如1.0版本发布时，再把`dev`分支合并到`master`上，在`master`分支发布1.0版本；

多人的话，每个人都有自己的 `dev` 分支，时不时地往主`dev`分支上合并就可以了。

所以，团队合作的分支看起来就像这样：

![branch](http://oslynwh8c.bkt.clouddn.com/image/hexo/branch.png)



关于分支，大概有四种操作：展示分支，切换分支，创建分支，删除分支。

| 命令                                       | 解释                   |
| ---------------------------------------- | -------------------- |
| git branch                               | 列出所有的本地分支            |
| git branch -r                            | 列出所有的远程分支            |
| git branch -a                            | 列出所有的本地和远程分支         |
| git branch &lt;branch-name>              | 新建分支                 |
| git checkout &lt;branch-name>            | 切换到指定的分支             |
| git checkout -b &lt;branch-name>         | 新建并切换到指定的分支          |
| git branch --track &lt;branch> &lt;remote-branch> | 新建分支，并与指定的远程分支建立追踪关系 |
| git branch -d &lt;branch-name>           | 删除分支                 |
| git push origin --delete &lt;branch-name> | 删除远程分支               |

<br>

#### merge

merge命令把不同的分支合并起来。

比如，当`dev` 分支开发完成之后，要合并到 `master` 分支上，这就要用到 merge 命令，整个过程如下：

```
      D---E (dev)						   D---E 
     /						==> 	     /		 \
A---B---C---F (master)				A---B---C---F--G    (dev,master)
```

每当提交或合并分支之前，需要将远程代码同步到本地，养成良好的习惯，防止出现重大的冲突，都是踩过的坑。

| 命令                    | 解释             |
| --------------------- | -------------- |
| git fetch &lt;remote> | 将远程代码仓库同步到本地   |
| git merge &lt;branch> | 将指定的分支合并到当前的分支 |

还有一点，就是合并时的主次要区分清楚，比如 将 `dev` 合并到 `master` ，那么主是 `master` ，次是 `dev` 。这时候首先要先切换分支到 `master` 分支上，将 `dev` 分支的内容 合并过来。

<br>

#### rebase

rebase又称为衍合，与 合并 的作用类似。

| 命令                          | 解释           |
| --------------------------- | ------------ |
| git rebase &lt;branch-name> | 将指定分支衍合到当前分支 |

在 `master` 分支上执行 `git rebase dev` ，那么就会将 `dev`  分支上的所有提交都在 `master` 的分支结点上重新执行一遍，这个过程中有很可能出现冲突，需要手动解决冲突。

      	 D---E (dev)						  
     	/						==> 	    
    A---B---C---F (master)				A---B---D---E---C'---F'    (dev,master)
可以看到与合并是有区别的。

<br>

#### reset

reset命令把当前分支指向另一个位置，并且相应的变动工作区和暂存区。

| 命令                              | 解释                                     |
| ------------------------------- | -------------------------------------- |
| git reset --hard HEAD           | 让工作区回到最新的提交点。HEAD代表当前版本，HEAD^代表上个版本... |
| git reset --hard &lt;commit-id> | 让工作区回到指定的提交点。 每次commit时都会有一个ID         |

在每次提交时，git 都会对此次提交生产一个ID，在回退版本时，一般通过 ID 来指定到对应版本。这个ID非常长，也不用输入完整，只要输入前面几位，保证唯一就行，git 会识别的。

<br>

#### push

push 是将本地仓库分支与远程仓库分支同步。

| 命令                               | 解释             |
| -------------------------------- | -------------- |
| git push &lt;remote> &lt;branch> | 将本地仓库分支同步到远程仓库 |
| git push &lt;remote> --force     | 强行推送当前分支到远程仓库  |
| git push &lt;remote> --all       | 推送所有分支到远程仓库    |
| git remote add origin &lt;url>   | 本地仓库关联一个远程仓库   |

如果远程库是空的，我们第一次推送`master`分支时，加上了`-u`参数，Git不但会把本地的`master`分支内容推送的远程新的`master`分支，还会把本地的`master`分支和远程的`master`分支关联起来，在以后的推送或者拉取时就可以简化命令。

<br>

当然，Git 还有一些更高级的操作。

如果不清楚，可以花点时间 到[廖雪峰的官方网站](https://www.liaoxuefeng.com/wiki/0013739516305929606dd18361248578c67b8067c8c017b000) ，仔细阅读关于Git 的教程。

<br><br>

### 参考

[git 使用简易指南](http://www.bootcss.com/p/git-guide/)

[Git远程操作详解](http://www.ruanyifeng.com/blog/2014/06/git_remote.html)

[Git教程](https://www.liaoxuefeng.com/wiki/0013739516305929606dd18361248578c67b8067c8c017b000)

[一篇文章，教你学会Git](http://www.jianshu.com/p/072587b47515)
