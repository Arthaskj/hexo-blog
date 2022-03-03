title: 返回Promise.then的值
category: 技能
date: 2020-10-22

---

返回Promise.then的值

<!--more-->

1.采用了ES7 语法 —— `async` `await`
2.下述代码是在 `React` 中运行的，调用函数采用的`this.funcName()`
3.获取 `Promise` 更常用的场景是在 `Redux` 中。在 `action` 中，返回 `promise` 状态值，在 `container` 组件中获取。

```js
fetchPromiseValue = () => {
    return new Promise(function(resolve, reject) {
        resolve({value: 'PromiseReturnValue'});   // Promise 抛出状态值
    })
};

handlePromise = async () =>{
    let  getValue  = await this.fetchPromiseValue();  // 异步获取 Promise  抛出的状态值
    console.log('Promise 的状态值： ',getValue.value);
};
```

