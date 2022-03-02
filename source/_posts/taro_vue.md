
title: taro-vue小程序开发
category: 技能
date: <文章日期> [YYYY-MM-DD]

---

闲来无事逛了下Taro官网，发现taro从3.x.x版本开始支持vue开发了，那这个必须得试一下，用了一下午简单写了个小程序，但是审核上遇到各种麻烦~~~

<!--more-->

## taro-vue小程序开发
闲来无事逛了下Taro官网，发现taro从3.x.x版本开始支持vue开发了，那这个必须得试一下，用了一下午简单写了个小程序，但是审核上遇到各种麻烦~~~


使用taro-vue开发的时候注意勾选`typescript`选项，否则后期引入`taro-ui-vue`时会报错，这个问题极其隐蔽。。。

开发了两个老掉牙功能，待办列表和留言板~



## 代码

```js
// api.js
import Vue from 'vue'
import './app.less'
import 'taro-ui-vue/dist/style/index.scss'
// app.js
import { AddUser } from './apis/apis';
import md5 from 'md5';
const APP_ID = '***';
const APP_SECRET = '***';
let OPEN_ID = '';
let SESSION_KEY = '';
let globalData = {
  userInfo: null,
  OPEN_ID: null,
  SESSION_KEY: null,
}
const App = {
  onLaunch() {
    const that = this;
    const logs = wx.getStorageSync('logs') || []
    logs.unshift(Date.now())
    wx.setStorageSync('logs', logs)
    // 登录
    wx.login({
      success: res => {
        // 发送 res.code 到后台换取 openId, sessionKey, unionId
        wx.request({
          //获取openid接口
          url: 'https://api.weixin.qq.com/sns/jscode2session',
          data:{
            appid:APP_ID,
            secret:APP_SECRET,
            js_code:res.code,
            grant_type:'authorization_code'
          },
          method:'GET',
          success:function(res){
            OPEN_ID = res.data.openid;//获取到的openid
            SESSION_KEY = res.data.session_key;//获取到session_key
            globalData.OPEN_ID = OPEN_ID;
            globalData.SESSION_KEY = SESSION_KEY;
            // 获取用户信息
            wx.getSetting({
              success: res => {
                if (res.authSetting['scope.userInfo']) {
                  // 已经授权，可以直接调用 getUserInfo 获取头像昵称，不会弹框
                  wx.getUserInfo({
                    success: res => {
                      // 可以将 res 发送给后台解码出 unionId
                      globalData.userInfo = res.userInfo
                      wx.setStorageSync('globalData', globalData);
						// 储存用户信息到数据库 
                      const params = {
                        userId: OPEN_ID,
                        name: res.userInfo.nickName,
                        pwd: md5('***'),
                        phoneNumber: 'weChat',
                        email: 'weChat',
                        avatar: res.userInfo.avatarUrl,
                        isWechat: true,
                        ...res.userInfo
                      }
                      AddUser(params).then(res => {
                        console.log(res)
                      })
                      // 由于 getUserInfo 是网络请求，可能会在 Page.onLoad 之后才返回
                      // 所以此处加入 callback 以防止这种情况
                      // if (this.userInfoReadyCallback) {
                      //   this.userInfoReadyCallback(res)
                      // }
                    }
                  })
                }
              }
            })
          }
        })
      }
    })

  },
  onShow (options) {
  },
  render(h) {
    // this.$slots.default 是将要会渲染的页面
    return h('block', this.$slots.default)
  },
}

export default App

```



```vue
// todo.vue
<template>
  <view class="h-box-height">
    <AtList class="index">
      <AtSwipeAction
        v-for="item in todoList"
        :key="item._id"
        :on-click="row => handleOnSwipeClick(row, item)"
        auto-close
        :options="swipeOptions"
      >
        <AtListItem
          class="normal"
          is-switch
          :title="item.content"
          :switch-is-check="item.completed"
          :on-switch-change="({ detail: { value } }) => handleOnSwitchChange(value, item)"
        >
          {{ item.content }}
        </AtListItem>
      </AtSwipeAction>

      <AtFloatLayout
        :is-opened="show"
        title="待办"
        :on-close="() => this.show = false"
      >
        <AtTextarea
          :value="message"
          :on-change="v => this.message = v"
          :max-length="200"
          placeholder="请留言..."
        />
        <AtButton
          class-name="btn"
          type="primary"
          :on-click="onSubmit"
          style="margin-top: 20px;"
        >
          提交
        </AtButton>
      </AtFloatLayout>
    </AtList>

    <AtFab
      class-name="fab"
      :on-click="() => this.show = true"
    >
      <text class="at-fab__icon at-icon at-icon-add" />
    </AtFab>

    <CustomTabBar
      :current="current"
    />
  </view>
</template>

<script>
import Taro from '@tarojs/taro'
import './todo.less'
import { AtFab, AtSwipeAction, AtTextarea, AtList, AtListItem, AtFloatLayout, AtButton } from 'taro-ui-vue';
import { GetAll, AddTodo, DeleteById, ChangeStatus } from './apis';
import { Toast } from '../../utils';
import CustomTabBar from '../../components/customTabBar.vue';
const { OPEN_ID, userInfo: { nickName } } = wx.getStorageSync('globalData') || {};

export default {
  components: {
    AtFab,
    AtSwipeAction,
    CustomTabBar,
    AtTextarea,
    AtListItem,
    AtList, 
    AtFloatLayout, 
    AtButton
  },
  data () {
    return {
      current: 0,
      swipeOptions: [
        {
          text: '删除',
          style: {
            backgroundColor: '#FF4949'
          }
        }
      ],
      show: false,
      todoList: [],
      message: '',
    }
  },
  mounted() {
    this.getTodoList();
  },
  methods: {
    getTodoList(cb) {
      GetAll().then(res => {
        this.todoList = res;
        cb && cb();
      })
    },
    onSubmit() {
      if (!this.message) return;
      const data = {
        content: this.message,
        userId: OPEN_ID,
        userName: nickName,
      }
      AddTodo(data).then(res => {
        Toast.success('保存成功!');
        this.message = '';
        this.show = false;
        this.getTodoList();
      })
    },
    handleOnSwitchChange(completed, { _id }) {
      ChangeStatus({ _id, completed }).then(res => {
        console.log(res)
      });
    },
    handleOnSwipeClick({ text }, { _id }) {
      if (text === '删除') {
        DeleteById({ _id }).then(res => {
          Toast.success('删除成功!');
          this.getTodoList();
        })
      }
    },
  },
  onPullDownRefresh() {
    this.getTodoList(() => Taro.stopPullDownRefresh());
  },
}
</script>

```



## 展示

![image-20210325135229725](/images/taro_vue.assets/image-20210325135229725.png)

![image-20210325135244012](/images/taro_vue.assets/image-20210325135244012.png)

![image-20210325135257164](/images/taro_vue.assets/image-20210325135257164.png)

![image-20210325135308161](/images/taro_vue.assets/image-20210325135308161.png)