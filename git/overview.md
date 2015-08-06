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

### 解决冲突

- git merge 之后 ` git status ` 可以查看冲突情况,Git用<<<<<<<，=======，>>>>>>>标记出不同分支的内容，修改并保存冲突文件之后,再修改提交

    ```
    $ git add readme.txt 
    $ git commit -m "conflict fixed"
    ```
        
    用带参数的git log也可以看到分支的合并情况：

    `$ git log --graph --pretty=oneline --abbrev-commit`
### 分支管理策略

#### no fast forward
 通常，合并分支时，如果可能，Git会用Fast forward模式，但这种模式下，删除分支后，会丢掉分支信息。

    如果要强制禁用Fast forward模式，Git就会在merge时生成一个新的commit，这样，从分支历史上就可以看出分支信息。
 `git merge --no-ff -m "merge with no-ff" dev`
在实际开发中，我们应该按照几个基本原则进行分支管理：

#### 分支管理
首先，master分支应该是非常稳定的，也就是仅用来发布新版本，平时不能在上面干活；

那在哪干活呢？干活都在dev分支上，也就是说，dev分支是不稳定的，到某个时候，比如1.0版本发布时，再把dev分支合并到master上，在master分支发布1.0版本；

你和你的小伙伴们每个人都在dev分支上干活，每个人都有自己的分支，时不时地往dev分支上合并就可以了。

