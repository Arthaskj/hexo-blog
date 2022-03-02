
title: ## taro-vue小程序开发（二）
category: 技能
date: <文章日期> [YYYY-MM-DD]

---


<!--more-->

## taro-vue小程序开发（二）
使用Taro的vue版本开发小程序要注意一点，那就是不要使用Vuex作为全局状态管理，因为使用Vuex在开发者工具里能正常使用，一旦到了实机上就会出现各种问题，所以还是使用自带的`getStorageSync`和`setStorageSync`比较好。