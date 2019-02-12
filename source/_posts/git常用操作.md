---
title: git常用操作
date: 2018-12-06 11:53:32
categories: tools
tags: [tools, git]
---

![git](https://user-gold-cdn.xitu.io/2018/12/14/167aad6f6ad558c8?w=1177&h=507&f=png&s=131075)

> git

<!-- More -->

#### 撤销commit

1 查看每一commit的版本号  git log 

2 代码恢复

git reset --hard commit_id

完成撤销,同时将代码恢复到前一commit_id 对应的版本。

3 git reset commit_id  -- 取消commit

完成Commit命令的撤销，但是不对代码修改进行撤销，可以直接通过git commit 重新提交对本地代码的修改

#### 撤销 merge

1 查看版本号

git reflog

2 代码恢复

git reset --hard xxx

#### fetch 分支

git fetch origin 分支名称

#### 强行push

git push origin master -f


#### git 提交commit格式

feat：新功能（feature）
fix：修补bug
docs：文档（documentation）
style： 格式（不影响代码运行的变动）
refactor：重构（即不是新增功能，也不是修改bug的代码变动）
test：增加测试
chore：构建过程或辅助工具的变动