所以，团队合作的分支看起来就像这样：
![s](http://www.liaoxuefeng.com/files/attachments/001384909239390d355eb07d9d64305b6322aaf4edac1e3000/0 "团队分支管理")

#### bug 分支

软件开发中，bug就像家常便饭一样。有了bug就需要修复，在Git中，由于分支是如此的强大，所以，每个bug都可以通过一个新的临时分支来修复，修复后，合并分支，然后将临时分支删除。
Git还提供了一个stash功能，可以把当前工作现场“储藏”起来，等以后恢复现场后继续工作.`git stash`  
现在，用git status查看工作区，就是干净的（除非有没有被Git管理的文件），因此可以放心地创建分支来修复bug。
修复bug 之后`$ git stash list`
工作现场还在，Git把stash内容存在某个地方了，但是需要恢复一下，有两个办法：
- 一是用`git stash apply`恢复，但是恢复后，stash内容并不删除，你需要用`git stash drop`来删除；
- 另一种方式是用git stash pop，恢复的同时把stash内容也删了：
`$ git stash pop`
你可以多次stash，恢复的时候，先用git stash list查看，然后恢复指定的stash，用命令：
`$ git stash apply stash@{0}`

#### feature 分支

添加一个新功能时，你肯定不希望因为一些实验性质的代码，把主分支搞乱了，所以，每添加一个新功能，最好新建一个feature分支，在上面开发，完成后，合并，最后，删除该feature分支。

#### 多人协作
 
用git remote -v显示详细的信息：

```
$ git remote -v
origin  git@github.com:michaelliao/learngit.git (fetch)
origin  git@github.com:michaelliao/learngit.git (push)
```
上面显示了可以抓取和推送的origin的地址。如果没有推送权限，就看不到push的地址。

####推送分支

推送分支，就是把该分支上的所有本地提交推送到远程库。推送时，要指定本地分支，这样，Git就会把该分支推送到远程库对应的远程分支上：
`$ git push origin master`
如果要推送其他分支，比如dev，就改成：
`$ git push origin dev`
但是，并不是一定要把本地分支往远程推送，那么，哪些分支需要推送，哪些不需要呢？

master分支是主分支，因此要时刻与远程同步；
dev分支是开发分支，团队所有成员都需要在上面工作，所以也需要与远程同步；
bug分支只用于在本地修复bug，就没必要推到远程了，除非老板要看看你每周到底修复了几个bug；
feature分支是否推到远程，取决于你是否和你的小伙伴合作在上面开发。

总之，就是在Git中，分支完全可以在本地自己藏着玩，是否推送，视你的心情而定！

#### 抓取分支

`$ git clone git@github.com:michaelliao/learngit.git`
当你的小伙伴从远程库clone时，默认情况下，你的小伙伴只能看到本地的master分支。
现在，你的小伙伴要在dev分支上开发，就必须创建远程origin的dev分支到本地，于是他用这个命令创建本地dev分支：

**`$ git checkout -b dev origin/dev`**
你的小伙伴已经向origin/dev分支推送了他的提交，而碰巧你也对同样的文件作了修改，并试图推送, 推送失败，因为你的小伙伴的最新提交和你试图推送的提交有冲突，解决办法也很简单，Git已经提示我们，先用git pull把最新的提交从origin/dev抓下来，然后，在本地合并，解决冲突，再推送：git pull也失败了，原因是没有指定本地dev分支与远程origin/dev分支的链接，根据提示，设置dev和origin/dev的链接：这回git pull成功，但是合并有冲突，需要手动解决，解决的方法和分支管理中的解决冲突完全一样。解决后，提交，再push：

#### 多人合作方式总结
 多人协作的工作模式通常是这样：

1.  可以试图用git push origin branch-name推送自己的修改；

2. 如果推送失败，则因为远程分支比你的本地更新，需要先用git pull试图合并；

3. 如果合并有冲突，则解决冲突，并在本地提交；

4. 没有冲突或者解决掉冲突后，再用`git push origin branch-name`推送就能成功！

5. 如果`git pull`提示“no tracking information”，则说明本地分支和远程分支的链接关系没有创建，用命令`git branch --set-pstream branch-name origin/branch-name`。

 ## 标签

发布一个版本时，我们通常先在版本库中打一个标签，这样，就唯一确定了打标签时刻的版本。将来无论什么时候，取某个标签的版本，就是把那个打标签的时刻的历史版本取出来。所以，标签也是版本库的一个快照。

Git的标签虽然是版本库的快照，但其实它就是指向某个commit的指针（跟分支很像对不对？但是分支可以移动，标签不能移动），所以，创建和删除标签都是瞬间完成的。
### 新建标签
在Git中打标签非常简单，首先，切换到需要打标签的分支上：
然后，敲命令`git tag <name>`就可以打一个新标签：
可以用命令`git tag`查看所有标签：
` git tag v0.9 6224937`  对历史记录打标签
还可以创建带有说明的标签，用-a指定标签名，-m指定说明文字：
### 删除标签
- 命令`git push origin <tagname>`可以推送一个本地标签；
- 命令`git push origin --tags`可以推送全部未推送过的本地标签；
- 命令`git tag -d <tagname>`可以删除一个本地标签；
- 命令`git push origin :refs/tags/<tagname>`可以删除一个远程标签。

## github
## 自定义git
###忽略特殊文件
有些时候，你必须把某些文件放到Git工作目录中，但又不能提交它们，比如保存了数据库密码的配置文件啦，等等，每次`git status`都会显示`Untracked files ...`，有强迫症的童鞋心里肯定不爽。

好在Git考虑到了大家的感受，这个问题解决起来也很简单，在Git工作区的根目录下创建一个特殊的`.gitignore`文件，然后把要忽略的文件名填进去，Git就会自动忽略这些文件。

不需要从头写`.gitignore`文件，GitHub已经为我们准备了各种配置文件，只需要组合一下就可以使用了。所有配置文件可以直接在线浏览：<https://github.com/github/gitignore>

忽略文件的原则是：

- 忽略操作系统自动生成的文件，比如缩略图等；
- 忽略编译生成的中间文件、可执行文件等，也就是如果一个文件是通过另一个文件自动生成的，那自动生成的文件就没必要放进版本库，比如Java编译产生的.class文件；
- 忽略你自己的带有敏感信息的配置文件，比如存放口令的配置文件。
**.gitignore文件本身要放到版本库里，并且可以对.gitignore做版本管理！**

### 配置别名
`$ git config --global alias.st status`
`$ git config --global alias.unstage 'reset HEAD'`

### 配置文件
配置文件

配置Git的时候，加上`--global`是针对当前用户起作用的，如果不加，那只针对当前的仓库起作用。

配置文件放哪了？每个仓库的Git配置文件都放在`.git/config`文件中
别名就在[alias]后面，要删除别名，直接把对应的行删掉即可。
而当前用户的Git配置文件放在用户主目录下的一个隐藏文件`.gitconfig`中：

## git服务器
