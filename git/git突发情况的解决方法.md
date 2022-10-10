## 目录
- [目录](#目录)
- [说明](#说明)
- [项目结构](#项目结构)
- [工作环境](#工作环境)
- [常用命令](#常用命令)
  - [git stash](#git-stash)
  - [git fetch](#git-fetch)
  - [git branch](#git-branch)
  - [git checkout](#git-checkout)
  - [git reset](#git-reset)
  - [git restore](#git-restore)
- [使用技巧](#使用技巧)
  - [1.新建本地开发分支](#1新建本地开发分支)
    - [情景](#情景)
    - [解决方法](#解决方法)
    - [关键命令](#关键命令)
  - [2.多模块开发代码不同步](#2多模块开发代码不同步)
    - [情景](#情景-1)
    - [解决方法](#解决方法-1)
    - [关键命令](#关键命令-1)
  - [3.提交记录复查](#3提交记录复查)
    - [情景](#情景-2)
    - [解决方法](#解决方法-2)
    - [关键命令](#关键命令-2)

## 说明
本文收集本人在工作中所遇到的一些突发情况，以及日后避免问题出现的解决方法。（持续更新）
> 例子说明中，主要有主模块代码、多个副模块代码。每个模块含主分支main，开发分支develop。

## 项目结构
项目结构大致为主模块（文件夹）包含多个副模块（文件夹，层级不论）的形式
>主模块    
├─副模块1  
├─副模块2  
├─副模块3  
├─...  

## 工作环境
Windows 10 Professional

## 常用命令
先大致了解下，所需要频繁用到的一些命令
> 附：[在网页上练习分支操作](https://learngitbranching.js.org/?locale=zh_CN&NODEMO)
### git stash
```shell
# 贮藏本地修改的文件，包括新增文件
git stash -u -m "my message"

# 显示贮藏过的修改列表。
git stash list

# 应用最近一次贮藏的修改
git stash apply

# 应用指定贮藏的修改。如stash@{1}代表从栈顶0开始的第二个贮藏。
# 从git stash list能查看到想要应用的贮藏
git stash apply stash@{1}
```

### git fetch
```shell
# 例如在develop分支中，拉取main分支的代码
git fetch origin main:main
```

### git branch
```shell
# 显示本地的所有分支
git branch

# 显示本地及远程的所有分支
git branch -a

# 创建分支bugfix
git branch bugfix

# 从commit hash为XXXX的版本创建分支bugfix
git branch bugfix XXXX

# 从当前分支倒数倒数第三条commit的版本创建分支bugfix
git branch bugfix HEAD~3

# 删除bugfix分支
git branch -d bugfix

# 将当前分支的名字改为develop
git branch -m develop
```

### git checkout
```shell
# 切换到develop分支
git checkout develop

# 创建bugfix分支，并切换到此分支
git checkout -b bugfix

# 从commit hash为XXXX的版本创建分支bugfix，并切换到此分支
git checkout -b bugfix XXXX

# 从当前分支倒数倒数第三条commit的版本创建分支bugfix，并切换到此分支
git checkout -b bugfix HEAD~3

# 回滚当前分支修改过的文件（不包括新增）
git checkout -- .
```

### git reset
```shell
# 撤销最近一次的提交
git reset HEAD~1

# 撤销最近一次的提交，并保留本地修改
git reset HEAD~1 --soft
```

### git restore
```shell
# 回滚当前分支修改过的文件（不包括新增）
git restore .
```

## 使用技巧

### 1.新建本地开发分支

#### 情景 
拉取了代码出现了编译不过等影响工作继续进行  

#### 解决方法
从某一个稳定的历史提交版本，或某个稳定的分支，创建新建分支来继续进行工作  

#### 关键命令
```shell
# 从确人为稳定版本的commit hash为3d5cb75d8的版本创建分支stable，并切换到此分支
git checkout -b stable 3d5cb75d8
```

### 2.多模块开发代码不同步

#### 情景 
当副模块（develop分支）的代码更到最新时，而主模块（main分支）的代码由于某种原因未更到最新。整体进行编译时，副模块因缺少主模块某个提交的改动而编译不过。

#### 解决方法
从主模块cherry-pick能解决编译问题的某个提交，然后可选择性地reset撤销本地仓库的提交，即得到缺失的文件。
后续在主模块处理好问题后，可使develop重定基（rebase）到main分支上。

#### 关键命令
```shell
git checkout main

# 缺失的文件在主模块main分支的提交a20d551ded上，将其cherry-pick过来
# 在主分支下运行
git cherry-pick a20d551ded

# （可选）重置这条a20d551ded在副模块develop分支上的提交，使其回退到本地修改
# HEAD~1为上一次提交，HEAD~2位倒数第二次，依次类推
git reset HEAD~1
```
此时编译完如有需要合并的某个提交，可再按上述操作进行。
主模块处理好之后，可执行以下操作来使develop与main同步
```shell
git checkout develop
git rebase develop
```

### 3.提交记录复查

#### 情景 
当某个bug无法从代码逻辑中找到源头时，可能需要通过反复从历史记录中切换出分支来进行精准定位

#### 解决方法 
从某个出现问题的日期至最新代码的这部分提交中，可以按二分法的步骤依次从选定范围的中间提交记录进行逐步定位

#### 关键命令
```shell
# 从提交记录b0508b937e中创建新分支review，并切换过去
git checkout -b review b0508b937e

# 将当前分支向前推进到3a3fc3576b这条提交记录，即更新至该条提交记录
git merge 3a3fc3576be
```
处理好bug后的一些收尾工作
```shell
# 处理好bug后，删除创建的分支
git branch -d review

# 大写-D为强制删除
git branch -D review
```
