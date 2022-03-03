title: 项目中使用高德地图组件
category: 技能
date: 2021-05-27
index_img: /images/amap.assets/image-20210527093803096.png

---

访问[高德开放平台](https://lbs.amap.com/)注册成为开发者，在控制台中新建应用，并点击设置，添加一个key，以后就可以使用这个key调用高德地图的API了。

<!--more-->

# 项目中使用高德地图组件

## 申请高德开发者

访问[高德开放平台](https://lbs.amap.com/)注册成为开发者，在控制台中新建应用，并点击设置，添加一个key，以后就可以使用这个key调用高德地图的API了。

![image-20210527093803096](/images/amap.assets/image-20210527093803096.png)

![image-20210527093844051](/images/amap.assets/image-20210527093844051.png)

![image-20210527093744501](/images/amap.assets/image-20210527093744501.png)



### VUE项目中使用

vue项目可以选择安装`vue-amap`包使用高德地图。

```js
// main.js
import VueAMap from 'vue-amap';

Vue.use(VueAMap);

VueAMap.initAMapApiLoader({
  // 高德的key
  key: '你注册的key',
  // 插件集合
  plugin: ['AMap.Autocomplete', 'AMap.PlaceSearch', 'AMap.Scale', 'AMap.OverView', 'AMap.ToolBar', 'AMap.MapType', 'AMap.PolyEditor', 'AMap.CircleEditor'],
  // 高德 sdk 版本，默认为 1.4.4
  v: '1.4.4'
});
```

```vue
<template>
  <el-amap vid="amapDemo" :bubble="true" :zoom="zoom" :center="center">
    <!--设置坐标点-->
    <el-amap-marker
       :position="[119.034127,30.649705]"
    />
  </el-amap>
</template>
<script>
export default {
  name: "amap",
  data() {
    return {
      zoom: 15,
      center: [119.034127,30.649705],
    }
  },
}
</script>

```



### 如何获取某个点的坐标

<a href="/assets/getPoint.html" download>获取点坐标</a>