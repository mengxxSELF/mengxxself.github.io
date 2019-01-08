---
title: ssh-copy-id
date: 2018-05-26 19:45:03
categories: LINUX
tags: linux
---

如何在本机中进行ssh-copy-id

<!-- more -->

在Linux中登录服务器可以使用

```
ssh root@xxxxx
```

然后会让你输入密码 就可以登录了

可以通过shh-copy-id实现 无密码登录

相当于本机电脑与远程服务器之间建立信任关系

## 建立信任关系

* 1 先查看本机中用户主目录下是否有 .ssh目录

```
cd /User/xxx/.ssh

ls -a
```

可以看到 id_rsa和id_rsa.pub这两个文件

![svg](/img/SSH/ssh1.png)


* 2 将 id_rsa.pub 中内容添加到git中

![svg](/img/SSH/ssh2.png)
![svg](/img/SSH/ssh3.png)


* 3 去执行ssh-copy-id吧


如果 本机中并没有 那两个文件呢 

* ssh-keygen 

```
 ssh-keygen -t rsa -C "youremail@example.com"
```
然后就生成那两个文件了

## 参考文章 

[远程仓库](https://www.liaoxuefeng.com/wiki/0013739516305929606dd18361248578c67b8067c8c017b000/001374385852170d9c7adf13c30429b9660d0eb689dd43a000)
