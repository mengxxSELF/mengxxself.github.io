---
title: redis-数据类型及其使用
date: 2017-10-12 17:33:23
categories: 数据库
tags: [redis]
---

redis 是目前流行数据库的一种

### 数据类型

String、 Hash、 List、 set、 sotred set

字符串类型  哈希  列表  集合 有序集合

<!--more-->

### 字符串

字符串类型是Redis中最基本的数据类型  它可以存储任何形式的字符串 包括二进制数据

可以存储用户的邮箱 JSON对象甚至图片 一个字符串键最大能存储512MB

字符串类型是其他4种数据类型的基础   差别从某些角度来说 只是组织字符串的形式不同

比如 列表类型List 是以列表形式组织字符串  而集合类型Set则是以集合形式

Redis字符串命令用于管理Redis中的字符串值

基本使用方式如下

```javascript
let liveRedis = reqs('liveRedis')
yield liveRedis.COMMAND(KEY_NAME)
 ```

#### Redis 字符串命令

##### 设置 与 获取

set 设置值
setnx  key不存在则设置其值
get 获取值
getset 设置并且将旧值返回
mset 设置多个值
msetnx key不存在时设置多个值
mget 返回多个key值

1.1  set key value  设置指定key值

```javascript
liveRedis.set(name,'tom')
```

2.2 setnx key不存在时设置其值

```javascript
liveRedis.setnx(name,'tom')
```

将 key 的值设为 value ，当且仅当 key 不存在。

若给定的 key 已经存在，则 不做任何动作。

setnx -> 『SET if Not eXists』(如果不存在，则 SET)的简写。

1.3 get key  获取指定key值

```javascript
liveRedis.get(name)
```

当键不存在时候 返回null   如果key 储存的值不是字符串类型，返回一个错误

1.4 getset 设置值并返回旧值

如果key不存在 返回null

1.5 mset 设置多个值

用于同时设置一个或多个 key-value 对

```javascript
MSET key1 value1 key2 value2 .. keyN valueN
```

1.6 msetnx 当key值不存在时 设置其值

用于所有给定 key 都不存在时，同时设置一个或多个 key-value 对

```javascript
MSETNX key1 value1 key2 value2 .. keyN valueN
```

所有 key 都成功设置，返回 1 。 如果所有给定 key 都设置失败(至少有一个 key 已经存在)，那么返回 0

1.7 mget key1 key2 ..获取所有指定建的值

对于不包含字符串值或不存在的每个键，返回特殊值 - nil

```javascript
liveRedis.mget(name , age ,habit ,valentines)
```

返回值为一个包含所有给定 key 的值的列表

2 设置值以及有效期

setex 设置key值 并以秒为单位指定过期时间
psetex 设置key值 并以毫秒为单位指定过期时间

2.1 setex

在Redis键中的指定超时，设置键的字符串值

```javascript
SETEX KEY_NAME TIMEOUT KEY_VALUE
```

为指定的 key 设置值及其过期时间。如果 key 已经存在， SETEX 命令将会替换旧的值

设置成功 返回值为OK  否则返回错误

注意 此命令以秒为单位设定过期时间

2.2 psetex

该命令类型与setex类似，但是使用毫秒指定过期时间

### 哈希 hash

Redis以键值对的形式存储数据   而数据结构之一的哈希类型也是键值对形式存储数据

Hash类型适合存储对象 使用对象类别和ID构成键名 使用字段表示对象属性 而字段值则存储属性值

Hash存储了字段和字段值的映射 但是字段值只能是字符串 不支持其他数据类型



Redis 中每个 hash 可以存储 232 - 1 键值对

let liveRedis = reqs('liveRedis')


#### Redis hash命令

##### 设置

1.1 HSET  哈希表中指定字段的进行赋值

HSET key filed value

设置一个用户信息

```javascript
hset let startT = yield liveRedis.hset('user', 'name','tom')
```

hset的命令不区分插入和更新操作  这意味着修改数据不用事先判断字段是否存在来决定要执行的是插入还是更新操作

如果执行的是插入[之前该字段并不存在] 则返回的是1  如果执行的是更新[之前该字段存在] 则返回的是0

如果该键不存在 则会自动创建此键

1.2 HSETEX  字段不存在则设置其值

```javascript
HSET key filed value
```

与HSET命令一样，HSETEX同样会设置哈希表key的field字段值为value。但仅当field不存在才会设置。如果field字段已经存在，该操作无效

1.3  HGET  获取存储在哈希表中指定字段的值

```javascript
HGET key filed
```

返回哈希表key中field的值

获取到car字段的price属性

```javascript
let startT = yield liveRedis.hget('car', 'price')
```

1.4 HGETALL

HGETALL key

获取存储在哈希表key中全部字段以及其值

```javascript
let info = yield liveRedis.hgetall('car')
```

一般在想要获取全部字段 又不知道有哪些字段时候使用

这个其实返回的不是很直观 不过在node中 可以将其处理为合适的对象格式

```javascript
liveRedis.hgetall('car',function(error,car){
  // 已经将其封装为一个对象了
  console.log(car.price)
})

```


