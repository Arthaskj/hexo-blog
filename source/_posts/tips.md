title: 前端面试知识点
category: 知识点
date: <文章日期> [YYYY-MM-DD]

---

前端面试知识点积累

<!--more-->

# Tips

## require，import区别？
> require/exports 出生在野生规范当中，什么叫做野生规范？即这些规范是 JavaScript 社区中的开发者自己草拟的规则，得到了大家的承认或者广泛的应用。比如 CommonJS、AMD、CMD 等等。import/export 则是名门正派。TC39 制定的新的 ECMAScript 版本，即 ES6（ES2015）中包含进来。
>
> 
>
> 1、CommonJS 还是 ES6 Module 输出都可以看成是一个具备多个属性或者方法的对象；
>
> 2、default 是 ES6 Module 所独有的关键字，export default fs 输出默认的接口对象，import fs from 'fs' 可直接导入这个对象；
>
> 3、ES6 Module 中导入模块的属性或者方法是强绑定的，包括基础类型；而 CommonJS 则是普通的值传递或者引用传递。
>
> 
>
> CommonJS 作为 Node.js 的规范，一直沿用至今。由于 npm 上 CommonJS 的类库众多，以及 CommonJS 和 ES6 之间的差异，Node.js 无法直接兼容 ES6。所以现阶段 require/exports 任然是必要且实必须的。出自 ES6 的  import/export 相对就晚了许多。被大家所熟知和使用也是 2015 年之后的事了。 这其实要感谢 babel（原来项目名叫做 6to5，后更名为 babel） 这个神一般的项目。由于有了 babel 将还未被宿主环境（各浏览器、Node.js）直接支持的 ES6 Module 编译为 ES5 的 CommonJS —— 也就是 require/exports 这种写法 —— Webpack 插上 babel-loader 这个翅膀才开始高飞，大家也才可以称 " 我在使用 ES6！ "
>
> 
>
> 这也就是为什么前面说 require/exports 是必要且必须的。因为事实是，目前你编写的 import/export 最终都是编译为 require/exports 来执行的。



# nexttick什么时候用

> Vue是异步执行dom更新的，一旦观察到数据变化，Vue就会开启一个队列，然后把在同一个事件循环 (event loop) 当中观察到数据变化的 watcher 推送进这个队列。如果这个watcher被触发多次，只会被推送到队列一次。这种缓冲行为可以有效的去掉重复数据造成的不必要的计算和DOm操作。而在下一个事件循环时，Vue会清空队列，并进行必要的DOM更新。
> 当你设置 vm.someData = 'new value'，DOM 并不会马上更新，而是在异步队列被清除，也就是下一个事件循环开始时执行更新时才会进行必要的DOM更新。如果此时你想要根据更新的 DOM 状态去做某些事情，就会出现问题。。为了在数据变化之后等待 Vue 完成更新 DOM ，可以在数据变化之后立即使用 Vue.nextTick(callback) 。这样回调函数在 DOM 更新完成后就会调用。




# 严格模式（Strict Mode）
它不是一条语句，但是是一个字面量表达式，在 JavaScript 旧版本中会被忽略。
"use strict" 的目的是指定代码在严格条件下执行。
严格模式下不能使用未声明的变量。

消除Javascript语法的一些不合理、不严谨之处，减少一些怪异行为;
消除代码运行的一些不安全之处，保证代码运行的安全；
提高编译器效率，增加运行速度；
为未来新版本的Javascript做好铺垫。
"严格模式"体现了Javascript更合理、更安全、更严谨的发展方向，包括IE 10在内的主流浏览器，都已经支持它，许多大项目已经开始全面拥抱它。

另一方面，同样的代码，在"严格模式"中，可能会有不一样的运行结果；一些在"正常模式"下可以运行的语句，在"严格模式"下将不能运行。掌握这些内容，有助于更细致深入地理解Javascript，让你变成一个更好的程序员。



# 闭包（Closures）

**闭包**就是**可以访问另一个函数作用域中的变量函数**。

