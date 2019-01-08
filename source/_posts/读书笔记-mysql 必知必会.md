---
title: 读书笔记- mysql 必知必会
date: 2017-12-10 11:57:31
categories: 读书笔记
tags: [数据库, mysql]
---

<!--more-->

## 创建表

```javascript

CREATE TABLE `ACTIVITY20180529_DAILY` (
  `lid` int(11) NOT NULL COMMENT '这里是相关注释',
  `day` int(11) NOT NULL,
  `uid` int(11) NOT NULL DEFAULT '0',
  `count` int(11) NOT NULL DEFAULT '0',
  `time` timestamp NOT NULL DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
  `lan` char(11) NOT NULL DEFAULT 'en',
  PRIMARY KEY (`uid`,`lid`,`day`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8

```


## 表数据操作

### 查询表数据

```
SELECT uid FROM TABLEBA 
```

* 查询不同行

```
SELECT DISTINCT uid FROM TABLEBA 
```

相当于查询每一个不重复的UID


注意： DISTINCT 关键字应用与后面所有的列

#### limit 

```
SELECT DISTINCT uid FROM TABLEBA  LIMIT start, number
```

表示从start 查询 number 个 等同于


```
SELECT DISTINCT uid FROM TABLEBA  LIMIT start OFFSET number
```

如果LIMIT的后面只写一个 则表示取Number 个

```
SELECT DISTINCT uid FROM TABLEBA  LIMIT number
```

例子：

```javascript

`select id, uid, goods_id as goodsId, live_id from ${TABLE['game']} where uid = ${uid} order by datetime desc limit ${number}`

```

表示选取number 个

```javascript

`select id, uid, goods_id, live_id from ${TABLE['game']} where uid = ${uid} order by datetime desc limit ${start}, 20`

```

这里的limit m, n

表示从m 开始 选取n 个


### 排序 order by

查询的结果按照某列排序

```javascript
select uid from tablea order by count
```

* 按照多列排序

```javascript
select uid from tablea order by count, day
```
多列排序的时候， 会先按照count排序 当count相等的时候 再按照day排序

#### order by xxx desc

默认排序是从小到大。如果希望从大到小排序。则需要添加关键字 desc


```javascript
select uid from tablea order by count desc, day
```
* desc 只针对于紧挨着关键字的列排序 如果想多列都降序排序 则需要将每一列都添加 DESC

### 筛选数据 WHERE

```javascript
select uid from tablea where day > 20180412 order by count desc, day 
```

where 子句

| 含义  | 操作符 |
| --- | --- | --- | --- |
| 等于 | = |
| 不等于 | != 或者 <> |
| 大于 | > |
| 大于等于 | >= |
| 小于 | < |
| 小于等于 | <= |
| 在两者之间 | between |

#### 关于查找 值为 null 的列


```javascript
select uid from tablea where day is null
```

这样虽然能查到结果 但是不建议这么操作 [未知具有特殊含义 数据库不知道他们是否匹配]

可以将 默认值 NULL 修改为字符串null 然后 where day <> 'null'

#### 操作符 OR  AND IN

```javascript
select uid from tablea where day = 20180412 or day =20180414
```

```javascript
select uid from tablea where day = 20180412 and city = 'th'
```

```javascript
select uid from tablea where uid in (123, 345, 567)
```

#### NOT

用于否定跟在之后的条件 

### 插入

#### insert into

```
`insert into ${TABLE} (uid, name) values (${uid}, ${name})`

```

如果想一次批量插入 先将语句利用循环进行拼接

```javascript
let all = []
end.forEach((uid, name) => {
  all.push(`(${uid}, ${name})`)
})

`insert into ${TABLE} (uid, name) values (${all.join(',')})`

```

insert into 中如果有一列是 主键 则如果插入相同值的时候 会报错

#### insert ignore

没有就进行插入 有就不做任何操作

* 这个一般会有性能问题 不建议使用


#### 插入检索出来的数据

从TABLEA 检索数据 放入 TABLEB 中

* 从A中查询出所有数据然后 在放入 TABLEB中

* 直接放入从A检索处理来的数据

```
INSERT INTO ACTIVITY20180529_ANCHOR (uid) SELECT uid FROM ACTIVITY20180529_ROOM

```

注意，通过这种方式插入的列数据 不是根据列名去插入 而是根据写的位置去插入  比如

```
INSERT INTO TABLEA (uid, city, time) SELECT city, uid, day FROM TABLEB 

```
如果是这样写的话 从TABLEB 中查询出来的 city 列 会进入 TABLEA 的 uid 列

### 更新

```javascript

update ${TABLE} set name = ${name} where uid = 4 limit 10

```

这个更新有一个问题  无法批量进行更新

### 删除

```javascript
`DELETE FROM ${TABLE} WHERE id=3`
```


##  日期和时间处理函数

### 时间戳 && 时间

* 时间戳 -》时间

如果表中存放的是时间戳 但是想获取的是普通时间格式

```javascript
`select FROM_UNIXTIME(1516243968)`
```

* 时间 -》时间戳

```javascript
`select UNIX_TIMESTAMP('2017-01-01')`
```

## 汇总函数

| 含义  | 操作符 |
| --- | --- | --- | --- |
| 求和 | SUM |
| 返回某列的行数 | COUNT |

* sum 

求和

比如 计算某一个用户收到的礼物总和

```javascript
SELECT SUM(beans) FORM TABLE WHER UID = 131
```


* count 

计算行的出现次数

比如，筛选某一个用户抽奖次数

```javascript
SELECT COUNT(uid) FORM TABLE WHER UID = 131
```

注： 如果使用的 COUNT(*) 则将会包含 NULL 值的那些行  如果不是用时*  那么会忽略掉 NULL  行


##  分组 GROUP BY


GROUP BY xxx
