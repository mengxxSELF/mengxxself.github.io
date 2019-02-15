---
title: String类型
date: 2017-02-04 17:22:53
categories: javascript基础
tags: [笔记迁移, String]
---

> 笔记迁移系列，由于原始笔记所在网站即将下线，现将笔记全部迁移，并对原有笔记进行修正或添加

<!-- More -->

### 字符串

```js
let str = 'I am string'
console.log(str.length)  // 输出字符串长度
```

一个字符占一个长度，空格也算的

### 字符串中方法

2.1 charAt(索引数字)  通过索引找到该位置的字符

```js
let str = 'I am string'
console.log(str.charAt(2)) //a
```

2.2 charCodeAt(索引数字)  通过索引找到该位置的字符对应的Unicode编码

```js
let str = 'I am string'
console.log(str.charCodeAt(2)) //a
```

2.3 indexOf('a') 找到字符第一次出现位置 返回索引 没有则返回-1

2.4 lastIndexOf('a') 找到字符最后一次出现位置 返回索引 没有则返回-1

这两个方法完全兼容。

2.5 字符串的截取

2.5.1 str.substr(n,m)   从索引n开始 截取m个

2.5.2 str.substring(n,m) 从索引n开始 截取到m，不包括m      【包前不包后】

2.5.3 str.slice(n,m) 从索引n开始 截取到m，不包括m      【包前不包后】

但是slice和substring 不同在于,slice支持负数

console.log(str.slice(1,-5))
2.6 转化大小写 toLowerCase  toUpperCase

2.7 替换  replace.(旧字符,新字符)  这个方法不会修改原字符串

console.log(str.replace('I' ,'who'))
要想修改原字符串记得对原字符串重新赋值

2.7  分割为数组  split

```js
let str = 'I am string'
console.log(str.split(' ')) //["I", "am", "string"]
```

2.7.1 str.split()  // [原字符串]

```js
let str1 = 'hello'
console.log(str1.split()) //["hello"]
```

2.7.1 str.split('')   //每一个字符都被分割

```js
let str1 = 'hello'
console.log(str1.split('')) //["h", "e", "l", "l", "o"]
let str1 = 'hello'
console.log(str1.split('m')) //["hello"]  如果分割字符不存在，返回原数组
```

2.8  match  捕获 返回字符的详细信息

```js
console.log(str.match('a')) //["a", index: 2, input: "I am string"]
```

2.9 search  同indexOf  返回字符所在位置索引 没有则返回-1

不过search可以写正则

### es6新增 

* includes(), startsWith(), endsWith()

includes()：返回布尔值，表示是否找到了参数字符串。

startsWith()：返回布尔值，表示参数字符串是否在原字符串的头部。

endsWith()：返回布尔值，表示参数字符串是否在原字符串的尾部。

```js
let s = 'Hello world!';

s.startsWith('Hello') // true
s.endsWith('!') // true
s.includes('o') // true
```

* repeat()

repeat方法返回一个新字符串，表示将原字符串重复n次。

```js
'x'.repeat(3) // "xxx"
'hello'.repeat(2) // "hellohello"
'na'.repeat(0) // ""
```
* padStart() padEnd()

两个参数， 第一个参数是字符串补全生效的最大长度，第二个参数是用来补全的字符串。

```js
'x'.padStart(5, 'ab') // 'ababx'
'x'.padStart(4, 'ab') // 'abax'

'x'.padEnd(5, 'ab') // 'xabab'
'x'.padEnd(4, 'ab') // 'xaba'
```

### 练习

获取四位不同字符当做验证码

借助字符串和Math

```js
let str2 = 'qazwsxedcrfvtgbyhnujmikolpQAZWSXEDCRFVTGBYHNUJMIKOLP'
let endStr2 ='';
while(endStr2.length!=4){
    let cur = Math.round(Math.random()*51); // 获取随机数字
    let theCode = str2.charAt(cur); // 获取索引对应的字符
    if(endStr2.indexOf(theCode)==-1){ // 判断字符是否已经存在于字符串
        endStr2 += theCode; // 没有则拼接到字符串
    }
}
console.log(endStr2)
```
