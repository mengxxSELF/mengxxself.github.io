---
title: vscode使用指北
date: 2019-01-25 14:24:33
tags: [vscode]
---

> vscode 使用笔记

<!-- More -->

#### 

打开控制面板  F1 / cmd + shift + p

#### shell

在shell中直接输入  code -r 。 就可以打开这个项目了

#### 光标位置

回到上一个光标的位置，Cmd+U

在不同的文件之间回到上一个光标的位置 Control + - (你改了a文件，改了b文件之后想回到a文件继续编辑，mac使用controls+-)

#### 选中文本

选中单词 Cmd+D

#### 代码踢动

Option+上下方向键

代码移动的同时按住shift就可以实现代码复制 Option+Shift+上下


####  注释

注释有两种形式，单行注释和块注释(在js中，单行注释//,块注释/**/)

单行注释 Cmd+/ （win Ctrl +/）
块注释 Option+Shift+A

#### 代码缩进

单个文档进行缩进调节， 使用Cmd+Shift+P打开命令面板，输入缩进,然后选择相应的命令

#### other

合并代码行，多行代码合并为一行，Cmd+J

行排序，将代码行按照字母顺序进行排序，无快捷键，调出命令面板，输入按升序排序或者按降序排序

Cmd+P 输入你要打开的文件名,回车打开 . 选中你要打开的文件后，按Cmd+Enter,就会在一个新的编辑器窗口打开

#### 行跳转

如何快速跳转到某一行  Ctrl+g 输入行号

如果你想跳转到某个文件的某一行，你只需要先按下 “Cmd + P”，输入文件名，然后在这之后加上 “:”和指定行号即可。

f12跳到函数的定义处

Cmd+f12(win Ctrl+f12)跳转到函数的实现处


#### 插件使用

* [Polacode](https://my.oschina.net/u/172914/blog/1800776)

代码截屏

#### 参考文章

[文章来源](https://segmentfault.com/a/1190000017949680) 


#### markdown 中表格自动化

快捷键 - option + shift + f

[clicke me](https://segmentfault.com/a/1190000018271405)