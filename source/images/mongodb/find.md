## [find()](https://mongoosejs.com/docs/api/model.html#model_Model.find)

```
Model.find(filter[, projection][, options][, callback])
```

### 参数一：filter

查询条件使用 JSON 文档的格式，JSON 文档的语法跟 [`MongoDB shell`](https://docs.mongodb.com/manual/reference/method/db.collection.find/) 中一致。

```
{ field1: value1, field2: { operator: value2 } ... }
```

#### 1. 查找全部

​    

```
Model.find()
Model.find({})
```

#### 2. 精确查找

​                        

```
Model.find({author:'dora'})
```

#### 3. 使用[操作符](https://docs.mongodb.com/manual/reference/operator/query/#query-selectors) 

**对比相关操作符**

| 符号 | 描述                                         |
| ---- | -------------------------------------------- |
| $eq  | 与指定的值**相等**                           |
| $ne  | 与指定的值**不相等**                         |
| $gt  | **大于**指定的值                             |
| $gte | **大于等于**指定的值                         |
| $lt  | **小于**指定的值                             |
| $lte | **小于等于**指定的值                         |
| $in  | 与查询数组中指定的值中的任何一个匹配         |
| $nin | 与查询数组中指定的值中的任何一个都**不**匹配 |

​                        

```
Model.find({ age: { $in: [16, 18]} })
```

返回 `age` 字段等于 `16` 或者 `18` 的所有 document。

**逻辑相关操作符**

| 符号 | 描述                                   |
| ---- | -------------------------------------- |
| $and | **满足**数组中指定的**所有**条件       |
| $nor | **不满足**数组中指定的**所有**条件     |
| $or  | 满足数组中指定的条件的**其中一个**     |
| $not | 反转查询，返回**不满足**指定条件的文档 |

​                        

```
// 相关语法
{$and:[ {expression1},{expression2}, ... ,{expressionN} ]}
{$nor:[ {expression1},{expression2}, ... ,{expressionN} ]}
{$or:[ {expression1},{expression2}, ... ,{expressionN} ]}
{field: { $not: { <operator-expression> }}}
```

逻辑操作符中的比较包括字段不存在的情况。

​                        

```
Model.find( { age: { $not: { $lte: 16 }}})
// 返回 age 字段大于 16 或者 age 字段 不存在 的文档
```

**字段相关操作符**

| 符号                                                         | 描述                                                  |
| ------------------------------------------------------------ | ----------------------------------------------------- |
| $exists                                                      | 匹配**存在**指定字段的文档 `{ field: { $exists:  } }` |
| [$type](https://docs.mongodb.com/manual/reference/operator/query/type/#available-types) | 返回字段属于指定**类型**的文档 `{field: { $type:  }}` |

#### 4. 嵌套对象字段的查找

数据如下

​                        

```
{
  name: { first: "dora", last: "wang" }
}
```

精确匹配，顺序、字段都必须一致。

​                        

```
Model.find({ name: { last: "wang", first: "dora" } })
// [] 找不到数据
```

使用点语法，可匹配嵌套的字段，其中字段名必须用引号引起来。

​                        

```
Model.find({ 'name.last': 'wang' })
```

#### 5. 数组字段的查找

| 符号       | 描述                                                         |
| ---------- | ------------------------------------------------------------ |
| $all       | 匹配**包含**查询数组中指定的**所有**条件的数组字段           |
| $elemMatch | 匹配数组字段中的某个值满足 `$elemMatch` 中指定的**所有**条件 |
| $size      | 匹配数组字段的 length 与指定的大小一样的 document            |

数据如下

​                        

```
{ year: [ 2018, 2019 ] }
{ year: [ 2017, 2019, 2020 ] }
{ year: [ 2016, 2020 ] }
```

**查找数组中的至少一个值**

可使用精确查找写法 `{field: value}`

​                        

```
Model.find({ year: 2019 });
// { "_id" : ..., "year" : [ 2018, 2019 ] }
// { "_id" : ..., "year" : [ 2017, 2019, 2020 ] }
```

**查找数组中的多个值**

使用 `$all` 查找同时存在 `2019` 和 `2020` 的 `document`

​                        

```
Model.find({ year: { $all: [ 2019, 2020 ] } });
// { "_id" : ..., "year" : [ 2017, 2019, 2020 ] }
```

**操作符组合查询**

可使用操作符进行筛选，`{:{operator: value}}`，比如 `$in`

​                        

```
Model.find({ year: { $in: [ 2018, 2020 ] } });
// { "_id" : ..., "year" : [ 2018, 2019 ] }
// { "_id" : ..., "year" : [ 2017, 2019, 2020 ] }
```

使用操作符组合查询 `{:{operator1: value1, operator2: value2}}`

​                        

```
Model.find({ year: { $gt: 2019, $lt: 2018 } });
// { "_id" : ..., "year" : [ 2017, 2019, 2020 ] }
// { "_id" : ..., "year" : [ 2016, 2020 ] }
```

数组字段包含满足查询条件的元素，可以是不同元素分别满足条件也可以是同一个元素满足所有条件，如上例，是一个值满足大于2019的条件，另一个值满足小于2018的条件。

**$elemMatch 单个字段值满足所有查询条件**

`$elemMatch` 查找数组字段中的值同时满足所有条件的 `document`。

```
{field: { $elemMatch: { , , ... }}}
```

​                        

```
Model.find({ year: { $elemMatch: { $gt: 2016, $lt: 2018 } } })
// { "_id" : ..., "year" : [ 2017, 2019, 2020 ] }
```

**数组下标查询**

​                        

```
Model.find({ 'year.1': { $gt: 2019 } })
// { "_id" : ..., "year" : [ 2016, 2020 ] }
```

数组 `year` 的第二个值大于`2019`。

#### 6. 数组对象的查找

数据如下

​                        

```
{author: [{name: "dora", age: 18 },{name: "wang", age: 16 }]}
```

**精确查询**

精确匹配，顺序、字段都必须一致。

​                        

```
Model.find({ author: { name: "dora", age: 18 } })
```

**点语法查询**

​                        

```
Model.find({ 'author.age': { $gte: 18 } })
```

**$elemMatch 同时满足所有查询条件**

​                        

```
Model.find({ "author": {$elemMatch: {name: 'dora', age:{ $lt: 18 }}})
// []
```

### 参数二：projection

指定要包含或排除哪些 `document` 字段(也称为查询“投影”)，必须同时指定包含或同时指定排除，不能混合指定，`_id` 除外。

在 mongoose 中有两种指定方式，字符串指定和对象形式指定。

字符串指定时在排除的字段前加 `-` 号，只写字段名的是包含。

​                        

```
Model.find({},'age');
Model.find({},'-name');
```

对象形式指定时，`1` 是包含，`0` 是排除。

​                        

```
Model.find({}, { age: 1 });
Model.find({}, { name: 0 });
```

**使用 select() 方法定义**

​                        

```
Model.find().select('name age');
Model.find().select({ name: 0 });
```

### 参数三：options

​                        

```
// 三种方式实现
Model.find(filter,null,options)
Model.find(filter).setOptions(options)
Model.find(filter).<option>(xxx)
```

`options` 选项见官方文档 [`Query.prototype.setOptions()`](https://mongoosejs.com/docs/api.html#query_Query-setOptions)。

-  `sort`：按照[排序规则](https://docs.mongodb.com/manual/reference/bson-type-comparison-order/#bson-types-comparison-order)根据所给的字段进行排序，值可以是 `asc`, `desc`, `ascending`, `descending`, `1`, 和 `-1`。
-  `limit`：指定返回结果的最大数量
-  `skip`：指定要跳过的文档数量
-  [`lean`](https://mongoosejs.com/docs/tutorials/lean.html)：返回普通的 js 对象，而不是 `Mongoose Documents`。建议不需要 mongoose 特殊处理就返给前端的数据都最好使用该方法转成普通 js 对象。

​                        

```
// sort 两种方式指定排序
Model.find().sort('age -name'); // 字符串有 - 代表 descending 降序
Model.find().sort({age:'asc', name:-1});
```

`sort` 和 `limit` 同时使用时，调用的顺序并不重要，返回的数据都是先排序后限制数量。

​                        

```
// 效果一样
Model.find().limit(2).sort('age');
Model.find().sort('age').limit(2);
```

### 参数四：callback

#### 传入

Mongoose 中所有传入 `callback` 的查询，其格式都是 `callback(error, result)` 这种形式。如果出错，则 `error` 是出错信息，`result` 是 `null`；如果查询成功，则 `error` 是 `null`， `result` 是查询结果，查询结果的结构形式是根据查询方法的不同而有不同形式的。

`find()` 方法的查询结果是数组，即使没查询到内容，也会返回 `[]` 空数组。

#### 不传

不传入 `callback` 时，查询方法返回的是一个 `Query` 实例，实例继承了 [`Query` 原型](https://mongoosejs.com/docs/api/query.html) 上的所有方法，因此返回的实例可以链式调用其它方法，从而组成查询链。

​                        

```
let query = Model.find({ name: 'Dora' });

query.select('name age -_id');
```

查询方法不传入回调函数时，获取查询数据的方式有两种：

**1. exec()**

使用 `query` 实例的 `exec()` 方法执行查询，即在链式语法的最后统一通过传入 `callback` 获取查询数据。

​                        

```
// 效果一样
Model.find(
  { name: /Dora/, age: { $gte: 16, $lt: 18 } },
  'name age -_id',
  { limit: 2, sort: 'age' },
  (err,res)=>{
    if (err) return handleError(err);
    console.log(res);
  }
});

let query = Model.
  find({ name: /Dora/ }).
  where('age').gte(16).lt(18).
  select('name age -_id').
  limit(2).sort('age');

  query.exec((err, res)=> {
    if (err) return handleError(err);
    console.log(res);
  });
```

**2. then()**

使用 `query` 实例的 `then()` 方法将查询链当作 `promise` 来处理。

​                        

```
query.then(
  (res) => {console.log(res)},
  (err) => {console.log(err)}
);
```

使用 `async / await` 获取查询结果。

​                        

```
let res = await query;
console.log(res);
```

## [findOne()](https://mongoosejs.com/docs/api/model.html#model_Model.findOne)

```
Model.findOne(conditions[, projection][, options][, callback])
```

### conditions

如果查询条件是 `_id`，建议使用 `findById()`。

### options 选项有限

并不是所有 `options` 都可以用，因此链式调用 `Query` 原型上的方法时，也只能调用可用的方法。参考 [`Query.prototype.setOptions()`](https://mongoosejs.com/docs/api.html#query_Query-setOptions)。

### result 查询结果

- 返回数据的格式是 `{}` 对象形式。
- 有多个数据满足查询条件的，只返回第一条。
- 查询条件 `conditions` 为 `{}`、 `null` 或 `undefined`，将任意返回一条数据。
- 没有符合查询条件的数据，`result` 返回 `null`。

## [findById()](https://mongoosejs.com/docs/api/model.html#model_Model.findById)

```
Model.findById(id[, projection][, options][, callback])
```

### id

`Model.findById(id)` 相当于 `Model.findOne({ _id: id })`。

?? 摘自官方

> 不同之处在于处理 `id` 为 `undefined` 时的情况。`findOne({ _id: undefined })` 相当于 `findOne({})`，返回任意一条数据。而 `findById(undefined)` 相当于 `findOne({ _id: null })`，返回 `null`。

测试结果为 `findOne({ _id: undefined })` 依旧返回 `null`。所以也可能是 mongoose 版本的问题，但是即使数据返回正常，依然建议 `_id` 查询使用 `findById()`。

### result 查询结果

- 返回数据的格式是 `{}` 对象形式。
-  `id` 为 `undefined` 或 `null`，`result` 返回 `null`。
- 没符合查询条件的数据，`result` 返回 `null`。

作者：Dora36
        链接：https://segmentfault.com/a/1190000021010300?utm_source=tag-newest
        来源：SegmentFault 思否
        著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。