2 批量操作

2.1 HMSET  批量设置多个字段以及值

```javascript
HMSET key field value [field value ...]
```

将一个或多个field-value对设置到哈希表key。

如果要设置的field已存在，则会覆盖其值。如果哈希表不存在，首先会创建再执行HMSET操作。

2.2 HMGET  获取存储在哈希表中多个字段的值

```javascript
HMGET key field [field ...]
```

返回哈希表key中，一个或多个指定的定段。如果指定的字段在哈希表中不存在，则返回一个nil

比如

```javascript
let info = yield liveRedis.hmget('car', 'price', 'time', 'user')
```

hlen 获取哈希表中字段的数量

HLEN KEY_NAME

哈希表中字段的数量。 当 key 不存在时，返回 0

```javascript
let len = yield liveRedis.hlen('car')
```

### List

Redis的列表类型List是有序的字符串列表。

LIST 内部使用双向链表实现  可以在其头部(左边)和尾部(右边)添加新的元素  而获取数据越位于链表两端的时候 速度就越快

操作列表元素时，如果是从链表的两头插入或删除元素，操作效率会非常高。即使列表中已存储了百万条数据，该操作也可以在常量短的时间内完成

比如在千万个数据的表中获取前后20个的速度与从具有100个的表中获取前后20个速度一样

不过使用链表的代价是通过索引访问元素比较慢

比如 将元素插入列表中间或是删除位于链表中间元素，那操作效率会非常的低



#### Redis List命令

##### 插入新元素

1.1 LPUSH 向开始位置插入元素 LPUSH

```javascript
lpush key value [value...]
```

将一个或多个值value插入到列表key的开始位置

如果有多个value，那么从左到右依次插入列表

如果列表key不存在，首先会创建一个空列表再执行LPUSH操作

命令执行成功后，返回列表的长度

如果key存在，但不是List类型，会返回一个错误提示

1.2  如果列表存在则将元素插入表头 lpushx

```javascript
lpushx key value
```

将一个或者多个value插入列表头部位置

如果列表key存在且是List类型，则将值value插入到列表key的头部

如果列表key不存在，则无操作

1.3 将指定元素插入列表末尾 RPUSH

此命令同lpush类似 只不过是插入元素的位置不同

1.4  如果列表存在则将元素插入表尾 RPUSHX

```javascript
rpushx key value
```

##### 取值

2.1 LPOP

左侧弹出一个数值

2.2 RPOP

右侧弹出一个数值

2.3 获取列表元素个数

```javascript
LLEN numbers
```

当键不存在 则返回0

2.4 获取列表片段

```javascript
LRANGE key start end
```

它用来获取列表中某一片段 将返回此片段内所以的元素（包含两端的元素）

Redis列表的起始索引为0

lrange 命令支持索引为负数 表示从右侧开始读取元素

```javascript
lrange user -2 -1
```

截取右侧第二个 到右侧第一个

所以获取列表中所有的值 使用

```javascript
lrange user 0 -1
```

然后注意start 与 stop的大小处理

如果start > stop 则返回空列表

如果 stop 大于实际的索引 则只是返回所有的元素

2.5  通过索引获取列表中指定位置的元素 lindex

```javascript
lindex key index
```

##### 删除列表中值

3.1 LREM

LREM key count val

删除列表中val的值 删除个数为count

返回删除的元素个数


### Set 集合

Redis中的集合(Set)类型类似于List类型

Set是无序列的   集合成员是唯一的，这就意味着集合中不能出现重复的数据。如果多次添加相同元素，Set中将仅保留该元素的一份拷贝

和List类型一样，我们可以对集合类型进行元素的添加、删除或判断元素是否存在等操作

```javascript
let liveRedis = reqs('liveRedis')
```
#### Redis 集合命令

##### 1 添加元素 成员之间的操作

sadd 添加元素
srem 移除元素

1.1  sadd 往集合中添加元素

```javascript
sadd key member [member ...]
```

将一个或多个元素member添加到集合key中，如果要添加的元素在集合中已存在，那么该元素将被忽略

如果集合key不存在，那么包含元素member的集合会被创建

返回值 是被添加到集合中的新元素的数量，不包括被忽略的元素

```javascript
yield liveRedis.sadd("cars" ,"tout")
```

1.2  移除元素

```javascript
SREM key member [member ...]
```

返回值：被移除的元素数量；如果key不是集合类型，会返回一个错误

2 查询元素

scard 集合元素数
smembers 返回集合成员
sismember  判断元素是不是集合的成员

2.1 获取集合中元素数量  scard

scard key
返回值 是集合的数量。 当集合 key 不存在时，返回 0

```javascript
yield liveRedis.scard("cars" )
```

2.2  SMEMBERS - 返回集合中成员

smembers key
Redis Smembers 命令返回集合中的所有的成员。 不存在的集合 key 被视为空集合

```javascript
liveRedis.sadd('followers','tom','jury','hell')
let users = yield liveRedis.smembers('followers')
// tom jury hell
```

2.3  SISMEMBER - 判断元素是否是集合的成员

