---
title: js基础练习题
date: 2017-02-02 09:31:55
categories: javascript基础
tags: [笔记迁移, 练习]
---

> 笔记迁移系列，由于原始笔记所在网站即将下线，现将笔记全部迁移，并对原有笔记进行修正或添加

<!-- More -->

#### 传入任意参数，求出其中被3和5整除的之和

```js
let total =0;
function add(){
    for(let i=0;i<arguments.length;i++){
        let cur = arguments[i];
        if(cur%3==0 && cur%5==0){
            total += cur;
        }
    }
    return total;
}
document.querySelector('div').innerHTML = add(15,2,30);
```

#### 十秒之内每秒返回一个不重复的四位随机数，每一位都在【0-60】

```js
let n=0;
getN ();
function getN (){
    let str='';
    while(str.length!=4){
        let cur = Math.round(Math.random()*(60));
        if(str.indexOf(cur)==-1){
            str += cur;
        }
    }
    document.getElementsByTagName('div')[0].innerHTML= str+','+n;
    n++;
    clearTimeout(timer)
    if(n==10) {
        return
    }
    let timer = setTimeout(getN,1000)
}
```

#### 输出一个四位不重复，从指定字符中选择。

```js
let str = 'qazwsxedcrfvtgbyhnujmiklop123456780'
let end =''
while(end.length!=4){
    let cur = Math.round(Math.random()*(str.length))
    let code = str.charAt(cur)
    if(end.indexOf(cur)==-1){
        end+= code;
    }
    document.getElementsByTagName('div')[0].innerHTML= end;
}
```

#### 函数创建

请创建一个函数 该函数参数值为一个整数 函数返回值为一个数字 该数字由整数中相同数字组成 但是该数字要比传入的参数值大

如果无法获取更大的数字 返回-1

```js
nextBigger(33) -> -1
nextBigger(123) -> 213
nextBigger(303) -> 330
```

思路分析

将传入的参数转化为数组形式 该数组位数从左到右依次降低  以输入 2017为例 得到数组 [2,0,1,7]

则依次对应位置为 千分为 百分为  十位 个位

所以从右边开始依次与左边进行比较 一旦左侧数字小于右侧 两者进行位置交换 一旦数组中有发生一次位置交换 则停止遍历 输出此时的值

代码展示

```js
function nextBigger(num) {
 if (num < 10 || typeof num != 'number') {
 // 小于10 各位数字 直接返回 -1
 return -1
 }
 let originAry = String(num).split('') // [3, 3, 3]
 // 进行一次数组去重
 let newAry = Array.from(new Set(originAry))
 if (newAry.length === 1) {
 // 如果数组去重之后长度为1 表示参数类型为多位相同值重复
 return -1
 }
 // 从右到左依次判断 如果左侧大 则进行位置交换 只要发生一次位置交换 则可将数值返回
 let i = originAry.length - 1
 while (i > 0) {
 let right = originAry[i]
 let left = originAry[i - 1]
 if (right > left) {
   // 交换位置
   originAry.splice(i - 1, 2, right, left)
   i = 0
   break
 }
   i--
 }
 let lastNum = Number(originAry.join(''))
 let endNum = num === lastNum ? -1 : lastNum
 return endNum
}
```

其实最开始的时候没有看清题目要求  我之间返回了进行重新排列之后最大的数字

代码展示

```js
function nextBigger (num) {
 if (num < 10 || typeof num != 'number') {
 // 小于10 各位数字 直接返回 -1
 return -1
 }
 let originAry = String(num).split('').sort() // [3, 3, 3]
 // 进行一次数组去重
 let newAry = Array.from(new Set(originAry))
 if (newAry.length === 1) {
 // 如果数组去重之后长度为1 表示参数类型为多位相同值重复
 return -1
 }
 let lastNum = Number(originAry.reverse().join(''))
 let endNum = num === lastNum ? -1 : lastNum
 return endNum
 }
```

