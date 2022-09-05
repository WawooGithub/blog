---
title: my first article——GIT使用学习笔记
date: 2022-09-04 13:08:40
tags: GIT
---

前言：这是我写的第一篇博客，后面我会陆续把我之前的笔记整理出来。  

<!-- more -->

### git仓库结构
- `.git文件`：存放git管理信息目录，·git init·时创建
- `.gitignore`：里面包含了一系列文件与文件夹，.gitignore中的文件将会被忽略，不会被git管理
- `.gitmoudles`：里面包含了模块的submoudles，当项目里还包含其他仓库作为子项目时会有此文件，git通过.gitmoudles去初始化子模块，详见[官网教程](https://git-scm.com/book/zh/v2/Git-%E5%B7%A5%E5%85%B7-%E5%AD%90%E6%A8%A1%E5%9D%97)
- `Directory`：git管理的一个文件夹（或者说仓库），也就是.git文件夹所在的目录
- `Workspace`：工作区，包含git管理的目录和文件，除了.git文件夹和.gitignore文件中的文件之外的都属于工作区
- `Index/Stage`：暂存区，在提交进入仓库前，可以把更新add进暂存区
- Local Repo：本地仓库，存放在本地的版本库，HEAD是当前所在的分支，也就是·git branch·时打星的那个分支
- `Stash`：是一个工作状态保存栈，用于保存/回复Workspace中的临时状态。详见[官网教程](https://git-scm.com/book/zh/v2/Git-%E5%B7%A5%E5%85%B7-%E8%B4%AE%E8%97%8F%E4%B8%8E%E6%B8%85%E7%90%86#_git_stashing)

---

### 基本操作
- `git init`
初始化git  
- `git status`
查看仓库状态
- `git add`
向暂存区添加文件
- `git commit`
将暂存区文件保存至仓库的历史记录

> > `git add .` + `git commit` 可以简化成`git commit -am 'message'`  
> > 
> -m message信息
> -a 自动add然后提交

- `git diff`
查看工作树（Working  Directory）、最新commit和暂存区的区别，`+`表示新增,`-`表示删除
- `git log | git log --graph` ：查看提交日志 | 以图表形式显示提交日志

---

### 分支操作
- `git branch`： 显示各分支与当前所在分支
- `git checkout -b feature-A`：创建并切换到分支feature-A
> 相当于`git branch feature-A` +`git checkout feature-A`
- `git checkout -`：切回上一个分支
- `git merge`：分支合并
> [--ff、--no-ff与--squash区别](https://juejin.cn/post/7137286838196928548)
- `git rebase`：分支变基
- ~与^：~控制分支历史回退的步数，^控制分支历史回退的方向，如果只有一条分支，两者没区别。
e.g.
HEAD~1指回退一个快照，可以简写为HEAD~
HEAD~2指回退两个快照
参考链接

>e.g1.  
1. 为了实现feature-A，我们创建了feature-A，如下图
2. 然后实现feature-A，创建了feature-A.md
3. 接下来和master分支合并:
    - 先切换到master分支，`git checkout master`
    - 在和feature-A分支合并，`git merge --no-ff feature-A` 

>e.g.2，详见[官网教程](https://git-scm.com/book/zh/v2/Git-%E5%88%86%E6%94%AF-%E5%88%86%E6%94%AF%E7%9A%84%E6%96%B0%E5%BB%BA%E4%B8%8E%E5%90%88%E5%B9%B6)


#### 特性分支/主题分支
在使用GIT开发时应保证master分支为稳定版本（就是可以拿出来用的），当需要实现功能A时，应当branch出一个分支来，这个分支只用于实现功能A，这个分支便称为特性分支。**即使出现了bug需要修复，也应当branch一个新的分支修正**

---

### 一些常见操作
#### 1. reset
> 关于reset，详见[官方文档](https://git-scm.com/book/zh/v2/Git-%E5%B7%A5%E5%85%B7-%E9%87%8D%E7%BD%AE%E6%8F%AD%E5%AF%86)
> 1. 移动 HEAD 指向的分支。 
> 2. 使索引看起来像 HEAD。
> 3. 使工作目录看起来像索引。（如果添加--hard的话）

- `git reset 上次commit的hash/想要取消暂存的文件或目录`（ **没有--hard**）：清除index
- `git reset --hard` ：回溯历史版本
- `git reset --hard HEAD~`：回溯到上一次提交的状态

#### 2. 查看部分的分支历史
> 详见[官方文档](https://git-scm.com/book/zh/v2/Git-%E5%B7%A5%E5%85%B7-%E9%80%89%E6%8B%A9%E4%BF%AE%E8%AE%A2%E7%89%88%E6%9C%AC#_triple_dot)
- **双点**：
e.g.
`git log master..experiment`：在 experiment 分支中而不在 master 分支中的提交
`git log refA refB`：同时在分支refA和refB中的提交
`git log refA --not refB`：在refA但不在refB中的提交
- **多点**：
e.g.
`git log refA refB --not refC`：同时在refA和refB，但不在refC的
- **三点**：
这个语法可以选择出被两个引用 之一 包含但又不被两者同时包含的提交
e.g.
`git log master...experiment`：master 或者 experiment 中包含的但不是两者共有的提交
`git log --left-right master...experiment`：--left-right会显示每个提交到底处于哪一侧的分支， 这会让输出数据更加清晰。

#### 3. 合并冲突与撤销合并
> 关于合并，详见[官方文档：分支的新建与合并](https://git-scm.com/book/zh/v2/Git-%E5%88%86%E6%94%AF-%E5%88%86%E6%94%AF%E7%9A%84%E6%96%B0%E5%BB%BA%E4%B8%8E%E5%90%88%E5%B9%B6)、[官方文档：变基](https://git-scm.com/book/zh/v2/Git-%E5%88%86%E6%94%AF-%E5%8F%98%E5%9F%BA)和[官方文档：高级合并](https://git-scm.com/book/zh/v2/Git-%E5%B7%A5%E5%85%B7-%E9%AB%98%E7%BA%A7%E5%90%88%E5%B9%B6)
- 分支合并时用rebase和merge的区别：  
 rebase相当于在master分支上执行一遍branch-to-be-merge的修改，最后的分支历史中master分支和branch-to-be-merge是同一条分支；（个人感觉，还是尽量用merge，虽然用rebase会让历史非常的线性）
merge会保留branch-to-be-merge的分支历史，  因为它只操作相关的三个commit。
- 查看冲突的位置：`git log --oneline --left-right HEAD...MERGE_HEAD` --merge
- 撤销合并：`git reset --hard HEAD~`
> 还有一个`git revert`，但感觉效果不太好，会把历史弄乱。

#### 4. 修改提交信息与压缩提交历史（通过变基）
> 详见[官方文档](https://git-scm.com/book/zh/v2/Git-%E5%B7%A5%E5%85%B7-%E9%87%8D%E5%86%99%E5%8E%86%E5%8F%B2)
- 修改最近一次提交：`git commit --amend`（相当于一次小的变基）
- 修改多个提交信息：`git rebase -i HEAD~3`（修改从HEAD~2到HEAD的3次提交信息,***这里指定的是要修改的父提交***）
> -i 交互式的
- 压缩历史：比如压缩最近两次的，`git rebase -i  HEAD~2`，按照编辑文档的注释来即可

#### 5. 引用日志：
当你在工作时， Git 会在后台保存一个引用日志（reflog）， 引用日志记录了最近几个月你的 HEAD 和分支引用所指向的历史。，通过`git reflog`查看

---
### 参考文档：
[官方文档](https://git-scm.com/book/zh/v2/%E8%B5%B7%E6%AD%A5-%E5%85%B3%E4%BA%8E%E7%89%88%E6%9C%AC%E6%8E%A7%E5%88%B6）)
[git 理解 HEAD^与HEAD~](https://git-scm.com/book/zh/v2/Git-%E5%B7%A5%E5%85%B7-%E8%B4%AE%E8%97%8F%E4%B8%8E%E6%B8%85%E7%90%86#_git_stashing)
[Git命令中波浪号~与脱字符^的区别](https://www.cnblogs.com/phillee/p/15821555.html)
[--ff、--no-ff与--squash区别](https://juejin.cn/post/7137286838196928548)