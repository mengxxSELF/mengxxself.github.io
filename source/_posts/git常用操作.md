---
title: git常用操作
date: 2018-12-06 11:53:32
categories: tools
tags: [tools, git]
---

![git](https://user-gold-cdn.xitu.io/2018/12/14/167aad6f6ad558c8?w=1177&h=507&f=png&s=131075)

> git

<!-- More -->

* 撤销commit

1 查看每一commit的版本号  git log 

2 代码恢复

git reset --hard commit_id

完成撤销,同时将代码恢复到前一commit_id 对应的版本。

3 git reset commit_id  -- 取消commit

完成Commit命令的撤销，但是不对代码修改进行撤销，可以直接通过git commit 重新提交对本地代码的修改

* 撤销 merge

1 查看版本号

git reflog

2 代码恢复

git reset --hard xxx

* fetch 分支

git fetch origin 分支名称