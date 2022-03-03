title: Eggjs数据库操作
category: 技能
date: 2019-08-14
index_img: 

---

Eggjs数据库操作

<!--more-->

## 增删改查

#### 增

```js
await this.ctx.model.Todo.create(params);
```

####  删

```js
await this.ctx.model.Todo.deleteOne({ _id: id });
```



#### 改

```js
await this.ctx.model.Todo.updateOne({ _id }, params);
```



#### 查

```js
await this.ctx.model.Todo.find({ id: id });
await this.ctx.model.User.findOne({ name, pwd});
```





## 设置Schema

```js
'use strict';

module.exports = app => {
  const mongoose = app.mongoose;
  const Schema = mongoose.Schema;
  // 下面得操作是连接数据库
  const TodoSchema = new Schema({
    // 修改和新增用到，规定字段得类型和其他条件等
    // _id: {
    //   type: Schema.ObjectId,
    //   default: mongoose.Types.ObjectId(),
    //   required: true,
    // },
    id: {
      type: String,
    },
    userId: {
      type: String,
      required: true,
    },
    userName: {
      type: String,
      required: true,
    },
    content: {
      type: String,
      required: true,
    },
    createTime: {
      type: String,
      default: Date.now(),
      required: true,
    }
  }, { versionKey: false });

  return mongoose.model('Todo', TodoSchema, 'todo');
};
```