#### 数组的问题

```js
let arr=[3,1,2,3,3,3,2,1,1,1,1,2] 
```

要求输出 每个元素出现的次数 并且按照次数排序


处理方案

```js
 let ary = [12,12,2,2,3,4,5,6,6,88]
 let obj = {}
 ary.map( (item) => obj[item] = obj[item] ? obj[item] + 1 : 1 )
 conosle.log(obj)

```

*  变动题目为

```js
let arr=[3,1,2,3,3,3,2,1,1,1,1,2]
```

要求输出的 按照Value进行排序 【出现次数】

比如 
 1:5
 3:4
 2:3

解决思路

```js
 let ary = [12,12,2,2,3,4,5,6,6,88,234,234,88,88,11]
 let obj = {}
 ary.map( (item) => obj[item] = obj[item] ? obj[item] + 1 : 1 )
 let newAry = Object.keys(obj)
 let endAry = []
 newAry.map((item) => {
 let once = {}
 once[item] = obj[item]
 endAry.push(once)
 })
 let ans = endAry.sort((a,b) => Object.values(b)[0] - Object.values(a)[0])
 console.log(ans)
```

思路2

```js
 let arys = [123,123,44,55,22,44,123,0]
 let shortAry = Array.from(new Set(arys))
 let endAry = []
 shortAry.map((item) => {
 let obj = {key: null, val: null} // key 是谁出现 val 出现次数
 obj.key = item
 while (arys.includes(item)) {
 obj.val ++
 arys.splice(arys.indexOf(item), 1)
 }
 endAry.push(obj)
 })
 endAry.sort((a, b) => b.val - a.val ) // 按照val 出现次数 进行排序
 console.log(endAry)
```


#### 函数分析

```js
let x= 1;
if((function f(){})()){
    x += typeof f
}
console.log(x);
```

这个是输出1 因为if条件不成立 所以不执行代码

函数执行 没写return 返回值是undefined  在js中 条件为false的情况是 undefined null  0 ''  NaN  false


#### 统计字符串中出现最多的字符

请封装一个函数，该函数的功能是：

1 传入任意字符串,统计该字符串中出现最多的字符并输出结果.不用考虑出现次数相同的字符。

```js
// 比如
function get(str){
   // 函数体内部代码
}
var myStr = '5ssffflsjkssssmddnjkdkuuua6';
console.log(get(myStr)) // 控制台输出:    字符出现次数最多的是s,出现次数为7
```

2 进阶版

传入任意字符串,统计该字符串中出现最多的字符并输出结果。并且考虑出现次数相同的字符。

```js
// 比如
function get(str){
   // 函数体内部代码
}
var myStr = 'wwwdddcccvvvnklkopl';
console.log(get(myStr)) // 控制台输出:    字符出现次数最多的是w,d,c,v,出现次数全部为3
```

基础版思路分析

1 思路1 利用对象
A 基础版
A1 创建空对象 循环字符串 将字符串保存为属性名 出现次数为属性值，重复出现次数加1
A2 创建新数组 for in循环对象 将每一个属性值存入数组
A3 利用Math 查找数组中最大值保存为max -》 出现的最多次数数值
A4 for in循环对象 当属性值==max 输出该属性名 -》 就是出现次数最多的字符

2 思路2 利用数组
A 基础版
A1 创建两个空数组：字符数组与数值数组，循环字符串
将字符存入第一个数组 将字符出现次数存入第二个数组
A2 利用Math查找次数数组中最大值并记录位置index -》 出现的最多次数数值
A3 输出字符数组index位置处的字符 -》 就是出现次数最多的字符

3 思路3 利用正则的重复子项
A1 将分开的相同字符写到一起。也就是利用sort重新排序。字符串转数组-》sort排序-》join重新拼接
A2 利用重复子项查找字符出现次数
以下代码为利用对象基础版

