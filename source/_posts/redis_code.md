
title: Egg搭配Redis进行验证码校验
category: 技能
date: 2021-04-20
index_img: /images/redis_code.assets/image-20210420161704441.png

---


用户请求验证码时后台生成随机的6位验证码并发送给用户填写的邮箱，同时用邮箱地址作为唯一键存储在Redis中并设置过期时间，用户进行注册时到Redis中拿验证码并与用户传过来的验证码进行比对，验证码过期或者不匹配都不会注册成功。

<!--more-->

# Egg搭配Redis进行验证码校验



用户请求验证码时后台生成随机的6位验证码并发送给用户填写的邮箱，同时用邮箱地址作为唯一键存储在Redis中并设置过期时间，用户进行注册时到Redis中拿验证码并与用户传过来的验证码进行比对，验证码过期或者不匹配都不会注册成功。



## 登录接口

```js
async login() {
    const { ctx } = this;
    const params = ctx.KParams;
    const { phoneNumber, code, email } = params;
    const vCode = await this.service.redis.get(`code_${email}`);
    if (+vCode === +code) {
      const user = await ctx.service.user.find({ phoneNumber, email });
      if (user && user.length) {
        ctx.body = { success: false, message: '用户重复！' };
      } else {
        const result = await ctx.service.user.add(params);
        if (result.success) ctx.body = { success: true, message: '注册成功！' };
      }
    } else {
      ctx.body = { success: false, data: {}, message: '验证码已过期或错误!' };
    }
  }
```



## 验证码发送

```js
async SendCode() {
    const { email } = this.ctx.KParams;
    if (!email) {
      this.ctx.body = { success: false, message: '邮箱地址有误!' };
      return;
    }
    // 生成6位随机码
    const code = Math.floor(Math.random() * 899999 + 100000);
    // 记录验证码并设置过期时间
    await this.service.redis.set(`code_${email}`, code, 60);
    const tpl = `<b style="font-size: 18px;">您的用户验证码是：<span style="color:#1890ff">${code}</span></b>`;
    await sendMail([ email ], 'Arthaskj用户注册验证码', tpl);
    this.ctx.body = { success: true };
  }
```



## 前端代码

```js
<template>
  <div>
    <common-nav-bar
      title="注册"
    />
    <van-form @submit="onSubmit" class="container">
      <van-field
        v-model="username"
        name="用户名"
        label="用户名"
        placeholder="用户名"
        :rules="[{ required: true, message: '请填写用户名' }]"
      />
      <van-field
        v-model="password"
        type="password"
        name="密码"
        label="密码"
        placeholder="密码"
        :rules="[{ required: true, message: '请填写密码' }]"
      />
      <van-field v-model="tel" type="tel" label="手机号" :rules="[{ required: true, message: '请填写手机号' }]"/>
      <van-field v-model="email" type="email" label="邮箱" :rules="[{ required: true, message: '请填写邮箱' }]"/>
      <van-field
        v-model="code"
        center
        clearable
        label="邮箱验证码"
        placeholder="请输入邮箱验证码"
        :rules="[{ required: true, message: '请填写邮箱验证码' }]"
      >
        <template #button>
          <van-button size="small" class="login-code-btn" type="primary" @click="authCode" :disabled="codeDisabled">{{codeText}}</van-button>
        </template>
      </van-field>
      <div class="button-group">
        <van-button round block type="info" native-type="submit">
          注册
        </van-button>
      </div>
    </van-form>
  </div>
</template>

<script>
  import CommonNavBar from "../../components/CommonNavBar";
  import md5 from 'md5';
  import { Login, SendCode } from './apis';

  const md5Key = '自定义标记进行md5加密';

  export default {
    name: "login",
    data() {
      return {
        username: '',
        password: '',
        tel: '',
        email: '',
        code: '',
        codeText: '发送验证码',
        codeDisabled: false,
      }
    },
    components: {
      CommonNavBar
    },
    beforeMount() {
      this.$store.commit('setTitle', { title: 'login' });
    },
    methods: {
      onSubmit(value) {
        const params = {
          name: this.username,
          pwd: md5(this.password + md5Key),
          phoneNumber: this.tel,
          email: this.email,
          code: this.code,
        }
        Login(params).then(res => {
          console.log(res)
          this.$router.replace('/signOn')
        })
      },
      authCode() {
        let second = 60;
        let timer = null;
        // 发送验证码
        SendCode({  email: this.email }).then(() => {
          this.codeDisabled = true;
          timer = setInterval(() => {
            second--;
            this.codeText = `${second}s`;
            if (second === 0) {
              clearInterval(timer);
              timer = null;
              this.codeDisabled = false;
              this.codeText = '发送验证码';
            }
          }, 1000)
        });
      }
    }
  }
</script>

<style scoped lang="less">
  .container {
    padding: 20px 5px;
  }
  .login-code-btn {
    width: 78px;
  }
  .button-group {
    margin: 25px 16px 16px 16px;
    button {
      margin-bottom: 16px;
    }
  }
</style>

```



![image-20210420161704441](/images/redis_code.assets/image-20210420161704441.png)

![image-20210420161740751](/images/redis_code.assets/image-20210420161740751.png)