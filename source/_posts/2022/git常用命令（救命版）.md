---
layout: post
title: git常用命令（救命版）
date: 2022-08-12 15:38:03
tags: 
	- git
---

`git -h` 获取帮助

## 命令解释
1. HEAD 是指当前的快照
`HEAD~1` 指回退一个快照，可以简写为 `HEAD~`，`HEAD~2` 指回退两个快照
2. 

## commit
* 添加并提交已修改的文件（仅已被`add`的文件）
``` shell
git commit -m 'message'
```

## push
* 推送当前分支所有commit至远程
``` shell
# 默认提交到与当前分支同名称的远程分支，远程没有则自动创建
git push
# 提交到指定remote，指定分支
git push origin master
```

## reset
* 撤销已提交的记录
HEAD~1，表示
``` shell
# 还未 push
git reset --soft HEAD~1
# 已经 push，从远程分支上撤销，同时执行以下命令
git push origin <branch> --force
```

* 已经 commit，从远程分支上撤销当前提交
``` shell
git 
```

## rebase
* 合并多条 commit
git log 记录从上至下如 A > B > C > D
若要合并 A ~ C 的记录
``` shell
# 查看历史记录，找到记录 C 的 id
git log
# -i 开启交互模式
git rebase -i <D的 id>
```
在 vim 编辑器中将 ABC 记录前的 `pick` 改为 `squash` 或 `s`，保存退出  
退出后会进入新的 vim 编辑器，在这里删除多余 message 或修改为需要的 message，保存退出
这时用 `git log` 查看可以发现 ABC 的记录已经被合并了  
``` shell
# 这时用 `git log` 查看可以发现 ABC 的记录已经被合并了  
git log
# 强制推送至指定分支
git push origin <branch> --force
```

* 


## 其他操作
* 对于已push的文件，想从远程仓库删除且以后都不在提交
``` shell
git rm --cached [<file>...]
```

* 对于已push的文件，想在以后不提交，但不从远程仓库删除
``` shell
git update-index --assume-unchanged [<file>...]

# 若是要忽略某个目录下所有文件，使用git bash，cd到目标目录下
git update-index --assume-unchanged $(git ls-files | tr '\n' ' ')
```

* 对于已经add的文件
``` shell
# 还未commit
git restore --staged [file_path]
```