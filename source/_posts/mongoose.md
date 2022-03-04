
title: Mongoose操作命令
category: 技能
tags: mongoose
date: 2019-08-14
index_img: 

---

Mongoose操作命令

<!--more-->

### 增

```js
insertOne: db.XXX.insertOne({}) //XXX表名
insertMany: db.XXX.insertMany({name:'zhangshan',age:19},{name:'lisi',age:20}) //增加多条
```

### 删

```js
deleteOne
deleteMany
```

### 改

```js
 updateOne: db.hello.updateOne({name:'zhangshan'},{$set : {age:20}}) //修改一条
 updateMany: db.hello.updateMany({name:'zhangshan'},{$set : {age:20}}) //修改多条
 {
    field: {id:747},
    data: {id:747,name:'kj',sex:'man',age:18}
 }
```

### 查

```js
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

### 使用 skip() 与 limit() 实现分页

```js
 1. pageNum 当前第几页
 2. pageSize 每页显示多少条
 3. totalPage 一共有几页       Math.ceil (totalSize/pageSize)
 4. totalSize 一共有多少条数据    db.XXX.find().count()
```