```js
function A(){
var money=10000;
    function set(){
money=money+10;
return money
}
function get(){
    money=money-10;
return money;
}
return {
'set':set,
'get':get
}
}
console.log(A.set())
```

优点：保护函数内的**变量安全**,加强了**封装性**，变量可以**一直存储在内存**中。

缺点：处理不好容易造成`内存泄漏`



# 异步编程（Async programming）和回调（callbacks）

JavaScript`synchronous`默认是，并且是`single threaded`。这意味着代码无法创建新线程并不能并行运行。

每一行代码都是依次执行的，这种执行流程也称为`Blocking Code/Mode`。

回调无处不在，不仅在DOM事件中使用。

一个常见的示例是使用计时器：

```js
setTimeout(() => {

  // runs after 2 seconds

}, 2000)
```



# 循环（Loops）和作用域（Scope）

作用域，就是变量或者是函数能作用的范围。除了函数中定义的变量之外，都是全局作用域。

```js
//经典例子
for(var i=0;i<4;i++){
    setTimeout(function(){
    	console.log(i);
    },200);
}
//4 4 4 4

//利用函数形成函数作用域
for(var i=0;i<4;i++){
    (function(j){
            setTimeout(function(){
    	console.log(j);
    },200);
    })(i)
}
//0 1 2 3

//利用let形成块级作用域
for(leti=0;i<4;i++){
    setTimeout(function(){
    	console.log(i);
    },200);
}
//0 1 2 3
```

## 作用域链
当查找变量的时候，会先从当前作用域的变量对象中查找，如果没有找到，就会从父级作用域(上层环境)的变量对象中查找，一直找到全局作用域的变量对象，也就是全局对象。这样由多个作用域的变量对象构成的链表就叫做作用域链。它由当前环境与上层环境的一系列变量对象组成，保证了当前执行环境对符合访问权限的变量和函数的有序访问。



## 取范围内随机整数

```js
function GetRandomNum(start, end) {
    const range = end - start;
    return Math.floor(Math.random() * range + start);
}

// 取100000~999999之间的随机整数
GetRandomNum(100000, 999999)
```



## Vuex

Vuex是专门为Vue服务，用于管理页面的数据状态、提供统一数据操作的生态系统。state、mutations、actions、getters、module

### 常见面试题：

**1、vuex有哪几种属性？**
答：有五种，分别是 State、 Getter、Mutation 、Action、 Module

**2、vuex的State特性是？**
答：
一、Vuex就是一个仓库，仓库里面放了很多对象。其中state就是数据源存放地，对应于与一般Vue对象里面的data
二、state里面存放的数据是响应式的，Vue组件从store中读取数据，若是store中的数据发生改变，依赖这个数据的组件也会发生更新
三、它通过mapState把全局的 state 和 getters 映射到当前组件的 computed 计算属性中

**3、vuex的Getter特性是？**
答：
一、getters 可以对State进行计算操作，它就是Store的计算属性
二、 虽然在组件内也可以做计算属性，但是getters 可以在多组件之间复用
三、 如果一个状态只在一个组件内使用，是可以不用getters

**4、vuex的Mutation特性是？**
答：
一、Action 类似于 mutation，不同在于：
二、Action 提交的是 mutation，而不是直接变更状态。
三、Action 可以包含任意异步操作

**5、Vue.js中ajax请求代码应该写在组件的methods中还是vuex的actions中？**
答：
一、如果请求来的数据是不是要被其他组件公用，仅仅在请求的组件内使用，就不需要放入vuex 的state里。
二、如果被其他地方复用，这个很大几率上是需要的，如果需要，请将请求放入action里，方便复用，并包装成promise返回，在调用处用async await处理返回的数据。如果不要复用这个请求，那么直接写在vue文件里很方便。

**6、不用Vuex会带来什么问题？**
答：

一、可维护性会下降，你要想修改数据，你得维护三个地方

二、可读性会下降，因为一个组件里的数据，你根本就看不出来是从哪来的

三、增加耦合，大量的上传派发，会让耦合性大大的增加，本来Vue用Component就是为了减少耦合，现在这么用，和组件化的初衷相背。

