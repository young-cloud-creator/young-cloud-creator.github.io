---
title: Git基本原理和常用操作
index_img: /illustration/git-note/index.png
tags:
    - Git
categories:
    - Git
excerpt: 版本控制系统（Version Control System）是一种用于记录文件（以及文件目录）的变化的工具。而Git是目前最为主流的版本控制系统，它已经成为版本控制系统的事实标准。
---

版本控制系统（Version Control System）是一种用于记录文件（以及文件目录）的变化的工具。通过版本控制系统，我们可以了解文件（以及文件目录）的改动历史（谁在何时对文件做了什么更改），并能够随时将文件恢复到之前任何一个状态。版本控制系统为团队合作带来了便利，团队可以同时开发并在最后将各自的成果合并起来；而使用版本控制系统的个人也能利用它方便地管理自己的文件。

Git最初由林纳斯·托瓦兹创作，于2005年以GPL许可协议发布。最初目的是为了更好地管理Linux内核开发而设计。Git是目前最为主流的版本控制系统，它已经成为版本控制系统的事实标准（the de facto standard）。与CVS、SVN等集中式版本控制系统不同，Git是一个分布式版本控制系统，也就是说它不将项目的版本库存放在中央服务器上，而是在每一台计算机上都存放一个完整的项目版本库。

