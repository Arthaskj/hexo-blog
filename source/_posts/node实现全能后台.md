title: 开源！！！node实现"全能"后台接口
category: 技能
tags: 
  - nodejs
  - express
  - mvc
date: 2019-08-14
index_img: /images/assets/1571795692815.png

---

最近打算将之前写的代码全部使用typescript重写，再加上适逢**1024程序猿节**，决定将之前写的代码全部开源！！！此项目包含以下功能：

<!--more-->
### 开源！！！node实现"全能"后台接口

最近打算将之前写的代码全部使用typescript重写，再加上适逢**1024程序猿节**，决定将之前写的代码全部开源！！！此项目包含以下功能：

- 接受http和https请求(https需要ssl证书)

- 连接/查询mysql数据库(支持连接多个数据库)

- 连接/查询redis

- 发送自定义内容邮件

- 验证/开发微信公众号平台(开发需要你自己开发)

- 验证/开发微信小程序(开发需要你自己开发，需要支持https)

  

#### 1、目录结构

![1571795692815](/images/assets/1571795692815.png)



#### 2、配置文件 SysConfig.js

```js
/**
 * @Author: 柯军
 * @Date: 2019/09/11 18:49:44
 * @Description: 系统配置项目
 */
module.exports = {
  httpsPort: 4380, // 自定义数字端口，给https用的端口
  httpPort: 3000, // 自定义数字端口，给http用的端口

  // email发送方配置
  email: {
    service: 'QQ',
    user: '****@qq.com',
    password: '****', // 授权码，不是邮箱密码
  },

  // 数据库配置
  mysql: {
    pysp: {
      host: '127.0.0.1',
      user: 'py',
      password: '****',
      database: 'databasename',
      port: '3306'
    },
    project: {
      host: '127.0.0.1',
      user: 'arthas',
      password: '****',
      database: 'databasename',
      port: '3306'
    }
  },

  // 微信公众号配置
  wechat: {
    appId: '****',
    appSecret: '****',
    token: '****',
    encodingAESKey: '****'
  },

  // 微信小程序配置
  wechatP: {
    appId: '****',
    secret: '****',
    grant_type: '****'
  },

  // redis配置
  redis: {
    port: 6379,
    path: '127.0.0.1'
  }
}
```



#### 3、接口路由

```js
/*
 * @Author: 柯军
 * @Date: 2019-08-20 10:48:49
 * @Description: 接口路由中转
 */

const express = require('express');

const router = express.Router();
module.exports = router;

// 第一次请求后将类保存到map中，下次直接从map中拿接口数据      2019/10/12 16:20:10      --柯军
const ContronllersMap = new Map();

// TODO: 这里没有区分是post还是get请求方式，也就是说同一个请求两种方式都可以，将来试着使用ts解释器实现区分      2019/10/23 08:58:20      --柯军
// 抓取所有请求并进行分析路由      2019/08/20 19:59:50      --柯军
router.all('*', async(request, response) => {
  let url = request._parsedUrl.pathname;
  if (url !== '/favicon.ico') { // favicon.ico是页面title左侧的网站图标，会默认请求，因此清除第二次访问
    let urlArr = url.split('/'),
      ContronllerName = urlArr[1],
      actionName = urlArr[2],
      method = request.method;

    /**
     * @createtime 2019/08/20 17:37:42
     * @author 柯军 <arthaskj@163.com>
     * @param {string}
     * @desc 根据请求类型获取前端传递的参数
     */
    let setParams = (method, request) => {
      let val = null;
      // 根据GET或者POST来获取传输给后台的参数
      switch (method) {
        case 'GET':
          val = request.query;
          break;
        case 'POST':
          val = request.body;
          break;
        default:
          val = request.query;
          break;
      }
      return val;
    };

    // 根据路由去寻找Service，找不到或者出错就抛出异常      2019/08/20 19:59:25      --柯军
    try {
      let params = setParams(method, request),
        Contronller = null,
        contronllerClass = null;
      if (ContronllersMap.has(ContronllerName)) {
        contronllerClass = ContronllersMap.get(ContronllerName);
      } else {
        contronllerClass = require(`../Contronllers/${ContronllerName}`);
        if (contronllerClass) ContronllersMap.set(ContronllerName, contronllerClass);
      }

      Contronller = new contronllerClass({request, response});
      let result = await Contronller[actionName](params);
      response.status(200);
      // 为了通过微信公众号验证所作更改      2019/10/15 17:33:32      --柯军
      // response.json(result);
      response.send(result);
    } catch (error) {
      // 请求抛出错误并停止      2019/10/12 16:19:42      --柯军
      response.status(400);
      response.json(error.toString());
      throw new Error(error.toString()); // 抛出异常
    }
  }
})
```



#### 4、请求案例

```js
$.ajax({
    url:'http://localhost:3000/api/AboutContronller/GetContent',
    success(res){
    	console.log(res)
    }
})
```



#### 5、获取代码

代码地址: [https://github.com/Arthaskj/nodeApi.git](https://github.com/Arthaskj/nodeApi.git)



使用过程中遇到什么问题，可以联系我~