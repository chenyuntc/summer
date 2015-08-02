#git overview

## git  简介
Git是目前世界上最先进的分布式版本控制系统（没有之一）。

和集中式版本控制系统相比，分布式版本控制系统的安全性要高很多，因为每个人电脑里都有完整的版本库，某一个人的电脑坏掉了不要紧，随便从其他人那里复制一个就可以了。而集中式版本控制系统的中央服务器要是出了问题，所有人都没法干活了。

在实际使用分布式版本控制系统的时候，其实很少在两人之间的电脑上推送版本库的修改，因为可能你们俩不在一个局域网内，两台电脑互相访问不了，也可能今天你的同事病了，他的电脑压根没有开机。因此，分布式版本控制系统通常也有一台充当“中央服务器”的电脑，但这个服务器的作用仅仅是用来方便“交换”大家的修改，没有它大家也一样干活，只是交换修改不方便而已。

 当然，Git的优势不单是不必联网这么简单，后面我们还会看到Git极其强大的分支管理，把SVN等远远抛在了后面。

 除了免费的外，还有收费的集中式版本控制系统，比如IBM的ClearCase（以前是Rational公司的，被IBM收购了），特点是安装比Windows还大，运行比蜗牛还慢，能用ClearCase的一般是世界500强，他们有个共同的特点是财大气粗，或者人傻钱多。

 安装完成后，还需要最后一步设置，在命令行输入：

```
$ git config --global user.name "Your Name"
$ git config --global user.email "email@example.com"
```
因为Git是分布式版本控制系统，所以，每个机器都必须自报家门：你的名字和Email地址。 
注意git config命令的--global参数，用了这个参数，表示你这台机器上所有的Git仓库都会使用这个配置，当然也可以对某个仓库指定不同的用户名和Email地址。
####创建版本库 
git init-->git add-->git commit

## 版本控制

### 工作区和暂存区,版本库
-  工作区 : Workding Directory
	电脑目录,实际看得到的文件

- 版本库: repository
	工作区有一个隐藏目录.git，这个不算工作区，而是Git的版本库
	Git的版本库里存了很多东西，其中最重要的就是称为stage（或者叫index）的暂存区，还有Git为我们自动创建的第一个分支master，以及指向master的一个指针叫**HEAD**。
	![版本库](http://www.liaoxuefeng.com/files/attachments/001384907702917346729e9afbf4127b6dfbae9207af016000/0 "三者关系")

- 暂存区 (stage)
	是工作区与版本库之间的缓冲区

git  add 只负责把修改加入到暂存区,gitcommit 只负责把暂存区的修改提交到版本库
第一次修改 ->` git add `-> 第二次修改 -> `git add` -> `git commit`



### 管理修改

#### 丢弃修改
- checkout 修改工作目录
- reset 修改暂存区
##### 错误未添加到暂存区
 你可以发现，Git会告诉你，`git checkout -- file`可以丢弃工作区的修改：
 命令`git checkout -- readme.txt`意思就是，把readme.txt文件在工作区的修改全部撤销，这里有两种情况：
- readme.txt自修改后还没有被放到暂存区，现在，撤销修改就回到和版本库一模一样的状态；
- readme.txt已经添加到暂存区后，又作了修改，现在，撤销修改就回到添加到暂存区后的状态。

总之，就是让这个文件回到最近一次git commit或git add时的状态。或者说回退到暂存区的的状态
*git checkout -- file命令中的--很重要，没有--，就变成了“创建一个新分支”的命令*

##### 错误已添加到暂存区
在commit之前，你发现了这个问题。用git status查看一下，修改只是添加到了暂存区，还没有提交：
Git同样告诉我们，用命令git reset HEAD file可以把**暂存区的修改撤销掉**（unstage），重新放回工作区：
`git reset HEAD readme.txt`
git reset命令既可以回退版本，也可以把暂存区的修改回退到工作区。当我们用HEAD时，表示最新的版本。

#####错误已经提交到版本库
`$ git reset --hard HEAD^`
--hard表示修改工作区和add HEAD^ 表示上一个版本, HEAD~100 表示之前100个版本
如果想恢复之前的修改 
git reset --hard 3628164id
id通过git reflog查看 Git提供了一个命令`git reflog`用来记录你的每一次命令：
git reflog 可以查看所有分支的所有操作记录（包括（包括commit和reset的操作），包括已经被删除的commit记录，git log则不能察看已经删除了的commit记录

#### 删除
`git rm`
现在，文件就从版本库中被删除了。

另一种情况是删错了，因为版本库里还有呢，所以可以很轻松地把误删的文件恢复到最新版本：
`$ git checkout -- test.txt`
`git checkout`实是用版本库里的版本替换工作区的版本，无论工作区是修改还是删除，都可以“一键还原”。

##远程仓库
###添加远程版本库	
`$ git remote add repository_name git@github.com:michaelliao/learngit.git`
得先添加ssh KEY

`git push -u origin master`
把本地库的内容推送到远程，用git push命令，实际上是把当前分支master推送到远程。

由于远程库是空的，我们第一次推送master分支时，加上了-u参数，Git不但会把本地的master分支内容推送的远程新的master分支，还会把本地的master分支和远程的master分支关联起来，在以后的推送或者拉取时就可以简化命令。
从现在起，只要本地作了提交，就可以通过命令：
$ git push origin master


或者
` git clone git@github.com:michaelliao/gitskills.git`

## 分支管理
截止到目前，只有一条时间线，在Git里，这个分支叫主分支，即master分支。HEAD严格来说不是指向提交，而是指向master，master才是指向提交的，所以，HEAD指向的就是当前分支。
![git分支](http://www.liaoxuefeng.com/files/attachments/0013849087937492135fbf4bbd24dfcbc18349a8a59d36d000/0)
当我们创建新的分支，例如dev时，Git新建了一个指针叫dev，指向master相同的提交，再把HEAD指向dev，就表示当前分支在dev上：
![git分支](http://www.liaoxuefeng.com/files/attachments/001384908811773187a597e2d844eefb11f5cf5d56135ca000/0 "git 分支")
假如我们在dev上的工作完成了，就可以把dev合并到master上。Git怎么合并呢？最简单的方法，就是直接把master指向dev的当前提交，就完成了合并：
![分支合并](假如我们在dev上的工作完成了，就可以把dev合并到master上。Git怎么合并呢？最简单的方法，就是直接把master指向dev的当前提交，就完成了合并：)
所以Git合并分支也很快！就改改指针，工作区内容也不变！

合并完分支后，甚至可以删除dev分支。删除dev分支就是把dev指针给删掉，删掉后，我们就剩下了一条master分支：
![分支合并](http://www.liaoxuefeng.com/files/attachments/001384908867187c83ca970bf0f46efa19badad99c40235000/0)

`git checkout -b newbranch`-b 新建 checkout 切换分支, git branch 直接新建分支,
`git branch -d` 删除

**Git鼓励大量使用分支：**

- 查看分支：`git branch`
- 创建分支：`git branch <name>``
- 切换分支：`git checkout <name>``
- 创建+切换分支：`git checkout -b <name>``
- 合并某分支到当前分支：`git merge <name>``
- 删除分支：`git branch -d <name>`

