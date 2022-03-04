title: VUE学习
category: 技能
tags: vue
date: 2019-08-14
index_img: /images/assets/1741752219-59c9b774a4ccf_articlex.png

---

VUE学习

<!--more-->

[参考地址](<https://cn.vuejs.org/>)

#### Vue生命周期

![1741752219-59c9b774a4ccf_articlex](/images/assets/1741752219-59c9b774a4ccf_articlex.png)

#### 计算属性

```html
<div id="example">
  <p>Original message: "{{ message }}"</p>
  <p>Computed reversed message: "{{ reversedMessage }}"</p>
</div>
```

```js
var vm = new Vue({
  el: '#example',
  data: {
    message: 'Hello'
  },
  computed: {
    // 计算属性的 getter
    reversedMessage: function () {
      // `this` 指向 vm 实例
      return this.message.split('').reverse().join('')
    }
  }
})

methods: {
  reversedMessage: function () {
    return this.message.split('').reverse().join('')
  }
}
```

> 我们可以将同一函数定义为一个方法而不是一个计算属性。两种方式的最终结果确实是完全相同的。然而，不同的是**计算属性是基于它们的响应式依赖进行缓存的**。只在相关响应式依赖发生改变时它们才会重新求值。这就意味着只要 `message` 还没有发生改变，多次访问 `reversedMessage` 计算属性会立即返回之前的计算结果，而不必再次执行函数。
>
> 使用computed在下次访问reversedMessage时，只要message没有变化，则直接返回上次计算的值，使用methods则每次都会重新计算，这里使用缓存效果更好



#### 侦听器

```js
var vm = new Vue({
  el: '#example',
  data: {
    message: 'Hello'
  },
  watch: {
    // 计算属性的 getter
    message: function (val) {
      // `this` 指向 vm 实例
      console.log(val)
    }
  }
})
```

> 虽然计算属性在大多数情况下更合适，但有时也需要一个自定义的侦听器。这就是为什么 Vue 通过 `watch` 选项提供了一个更通用的方法，来响应数据的变化。当需要在数据变化时执行异步或开销较大的操作时，这个方式是最有用的。
>
> 当监听的元素发生改变时触发



#### $emit

> 子组件可以通过调用内建的 [**$emit** 方法](https://cn.vuejs.org/v2/api/#vm-emit) 并传入事件名称来触发一个事件：

```js
<button v-on:click="$emit('enlarge-text')">
  Enlarge text
</button>
```

有了这个 `v-on:enlarge-text="postFontSize += 0.1"` 监听器，父级组件就会接收该事件并更新 `postFontSize` 的值。



**`Vue.nextTick(callback)`，当数据发生变化，更新后执行回调。**

**`Vue.$nextTick(callback)`，当dom发生变化，更新后执行的回调。**

```js
<ul id="demo">
    <li v-for="item in list">{{item}}</div>
</ul>
 
new Vue({
    el:'#demo',
    data:{
        list=[0,1,2,3,4,5,6,7,8,9,10]
    },
    methods:{
        push(){
            this.list.push(11);
            this.nextTick(function(){
                alert('数据已经更新')
            });
            this.$nextTick(function(){
                alert('v-for渲染已经完成')
            })
        }
    }})
```



### Store

[参考文档](https://www.cnblogs.com/qianduangaoshou/p/7009044.html)

- **state**  变量声明
- **mutations** 保存值得方法`不允许在组件内直接对state变量进行操作，只能通过方法对state进行赋值`
- **actions** 异步方法，异步调用`mutations`，中间可以进行其他操作
- **getters** getters是用于计算state的函数，这实际上是函数，使用这个函数我们可以对state进行处理
- **commit** 调用`mutations`方法
- **dispatch** 调用`actions`方法

> 在store中存在这state，这里面保存着所有组件之间共享的数据：这里面的状态是响应式的，如果store中的状态得到变化，那么相应的组件的状态也会得到变化；
>
> 我们通过mutations来实现改变store中的state
>
> 我们通过 store.state来获取状态对象，我们通过 store.commit来触发状态更新

```js
/**
 * store文件
 */
import API from '@/api'

const state = {
    demo: '',
    demos: []
}

const getters = {
    doDemos: state => {
      return state.demos.filter(todo => todo.done)
    }
}

const mutations = {
    setDemo(state, params){
        state.demo = 'data1';
    }
}

const actions = {
    getDemo(context,params){
        API.demo
          .getDemo(params)
          .then(({ data }) => {
            context.commit({
              type: 'setDemo',
              payload: data,
            });
          });
    }
}

export default {
  namespaced: true,
  state,
  getters,
  actions,
  mutations
};
```

```js
/**
 * 调用
 */
// 在单独构建的版本中辅助函数为 Vuex.mapState
import { mapState } from 'vuex'
 
export default {
  // ...
  computed: {
      ...mapState({
        demo: state => state.demo,
        // 箭头函数可使代码更简练
        count: state => state.count,

        // 传字符串参数 'count' 等同于 `state => state.count`
        countAlias: 'count',

        // 为了能够使用 `this` 获取局部状态，必须使用常规函数
        countPlusLocalState (state) {
          return state.count + this.localCount
        }
      })
  },
    methods: {
        fun1(){
       		this.$store.commit('setDemo', {data:''}); // 直接操作 mutations
        },
        fun2(){
            this.$store.dispatch('getDemo', {data:''}); // 操作 actions 异步操作 mutations
        },
        fun3(){
            this.demo; // 直接能拿到vux中demo得数据
        }
    }
}
```

