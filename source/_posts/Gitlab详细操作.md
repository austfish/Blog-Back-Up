title: Gitlab详细操作
date: 2019-09-19 14:52:49
tags:
	- 工具
	- git
---
基本操作、远程仓库、进阶操作、分支管理、标签管理、协同开发实例、常见问题
<!--more-->
## 基本操作

**git安装**
在Windows上使用Git，可以从Git官网直接下载安装程序，默认安装即可。安装完成之后会多处git bash和git GUI两个程序，我们使用git bash 采用命令行的方式进行一系列的操作。

**本地git仓库**
**1、什么是本地仓库**
版本库又名仓库，英文名repository，你可以简单理解成一个目录，这个目录里面的所有文件都可以被Git管理起来，每个文件的修改、删除，Git都能跟踪，以便任何时刻都可以追踪历史，或者在将来某个时刻可以“还原”

**2、本地仓库的建立**
在本地选择一个目录，新建文件夹，并在终端打开这个文件夹。
将这个文件夹初始化为仓库，使用命令 git init 将这个文件夹初始化为git可以管理的仓库，那么现在我们的这个本地文件夹就变化了Git可以管理的仓库，在这个文件夹下会有一个隐藏的 .git 文件夹
**3、在本地的仓库添加或者修改文件**
在本地的仓库进行代码编写的时候，我们首先要明白git中工作区和暂存区的概念。

 - 工作区

我们在资源管理器里可以看到的文件就是我们的工作区

 - 暂存区

当我们的代码完成了一个阶段，我们想当前的这个版本在本地仓库进行保存一个版本，也就是commit操作，但是假如我们每次文件修改后都需要进行一次commit，会比较麻烦，所以Git给我们提供了一种方式，就是将修改的文件进行一次add操作，添加到暂存区中，在进行了一些add之后，统一进行commit操作。当然也可以直接每次add之后就进行commit。

    git add filename --将文件添加到暂存区
    git add . -- 将本地仓库所有的文件添加到暂存区
    git commit -m "提交说明" --使用commit将暂存区的文件进行提交到本地的分支，-m 代表本次的提交说明

**4、使用git status查看工作区和暂存区的状态**
当我们在工作区进行了文件的修改和文件的增加的时候，git status会告诉我们发生了什么变化，来帮助我们进行判断。

## 远程仓库

**1、github和gitlab的区别**
github和github都是基于git的web代码仓库管理软件。区别主要在于github上的仓库基本上都是开源的，当然也可以创建私有的代码仓库，但是费用比较昂贵。而gitlab是可以在企业内部搭建，可以创建私有的代码仓库，除此之外，gitlab还有一些其他的高级特性。相同之处是他们都是基于版本管理系统git，都使用git的命令进行操作。

**2、远程仓库和本地仓库的关联**
将本地的仓库仓库推送到远程是比较安全的，当本地代码出现问题的时候，我们可以很方便在的远程仓库上进行代码的拉取。具体操作如下：

1.如果我们按照上面的步骤已经在本地上建好了一个仓库，想和远程仓库进行关联，我们首先要在远程的仓库上新建一个项目，然后我们使用下面的代码进行关联（http方式）

    git remote add origin http://github.com/username/learngit.git
    username 是远程仓库的用户名，learngit是远程仓库的名称

或者（ssh密钥方式）:

    git remote add origin git@github.com:michaelliao/learngit.git

2.关联的时候有两种方式来验证用户的信息，一种是http方式，一种是ssh密钥方式，如果想要简化配置流程，使用http方式即可。

3.在经过了上述的关联之后，我们下一次进行推动的时候，只需要使用 git push origin master 即可完成推送。origin代表远程仓库，master是这个仓库的一个主分支也是默认分支。但是实际上我们是应该在其他分支上进行开发和推送，在下文会讲到。

**3、从远程库上进行克隆**
在远程仓库新建项目，使用git clone命令克隆到本地进行开发。这时候本地就会出现一个和项目名称相同的文件夹，在文件夹下同样.git文件夹来记录版本信息，这时候git已经在本地帮我们建立好了一个仓库。因为我们是直接在git上克隆下来的，所有已经和远程的仓库建立了关联，我们可以直接进行代码的推送

## 进阶操作

**版本回退**
当我们在开发的时候，将文件进行添加到暂存区然后提交到本地的版本库。版本库会将我们每一次的操作来进行一次存档。那么久方便我们很容易回退到某个版本。
我们可以使用 `git diff HEAD -- filename` 命令可以查看工作区和版本库里面最新版本的区别，如果确定需要回退，那就按照下述的方式来进行回退。我们分几个情境来进行版本回退

