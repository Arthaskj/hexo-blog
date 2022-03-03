title: EggJs操作Redis
category: 技能
date: 2021-04-20

---



<!--more-->

# EggJs操作Redis

## 安装egg-redis

` npm i egg-redis --save`



## 配置插件

`plugin`

```js
module.exports = {
  redis: {
    enable: true,
    package: 'egg-redis',
  },
};
```



## 添加redis配置

```js
// 单个配置
config.redis = {
    client: {
        port: cConfig.redis.port, // Redis port
        host: cConfig.redis.path, // Redis host
        password: cConfig.redis.password, // Redis password
        db: 0,
    },
};

// 多个配置
config.redis = {
    client: {
        db0: {
            port: cConfig.redis.port, // Redis port
            host: cConfig.redis.path, // Redis host
            password: cConfig.redis.password, // Redis password
            db: 0,
        },
        db1: {
            port: cConfig.redis.port, // Redis port
            host: cConfig.redis.path, // Redis host
            password: cConfig.redis.password, // Redis password
            db: 1,
        }
    },
};
```



## 使用

```js
// 单个操作
this.app.redis.set('kj', 'is a cool boy!');
this.app.redis.set('kj', 'ia a cool boy', 'EX', 36000 * 24); // 设置有效时间
this.app.redis.get('kj'); // 获取
this.app.redis.flushall(); // 清空redis

// 多个配置操作
// set
await this.app.redis.get('db0').set('foo', 'bar');
// get
await this.app.redis.get('db0').get('foo');
```

