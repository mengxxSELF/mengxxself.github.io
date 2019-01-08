---
title: sequelize入门篇
date: 2018-10-21 10:43:26
tags:
---

![sequelize](https://user-gold-cdn.xitu.io/2018/10/22/16699aab6d18add9?w=774&h=380&f=png&s=44639)


> ORM框架 --- sequelize 用于将数据库的表关系映射到对象

<!-- More -->

mysql的一个表的数据结构如下

![mysql](https://user-gold-cdn.xitu.io/2018/10/17/16680010bf1d2e17?w=296&h=181&f=png&s=13797)

每一行其实是一个对象

```js
{
    id: 3,
    name: 'tom',
    password: 123
}
```
这个就是 ORM 框架做的事情

ORM使我们读写的都是JavaScript对象，Sequelize帮我们把对象变成数据库中的行

### 储备知识

#### 本地安装 mysql

网上提供了两种方式，文件安装或者使用 homebrew，先使用文件安装，之后一直无法启动。后来删除之后使用brew重新安装

有的Mac可以直接使用

```js
brew install mysql
```

但是我的一直安装失败，查阅资料发现，是因为系统没有升级，所以不能这么处理 需要指定安装mysql的版本

```js
brew search mysql // 查看有哪些版本可安装

brew install mysql@5.7

brew services start mysql@5.7
```

安装之后启动时mysql

```
mysql.server start
```

关于其中会一直报错 命令找不到，需要调整 全局变量 修改了两个文件

```
export PATH="/usr/local/opt/mysql@5.7/bin:$PATH"
```


登录mysql

```
mysql -u root -p
```
这个方式是需要输入 mysql 密码的，但是我并没有注意到安装mysql的时候 有提示密码是什么。。。


更换登录方式

```
mysql -u root mysql
```
这个命令虽然可以登录，但是我们想使用sequel pro 本地连接，还是需要密码的，所以找了一个重置密码的方式

[参考文章](https://www.cnblogs.com/jiuyi/p/6211271.html)

```js
// 停止mysql
mysql.server stop

// 进入安全模式

sudo mysqld_safe --skip-grant-tables

// 登录mysql
mysql -u root mysql

// 切换数据库
use mysql;

// 修改 密码 -- 密码字段为 authentication_string

UPDATE mysql.user SET authentication_string=PASSWORD('mypassword') where User='root';

// 刷新权限，使配置生效

flush privileges;

// 启动 MySQL

mysql.server start
```

总结一下，就是修改 mysql 库的user表中 User 为 root 的 authentication_string 字段值

#### sequel pro 可视化工具链接mysql

mysql这种表结构一般使用 可视化工具方便查看信息

![mysql](https://user-gold-cdn.xitu.io/2018/10/17/16680258114aa571?w=992&h=732&f=png&s=87561)

这里的host 不可以使用 localhost

![](https://user-gold-cdn.xitu.io/2018/10/17/1668026c4729dbfd?w=806&h=404&f=png&s=104581)

### mysql

普通的直接使用mysql链接

```js
const mysql = require('mysql')

const mysqlConfig = {
  host: ['127.0.0.1'], // 网址ip
  port: 3306,  // 端口
  user: 'root',  // 用户
  password: '5211314mxx', // 密码
  database: 'activity', // 数据库
  key: 'mysql',
}
const pool = mysql.createPool(mysqlConfig)

let query = function( sql, values ) {
  return new Promise(( resolve, reject ) => {
    pool.getConnection(function(err, connection) {
      if (err) {
        resolve( err )
      } else {
        connection.query(sql, values, ( err, rows) => {
          if ( err ) {
            reject( err )
          } else {
            resolve( rows )
          }
          connection.release()
        })
      }
    })
  })
}
```



### sequelize

> Sequelize是一个关系型数据库ORM框架，它基于Promise构建。支持MySQL、PostgreSQL、MariaDB、SQLite和MSSQL几种关系型数据库，功能非常强大。


安装包

```js
npm i sequelize mysql2
```

sequelize 类对象，通过new关键字进行实例化，以连接池的形式连接到所使用的数据库

创建一个实例

```js
const sequelizeObj = new Sequelize(数据库， 用户名， 密码， 配置参数config )
```

sequelize 实例API


* define 定义模型

* getDialect 返回数据库类型

* models 返回所有模型


```js
sequelizeObj.models()
```

* import 导入模型

```js
sequelize.import(path) -> Model
```

* query  执行SQL语句的查询

```js
sequelize.query(sql, [options={}]) -> Promise
```

* sync 同步模型到数据库

```js
sequelize.sync()
```

* drop 删除表
*



#### 链接mysql表

```js
const Sequelize = require("sequelize")

const sequelizeObject = new Sequelize('activity', 'root', '5211314mxx', {
  host: 'localhost',
  port: '3306',
  dialect: 'mysql',
  pool: {   //连接池设置
    max: 5, //最大连接数
    min: 0, //最小连接数
    idle: 10000
  },
  define: {
    underscored: false, // 驼峰命名
    timestamps: true, // 时间戳
    freezeTableName: true // Model 对应的表名将与model名相同
  }
})

```

参数含义

| 属性 | 含义 |
| ------ |  ----- |
| host | 数据库地址,默认本机 |
| dialect | 数据库 'mysql'|'mariadb'|'sqlite'|'postgres'|'mssql' |
| pool | 连接池设置 |
| logging | 是否将SQL语句输出到命令行 |
| define | 定义一些其余属性 |

| define 参数 | 含义 |
| ------ |  ----- |
| freezeTableName | 在查询的时候，sequelize可能会使用复数的表名 比如 use-> users , 设定为TRUE 则不会使用这种复数的表名 |


其实还可以将配置参数简写为一个

```js
new Sequelize('mysql://root:5211314mxx@127.0.0.1/activity', {})
<!-- 数据库类型 用户名 密码  IP databases  -->
```

当实例化对象后，就可以通过其返回的sequelize实例定义Model、执行query查询


#### 模型

定义表结构  define 就是常见一个表模型

define 用于定义模型和表的映射关系

使用方式

obj.define('你的表名', {字段配置 segConfig }, {设置其余信息 tableConfig})

```js
// 定义模型
const User = sequelizeObject.define('eyes_users', {
  id: {
    type: Sequelize.INTEGER,
    primaryKey: true
  },
  name: Sequelize.STRING(100), // 用户名
  password: Sequelize.INTEGER(100) // 密码
}, {
  timestamps: false
})
```

* segConfig 常用参数

| 属性 | 属性值以及含义 |
| ----- | ------ |
| type | 当前字段类型 |
| defaultValue | 默认值 |
| allowNull | 是否可以为null  |
| primaryKey  | 是否是主键  |
| autoIncrement | 是否是自增 |
| comment | 相当于注释 |
| unique | 是否是索引  |


* tableConfig 常用参数

| 属性 | 属性值以及含义 |
| ----- | ------ |
| timestamps | 是否添加时间戳, 启用该配置后会自动添加createdAt、updatedAt两个字段，分别表示创建和更新时间 |
| underscored | 自动添加的字段会在数据段中使用“蛇型命名”规则，如：createdAt在数据库中的字段名会是created_at |
| paranoid | 是否使用虚拟删除。启用该配置后，数据不会真实删除，而是添加一个deletedAt属性 |

### 常用的数据类型  Datatypes

| 类型 | 含义 |
| ----- | ------ |
| STRING | 字符串 最长255 |
| STRING(123) | 字符串 最长123 |
| TEXT | 文本格式 |
| DATE | 时间 |
| FLOAT | 浮点数 |
| TIME | 日期时间 |
| ARRAY | 数组 |
| BOOLEAN | 布尔类型 |
| ENUM | 枚举 |

```js
const User = sequelize.define('eyes_user', {
    id: {
    <!-- 主键 -->
        type: Sequelize.NUMBER, primaryKey: true
    }
    registerTime: {
    <!--时间  默认值为创建时间-->
        type: Sequelize.DATE, defaultValue: Sequelize.NOW
    },


})
```

### model 常用API

####  sync  同步模型到数据库

define 定义好了模型对象，这些表是无法自动在数据库中创建的，需要调用一下 sync 才能在对应数据库中创建这些表

```js
app.listen(port, async() => {
  // force:true  表示首先删除表再重新创建表
  await User.sync({ force: true }).then(() => {
    cosnole.log('created')  
  })
  console.log('the port is', port)
})
```

** 关于 sync方法 **

Model.sync()只会同步当前模型到数据库中

sequelize.sync()会同步sequelize实例中定义所有模型

#### create find update destory

常用的mysql使用 - 增删改查

##### 查

| 方法 | 含义 |
| ----- | ------ |
| findOne | 检索一个元素 |
| findAll | 检索所有 |
| findById | 根据id检索 |

```js
    const user = await User.findOne({
      where: {
        name
      },
      attributes: ['password']
    })
```

对应的sql语句

```js
select password from table where name = name limit 1
```

有的时候我们需要将检索出来的字段做命名处理

```js
select uid, user_age as age from table where id = 3
```
就可以写为

```js
user.findOne({
   where: {id: 3},
   attributes: {
    uid,
    [user_age, age]
   }
})
```
##### 增

* 单条记录增加

```js
  await User.create({
    name,
    password: encodePwd
  })
```

对应SQL语句

```js
insert into table (name, password) values (name, encodePwd)
```

* 批量增加

```js
await User.bulkCreate([
  {name: 'tom', birthday: '123'},
  {name: 'tom2', birthday: '1234'},
])
```

##### 修改

* 直接更新

```js
await Info.update({
    brithday: 20180101,
    sex: 'boy'
}, {
    where: { name }
})
```

对应sql语句

```js
udpate table set brithday = 20180101, sex = 'boy' where name = name
```

* 有则更新 没有则创建

upsert

```js
    await Info.upsert({
      birthday,
      userId
    }, {
      fields: ['birthday']
    })
```

SQL语句为

```js
 INSERT INTO `eyes_info` (`userId`,`birthday`) VALUES ('1','2088') ON DUPLICATE KEY UPDATE `birthday`=VALUES(`birthday`);
```

感觉这个和replace into 作用一样

```js
replace into tabale (`userId`,`birthday`) values (1, 100)
```

##### 删

```js
    await User.destroy({
      where: {id}
    }).then(() => {
      body = { code: 200, message: 'success' }
    }).then(() => {
      body = { code: 500, message: 'failture' }
    })
```

#### 条件语句

##### where

基本使用

```js
User.findAll({
    where: {
        id: [1, 2, 4],
        city: 'beijing',
        home: null
    }
})
```
where 字段中的对象， key: value 格式， 彼此之间属于 and 条件，也就是

key1 = v1 and key2 = v2

* key: null 意味着 key is null

* key: []   意味着  key in ()

* value 还可以是一个对象格式 可以进一步对字段进行约束，比如 >  like

betweent 等

```js

id: {
    $eq: 1 // = 1
    $ne: 1 // != 1
    $or: [
       [1, 2, 3],
       { $gt: 10 }
    ] // id in (1, 2, 3) or id > 10
}
```
| 操作符 | 含义 |
| ---- | ----- |
| $eq | =  |
| $gt | > |
| $gte | >= |
| $lt | < |
| $betweent | between 6 and 10 |
| $notBetween | not between 6 and 10  |
| $like | like 'xxx' |
| $or | or |

```js
where name != null

where age > 18

where uid in (134, 145)
```

##### 其余常用方法

* limit offset

```js
User.findAndCountAll({
  limit: 20,
  offset: 10
})
```

order 做排序

```js
User.findAll({ order: 'updateAt' })

User.findAll({ order: 'count DESC' })
```

group 做分组

```js
User.findAll({ group: 'uid' })
```

* count

统计元素个数

```js
Info.count()

Info.count({ where: { id: {$gt: 10} } })

```
* max min

```js
Product.min('id', { limit: 30, offset: 10 })
```

#### 表关系处理

建立三个表

| 表 | 含义 |
| ----- | ------- |
| User |  用户注册表 每注册一个用户 则存放一条数据 |
| Info |  用户信息表 一个用户UID 存放一条数据 |
| Group |  用户组 一个用户UID  可以加入多个 组  一个组可以有多个用户UID |

```js
// 用户注册表
const User = sequelizeObject.define('eyes_users', {
  id: {
    type: Sequelize.INTEGER,
    primaryKey: true
  },
  name: Sequelize.STRING(100), // 用户名
  password: Sequelize.INTEGER(100) // 密码
})

// 用户信息表
const Info = sequelizeObject.define('eyes_info', {
  userId: {
    type: Sequelize.INTEGER,
    primaryKey: true
  },
  birthday: Sequelize.STRING(100), // 生日
  age: Sequelize.INTEGER(100) // 年龄
})

// 用户组 一组可以有多个UID  同一个UID可以进不同group
const Group = sequelizeObject.define('eyes_group', {
  groupId: {
    type: Sequelize.INTEGER,
    primaryKey: true
  },
  uid: {
    type: Sequelize.INTEGER(100),
    primaryKey: true
  }
})

// vip   一个用户UID只能有一个vip等级
const Vip = sequelizeObject.define('eyes_vip', {
  vip: {
    type: Sequelize.INTEGER,
    primaryKey: true
  },
  uid: {
    type: Sequelize.INTEGER(100),
    primaryKey: true
  }
})

```

#### 关系

* 一对一关系

>  指一个实体的某个数据与另外一个实体的一个数据有关联关系

比如 User 与 Info 表

* 一对多

> 一对多与多对一是一个概念 指一个实体的某个数据与另外一个实体的多个数据有关联关系

比如 Vip 与 User 表

* 多对多关系

> 一个实体的数据对应另外一个实体的多个数据，另外实体的数据也同样对应当前实体的多个数据

比如 User 与 Group 表

#### 表连接

以 Info 表和Vip表为例

##### 内连接

```js
SELECT * FROM join_table
[INNER] JOIN join_table2
[ON join_condition]
WHERE where_definition
```

只列出这些连接表中与连接条件相匹配的数据行。INNER可以不写，则默认为内连接。[ON join_condition]里面写的是连接的条件。

比如 检索 vip 为 2 的 所有用户年龄

```js
select tableA.uid, tableA.birthday, tableA.age
   from eyes_info as tableA
   join eyes_vip as tabeleB join vip = 2 and uid = userId


+-----------------+----------+------------+
| uid  | birthday | age      |
+-----------------+----------+------------+
| 12   | 01-10    |       22 |
| 137  | 08-9     |       18 |
+-----------------+----------+------------+

```

##### 外连接

左外连接(LEFT [OUTER] JOIN)

右外连接(RIGHT [OUTER] JOIN)

不仅列出与连接条件（on）相匹配的行，还列出左表table1(左外连接)、或右表table2(右外连接)、或两个表(全外连接)中所有符合WHERE过滤条件的数据行。一般都是用左连接或者外连接

其中，[OUTER]部分可以不写，(LEFT | RIGHT | FULL)部分要写其中一个

```js
SELECT … FROM join_table1

(LEFT | RIGHT | FULL) [OUTER] JOIN join_table2

ON join_condition

WHERE where_definition
```

* 左外连接：左表列出全部，右表只列出匹配的记录

* 右外连接：右表列出全部，左表只列出匹配的记录