**1、当前编辑的文件还没有提交到暂存区**
如果只是做了简单的修改，我们可以手动恢复到我们想要的状态，但是加入修改过多，已经忘记自己修改了什么内容，那么使用
`git checkout -- file` 可以丢弃工作区的修改，在这里可能有两种情况：

一种是file自修改后还没有被放到暂存区，现在，撤销修改就回到和版本库一模一样的状态；

一种是readme.txt已经添加到暂存区后，又作了修改，现在，撤销修改就回到添加到暂存区后的状态。简单说就是让这个文件回到最近一次git commit或git add时的状态。

**2、文件已经提交到了暂存区但是并没有提交到本地的版本库**
git reset HEAD <file> 可以把暂存区的修改撤销掉（unstage），重新放回工作区。git reset命令既可以回退版本，也可以把暂存区的修改回退到工作区。当我们用HEAD时，表示最新的版本。
使用checkout 就可以丢弃工作区的修改了。

**3、已经将文件存储到暂存区并提交到了本地版本库**
当我们进行回退的时候，要知道我们要回退到哪个版本，在git中使用HEAD表示当前的版本，那么如果要是回退到上个版本的话就是使用： git reset --hard HEAD^ 我们的版本就会被还原到上一个版本，使用HEAD^可以回退到上上一个版本，当然==再往上100个版本写100个比较容易数不过来，所以写成HEAD~100。

在使用git reset的时候，有两个参数可以选择，分别是git reset --hard/soft，hard和soft的主要区别在于，soft只是回退了commit的信息，也就是指向之前的commit信息，但是对应的文件并不会发生改变。而使用hard方式commit信息会指向之前的信息，同时在工作区的文件也会跟随者进行变化。

**4、当我们回退后又想前进到回退前的版本，git也提供了对应的操作方式**
我们可以使用 git reflog 命令查看操作记录，来判断我们要回到哪个版本。

## 删除文件恢复

在git中，删除也是一个修改操作，一般会有以下两种操作。

**1、误删除文件（已经add或者已经commit）**
在本机上将文件进行删除后，我们介可以使用git status检测到文件的变动。但是原来的文件必须是已经add操作的
使用`git checkout -- 误删除文件名称`，和上文介绍的恢复工作区状态的指令相同。

**2、确定要删除文件**
当我们在资源管理器将某个文件删除，使用git status 会提示我们使用 git rm 进行删除文件
使用 `git rm （文件名称` 然后进行commit
当然也可以使用`git add .` 来添加全部修改或新增的文件
注意，当我们没有进行add 和commit操作的时候我们是无法从原来的版本库进行恢复的，也就是直接新建一个文件，没有经过任何操作，是无法恢复的。

## 分支管理

分支管理的目的就是将当前已经可以发布模块放在一个分支，在另外一个分支进行进一步开发，开发完成后可以进行分支合并。
在gitlab上默认的分支是master（主分支），也只有这一个分支，其他分支需要开发人员自行建立。
主要操作如下

**指令	功能**

 - git branch	查看分支（*指向的为当前的分支）
 - git branch<name>	创建分支
 - git checkout<name>	切换分支
 - git checkout -b <name>	创建加切换分支
 - git merge <name>	合并某分支到当前分支
 - git branch -d<name>	删除分支

**1、创建与合并分支**

 - 创建分支

在本地新建dev分支并指向dev分支后，这时候仓库下的文件更改都是在dev分支下的操作，当进行修改后，可以推送到远程的dev分支，如果远程没有dev分支，git会帮助我们新建dev分支并将文件推送。其他开发者在自己的本机建立dev分支，也向远程的dev分支进行推送（后面介绍本地dev分支和远程dev分支相连以及冲突的解决）。

 - 合并分支

