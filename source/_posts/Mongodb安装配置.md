
title: Mongodb安装配置
category: 技能
tags: 
  - centos
  - mongodb
date: 2019-08-14
index_img: 

---

Mongodb安装配置

<!--more-->

**针对Centos7**

安装好MongoDb之后记得配置防火墙ip

查看已经开放的端口：

```shell
firewall-cmd --list-ports
```

开启端口

```shell
firewall-cmd --zone=public --add-port=80/tcp --permanent
```

命令含义：

–zone #作用域

–add-port=80/tcp #添加端口，格式为：端口/通讯协议

–permanent #永久生效，没有此参数重启后失效

重启防火墙

**一定要重启，否则不生效**

```shell
firewall-cmd --reload #重启firewall
systemctl stop firewalld.service #停止firewall
systemctl disable firewalld.service #禁止firewall开机启动
firewall-cmd --state #查看默认防火墙状态（关闭后显示notrunning，开启后显示running）
```



### MongoDB 语句来操作数据库

```rust
show dbs  //显示数据库 （无内容数据库不显示）
db/db.getName() //查看当前所处数据库
use XXX  //创建/切换数据库

db.stats()  //显示当前db状态
db.dropDatabase() //删除当前数据库
```

#### 增

- insertOne
- insertMany

```csharp
- db.XXX.insertOne({}) //XXX表名 
- db.XXX.insertMany({name:'zhangshan',age:19},{name:'lisi',age:20})   //增加多条
```

#### 删

- deleteOne
- deleteMany

#### 改

- updateOne
- updateMany

```csharp
- db.XXX.updateOne(查询条件，修改内容)
- db.XXX.updateMany(查询条件，修改内容)

db.hello.updateOne({name:'zhangshan'},{$set : {age:20}}) //修改一条
db.hello.updateMany({name:'zhangshan'},{$set : {age:20}}) //修改多条
```

#### 查

```swift
db.XXX.find()  //查询所有记录 sql:select * from users

db.XXX.find(查询条件)
db.XXX.find({'age': 22}); //查询 age = 22 的记录  select * from users where age = 22;
db.XXX.find({age:{$gt:20}}) //查询年龄大于20的
db.XXX.find({age:{$lt:20}}) //查询年龄小于20的
db.XXX.find({age:{$gte:20}}) //查询年龄大于等于20的
db.XXX.find({age:{$1te:20}}) //查询年龄小于等于20的
db.XXX.find({'age': {$ne: 22}}); //查询 age != 22 的记录

db.XXX.find({'age': {$gte: 23, $lte: 26}}); //查询 age >= 23 并且 age <= 26
db.XXX.find({$or: [{age: {$gte: 23}}, {name: '张三'}]); //查询 age >= 23 或者 name == '张三'

db.XXX.find({'name': /mongo/}); //查询 name 中包含 mongo 的数据  select * from users where name like %mongo%;
db.XXX.find({'name': /^mongo/}); //查询 name 中已 mongo 开头的数据
db.XXX.find({'name': /mongo$/}); //查询 name 中已 mongo 结尾的数据

db.XXX.find({}, {name: 1, age: 1}); //查询 指定列 name 、age 的数据
db.XXX.find({age: {$gt: 25}}, {name: 1, age: 1}); //查询 指定列 name 、age 并且 age > 25

db.XXX.find().sort({age: 1}); //升序 倒序传-1

db.XXX.find({name:'zhangshan'},{age:20}) //查询 name = zhangsan , age = 20 的数据

db.XXX.find().limit(5); //查询 前5条数据
db.XXX.find().skip(10); //查询 10条以后的数据
db.XXX.find().limit(10).skip(5); //查询 在 5 - 10 之间的数据
db.XXX.find().count(); //查询 某个结果集的记录条数

db.XXX.findOne(); //查询第一条数据
```

#### 使用 skip() 与 limit() 实现分页

1. pageNum 当前第几页
2. pageSize 每页显示多少条
3. totalPage 一共有几页       Math.ceil (totalSize/pageSize)
4. totalSize 一共有多少条数据    db.XXX.find().count()

```css
第一页： db.XXX.find().skip((pageNum - 1) * pageSize).limit(pageSize)
第二页： db.XXX.find().skip(10).limit(10)
第三页： db.XXX.find().skip(20).limit(10)
第四页： db.XXX.find().skip(30).limit(10)
```

