---
title: git 使用
date: 2016-08-15 21:54:34
tags: [git, 每周总结]
---

git 实际上有两个区，一个是工作区，另一个是版本库。在版本库里，又分为暂存区和当前分支。

git add 将工作区的文件提交到暂存区，此时添加到暂存区的文件都是被 track 的。

git commit 将暂存区的文件提交到本地当前分支，此时暂存区会被清空。


?? 未跟踪
A  新添加到暂存区
_M 被修改但未放入暂存区


git init
初始化 git 目录

创建一个文件
将文件添加到工作区，文件状态为 Untracked，此时使用 git status -s 显示为 ??。

git add
将文件添加到暂存区，文件状态为 Staged，此时使用 git status -s 显示为 A

git commit
将文件提交到当前分支，文件状态为 Unmodified，此时使用 git status -s 显示为 _M

修改文件
文件已经被跟踪，但是有修改，此时使用 git status -s 显示为 M_