---
title: 一个登陆的shell脚本
date: 2019-01-15 19:12:37
tags: [linux]
---

![](https://user-gold-cdn.xitu.io/2019/1/12/168426279e212857?w=796&h=644&f=png&s=398044)

> 编写一个shell脚本，不用每次都查询服务器地址了

<!-- More -->

项目涉及到好多关于登陆的shell命令，比如登陆服务器，链接redis等

编写一个shell脚本，不用每次都查询服务器地址了

###  shell脚本格式

* 第一行是 #!/bin/bash  

因为我们使用的是bash， 已 #!/bin/bash 开头声明文件语法是用的是bash语法。 当程序被执行的时候，会加载bash相关环境配置文件

如果不写的话，系统可能无法判断程序需要什么shell来执行

* #相当于注释

除了在  #!/bin/bash 行出现的  # ，其余的在开头的 都表示的此行代码被注释

### 通过命令输入变量

可以编写命令行直接输入变量值

命令与变量的对应是

```
某一条命令 xxx xxx
$0         $1  $2      
```

比如 写一个输入变量的shell脚本

```
echo i am variable '$1', $1
```

执行一下

```
sh variable.sh mxtx
// i am variable $1, mxtx
```

![variable](https://user-gold-cdn.xitu.io/2019/1/12/168424c2fa11b524?w=816&h=138&f=png&s=79062)

还有一些特殊的变量含义

$# 表示后面有几个参数

### 与用户交互  read

read 命令可以获取到键盘输入信息

```
read [-pt] variable
```

其实感觉和创建了一个本地变量的概念类似

比如执行 

```
read name // 命令行进入等待状态

i am tom // 回车结束输入
```
然后输出一下这个 name 的变量值

```
echo $name // i am tom
```

![read](https://user-gold-cdn.xitu.io/2019/1/12/16842422e365015e?w=710&h=158&f=png&s=66202)

使用 -p 编写一个带有提示的命令

```
read -p "please enter your name:"
```

![variable](https://user-gold-cdn.xitu.io/2019/1/12/1684244bda769df2?w=1064&h=204&f=png&s=94620)

### if 语句

```
if [ 条件 ]; then 
  条件成立时候要执行的操作
fi 
```
这个格式必须这么固定 最后一行就是 fi  表示结束if 语句了

写一个例子，当输入的第一个变量值是 1 就输出 boy  就输出 否则 为 girl

```js
if [ $1 == 1 ]; then
  echo boy
else 
  echo gril
fi
```

![variable](https://user-gold-cdn.xitu.io/2019/1/12/1684252cea9982a6)

如果是多个条件

```
if [ 条件1 ]; then 
  条件1成立时候要执行的操作
elif [ 条件2 ]; then  
  条件2成立时候要执行的操作
fi 
```

### shell脚本的调试

脚本写完之后不一定保证写的正确，可以先进行一个语法的检查

```
sh -n youshell.sh
```

![shell](https://user-gold-cdn.xitu.io/2019/1/12/168425b99d5bd064?w=948&h=126&f=png&s=73861)

有三个参数可以使用

| 参数 | 含义 |
| ------------- | --------------- |
| n | 不执行脚本 只检查语法 |
| v | 执行前将脚本内容输出 |
| x | 将使用的内容输出 |


![vx](https://user-gold-cdn.xitu.io/2019/1/12/168425e372fbf0f1?w=834&h=440&f=png&s=133434)

### 编写脚本

```js
read -p "enter your choice: " choice

# 定义变量
test='work@xxxx'
master='work@xxx'
redis='xxxxxxx'

echo $choice

# 根据输入值 进行条件判断

if [ $choice == test ]; then
 ssh $test
elif [ $choice == master ]; then
 ssh $master
elif [ $choice == redis ]; then
 redis-cli -h $redis
fi
```

### 项目编写

根据添加的配置信息 生成shell脚本