[面试题](https://juejin.cn/post/6844903993374670855)



## new.target

**`new.target`**属性允许你检测函数或构造方法是否是通过[new](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/new)运算符被调用的。在通过[new](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/new)运算符被初始化的函数或构造方法中，`new.target`返回一个指向构造方法或函数的引用。在普通的函数调用中，`new.target` 的值是[`undefined`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/undefined)。

```js
// 使用instanceof不安全
function fn() {
    if (!(this instanceof fn)) 
    { 
        throw new Error("fn只能用作构造函数"); 
    } 
}

// 使用new.target替代
function fn() {
    if (!new.target)
    { 
        throw new Error("fn只能用作构造函数"); 
    } 
}

new fn(); // 正确
fn(); // 错误
```



## vue-router

####   **1、全局路由。**（全局导航钩子主要有两种钩子：前置守卫、后置钩子。）

​    注册一个全局前置守卫：**beforeEach**；

> const router = new VueRouter({ ... });
>
> router.beforeEach((to, from, next) => {
>
>    // do someting
>
> });

这三个参数 **to** 、**from** 、**next** 分别的作用：

  **1、to: Route，代表要进入的目标，它是一个路由对象；
**

  **2、from: Route，代表当前正要离开的路由，同样也是一个路由对象；
**

  **3、next: Function，这是一个必须需要调用的方法，而具体的执行效果则依赖 next 方法调用的参数；**

​    next参数须知：

​      next()：进入管道中的下一个钩子，如果全部的钩子执行完了，则导航的状态就是 confirmed（确认的）；

​      next(false)：这代表中断掉当前的导航，即 to 代表的路由对象不会进入，被中断，此时该表 URL 地址会被重置到 from 路由对应的地址；

​      next(‘/’) 和 next({path: ‘/’})：在中断掉当前导航的同时，跳转到一个不同的地址；

​      next(error)：如果传入参数是一个 Error 实例，那么导航被终止的同时会将错误传递给 router.onError() 注册过的回调；

**注意：next 方法必须要调用，否则钩子函数无法 resolved；**

​    全局后置钩子：**afterEach**；

> router.afterEach((to, from) => {
>
>    // do someting
>
> });

**注意：不同于前置守卫，后置钩子并没有 next 函数，也不会改变导航本身**

####   **2. 路由独享的钩子**** **

  顾名思义，即单个路由独享的导航钩子，它是在路由配置上直接进行定义的：**
**

> cont router = new VueRouter({
>
>   routes: [
>
> ​    {
>
> ​      path: '/file',
>
> ​      component: File,
>
> ​      beforeEnter: (to, from ,next) => { 
>
> ​        // do someting 
>
> ​      }
>
> ​    }
>
>   ]
>
> });

**注意：参数的使用，和全局前置守卫是一样的**

#### 3. 组件内的导航钩子

  组件内的导航钩子主要有这三种：**beforeRouteEnter**、**beforeRouteUpdate**、**beforeRouteLeave**。他们是直接在路由组件内部直接进行定义的。

  具体用法：

> const File = {
>
>   template: `<div>This is file</div>`,
>
>   beforeRouteEnter(to, from, next) {
>
> ​    // do someting
>
> ​    // 在渲染该组件的对应路由被 confirm 前调用
>
>   },
>
>   beforeRouteUpdate(to, from, next) {
>
> ​    // do someting
>
> ​    // 在当前路由改变，但是依然渲染该组件是调用 
>
>   }，
>
>   beforeRouteLeave(to, from ,next) {
>
> ​    // do someting
>
> ​    // 导航离开该组件的对应路由时被调用
>
>   }
>
> }

**注意：beforeRouteEnter 不能获取组件实例 this，因为当守卫执行前，组件实例被没有被创建出来，剩下两个钩子则可以正常获取组件实例 this**

但是并不意味着在 beforeRouteEnter 中无法访问组件实例，我们可以通过给 next 传入一个回调来访问组件实例。在导航被确认是，会执行这个回调，这时就可以访问组件实例了，如：

> beforeRouteEnter(to, from, next) {
>
>   next (vm => {
>
> ​    // 这里通过 vm 来访问组件实例解决了没有 this 的问题
>
>   })
>
> }

**注意，仅仅是 beforRouteEnter 支持给 next 传递回调，其他两个并不支持。因为归根结底，支持回调是为了解决 this 问题，而其他两个钩子的 this 可以正确访问到组件实例，所有没有必要使用回调**

> 最后是完整的导航解析流程：
>
>   1、导航被触发
>
>   2、在失活的组件里调用离开守卫
>
>   3、调用全局的 beforeEach 守卫
>
>   4、在重用的组件里调用 beforeRouteUpdate 守卫
>
>   5、在路由配置里调用 beforEnter
>
>   6、解析异步路由组件
>
>   7、在被激活的组件里调用 beforeRouteEnter
>
>   8、调用全局的 beforeResolve 守卫
>
>   9、导航被确认
>
>   10、调用全局的 afterEach 钩子
>
>   11、触发 DOM 更新
>
>   12、在创建好的实例调用 beforeRouteEnter 守卫中传给 next 的回调函数



# Webpack

### Loader和Plugin的不同？

**不同的作用**

- **Loader**直译为"加载器"。Webpack将一切文件视为模块，但是webpack原生是只能解析js文件，如果想将其他文件也打包的话，就会用到loader。 所以Loader的作用是让webpack拥有了加载和解析非JavaScript文件的能力。
- **Plugin**直译为"插件"。Plugin可以扩展webpack的功能，让webpack具有更多的灵活性。 在 Webpack 运行的生命周期中会广播出许多事件，Plugin 可以监听这些事件，在合适的时机通过 Webpack 提供的 API 改变输出结果。

**不同的用法**

- **Loader**在module.rules中配置，也就是说他作为模块的解析规则而存在。 类型为数组，每一项都是一个Object，里面描述了对于什么类型的文件（test），使用什么加载(loader)和使用的参数（options）
- **Plugin**在plugins中单独配置。 类型为数组，每一项是一个plugin的实例，参数都通过构造函数传入。



## .sync

vue 修饰符sync的功能是：当一个子组件改变了一个 prop 的值时，这个变化也会同步到父组件中所绑定。如果我们不用.sync，我们想做上面的那个弹窗功能，我们也可以props传初始值，然后事件监听，实现起来也不算复杂。这里用sync实现，只是给大家提供一个思路，让其明白他的实现原理，可能有其它复杂的功能适用sync。

```js
<template>
    <div class="details">
        <myComponent :show.sync='valueChild' style="padding: 30px 20px 30px 5px;border:1px solid #ddd;margin-bottom: 10px;"></myComponent>
        <button @click="changeValue">toggle</button>
    </div>
</template>
<script>
import Vue from 'vue'
Vue.component('myComponent', {
      template: `<div v-if="show">
                    <p>默认初始值是{{show}}，所以是显示的</p>
                    <button @click.stop="closeDiv">关闭</button>
                 </div>`,
      props:['show'],
      methods: {
        closeDiv() {
          this.$emit('update:show', false); //触发 input 事件，并传入新值
        }
      }
})
export default{
    data(){
        return{
            valueChild:true,
        }
    },
    methods:{
        changeValue(){
            this.valueChild = !this.valueChild
        }
    }
}
</script>
```



 ## 在地址栏里输入一个 URL,到这个页面呈现出来，中间会发生什么？ 

输入 url 后，首先需要找到这个 url 域名的服务器 ip,为了寻找这个 ip，浏览器首先会寻 找缓存，查看缓存中是否有记录，缓存的查找记录为：浏览器缓存-》系统缓存-》路由 器缓存，缓存中没有则查找系统的 hosts 文件中是否有记录，如果没有则查询 DNS 服务 器，得到服务器的 ip 地址后，浏览器根据这个 ip 以及相应的端口号，构造一个 http 请 求，这个请求报文会包括这次请求的信息，主要是请求方法，请求说明和请求附带的数 据，并将这个 http 请求封装在一个 tcp 包中，这个 tcp 包会依次经过传输层，网络层， 数据链路层，物理层到达服务器，服务器解析这个请求来作出响应，返回相应的 html 给浏览器，因为 html 是一个树形结构，浏览器根据这个 html 来构建 DOM 树，在 dom 树的构建过程中如果遇到 JS 脚本和外部 JS 连接，则会停止构建 DOM 树来执行和下载 相应的代码，这会造成阻塞，这就是为什么推荐 JS 代码应该放在 html 代码的后面，之 后根据外部央视，内部央视，内联样式构建一个 CSS 对象模型树 CSSOM 树，构建完成 后和 DOM 树合并为渲染树，这里主要做的是排除非视觉节点，比如 script，meta 标签和 排除 display 为 none 的节点，之后进行布局，布局主要是确定各个元素的位置和尺寸，之后是渲染页面，因为 html 文件中会含有图片，视频，音频等资源，在解析 DOM 的过 程中，遇到这些都会进行并行下载，浏览器对每个域的并行下载数量有一定的限制，一 般是 4-6 个，当然在这些所有的请求中我们还需要关注的就是缓存，缓存一般通过 Cache-Control、Last-Modify、Expires 等首部字段控制。 Cache-Control 和 Expires 的区别 在于 Cache-Control 使用相对时间，Expires 使用的是基于服务器 端的绝对时间，因为存 在时差问题，一般采用 Cache-Control，在请求这些有设置了缓存的数据时，会先 查看 是否过期，如果没有过期则直接使用本地缓存，过期则请求并在服务器校验文件是否修 改，如果上一次 响应设置了 ETag 值会在这次请求的时候作为 If-None-Match 的值交给 服务器校验，如果一致，继续校验 Last-Modified，没有设置 ETag 则直接验证 Last-Modified，再决定是否返回 304。

DNS 解析 => TCP 连接 => 发送 HTTP 请求  => 服务器处理请求并返回 HTTP 报文  => 浏览器解析渲染页面  =>  连接结束



## typeof与instanceof区别

typeof判断所有变量的类型，返回值有number，boolean，string，function，object，undefined。

typeof对于丰富的对象实例，只能返回"Object"字符串。

instanceof用来判断对象，代码形式为obj1 instanceof obj2（obj1是否是obj2的实例），obj2必须为对象，否则会报错！其返回值为布尔值。

instanceof可以对不同的对象实例进行判断，判断方法是根据对象的原型链依次向下查询，如果obj2的原型属性存在obj1的原型链上，（obj1 instanceof obj2）值为true。



## 描述一下 XSS 和 CRSF攻击？防御方法？

XSS, 即为（Cross Site Scripting）, 中文名为跨站脚本, 是发生在目标用户的浏览器层面 上的，当渲染 DOM 树的过程成发生了不在预期内执行的 JS 代码时，就发生了 XSS 攻击。 大多数 XSS 攻击的主要方式是嵌入一段远程或者第三方域上的 JS 代码。实际上是在目 标网站的作用域下执行了这段 JS 代码。 CSRF（Cross Site Request Forgery，跨站请求伪造），字面理解意思就是在别的站点伪造 了一个请求。专业术语来说就是在受害者访问一个网站时，其 Cookie 还没有过期的情 况下，攻击者伪造一个链接地址发送受害者并欺骗让其点击，从而形成 CSRF 攻击。 

XSS 防御的总体思路是：对输入(和 URL 参数)进行过滤，对输出进行编码。也就是对提 交的所有内容进行过滤，对 url 中的参数进行过滤，过滤掉会导致脚本执行的相关内容； 然后对动态输出到页面的内容进行 html 编码，使脚本无法在浏览器中执行。虽然对输 入过滤可以被绕过，但是也还是会拦截很大一部分的 XSS 攻击。

 防御 CSRF 攻击主要有三种策略：验证 HTTP Referer 字段；在请求地址中添加 token 并 验证；在 HTTP 头中自定义属性并验证