```js
function getStr(str){
    var obj={};
    for(var i=0;i<str.length;i++){
        var cur=str[i];
        if(obj[cur]){
            obj[cur]++;
        }else{
            obj[cur]=1;
        }
    }
    var ary=[];
    for(var key in obj){
        ary.push(obj[key])
    }
    ary.sort(function(a,b){
        return a-b;
    })
    for(var key in obj){
        if(obj[key]==ary[ary.length-1]){
            return "出现次数最多的字符是"+key+"共"+obj[key]+"次";
        }
    }
}
var str="2399llaaaaaa"
console.log(getStr(str));
```

以下代码为利用数组方式

1 基础版
```js
var str = 'aafabgbbcgcfffaaddd';
function findMax(str){
    var endCode='', max=0,codeAry =[], codeNum=[];
    /* 1 循环字符串  第一个数组 放入字符  第二个放入次数   这样两个数组之间形成映射关系
  2 次数数组查找最大值  查找对应位置 index  
 3 根据index输出第一组对应位置的字符  */
    for(var i=0;i<str.length;i++){
        if(codeAry.indexOf(str[i])==-1){
            /* 字符不存在放入数组*/
            codeAry.push(str[i]);
            codeNum[codeAry.indexOf(str[i])] = 1; // codeNum 对应位置字符次数 为1
        }else{
            /* 字符存在 */
            codeNum[codeAry.indexOf(str[i])] += 1; // codeNum 对应位置字符次数 +1
        }
    }
    var max = Math.max.apply(Math,codeNum); // 找到最大数字
    var cur = codeNum.indexOf(max);// max 在 codeNum 中出现的第一次的位置
    endCode = codeAry[cur];
    return '字符出现次数最多的是'+ endCode+',出现次数为'+max;
}
console.log(findMax(str));
```
2 进阶版

```js
var str = 'bab233c33222aeeeacbbace';
function findMax(str){
    var endCode='', max=0,codeAry =[], codeNum=[];
    for(var i=0;i<str.length;i++){
        if(codeAry.indexOf(str[i])==-1){
            /* 字符不存在放入数组*/
            codeAry.push(str[i]);
            codeNum[codeAry.indexOf(str[i])] = 1; // codeNum 对应位置字符次数 为1
        }else{
            /* 字符存在 */
            codeNum[codeAry.indexOf(str[i])] += 1; // codeNum 对应位置字符次数 +1
        }
    }
    var max = Math.max.apply(Math,codeNum); // 找到最大数字
    //cur = codeNum.indexOf(max);// max 在 codeNum 中出现的第一次的位置
    while( codeNum.indexOf(max) != -1){
        var cur = codeNum.indexOf(max);
        endCode += codeAry[cur];
        codeNum.splice(cur,1,'change');
// max 在 codeNum 中出现的第一次的位置 使用新字符进行替换 
// 注意这里不能删除 如果使用删除 会导致位置变化，两个数组之间映射关系会出错
    }
    return '字符出现次数最多的是'+ endCode+',出现次数为'+max;
}
console.log(findMax(str));
```

* 利用正则重复子项

```js
var str = 'aaCAcCDcDFF';
function regFind(str){
    str = str.split('').sort(function(a,b){return a.localeCompare(b)}).join('');
    // console.log(str);  这里会得到一个重新排序后的按照各自顺序aaAccCCDDFF
    var reg = /(\w)\1+/gi ; // 利用重复子项  这里忽略大小写
    var max=0,maxCode ='';
    str.replace(reg,function($0,$1){
        console.log(arguments);// ['aaA' 'a' 0  原字符串]  利用第一第二个参数
        if($0.length>max){
            max = $0.length;
            maxCode = $1;
        }else{// 最大值相同时  进行拼接
            maxCode += $1;
        }
    });
    return ('重复次数最高为 '+max+'，重复代码是'+maxCode);
}
document.write(regFind(str));
```


