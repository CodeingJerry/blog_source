---
title: Git 使用教程
date: 2021-05-22 15:15:20
updated: 2021-05-22 15:15:20
tags: Git
categories: 效率工具
---

### 基本概念
- __工作区__ 电脑里能看到的目录。

- __暂存区__ 存放在 .git 目录下的 index 文件（.git/index）中，所以也叫作索引（index）。

- __版本库__ 工作区有一个隐藏目录 .git，这个不算工作区，而是 Git 的版本库。
  <img src="/images/git/1352126739_7909.jpg" align="center">

- 图中左侧为工作区，右侧为版本库。在版本库中标记为 "index" 的区域是暂存区（stage/index），标记为 "master" 的是 master 分支所代表的目录树。

- 图中我们可以看出此时 "HEAD" 实际是指向 master 分支的一个"游标"。所以图示的命令中出现 HEAD 的地方可以用 master 来替换。

- 图中的 objects 标识的区域为 Git 的对象库，实际位于 ".git/objects" 目录下，里面包含了创建的各种对象及内容。

  <!--more-->

- 当对工作区修改（或新增）的文件执行 `git add` 命令时，暂存区的目录树被更新，同时工作区修改（或新增）的文件内容被写入到对象库中的一个新的对象中，而该对象的ID被记录在暂存区的文件索引中。

- 当执行提交操作（git commit）时，暂存区的目录树写到版本库（对象库）中，master 分支会做相应的更新。即 master 指向的目录树就是提交时暂存区的目录树。

- 当执行 `git reset HEAD` 命令时，暂存区的目录树会被重写，被 master 分支指向的目录树所替换，但是工作区不受影响。

- 当执行 `git rm --cached <file>` 命令时，会直接从暂存区删除文件，工作区则不做出改变。

- 当执行 `git checkout` . 或者 `git checkout -- <file>` 命令时，会用暂存区全部或指定的文件替换工作区的文件。这个操作很危险，会清除工作区中未添加到暂存区的改动。

- 当执行 `git checkout HEAD` . 或者 `git checkout HEAD <file>` 命令时，会用 HEAD 指向的 master 分支中的全部或者部分文件替换暂存区和以及工作区中的文件。这个命令也是极具危险性的，因为不但会清除工作区中未提交的改动，也会清除暂存区中未提交的改动。
### 基本操作
- Workspace：工作区
- Index / Stage：暂存区
- Repository：仓库区（或本地仓库）
- Remote：远程仓库
<img src="/images/git/bg2015120901.png" align="center">
1. `git clone` 下载远程代码仓库到本地。
2. 创建了自己的文件，或者进行了修改，可以用 `git add .` 把所有文件加入暂存区，等待建立节点。
3. `git commit -m "这一阶段工作描述"` 建立本地仓库的工作节点，尽量细化你的节点，别做了很多工作才提交一次。
4. `git push` 把之前建立的一系列节点推送到 GitHub 发布、保存。
5. `git pull` 将 GitHub 的改动同步到本地，比如你在办公室电脑的改动同步到家里，或者多人协作项目中他人的改动同步到本地。
#### 配置
Git的设置文件为.gitconfig，它可以在用户主目录下（全局配置），也可以在项目目录下（项目配置）。
```
# 显示当前的Git配置
$ git config --list

# 编辑Git配置文件
$ git config -e [--global]

# 设置提交代码时的用户信息
$ git config [--global] user.name "[name]"
$ git config [--global] user.email "[email address]"
```
#### 新建代码库
```
# 新建一个目录，将其初始化为Git代码库，project-name可省略
$ git init [project-name]

# 下载一个项目和它的整个代码历史，another-name可省略
$ git clone [url] [another-name]
```
`git clone` 包括了以下几步：
- `git init` (create the local repository)
- `git remote add origin ${URL}` (add the URL to that repository)
- `git fetch` (fetch all branches from that URL to your local repository)
- `git checkout` (create all the files of the main branch in your working tree)
#### 提交与修改
```
# git status 命令用于查看在你上次提交之后是否有对文件进行再次修改
$ git status -s

# git status 显示你上次提交更新后的更改或者写入缓存的改动， 而 git diff 一行一行地显示这些改动具体是啥
$ git diff

# 添加指定文件到暂存区
$ git add [file1] [file2] ...

# 添加指定目录到暂存区，包括子目录
$ git add [dir]

# 添加当前目录的所有文件到暂存区
$ git add .

# 删除工作区文件，并且将这次删除放入暂存区
$ git rm [file1] [file2] ...

# 停止追踪指定文件，但该文件会保留在工作区
$ git rm --cached [file]

# 改名文件，并且将这个改名放入暂存区
$ git mv [file-original] [file-renamed]

# 提交暂存区到仓库区
$ git commit -m [message]

# 提交暂存区的指定文件到仓库区
$ git commit [file1] [file2] ... -m [message]
```
#### 远程操作
```
# 显示所有远程仓库
$ git remote -v

# 增加一个新的远程仓库，并命名
$ git remote add [shortname] [url]

# 删除远程仓库
$ git remote rm [shortname]

# 下载远程仓库的所有变动
$ git fetch [remote]

# 取回远程仓库的变化，并与本地分支合并
$ git pull [remote] [branch]

# 上传本地指定分支到远程仓库
$ git push [remote] [branch]

# 强行推送当前分支到远程仓库，即使有冲突
$ git push [remote] --force
```
- `git pull [remote] [branch]` 相当于
```
# 获取远程仓库有而本地没有的数据
- git fetch [remote]
# 将服务器上的任何更新（假设有人这时候推送到服务器了）合并到你的当前分支
- git merge [remote]/[branch]
```
#### 分支
```
# 列出所有本地分支
$ git branch

# 列出所有远程分支
$ git branch -r

# 新建一个分支，但依然停留在当前分支
$ git branch [branch-name]

# 切换到指定分支，并更新工作区
$ git checkout [branch-name]

# 合并指定分支到当前分支
$ git merge [branch]

# 删除分支
$ git branch -d [branch-name]
```
#### 撤销
```
# 恢复暂存区的指定文件到工作区
$ git checkout [file]

# 恢复某个commit的指定文件到暂存区和工作区
$ git checkout [commit] [file]

# git reset 命令用于回退版本，可以指定退回某一次提交的版本。
# 重置暂存区的指定文件，与上一次commit保持一致，但工作区不变
$ git reset [file]

# 重置暂存区与工作区，与上一次commit保持一致
$ git reset --hard

# 重置当前分支的指针为指定commit，同时重置暂存区，但工作区不变
$ git reset [commit]

# 重置当前分支的HEAD为指定commit，同时重置暂存区和工作区，与指定commit一致
$ git reset --hard [commit]

# git reset HEAD 用以取消之前 git add 添加，但不希望包含在下一提交快照中的缓存
```
### 参考
[runoob.com|Git 教程](https://www.runoob.com/git/git-tutorial.html)
[阮一峰|常用 Git 命令清单](http://www.ruanyifeng.com/blog/2015/12/git-cheat-sheet.html)

