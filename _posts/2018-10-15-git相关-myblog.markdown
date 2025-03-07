---
layout:     post
title:      "git相关"
date:       2018-09-23 23:06:00
author:     "Qz"
header-img: "img/post-bg-2015.jpg"
catalog: true
tags:
    - Git
---

> “Yeah It's on. ”


## 正文
[网页链接](https://www.liaoxuefeng.com/wiki/0013739516305929606dd18361248578c67b8067c8c017b000)


### 本地分支与远程分支

github上已经有master分支 和dev分支

在本地

* git checkout -b dev 新建并切换到本地dev分支
* git pull origin dev 本地分支与远程分支相关联

----------



本地新建分支并推送到远程


* git checkout -b test
* git push origin test   这样远程仓库中也就创建了一个test分支



### 拉取远程分支并创建本地分支
git fetch origin 远程分支名x:本地分支名x

使用该方式会在本地新建分支x，但是不会自动切换到该本地分支x，需要手动checkout。


**重要：采用此种方法建立的本地分支不会和远程分支建立映射关系。**


### 将远程git仓库里的指定分支拉取到本地（本地不存在的分支）

当我想从远程仓库里拉取一条本地不存在的分支时：

```javascript
git checkout -b 本地分支名 origin/远程分支名
```

这个将会自动创建一个新的本地分支，并与指定的远程分支关联起来。







### 丢弃所有本地修改的方法

**重要**

**重要**

**重要**

```
git checkout .
```


### 退到上一次commit状态
退到上一次commit的状态
```javascript
git reset --hard HEAD^
```



### git log



#### 查看commit hash值

查看commit日志 执行下面命令
```
git log
```





#### 打印当前分支的所有提交信息

```bash
git log --graph --all --pretty=format:'%Cred%h%Creset -%C(yellow)%d%Creset %s %Cgreen(%cr) %C(bold blue)<%an>%Creset' --abbrev-commit --date=relative
```

效果：

```bash
* eab47ad - (HEAD -> feature-a) 666 (2 minutes ago) <渣臻>
* e8f73f5 - 555 (2 minutes ago) <渣臻>
* 8ff64a8 - 444 (2 minutes ago) <渣臻>
* 2e694f9 - (origin/feature-a) Update README.md (4 minutes ago) <渣臻>
* 7e97af0 - feat:featurea随便 (4 minutes ago) <渣臻>
* 3a7b123 - feat: feature-a (4 minutes ago) <渣臻>
* 7d75693 - (origin/main, origin/feature-b, origin/feature, origin/develop, origin/HEAD, main, feature-b, feature, develop) Update README.md (22 minutes ago) <
渣臻>
* 1505699 - Initial commit (2 weeks ago) <渣臻>
```









### 回滚、取消之前的提交


```
git reset --hard commit_id    

//退到/进到 指定commit的sha码 commit_id:239afed0857cc2e77c17c01014077808619af64d
```

再用“git log”查看版本信息，此时本地的HEAD已经指向之前的版本：



**使用“git push -f”提交更改**


此时如果用“git push”会报错，因为我们本地库HEAD指向的版本比远程库的要旧


所以我们要用“git push -f”强制推上去，就可以了





### reset和revert的区别

[https://blog.csdn.net/yxlshk/article/details/79944535](https://blog.csdn.net/yxlshk/article/details/79944535)


在利用github实现多人合作程序开发的过程中，我们有时会出现错误提交的情况，此时我们希望能撤销提交操作，让程序回到提交前的样子，两种解决方法：回退（reset）、反做（revert）。


>使用git的每次提交，Git都会自动把它们串成一条时间线，这条时间线就是一个分支。如果没有新建分支，那么只有一条时间线，即只有一个分支，在Git里，这个分支叫主分支，即master分支。有一个HEAD指针指向当前分支（只有一个分支的情况下会指向master，而master是指向最新提交）。

-----

git reset

**git reset的作用是修改HEAD的位置，即将HEAD指向的位置改变为之前存在的某个版本**



**适用场景： 如果想恢复到之前某个提交的版本，且那个版本之后提交的版本我们都不要了，就可以用这种方法。**

------

git revert

**原理： git revert是用于“反做”某一个版本，以达到撤销该版本的修改的目的。比如，我们commit了三个版本（版本一、版本二、 版本三），突然发现版本二不行（如：有bug），想要撤销版本二，但又不想影响撤销版本三的提交，就可以用 git revert 命令来反做版本二，生成新的版本四，这个版本四里会保留版本三的东西，但撤销了版本二的东西。**


![enter description here][1]


**适用场景： 如果我们想撤销之前的某一版本，但是又想保留该目标版本后面的版本，记录下这整个版本变动流程，就可以用这种方法。**





### git status

git status 命令用于查看在你上次提交之后是否有对文件进行再次修改。





### git diff

[https://www.runoob.com/git/git-diff.html](https://www.runoob.com/git/git-diff.html)

git diff 命令比较文件的不同，即比较文件在暂存区和工作区的差异。

git diff 命令显示已写入暂存区和已经被修改但尚未写入暂存区文件对区别。





### git clean

git clean 从你的工作目录中删除所有没有 tracked，没有被管理过的文件。

> **参数说明：**
>
> **n** ：显示将要被删除的文件
>
> d ：删除未被添加到 git 路径中的文件（将 .gitignore 文件标记的文件全部删除）
>
> f ：强制运行
>
> x ：删除没有被 track 的文件

```basic
git clean -xfd
```





### git show

显示 commit 的简短信息，并按照指定的格式输出

```
git show -s --format='%h %s'
```

- **`git show`**: 这个命令通常用于显示一个或多个 Git 对象的详细信息，比如 commit、tag 或 blob。 如果不指定任何 commit，默认显示 HEAD 所指向的 commit 信息。
- **`-s` 或 `--no-patch`**: 这个选项告诉 `git show` 不要显示 commit 的补丁 (diff)。 也就是说，它会省略 commit 引入的具体更改内容，只显示 commit 的元数据。
- **`--format='%h %s'`**: 这个选项允许你自定义 `git show` 的输出格式。 `%h` 和 `%s` 是占位符，分别代表以下含义：
  - `%h`: commit 的 abbreviated hash (简短的哈希值)。
  - `%s`: commit 的 subject (提交信息的标题行)。



### git branch

查看远程分支

* git branch -a，就是查看远程的所有分支列表
* git branch是查看本地的git分支

查看本地分支与远程分支的映射关系

[https://blog.csdn.net/tterminator/article/details/78108550](https://blog.csdn.net/tterminator/article/details/78108550)
使用以下命令（注意是双v）：

git branch -vv

```
$ git branch -vv
  2.1.0  2ca33c8c 提交
* master aeb60f00 [origin/master] 提交
```

可以看到分支2.1.0没有和远程分支建立任何映射，此时若执行如下拉取命令则不成功（因为git此时不知道拉取哪个远程分支和本地分支合并）

----

撤销本地分支与远程分支的映射关系

```
git branch --unset-upstream
```

删除本地分支 

```
删除本地分支：git branch -d 分支名（remotes/origin/分支名）

强制删本地：git branch -D 分支名
```

删除远程分支 

```
git push origin --delete <BranchName>
```

查看当前分支

```
git branch --show-current
```

建立当前分支与远程分支的映射关系

```
git branch -u origin/addFile
```

或者使用命令：

```
git branch --set-upstream-to origin/addFile
```

**此时git pull再次拉取，成功**

----



### git merge

[https://www.jianshu.com/p/6960811ac89c](https://www.jianshu.com/p/6960811ac89c)



![img](https://upload-images.jianshu.io/upload_images/5362354-57a7cb4a4dff5450.png?imageMogr2/auto-orient/strip|imageView2/2/w/294/format/webp)





我们知道A同学开发的dev分支是基于C2提交点切出来的，而这个时候master分支已经被更新了

如果A同学选择用git merge的方式进行合并dev到master分支，那么git会这么做

1. 找出dev分支和master分支的最近共同祖先commit点，即C2
2. 将dev最新一次commit（C5）和master最新一次commit（C6）合并后生成一个新的commit（C7），有冲突的话需要解决冲突
3. 将以上两个分支dev和master上的所有提交点（从C2以后的）按照**提交时间的先后顺序**进行依次放到master分支上



![img](https://upload-images.jianshu.io/upload_images/5362354-1acc938c5291f929.png?imageMogr2/auto-orient/strip|imageView2/2/w/289/format/webp)











### git stash

>**注意:这是个必须掌握的操作**

[https://www.cnblogs.com/tocy/p/git-stash-reference.html](https://www.cnblogs.com/tocy/p/git-stash-reference.html)


现在你想切换分支，但是你还不想提交你正在进行中的工作；所以你储藏这些变更。为了往堆栈推送一个新的储藏，只要运行 git stash：

```
$ git stash
Saved working directory and index state \
  "WIP on master: 049d078 added the index file"
HEAD is now at 049d078 added the index file
(To restore them type "git stash apply")
```

你的工作目录就干净了：

```
$ git status
# On branch master
nothing to commit, working directory clean
```


这时，你可以方便地切换到其他分支工作；你的变更都保存在栈上。要查看现有的储藏，你可以使用 git stash list：

```
$ git stash list
stash@{0}: WIP on master: 049d078 added the index file
stash@{1}: WIP on master: c264051 Revert "added file_size"
stash@{2}: WIP on master: 21d80a5 added number to log
```



在这个案例中，之前已经进行了两次储藏，所以你可以访问到三个不同的储藏。你可以重新应用你刚刚实施的储藏，所采用的命令就是之前在原始的 stash 命令的帮助输出里提示的：git stash apply。如果你想应用更早的储藏，你可以通过名字指定它，像这样：git stash apply stash@{2}。如果你不指明，Git 默认使用最近的储藏并尝试应用它：


```
$ git stash apply
# On branch master
# Changes not staged for commit:
#   (use "git add <file>..." to update what will be committed)
#
#      modified:   index.html
#      modified:   lib/simplegit.rb
```



要移除它，你可以运行 git stash drop，加上你希望移除的储藏的名字：
```
$ git stash list
stash@{0}: WIP on master: 049d078 added the index file
stash@{1}: WIP on master: c264051 Revert "added file_size"
stash@{2}: WIP on master: 21d80a5 added number to log
$ git stash drop stash@{0}
Dropped stash@{0} (364e91f3f268f0900bc3ee613f9f733e82aaed43)
```



-------------------

git stash通常用于：
1. 修复一个临时bug，需要清空工作目录
2. 使用git pull文件时和本地文件冲突


>[使用git pull文件时和本地文件冲突](http://www.01happy.com/git-resolve-conflicts/)






### git fetch & pull

* git fetch是将远程主机的最新内容拉到本地，用户在检查了以后决定是否合并到工作本机分支中。
* 而git pull 则是将远程主机的最新内容拉下来后直接合并，即：git pull = git fetch + git merge，这样可能会产生冲突，需要手动解决。















### git submodule

[https://www.jianshu.com/p/0107698498af](https://www.jianshu.com/p/0107698498af)

Git对于Submodule有特殊的处理方式，在一个主项目中引入了Submodule其实Git做了3件事情：

* 记录引用的仓库
* 记录主项目中Submodules的目录位置
* 记录引用Submodule的commit id



----------------


```javascript
git submodule init  # 初始化本地.gitmodules文件
git submodule update  # 同步远端submodule源码
```



>git submodule update 这条命令非常有用









---------------

**更新子模块**

**更新子模块的时候要注意子模块的分支默认不是master。**


方法一，先pull父项目，然后执行git submodule update，注意moduleA的分支始终不是master。

```
cd project2
git pull
git submodule update
cd ..
```


方法二，先进入子模块，然后切换到需要的分支，这里是master分支，然后对子模块pull，这种方法会改变子模块的分支。

```
cd project3/moduleA
git checkout master
cd ..
git submodule foreach git pull
cd ..
```

---


**删除子模块**

删除子模块较复杂，步骤如下：

* rm -rf 子模块目录 删除子模块目录及源码
* vi .gitmodules 删除项目目录下.gitmodules文件中子模块相关条目
* vi .git/config 删除配置项中子模块相关条目
* rm .git/module/* 删除模块下的子模块目录，每个子模块对应一个目录，注意只删除对应的子模块目录即可


执行完成后，再执行添加子模块命令即可，如果仍然报错，执行如下：

git rm --cached 子模块名称

完成删除。




### branch diverged

git分支分叉(branch diverged)的问题


```
$ git status
# On branch feature/worker-interface
# Your branch and 'origin/feature/worker-interface' have diverged,
# and have 1 and 4 different commit(s) each, respectively.
```





### squash

[https://www.cnblogs.com/hzglearn/p/13042282.html](https://www.cnblogs.com/hzglearn/p/13042282.html)

[https://www.jianshu.com/p/a122aa26dd2e](https://www.jianshu.com/p/a122aa26dd2e)

分支上过多commit的话，比如一个功能点我们可能分了几个提交，如果合并到主分支的话，提交记录会显得繁琐，最终我们重点关注的应该是这个功能点的提交，而不是开发者中间做了多少开发，这时候就要用到了git squash，两种情况



例子：

我想将本地开发分支的内容合并到master主线分支上，并且期望将一个功能的所有提交压缩成一个commit

1. 先checkout master分支并做本地分支和远程仓库同步
    git checkout master
    git pull  （一定要做同步，若本地代码和远端仓库不同步的话，就会出现很多远端的修改进入我们的squash时的commits当中）
2. 切换到开发分支 dev ， 并执行commits合并操作
    git checkout dev
    git rebase -i master



```bash
 # 执行上面的语句后，会进入以下编辑界面
 pick xxx: commit1 add : add xxx 
 pick xxx: commit2 fix: update xxxx
 pick xxx: commit3 fix: delete xxx
 pick xxx: commit4 modify : update xxxx 
# 进入编辑模式，将除第一行的pick外，其余的pick都改成 squash ，退出编辑模式，保存即可
 pick xxx: commit1 add : add xxx 
 squash xxx: commit2 fix: update xxxx
 squash xxx: commit3 fix: delete xxx
 squash xxx: commit4 modify : update xxxx 
```



> git 在merge的时候提供了这种squash merge的操作方式
>
> 在bitbucket中我们可以先squash在进行merge



### rebase

[git rebase 与 git merge](https://cloud.tencent.com/developer/article/2139812)

[一文搞懂 git rebase](https://juejin.cn/post/7038093620628422669)

[https://www.jianshu.com/p/4a8f4af4e803](https://www.jianshu.com/p/4a8f4af4e803)

[https://git-scm.com/book/zh/v2/Git-%E5%88%86%E6%94%AF-%E5%8F%98%E5%9F%BA](https://git-scm.com/book/zh/v2/Git-%E5%88%86%E6%94%AF-%E5%8F%98%E5%9F%BA)

为什么需要Rebase？

* 删除多次无用的 `commit` 

* 利于代码 `review`

* 你在查看一个经过rebase变基的分支的历史记录时会发现，尽管实际的开发工作是并行的， 但它们看上去就像是串行的一样，提交历史是一条直线没有分叉

  -----

**git merge和git rebase的优缺点**

- git merge
  - 优点：不会破坏原分支的提交记录。
  - 缺点：会产生额外的提交记录，并进行两条分支线的合并。
- git rebase
  - 优点：无需新增提交记录到目标分支，reabse后可以直接将对象分支的提交历史加到目标分支上，形成线性提交历史记录，更加直观。
  - 缺点：不能在一个共享分支上进行reabse操作，会带来分支安全问题。

**git merge和git rebase的正确使用**

- 合代码到公共分支的时候使用**git merge**，书写正确规范的**merge commits**留下记录。
- 合代码到个人分支的时候使用**git rebase**，可以不污染分支的历史提交记录，形成简介的线性记录。





[http://jartto.wang/2018/12/11/git-rebase/](http://jartto.wang/2018/12/11/git-rebase/)

rebase在git中是一个非常有魅力的命令，使用得当会极大提高自己的工作效率；相反，如果乱用，会给团队中其他人带来麻烦。它的作用简要概括为：可以对某一段线性提交历史进行编辑、删除、复制、粘贴；因此，合理使用rebase命令可以使我们的提交历史干净、简洁！



**前提：不要通过rebase对任何已经提交到公共仓库中的commit进行修改（你自己一个人玩的分支除外）**

> git-rebase 存在的价值是：对一个分支做「变基」操作。

1.当我们在一个过时的分支上面开发的时候，执行 `rebase` 以此同步 `master` 分支最新变动；
2.假如我们要启动一个放置了很久的并行工作，现在有时间来继续这件事情，很显然这个分支已经落后了。这时候需要在最新的基准上面开始工作，所以 `rebase` 是最合适的选择。

`git rebase` 是一个危险命令，因为它改变了历史，我们应该谨慎使用**（最好只在自己一人上分支使用）**。

因为：

变基操作的实质是丢弃一些现有的提交，然后相应地新建一些内容一样但实际上不同的提交。 如果你已经将提交推送至某个仓库，而其他人也已经从该仓库拉取提交并进行了后续工作，此时，**如果你用 `git rebase` 命令重新整理了提交并再次推送，你的同伴因此将不得不再次将他们手头的工作与你的提交进行整合，如果接下来你还要拉取并整合他们修改过的提交，事情就会变得一团糟。**

git rebase操作实际上是将当前执行rebase分支的所有基于原分支提交点之后的commit打散成一个一个的patch，并重新生成一个新的commit hash值，再次基于原分支目前最新的commit点上进行提交，并不根据两个分支上实际的每次提交的时间点排序，rebase完成后，切到基分支进行合并另一个分支时也不会生成一个新的commit点，可以保持整个分支树的完美线性



#### 合并到featature

假设有一条a分支和b分支，我们要将a分支东西合过去b分支

```bash
# 在 a 分支上
git rebase b
```







#### 合并多个commit

**合并多个commit为一个完整commit**

当我们在本地仓库中提交了多次，在我们把本地提交push到公共仓库中之前，为了让提交记录更简洁明了，我们希望把如下分支B、C、D三个提交记录合并为一个完整的提交，然后再push到公共仓库。

这里我们使用命令:


```
  git rebase -i  [startpoint]  [endpoint]
```

其中-i的意思是--interactive，即弹出交互式的界面让用户编辑完成合并操作，[startpoint] [endpoint]则指定了一个编辑区间，如果不指定[endpoint]，则该区间的终点默认是当前分支HEAD所指向的commit(注：该区间指定的是一个前开后闭的区间)。

在查看到了log日志后，我们运行以下命令：

```
git rebase -i 36224db
```
或:
```
git rebase -i HEAD~3 
```

---------



#### **将某一段commit粘贴到另一个分支上**


当我们项目中存在多个分支，有时候我们需要将某一个分支中的一段提交同时应用到其他分支中


我们使用命令的形式为:

```
    git rebase   [startpoint]   [endpoint]  --onto  [branchName]
```

其中，[startpoint]  [endpoint]仍然和上一个命令一样指定了一个编辑区间(前开后闭)，--onto的意思是要将该指定的提交复制到哪个分支上。


所以，在找到C(90bc0045b)和E(5de0da9f2)的提交id后，我们运行以下命令：

```
    git  rebase   90bc0045b^   5de0da9f2   --onto master
```

所以，虽然此时HEAD所指向的内容正是我们所需要的，但是master分支是没有任何变化的，git只是将C~E部分的提交内容复制一份粘贴到了master所指向的提交后面，我们需要做的就是将master所指向的提交id设置为当前HEAD所指向的提交id就可以了，即:

```
      git checkout master
      git reset --hard  0c72e64
```









#### 遇到的问题



#####  up to date

分支上执行rebase操作时候提示：Current branch xxx is up to date.

out of date  过时了

up to date  最新

**出现这个是因为命令无效，也就是指定的commit不对**



##### 退出（master|REBASE-i)

使用git rebase --abort 代码回退

 回到git rebase之前的状态





##### rebase continue 提示报错

[https://coldfunction.com/geyite/p/gk](https://coldfunction.com/geyite/p/gk)

运行`git rebase --continue`，但是又会报错：

```bash
Applying: Fix xxx issue
No changes - did you forget to use 'git add'?
If there is nothing left to stage, chances are that something else
already introduced the same changes; you might want to skip this patch.

Resolve all conflicts manually, mark them as resolved with
"git add/rm <conflicted_files>", then run "git rebase --continue".
You can instead skip this commit: run "git rebase --skip".
To abort and get back to the state before "git rebase", run "git rebase --abort".
```

这种情况是，可能这个Patch已经被合并过了，所以没什么需要提交的了，可以省略掉这个commit，运行：

```bash
git rebase --skip
```











### cherry-pick

[ https://www.jianshu.com/p/6e3c189c4995 ]( https://www.jianshu.com/p/6e3c189c4995 )



cherry-pick是Git里对commit操作很好的一个指令，比如想把test分支中的其中多个commit merge到master，那么你需要挑你所需要的commit合到master，这时候就用cherry-pick来捡。



```ruby
     |            
     |        C3
     |         |
    C1        C2
     |         |  test
     |        /
     |     /
    master
```





想将test分支中的C2 commit合并到master分支，丢弃C3的修改。
直接merge会把C3也合并进去，这时用git cherry-pick可以解决问题

  



- 先用git log查看，C2 commit的id，复制下来
- git checkout 到master分支下
- git cherry-pick <C2_id>



如果cherry-pick过程没有出现冲突的话，那就是完成了合并，如下图所示




```ruby
    |
   C4
     |  \         
     |    \   C3
     |      \ |
    C1        C2
     |        |  test
     |      /
     |    /
   master
```



如果出现冲突，

  

- 先解决冲突
- git add 将解决了冲突的文件添加到暂存区
- git cherry-pick --continue就行





### --porcelain

[https://stackoverflow.com/questions/6976473/what-does-the-term-porcelain-mean-in-git](https://stackoverflow.com/questions/6976473/what-does-the-term-porcelain-mean-in-git)

More importantly, the term "porcelain" applies to [high-level commands](https://schacon.github.io/git/git.html#_high_level_commands_porcelain), with output:

- **meant to be readable by human**
- **not meant to be parsed**
- **susceptible to changes/evolutions**

That is key: if you script, you should use if possible [*plumbing* commands](https://schacon.github.io/git/git.html#_low_level_commands_plumbing), with stable outputs. Not porcelain commands.

However, **you can use the output of a porcelain command which has a `--porcelain` option in script** (see below), like:

```
git status --porcelain
git push --porcelain
git blame --porcelain
```

> Although git includes its own **porcelain layer**, its low-level commands are sufficient to support development of alternative porcelains.
>
> 尽管git包含了自己的瓷器层，但它的低级命令足以支持替代瓷器(porcelain)的开发。
>
> The interface (input, output, set of options and the semantics) to these **low-level commands are meant to be a lot more stable** than Porcelain level commands, because **these commands are primarily for scripted use**.
> The interface to Porcelain commands on the other hand are subject to change in order to improve the end user experience.





### Pull Request

[https://www.ruanyifeng.com/blog/2017/07/pull_request.html](https://www.ruanyifeng.com/blog/2017/07/pull_request.html)

> "Pull Request 是一种通知机制。你修改了他人的代码，将你的修改通知原来的作者，希望他合并你的修改，这就是 Pull Request。"





在GitHub上，“Squash and merge”和“Rebase and merge”是两种不同的合并策略，它们在合并pull requests时有不同的功能和结果。以下是它们之间的主要区别：

- **Squash and Merge**：合并为一个提交，清理历史。
- **Rebase and Merge**：保留每个提交的独立性，形成线性历史。



#### Squash and Merge

- **功能**：将所有提交压缩为一个提交，然后将其合并到目标分支。
- **结果**：目标分支将只包含一个包含所有改动的新提交历史，提交历史更加整洁。
- **适用场景**：当你希望保持主分支的干净和简洁时，适合使用这种方式。

#### Rebase and Merge

- **功能**：将源分支的修改应用到目标分支的最新提交之上，基本上是在目标分支的前面重新应用每个提交。
- **结果**：这将保留提交历史中每个提交的独立性，并且不会生成额外的合并提交。如果有冲突，需要解决每个提交的冲突。
- **适用场景**：适合于需要保留完整提交历史的情况，特别是在team协作中，追踪每个成员的工作细节。





## Git 原理

https://jingsam.github.io/2018/06/03/git-objects.html

**从根本上来讲，Git是一个内容寻址的文件系统，其次才是一个版本控制系统**

Git对象包含三种：数据对象、树对象、提交对象。

在Git中，数据对象相当于文件内容，树对象相当于文件目录树，提交对象则是对文件系统的快照。





## Git Flow

[https://juejin.cn/post/6844903542084517896](https://juejin.cn/post/6844903542084517896)

**主要分支**

> master: 永远处在**`即将发布(production-ready)状态`**；
>
> develop: **`最新的开发状态`**；

**辅助分支**

> feature: **`开发新功能的分支, 基于 develop`**, 完成后 **`merge 回 develop`**；
>
> release: **`准备要发布版本的分支, 用来修复 bug. 基于 develop`**, 完成后 **`merge 回 develop 和 master`**；
>
> hotfix: **`修复 master 上的问题`**, 等不及 release 版本就必须马上上线. **`基于 master`**, 完成后 **`merge 回 master 和 develop`**；



### init

git flow init 在初始化git Flow模型项目时会有一些交互选项来设置一些预设分支与命名规则。

```haskell
$ git flow init
Initialized empty Git repository in /Users/abc/desktop/git-test/.git/
Branch name for production releases: [master] 
Branch name for "next release" development: [develop] 

How to name your supporting branch prefixes?
Feature branches? [feature/] 
Release branches? [release/] 
Hotfix branches? [hotfix/]

```







## node 脚本

```tsx
/**
 * 不满足条件，抛出错误
 * @param {*} cond
 * @param {string} message
 * @returns {asserts cond}
 */
function invariant(cond, message) {
  if (!cond) throw new Error(message);
}
```





### 检查工作区是否提交

```tsx
function ensureCleanWorkingDirectory() {
  let status = execSync(`git status --porcelain`).toString().trim();
  let lines = status.split("\n");
  invariant(
    lines.every(line => line === "" || line.startsWith("?")),
    "Working directory is not clean. Please commit or stash your changes."
  );
}
```



如果存在未commit的文件

```basic
➜  front-end-demo git:(master) ✗ git status --porcelain
 M src/pages/index.ts
 M src/router/index.tsx
?? src/pages/other/
```

如果所有文件都commit

```basic
➜  front-end-demo git:(master) git status --porcelain                       
➜  front-end-demo git:(master) 
```

执行结果为空







# Github



## workflow





###  matrix strategy

[https://docs.github.com/en/actions/writing-workflows/choosing-what-your-workflow-does/running-variations-of-jobs-in-a-workflow](https://docs.github.com/en/actions/writing-workflows/choosing-what-your-workflow-does/running-variations-of-jobs-in-a-workflow)

矩阵策略允许您在单个作业定义中使用变量来自动创建基于变量组合的多个作业运行。

例如，您可以使用矩阵策略在一种语言的多个版本或多个操作系统上测试代码。

```yaml
jobs:
  test:
    runs-on: ${{ matrix.os }}

    strategy:
      matrix:
        node: [lts/*]
        os: [ubuntu-latest, windows-latest, macos-latest]
      fail-fast: false

    steps:
      - uses: actions/checkout@v4
      - name: Set node ${{ matrix.node }}
        uses: actions/setup-node@v4
        with:
          node-version: ${{ matrix.node }}

      - name: Setup
        run: npm i -g @antfu/ni

      - name: Install
        run: nci

      - name: Build
        run: nr build

      - name: Test
        run: nr test
```





### secrets

* Environment secrets
* Repository secrets

* Orgnization secrets

如果创建了一个production的环境 要使用里面的secrets

```yaml
runs-on: ubuntu-latest
    environment: 
      name: production
    steps:
```



### job依赖

[示例：要求成功的依赖项作业](https://docs.github.com/zh/actions/writing-workflows/choosing-what-your-workflow-does/using-jobs-in-a-workflow#示例要求成功的依赖项作业)

```yaml
jobs:
  job1:
  job2:
    needs: job1
  job3:
    needs: [job1, job2]
```

在此示例中，`job1` 必须在 `job2` 开始之前成功完成，并且 `job3` 等待 `job1` 和 `job2` 完成。

[示例：不要求成功的依赖项作业](https://docs.github.com/zh/actions/writing-workflows/choosing-what-your-workflow-does/using-jobs-in-a-workflow#示例不要求成功的依赖项作业)

```yaml
jobs:
  job1:
  job2:
    needs: job1
  job3:
    if: ${{ always() }}
    needs: [job1, job2]
```

在此示例中，`job3` 使用 `always()` 条件表达式，确保始终在 `job1` 和 `job2` 完成（无论是否成功）后运行。 



# 补充

[Git基本操作](http://youngxhui.github.io/2016/08/13/Git%E5%9F%BA%E6%9C%AC%E6%93%8D%E4%BD%9C/)

[github小窍门](http://youngxhui.github.io/2016/08/28/GitHub-for-Windows%E4%BD%BF%E7%94%A8%E6%95%99%E7%A8%8B%EF%BC%88%E5%9B%9B%EF%BC%89/)

### 修改commit信息

```js
git commit --amend -m "重新提交注释"
```



![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/de72f49ffe144aef9b0d2786ec7b8e53~tplv-k3u1fbpfcp-zoom-1.image?imageslim)









### 解决.gitignore文件不起作用

原因是新建的文件在git中会有缓存，如果某些文件已经被纳入了版本管理中，就算是在.gitignore中已经声明了忽略路径也是不起作用的，这时候我们就应该先把本地缓存删除，然后再进行git的push，这样就不会出现忽略的文件了

```bash
git rm -r --cached .
git add .
git commit -m 'update .gitignore'
```







### 解决提交到GitHub不显示提交记录

[https://www.jianshu.com/p/3bc863b48f69](https://www.jianshu.com/p/3bc863b48f69)

**这个问题出现的原因是提交文件的电脑上的git的email地址不是github上的email地址。**


那么如何一台电脑两个邮箱分别用于GitHub和GitLab


我的做法是 git config --global user.email "youremail@email.com"   这里使用的是公司提供的邮箱 用于公司项目 gitlab上面提交

在自己的github项目中


执行

```bash
 $ git config user.email "qz519189636@qq.com"
```


手动将邮箱设置为和github上的邮箱一致。



-----------------


另外，如何补充上那些丢失的Contributions呢


[https://help.github.com/en/articles/changing-author-info](https://help.github.com/en/articles/changing-author-info)




### 解决github page build failed问题

上周在github部署blog突然失败

可以在commit记录中查看

[https://github.com/QinZhen001/QinZhen001.github.io/commits/master](https://github.com/QinZhen001/QinZhen001.github.io/commits/master)

因为我只改了几个md文件，所以找了很久还是未发现问题

最后通过邮件咨询了github找到了解决方案

```
// 问题

The page build failed for the `master` branch with the following error:

Page build failed. For more information, see https://help.github.com/en/articles/troubleshooting-jekyll-build-errors-for-github-pages-sites#troubleshooting-build-errors.

For information on troubleshooting Jekyll see:

  https://help.github.com/articles/troubleshooting-jekyll-builds

If you have any questions you can contact us by replying to this email.
```




```
// 反馈问题

Hi:     I don’t know why page build failure,I just modified some MD files as usual.     
Can you tell me the reason,Thank you for your reply.
```



```
// 定位问题

Thanks for writing in! I'd be happy to help clarify the issue you're experiencing. Here's the full error we're seeing:

  Conversion error: Jekyll::Converters::Markdown encountered an error while converting '_posts/2017-11-22-HTML5拖动-myblog.markdown':
                    Rouge::Guesser::Ambiguous
             Fatal: Rouge::Guesser::Ambiguous
                    Ambiguous guess: can't decide between ["html", "mason", "xml"]
We're currently having problems with 'Rouge'. A workaround for now is to use the backtick syntax with an explicit language when there is a code block:

https://help.github.com/en/articles/creating-and-highlighting-code-blocks#syntax-highlighting

Hope this helps!

```


问题所在


**can't decide between ["html", "mason", "xml"]**

```
 使用```高亮代码块
 但是没有标识是什么语言
 而且这个代码块包含很多东西 如 css html script
 所以导致解析
 进而部署失败
```

[https://help.github.com/en/articles/creating-and-highlighting-code-blocks#syntax-highlighting](https://help.github.com/en/articles/creating-and-highlighting-code-blocks#syntax-highlighting)





### 解决unable to update local ref


使用git pull拉取代码的时候，无法拉取最新代码，报"unable to update local ref"错误。



解决办法：

```
git gc --prune=now
git remote prune origin
git pull
```


或者

删除项目，重新git clone







## 解决检测文件名大小写修改

问题：本地仓库我已经将文件全部更改成为了大写驼峰，但是git远端仓库里面一直都是小写，git status也是显示没有变更操作。

```basic
// 执行命令
git config core.ignorecase false
```

通过这个命令把忽略大小写关掉





### merge 还是 rebase

[https://juejin.im/post/5dbe7a476fb9a0207f1035d0](https://juejin.im/post/5dbe7a476fb9a0207f1035d0)



![img](https://pic2.zhimg.com/80/v2-73db63a5abb3cac70f913155a854cf29_720w.jpg)





我通常用它来和主分支同步，比如一个新版本发布，主分支比我当前的功能分支超前，我使用rebase将当前分支和主分支“合并（变基）”。


merge还是rebase，有人提倡不要使用rebase，应该rebase改变了历史，有人提倡使用merge，保留完整的历史。

**我是这么做的，在私有的分支上，我始终使用rebase将主分支的更新合并到私有的分支上（后面还有很多使用rebase的操作，都是在私有的分支，这里的私有的分支，指的是只有自己使用的分支，一旦分享出去，或者有人基于你的分支开发，那就不再是私有），而在将自己的分支合并到其他分支（主分支或者待发布分支），则使用merge。**



**准则：不要对在你的仓库外有副本的分支执行变基。**

-----

**新功能开发**


新功能开发的时候从主分支新建新分支，所有该功能的开发工作都在这个分支上完成。如果主分支有新的发布，使用rebase同步主分支功能



**bug 修复及其发布**



bug修复大体上和新功能的开发类似，但是bug修复一般时间短，立马上线。 bug修复从主分支新建新分支，所有的bug修复工作都在这个分支上完成。如果主分支有新的发布，使用rebase同步主分支功能（这个步骤其实和新功能开发一样）















###  WARNING: REMOTE HOST  CHANGED 

出现了一个WARNING



```
[root@localhost backups]# scp root@172.xxx.xxx.xxx:/data/gitlabData/backups/1539717714_2018_10_17_9.4.3_gitlab_backup.tar .
@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
@    WARNING: REMOTE HOST IDENTIFICATION HAS CHANGED!     @
@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
IT IS POSSIBLE THAT SOMEONE IS DOING SOMETHING NASTY!
Someone could be eavesdropping on you right now (man-in-the-middle attack)!
It is also possible that a host key has just been changed.
The fingerprint for the ECDSA key sent by the remote host is
85:82:b1:58:20:21:a5:da:be:24:e8:14:9a:12:b2:d2.
Please contact your system administrator.
Add correct host key in /root/.ssh/known_hosts to get rid of this message.
Offending ECDSA key in /root/.ssh/known_hosts:5
ECDSA host key for 172.xxx.xxx.xxx has changed and you have requested strict checking.
Host key verification failed.


```



用OpenSSH的人都知ssh会把你每个你访问过计算机的公钥(public key)都记录在~/.ssh/known_hosts。当下次访问相同计算机时，OpenSSH会核对公钥。如果公钥不同，OpenSSH会发出警告，避免你受到DNS Hijack之类的攻击。

SSH对主机的public_key的检查等级是根据StrictHostKeyChecking变量来配置的。默认情况下，StrictHostKeyChecking=ask。



----



解决： 



```
ssh-keygen -R gitlab.xinghuolive.com
ssh -T  gitlab.xinghuolive.com
```







ssh-keygen -R [xxx.xxx.xxx.xxx](http://xxx.xxx.xxx.xxx/) (服务器ip地址)

> 目的是清除你当前机器里关于你的远程服务器的缓存和公钥信息，注意是大写的字母“R”。







###  error: some local refs could not be updated

 'git remote prune origin' to remove any old, conflicting branches

```bash
// 执行
git remote prune origin
```









### 利用github pages部署vue项目白屏

> 这里我将近被坑了两个小时左右，最好的使用master分支docs文件夹部署github pages，这样将打包后的dist文件夹改名成docs就行了



 [ https://juejin.im/post/5cb5d68e51882532525a097d ]( https://juejin.im/post/5cb5d68e51882532525a097d )



最终定位到问题是  **vue-router 不要开启 history 模式**



路径中的 **#** 比较丑，就开启了 vue-router 的 history 模式，去掉了 **#**。平时做项目也是默认开启 history 模式。折腾了半天发现，我这是部署到 github pages ，服务器没有配置，导致页面一直请求不到。



我哭了吗，记录到这里了











### [commitlint](https://github.com/conventional-changelog/commitlint)

[https://github.com/conventional-changelog/commitlint](https://github.com/conventional-changelog/commitlint)







[1]: https://s2.ax1x.com/2019/09/02/niK9fA.png