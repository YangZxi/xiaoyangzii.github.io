---
layout: post
title: git常用命令（救命版）
date: 2022-08-12 15:38:03
tags: 
	- git
---

`git -h` 获取帮助

## commit
* 添加并提交已修改的文件（仅已被`add`的文件）
``` shell
git commit -a -m 'message'
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
* 已经commit，还未提交，保留add
``` shell
git reset --soft HEAD~1
```

## rebase


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