{% note info %}
本文内容基于MIT的missing-semester课程中的Version Control (Git)一节，你可以在[https://missing.csail.mit.edu/2020/version-control/](https://missing.csail.mit.edu/2020/version-control/)找到完整的课程录像和讲义
{% endnote %}

# Git基本原理
在Git的术语中，一个文件被称为一个blob，一个文件是一组字节的集合；一个目录被称为一个tree（树），一个tree可以包含若干blobs和若干trees；一个snapshot（快照）是项目的顶层tree（即代表项目根目录的tree）。

一个项目的不同版本的集合可以看作是若干个snapshot的集合，Git使用有向无环图（Directed Acyclic Graph，DAG）来组织这些snapshot。在Git中，snapshot是一个commit的一部分，一个commit可以看作是一个包含更多信息的snapshot，每一个commit有若干个父commit。

例如，如果某个commit（记作B）直接由另一个commit（记作A）经过某些变化产生（比如在commit A的基础上修改了某些文件），那么commit B的父commit就只有一个，即commit A；
```None
A <-- B
```
而如果某个commit是由两个（或更多）commit合并而来，那么该commit的父commit就不止一个。比如commit A修改了文件A，commit B修改了文件B，现在我们通过一个commit（记作C）合并这两个commit，即创建一个既修改了文件A又修改了文件B的commit，那么这个commit的父commit就是commit A和commit B。
```None

A <---- C
       /
      v
      B
```
commit在创建之后就不能被修改，它所代表的是项目的修改历史，如果你要修改文件，只需要在某个或某几个commit的基础上创建一个新的commit。

## 数据结构
上面的描述可能比较模糊，下面用伪代码描述blob、tree和commit的数据结构。
```None
type blob = array<byte> //一个文件也就是blob是由若干字节构成的

//一个目录也就是tree包含若干个tree和blob
//为了区分这些不同的tree或者blob，需要一个字符串来标识它们（可以理解为文件名/目录名）
type tree = map<string, tree | blob>

//一个commit包含它的父commit（当然，实际上存储的是父commit的标识而非父commit本身）
//以及一些其他信息（例如commit的作者、commit的信息）
//和对顶层目录的snapshot（实际上存储的是snapshot的标识而非snapshot本身）
type commit = struct {
    parents: array<commit>
    author: string
    message: string
    snapshot: tree
}
```

现在定义object，一个object可以是blob、tree或者commit。
```None
type object = blob | tree | commit
```
在Git中，object统一使用SHA-1进行哈希，以哈希值作为object的地址。这样，tree对其包含的tree和blob的存储，实际上是存储这些tree和blob的地址（哈希值）。在终端中，可以通过`git cat-file -p`加上object的哈希值得到object。

## 引用 Reference
现在，尽管可以通过SHA-1哈希值访问object，但是这显然是不方便的，毕竟这一长串哈希值对人类来说不过是无意义的乱码，它难以被记忆和使用。Git对此的解决方法是通过**引用（reference）**，reference是一个类似`map<string, string>`的映射，它将名字（人类可读的字符串）与哈希值相绑定，从而使人类可以通过有意义的名字访问object。常见的reference例子是Git的分支名，Git的分支其实是指向某个commit的reference，通过`git checkout`，人类可以方便地使用有意义的分支名访问到某个特定commit。例如，`master`往往指向项目主分支的最新的一个commit，而`HEAD`则是指向当前所在的commit。

## 仓库 Repository
前面介绍了Git中的一些基本名词（blob、tree、commit、object、reference等）。粗略来说，Git仓库就是object和reference的集合，Git命令的实质是在commit构成的DAG中对object和reference进行操作，比如,如果想要修改`master`引用，使`master`引用指向哈希值为`5d83f9e`的commit，对应的Git命令是`git checkout master; git reset --hard 5d83f9e`。

## 暂存区 Staging Area
前面提到，一个项目的不同版本可以看作是一系列snapshot的集合，但Git并不是简单地创建当前状态的snapshot，而是给予用户更大的自主权，在创建snapshot时，Git允许用户挑选具体哪些更改将被保存到这一snapshot，比如，用户可以一次性实现两个功能，但为这两个功能分别创建一个snapshot。

为了实现上面的特性，Git提供了暂存区机制，用户将更改加入到暂存区，然后为暂存区中的所有更改创建一个snapshot（也就是commit）。这样，用户可以自由选择哪些更改将被加入到下一次commit，这种对更改的选择甚至可以具体到文件的某一行内容。

# Git常用操作
这里列出常用的Git操作，更具体的可以参考[<u>Pro Git</u>](https://git-scm.com/book/en/v2)、使用`git help`或者参考其他Git教程。

## 基础操作
- `git help <command>`  获取帮助/获取具体某一命令的帮助
- `git init` 创建一个新的空Git仓库
- `git status` 查看当前情况（有无文件被修改、有无新文件、暂存区情况等）
- `git add <filename>` 将文件加入暂存区
    - `git add -p <filename>` 进入交互模式，更具体地指定要加入暂存区的内容
- `git commit` 创建一个commit
- `git log` 以扁平模式展示历史commit记录
    - `git log --graph` 以DAG方式展示历史commit记录，可以看到commit之间的关系
- `git diff <filename>` 查看文件相较于暂存区内容（或HEAD指向的commit）的区别
    - `git diff <revision> <filename>` 查看文件在不同commit之间的区别，`<revision>`可以是branch的名字、commit的哈希值，如果`<revision>`只有一个branch名字或者commit哈希值，则会比较其指向的commit与当前commit的文件差异
- `git checkout <revision>` 更新HEAD，使其指向`<revision>`指出的commit，从而使项目目录变成`<revision>`指出的commit时的状态，可以用来切换分支、查看项目历史状态等

## 分支相关操作
分支的操作本质是对代表分支的reference的操作，例如切换分支是修改HEAD指向分支的reference，创建分支是创建一个指向HEAD指向的commit的新reference

- `git branch` 列出所有分支
    - `git branch <name>` 创建一个名为`<name>`的分支
- `git checkout <name>` 切换至`<name>`分支
    - `git checkout -b <name>` 创建并切换至`<name>`分支，等同于`git branch <name>; git checkout <name>`
- `git merge <revision>` 合并分支/commit到当前分支
- `git mergetool` 使用mergetool来处理合并时的冲突（conflict）
    - mergetool使用前需要自己在git config中指定mergetool，一般有vimdiff、beyondCompare、diffmerge等多种工具
    - mergetool帮助用户查看冲突所在，用户可以选择冲突两方中的一方内容作为最终内容，也可以选择冲突两方最近共同祖先的内容或者自己手动编辑内容，从而解决冲突
- `git rebase` merge操作将创建一个新的commit，父commit指向merge的分支；而rebase则会临时保存当前分支从与要合并分支的共同祖先开始的每一个commit记录作为patch（补丁），然后将当前分支reference指向要合并的分支，然后把保存的补丁逐个进行commit。
    - 如下所示，
    ```None
    before:
    A <- B <- C(branch A)
         ^
          \
            D <- E(branch B)
            
    after:
    A <- B <- C(branch A) <- D' <- E'(branch B)
         ^
          \
            D(throw away) <- E(throw away)
    ```
    - `git rebase -i` 交互式rebase

## 远程仓库相关操作
- `git remote` 列出所有与当前仓库绑定的远程仓库
    - `git remote add <name> <url>` 添加远程仓库
- `git push <remote> <local branch>:<remote branch>` 向指定远程仓库的指定分支推送本地指定分支的objects
- `git branch --set-upstream-to=<remote>/<remote branch>` 设置当前所在的本地分支与远程分支的绑定，从而之后可以直接使用`git push`命令而无需指定分支
- `git fetch` 从远程仓库获取objects/reference信息
- `git pull` 等同于`git fetch; git merge`，即从远程仓库获取信息后，合并本地分支与远程分支
- `git clone <remote> <localpath>` 从远程下载整个仓库

## 撤销相关操作
- `git commit --amend` 编辑commit的信息
- `git reset HEAD <file>` 取消对文件更改的暂存
- `git checkout -- <file>` 放弃对文件的更改

## 其他
- `git config` Git是高度自定义的，可以自定义你的Git
- `git clone --depth=1` 浅克隆，只下载远程仓库的最新版本而非整个版本库
- `git blame` 查看具体文件中某行的作者信息
- `git stash` 暂时移除对仓库的未commit的修改
    - `git stash pop` 恢复之前通过`git stash`暂时移除的修改
- `git bisect` 二分搜索版本历史
- `.gitignore` 通过该文件指出有意不通过Git追踪的文件，从而在Git操作时忽略它们

{% note info %}
<strong>如何写好git commit message：</strong>[https://tbaggery.com/2008/04/19/a-note-about-git-commit-messages.html](https://tbaggery.com/2008/04/19/a-note-about-git-commit-messages.html)、[https://cbea.ms/git-commit/](https://cbea.ms/git-commit/)

<strong>一些常见的Git错误操作和它们的补救措施：</strong>[https://ohshitgit.com](https://ohshitgit.com)
{% endnote %}
