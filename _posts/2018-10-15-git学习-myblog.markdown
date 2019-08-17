---
layout:     post
title:      "Git操作学习"
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


### 查看远程分支

* git branch -a，就是查看远程的所有分支列表
* git branch是查看本地的git分支


### 查看本地分支与远程分支的映射关系
[https://blog.csdn.net/tterminator/article/details/78108550](https://blog.csdn.net/tterminator/article/details/78108550)
使用以下命令（注意是双v）：

git branch -vv

```
$ git branch -vv
  2.1.0  2ca33c8c 提交
* master aeb60f00 [origin/master] 提交
```

可以看到分支2.1.0没有和远程分支建立任何映射，此时若执行如下拉取命令则不成功（因为git此时不知道拉取哪个远程分支和本地分支合并）


### 建立当前分支与远程分支的映射关系

git branch -u origin/addFile

或者使用命令：

git branch --set-upstream-to origin/addFile



**此时git pull再次拉取，成功**


### 撤销本地分支与远程分支的映射关系
```
git branch --unset-upstream
```

### 删除本地分支 
```
命令行 : $ git branch -a
```


### 删除远程分支 
```
命令行 : $ git push origin --delete <BranchName>
```

### 丢弃所有本地修改的方法
```
git checkout .
```


### 退到上一次commit状态
退到上一次commit的状态
```javascript
git reset --hard HEAD^
```





### 解决.gitignore文件不起作用

原因是新建的文件在git中会有缓存，如果某些文件已经被纳入了版本管理中，就算是在.gitignore中已经声明了忽略路径也是不起作用的，这时候我们就应该先把本地缓存删除，然后再进行git的push，这样就不会出现忽略的文件了



```
git rm -r --cached .
git add .
git commit -m 'update .gitignore'
```




### git log (查看commit hash值)
查看commit日志 执行下面命令
```
git log
```


### 回滚、取消之前的提交


```
git reset --hard commit_id    

//退到/进到 指定commit的sha码 commit_id:239afed0857cc2e77c17c01014077808619af64d
```


>**注意:这是个必须掌握的操作**


### git stash

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


### 补充


[Git基本操作](http://youngxhui.github.io/2016/08/13/Git%E5%9F%BA%E6%9C%AC%E6%93%8D%E4%BD%9C/)

[github小窍门](http://youngxhui.github.io/2016/08/28/GitHub-for-Windows%E4%BD%BF%E7%94%A8%E6%95%99%E7%A8%8B%EF%BC%88%E5%9B%9B%EF%BC%89/)






