```javascript
sismember key member
```

如果member是集合成员，返回1 如果不是集合成员或集合不存在，返回0

```javascript
liveRedis.smembers('followers')
// tom juar hui
let isM = liveRedis.sismember('fallowers','hui') // 1
```
项中使用案例 比如将某一天完成指定任务的用户存储在当天的key中 通过此命令可以判断当前用户是否已经完成了今日任务

3 集合之间的操作

sdiff 差集 A-B
sinter 交集
sunion  并集 A+B

3.1 一个或者多个集合的差集

```javascript
SDIFF key [key ...]
```

返回值为 差集的成员列表  也就是 A-B

比如当前有集合A与B A中元素为 1 2 3 B中元素为 2 3 4

计算A与B的差集

SDIFF A B

返回值为1

如果命令为

SDIFF B A

则返回值为 4

3.2 对多个集合进行交集运算

```javascript
SINTER key [key ...]
```

返回值为交集的成员列表

3.3对多个集合进行并集运算

```javascript
SUNION key [key ...]
```
### Redis 有序集合(sorted set)

Redis 有序集合和集合一样也是string类型元素的集合,且不允许重复的成员。

不同的是每个元素都会关联一个double类型的分数。redis通过分数来为集合中的成员进行从小到大的排序



有序集合的成员是唯一的,但分数(score)却可以重复

sort set 和 LIST 类型在某些方面相似 两者都是有序的 两者艘可以获取某一范围的元素

但是两者的使用场景不同

1 LIST 更适合于实现‘新鲜事’或者‘新日志’  而不是获取中间元素信息

2 sort set 的内部机制使其即使访问中间位置元素 速度也快

3 LIST 不能简单的更改元素位置 但是SORT SET可以简单的变动score来更改位置

4 SORT SET 比LIST耗费内存

```javascript
let liveRedis = reqs('liveRedis')
```

#### Redis 有序集合命令

#####  添加 修改集合元素

1 zadd 添加元素

```javascript
ZADD KEY_NAME SCORE1 VALUE1.. SCOREN VALUEN
```

Redis Zadd 命令用于将一个或多个成员元素及其分数值加入到有序集当中。

如果某个成员已经是有序集的成员，那么更新这个成员的分数值，并通过重新插入这个成员元素，来保证该成员在正确的位置上。

分数值可以是整数值或双精度浮点数。

如果有序集合 key 不存在，则创建一个空的有序集并执行 ZADD 操作。

当 key 存在但不是有序集类型时，返回一个错误

```javascript
liveRedis.zadd('user_votes',23,'tom',21,'meary')
```

##### 查询

2.1  zcard - 返回集合数量  zcard (key)

返回值：有序集合key的基数；当集合不存在时，返回0

```javascript
liveRedis.zadd('user_votes',20,'tom2')
let num1 = liveRedis.zcard('user_votes') // 1
liveRedis.zadd('user_votes',29,'tom3')
let num1 = liveRedis.zcard('user_votes') // 2
```


2.2 zrank  返回元素排名

ZRANK key member

返回有序集合key中的元素member的排名。元素成员按score值递增，相同score值的成员按字典排序。元素排名从0开始计数

显示所有成员及其 score 值

```javascript
ZRANGE keyname 0 -1 WITHSCORES
```


2.3 ZSCORE - 返回指元素的权重

ZSCORE key member
返回有序集合key中，元素member的score值

2.4 ZCOUNT - 返回集合两个权重间的元素数

ZCOUNT key min max
返回有序集合key，score值在min和max之间的元素数(包含值为min和max的元素)

2.5 ZRANGE - 返回指定区间内的元素

ZRANGE key start stop [WITHSCORES]
返回有序集合key指定区间内的元素。元素成员按score值递增，相同score值的成员按字典排序。

start和stop都是从0开始。当使用负数时，表示从集合的末尾开始计数

WITHSCORES用于指定是否同时返回元素的score

2.6 ZREVRANGE - 倒序返回指定区间内的元素

ZREVRANGE key start stop [WITHSCORES]
返回有序集合key中，指定区间内的成员。score值按倒序(从大到小)顺序排序

2.7 ZRANGEBYSCORE - 返回指定权重区间内的元素

ZRANGEBYSCORE key min max [WITHSCORES] [LIMIT offset count]
返回有序集合key指定区间内的元素。元素成员按score值递增，相同score值的成员按字典排序。

可选参数LIMIT用于指定返回元素数量，offset用于指定便移量


##### 移除元素

3.1 ZREM - 移除元素

```javascript
ZREM key member [member ...]
```

移除有序集合key中的一个或多个元素member的排名，不存在成员将被忽略。

移除全部元素

```javascript
ZRANGE page_rank 0 -1 WITHSCORES
```

3.2 ZREMRANGEBYRANK - 移除指定区间内的元素

ZREMRANGEBYRANK key start stop
移除有序集合key中，指定排名(rank)区间内的元素。

start和stop用于指定元素区间，start和stop包含在区间内。start和stop的底数以0开始，也可以使用负数，如-1表示最后一个元素，依次类推
