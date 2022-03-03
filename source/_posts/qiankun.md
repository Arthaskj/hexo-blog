title: qiankun微前端学习
category: 技能
date: <文章日期> [YYYY-MM-DD]
index_img: /images/qiankun.assets/image-20210318161647429.png

---


qiankun 是一个基于 [single-spa](https://github.com/CanopyTax/single-spa) 的[微前端](https://micro-frontends.org/)实现库，旨在帮助大家能更简单、无痛的构建一个生产可用微前端架构系统。

<!--more-->

## qiankun微前端学习

qiankun 是一个基于 [single-spa](https://github.com/CanopyTax/single-spa) 的[微前端](https://micro-frontends.org/)实现库，旨在帮助大家能更简单、无痛的构建一个生产可用微前端架构系统。

qiankun 孵化自蚂蚁金融科技基于微前端架构的云产品统一接入平台，在经过一批线上应用的充分检验及打磨后，我们将其微前端内核抽取出来并开源，希望能同时帮助社区有类似需求的系统更方便的构建自己的微前端系统，同时也希望通过社区的帮助将 qiankun 打磨的更加成熟完善。

目前 qiankun 已在蚂蚁内部服务了超过 200+ 线上应用，在易用性及完备性上，绝对是值得信赖的。

## 什么是微前端

> Techniques, strategies and recipes for building a **modern web app** with **multiple teams** that can **ship features independently**. -- [Micro Frontends](https://micro-frontends.org/)
>
> 微前端是一种多个团队通过独立发布功能的方式来共同构建现代化 web 应用的技术手段及方法策略。

微前端架构具备以下几个核心价值：

- 技术栈无关
  主框架不限制接入应用的技术栈，微应用具备完全自主权

- 独立开发、独立部署
  微应用仓库独立，前后端可独立开发，部署完成后主框架自动完成同步更新

- 增量升级

  在面对各种复杂场景时，我们通常很难对一个已经存在的系统做全量的技术栈升级或重构，而微前端是一种非常好的实施渐进式重构的手段和策略

- 独立运行时
  每个微应用之间状态隔离，运行时状态不共享

微前端架构旨在解决单体应用在一个相对长的时间跨度下，由于参与的人员、团队的增多、变迁，从一个普通应用演变成一个巨石应用([Frontend Monolith](https://www.youtube.com/watch?v=pU1gXA0rfwc))后，随之而来的应用不可维护的问题。这类问题在企业级 Web 应用中尤其常见。



## 主应用

主应用的配置十分简单，首先安装qiankun依赖包

`npm i qiankun -S`

更改`main.js`文件

```js
import Vue from 'vue'
import App from './App.vue'
import router from './router'
import store from './store'
import ViewUI from 'view-design';
import './style/main.css';
import 'view-design/dist/styles/iview.css';
// 导入qiankun.js
import {registerMicroApps,setDefaultMountApp,start} from 'qiankun';
Vue.use(ViewUI);

Vue.config.productionTip = false

new Vue({
  router,
  store,
  render: h => h(App)
}).$mount('#app')
// 注册子应用
registerMicroApps([
  {
    name: 'react app',            // 子应用名称
    entry: '//localhost:7101',  // 子应用入口
    container: '#container',    // 子应用所在容器
    activeRule: '/app-react',         // 子应用触发规则（路径）
  },
]);

// 启动默认应用
setDefaultMountApp('')
// 开启服务
start()

```

在`App.vue`中添加子应用dom锚点

```html
<template>
  <div>
    <div id="app">
      <div class="layout" :style="{height: '100%'}">
        <Content :style="{margin: '0', background: '#fff', minHeight: '220px', height: '100%'}">
          <router-view/>
        </Content>
      </div>
    </div>
      <!-- 子应用dom要与主应用dom同级 -->
    <div id="container"></div>
  </div>
</template>
```



## 子应用

修改`main.js`

```js
import Vue from 'vue'
import App from './App.vue'
import router from './router'
import store from './store'
import ViewUI from 'view-design';
import './style/main.css';
import 'view-design/dist/styles/iview.css';
Vue.use(ViewUI);

Vue.config.productionTip = false
let instance = null;

function render() {

  instance = new Vue({
    router,
    store,
    render: h => h(App)
  }).$mount('#app')
}
// webpack打包公共文件路径
if (window.__POWERED_BY_QIANKUN__) {
  __webpack_public_path__ = window.__INJECTED_PUBLIC_PATH_BY_QIANKUN__;
}
// 独立运行
if (!window.__POWERED_BY_QIANKUN__) {
  render();
}

// 生命周期
export async function bootstrap() {
  console.log('[vue] vue app bootstraped');
}
export async function mount(props: any) {
  console.log('[vue] props from main framework', props);
  // storeTest(props);
  render(props);
}
export async function unmount() {
  instance.$destroy();
  instance.$el.innerHTML = '';
  instance = null;
  router = null;
}

```

修改路由文件，添加统一路由前缀，要与主应用中注册的一致

```js
const router = new VueRouter({
  // mode: 'history',
  base: '/app-react', // 添加路由前缀
  routes
})
```

修改vue.config.js配置文件

```js
const path = require('path');
const { name } = require('./package');

function resolve(dir) {
  return path.join(__dirname, dir);
}

module.exports = {
  pluginOptions: {
    'style-resources-loader': {
      preProcessor: 'less',
      patterns: [
        // path.resolve(__dirname, './src/**/*.less')
      ]
    }
  },
  devServer: {
    port: 7101,
    // 跨域
    // 跨域是必须的，否则无法访问
    headers: {
      'Access-Control-Allow-Origin': '*',
    },
    proxy: {
      "/api": {
        target: "https://localhost", //跨域网址
        secure: true, // 如果是https接口，需要配置这个参数
        changeOrigin: true, //自动修改http header里面的host
        pathRewrite: {
          "^/api": "/api", //路径的替换规则
        }
      }
    }
  },
  // 自定义webpack配置
  // 必须以umd库格式才能被访问  
  configureWebpack: {
    resolve: {
      alias: {
        '@': resolve('src'),
      },
    },
    output: {
      // 把子应用打包成 umd 库格式(必须)
      library: `${name}-[name]`,
      libraryTarget: 'umd',
      jsonpFunction: `webpackJsonp_${name}`,
    },
  },
}

```

## 效果

![](/images/qiankun.assets/image-20210318161647429.png)

![image-20210318161722950](/images/qiankun.assets/image-20210318161722950.png)

在主应用输入子应用的地址就会访问子应用的内容，完成~



![image-20210318161927766](/images/qiankun.assets/image-20210318161927766.png)

之后我又试了试jquery项目，但是失败了，因为qiankun官网要求jquery项目所有路径必须是全路径，不能是相对路径。。。

后期可以在试试react项目。