切换到master分支之后，将dev分支合并到当前的master分支，合并完成后，git会提示我们当前分支比远程的分支超前一个。
[![nqjg8x.png](https://imgconvert.csdnimg.cn/aHR0cHM6Ly9zMi5heDF4LmNvbS8yMDE5LzA5LzE5L25xamc4eC5wbmc?x-oss-process=image/format,png)](https://imgchr.com/i/nqjg8x)

**合并图解**如下：
新建dev分支，指向master相同的提交，再把HEAD指向dev，就表示当前分支在dev上：
[![nqvKiR.png](https://imgconvert.csdnimg.cn/aHR0cHM6Ly9zMi5heDF4LmNvbS8yMDE5LzA5LzE5L25xdktpUi5wbmc?x-oss-process=image/format,png)](https://imgchr.com/i/nqvKiR)
新提交一次后，dev指针往前移动一步，而master指针不变：

[![nqvGLD.png](https://imgconvert.csdnimg.cn/aHR0cHM6Ly9zMi5heDF4LmNvbS8yMDE5LzA5LzE5L25xdkdMRC5wbmc?x-oss-process=image/format,png)](https://imgchr.com/i/nqvGLD)
合并dev分支，git的方式是将master指向dev分支

[![nqvBSP.png](https://imgconvert.csdnimg.cn/aHR0cHM6Ly9zMi5heDF4LmNvbS8yMDE5LzA5LzE5L25xdkJTUC5wbmc?x-oss-process=image/format,png)](https://imgchr.com/i/nqvBSP)
**在远程仓库进行合并的方式**

在实际的开发中，一般master主分支是受保护的，也就是说只有项目的负责人才有权限在master分支上进行相关的操作，其他普通开发者只能向dev分支进行推送，那么普通开发者怎么才能让负责人知道自己可以合并分支，这就需要在gitlab的dev分支界面发起合并请求，负责人收到请求后开始合并。

**2、分支冲突的解决**
**本地冲突**

当我们在本地使用master分支合并dev分支的时候出现冲突，如下图

[![nqv7mF.png](https://imgconvert.csdnimg.cn/aHR0cHM6Ly9zMi5heDF4LmNvbS8yMDE5LzA5LzE5L25xdjdtRi5wbmc?x-oss-process=image/format,png)](https://imgchr.com/i/nqv7mF)
使用git status 也可以查看冲突

[![nqxC0e.png](https://imgconvert.csdnimg.cn/aHR0cHM6Ly9zMi5heDF4LmNvbS8yMDE5LzA5LzE5L25xeEMwZS5wbmc?x-oss-process=image/format,png)](https://imgchr.com/i/nqxC0e)
使用cat命令查看冲突，Git用<<<<<<<，=======，>>>>>>>标记出不同分支的内容

[![nqxs91.png](https://imgconvert.csdnimg.cn/aHR0cHM6Ly9zMi5heDF4LmNvbS8yMDE5LzA5LzE5L25xeHM5MS5wbmc?x-oss-process=image/format,png)](https://imgchr.com/i/nqxs91)
其中HEAD代表当前的代码，>>>>代表其他版本冲突的代码，我们打开冲突的文件，将代码冲突进行修改之后，将HEAD，>>>等标志删除之后重新进行提交即可。

[![nqxhAH.png](https://imgconvert.csdnimg.cn/aHR0cHM6Ly9zMi5heDF4LmNvbS8yMDE5LzA5LzE5L25xeGhBSC5wbmc?x-oss-process=image/format,png)](https://imgchr.com/i/nqxhAH)
提交完成后重新进行合并

[![nqx54A.png](https://imgconvert.csdnimg.cn/aHR0cHM6Ly9zMi5heDF4LmNvbS8yMDE5LzA5LzE5L25xeDU0QS5wbmc?x-oss-process=image/format,png)](https://imgchr.com/i/nqx54A)
**远程推送出现冲突**

当我们的本地的分支推送到远程的分支的时候，可能远程上的分支已经进行了修改，那么我们将会push失败，解决方案是：
1.从远程的分支上拉最新的分支 git pull
2.在拉取完成后，会自动提示我们冲突的文件，我们按照相同的方式进行修改后重新提交推送即可。

**3、分支合并的模式**
有两种常用的合并分支的方式

1、快速合并方式

如果待合并的分支在当前分支的下游，也就是说没有分叉时，会发生快速合并，git会提示我们此次合并是快速合并。

[![nL3X4J.png](https://imgconvert.csdnimg.cn/aHR0cHM6Ly9zMi5heDF4LmNvbS8yMDE5LzA5LzE5L25MM1g0Si5wbmc?x-oss-process=image/format,png)](https://imgchr.com/i/nL3X4J)
这种方法相当于直接把master分支移动到test分支所在的地方，并移动HEAD指针。

2、普通合并方式

如果我们不想要快速合并，那么我们可以强制指定为非快速合并，只需加上--no-ff参数，普通合并实质上是在merge时生成一个新的commit，这样，从分支历史上就可以看出分支信息。所以需要我们添加commit描述

[![nL8A4H.png](https://imgconvert.csdnimg.cn/aHR0cHM6Ly9zMi5heDF4LmNvbS8yMDE5LzA5LzE5L25MOEE0SC5wbmc?x-oss-process=image/format,png)](https://imgchr.com/i/nL8A4H)
3、两种合并方式对比

下图中第二、三次提交是快速合并，都是基于dev分支提交，但在查看log信息的时候，看不出来是合并操作，紧接着我们在dev分支继续修改，然后使用普通合并模式进行修改，这次可以发现分支图上显示我们的操作流程，在dev分支进行修改，在master分支进行合并。推荐在合并的时候使用普通合并。


[![nL8egI.png](https://imgconvert.csdnimg.cn/aHR0cHM6Ly9zMi5heDF4LmNvbS8yMDE5LzA5LzE5L25MOGVnSS5wbmc?x-oss-process=image/format,png)](https://imgchr.com/i/nL8egI)
**4、分支管理策略**
实际开发时，应该按照以下几个原则进行开发

1、master分支应该是非常稳定的，也就是仅用来发布新版本，平时不在上面干活

2、干活都在dev分支，也就是说Dev分支是不稳定的，到某个时候，比如发布版本的时候，将dev分支合并到master上，在master分支发布版本

3、开发者可以在dev分支上开发，每个人都有自己的分支，最后往dev分支合并就可以了

4、合并分支的时候，加上--no-ff参数就可以用普通模式合并，合并后的历史有分支，可以看出曾经做过合并，而fast forward则看不出来

## 标签管理

发布一个版本时，我们通常先在版本库中打一个标签（tag），这样，就唯一确定了打标签时刻的版本。将来无论什么时候，取某个标签的版本，就是把那个打标签的时刻的历史版本取出来。所以，标签也是版本库的一个快照。我们在发布一个版本的时候，会对应有一个commit， 但是这个commit可能是比较难记的，所以我们选择一个好记忆的方式tag，例如v1.0 与某个commit绑定。

在Git中打标签非常简单，首先，切换到需要打标签的分支上：
然后，敲命令git tag 就可以打一个新标签：

    git tag v1.0

可以用命令git tag查看所有标签：

    $ git tag
    v1.0

默认标签是打在最新提交的commit上的。有时候，如果忘了打标签，比如，现在已经是周五了，但应该在周一打的标签没有打，怎么办？方法是找到历史提交的commit id，然后打上就可以了：

    git tag v0.9 f52c633

协同开发实例
项目负责人在远程仓库新建了仓库，克隆到本地，新建dev分支并推送到远程，那么远程仓库也就有了dev分支。

在克隆结束仓库之后，最好在本地这个仓库使用本地 set 的方法设置 gitlab 用户名和email(如果本机没有GitHub的环境可以忽略)

    git config —local user.name ‘XXX’ git config —local user.email ‘xxx@dist.com.cn

设置完之后可以使用`git config --list` 查看

设置完成之后将本地文件推到远程dev

其他开发人员从远程仓库克隆仓库到本地，在本地新建dev分支并直接切换到dev分支，然后将服务器上的dev版本使用`git pull` 命令拉下来，这样本地就具有了一个dev的开发环境，之后就在dev上进行开发和push（同样进行一下local的配置）

本地dev开发完成之后，push到远程dev，如果和远程有冲突，会出现下图的情况

这时候我们需要将远程的dev分支上的文件拉下来，git会提示我们什么地方出了问题，**git pull相当于自动的 fetch 和 merge 操作，会试图自动将远程库合并入本地库，在有冲突时再要求手动合并，git fetch 只是将远程的文件拉下来，不会与本地的分支进行合并，而pull的操作时将远程的代码拉取下来并与本地的分支进行合并。**

git提示我们 这是dev分支.txt 有冲突，那么我们就针对这个文件进行修改，其中HEAD代表当前的代码，>>>>代表其他版本冲突的代码，我们打开冲突的文件，将代码冲突进行修改之后，将HEAD，>>>等标志删除之后重新进行提交即可。

修改完冲突之后进行add和commit，最后push到云端没有问题

## 常见问题的解决

 - git推送到远程错误的文件怎么处理

先回退到前一个版本：

    git reset --hard HEAD^

然后强制推送当前这个版本到云端

    git push origin HEAD --force

 - 从远程pull数据时候存在冲突怎么解决？

出现这种问题的原因是远程上已经存在的代码和我们本地的代码，因为代码冲突并不止是存在于向远程推送时会发生。解决方式时根据提示，找到错误的文件，进行修改即可。要注意pull的意义就是为了在本地进行代码合并，来避免推送到远程的时候出现代码冲突。