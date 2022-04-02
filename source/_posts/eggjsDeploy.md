title: Eggjs开发
category: 技能
tags: 
  - nodejs
  - eggjs
date: 2019-08-14
index_img: 

---

Eggjs开发

<!--more-->

### 构建

```shell
$ cd baseDir
$ npm install --production
$ tar -zcvf ../release.tgz .
```

### 部署

首先，我们需要把 `egg-scripts` 模块作为 `dependencies` 引入：

```shell
$ npm i egg-scripts --save
```

添加 `npm scripts` 到 `package.json`：

```json
{
  "scripts": {
    "start": "egg-scripts start --daemon",
    "stop": "egg-scripts stop"
  }
}
```

这样我们就可以通过 `npm start` 和 `npm stop` 命令启动或停止应用。

### 启动命令

```shell
$ egg-scripts start --port=7001 --daemon --title=egg-server-showcase
```

如上示例，支持以下参数：

- `--port=7001` 端口号，默认会读取环境变量 `process.env.PORT`，如未传递将使用框架内置端口 `7001`。
- `--daemon` 是否允许在后台模式，无需 `nohup`。若使用 Docker 建议直接前台运行。
- `--env=prod` 框架运行环境，默认会读取环境变量 `process.env.EGG_SERVER_ENV`， 如未传递将使用框架内置环境 `prod`。
- `--workers=2` 框架 worker 线程数，默认会创建和 CPU 核数相当的 app worker 数，可以充分的利用 CPU 资源。
- `--title=egg-server-showcase` 用于方便 ps 进程时 grep 用，默认为 `egg-server-${appname}`。
- `--framework=yadan` 如果应用使用了[自定义框架](https://eggjs.org/zh-cn/advanced/framework.html)，可以配置 `package.json` 的 `egg.framework` 或指定该参数。
- `--ignore-stderr` 忽略启动期的报错。
- `--https.key` 指定 HTTPS 所需密钥文件的完整路径。
- `--https.cert` 指定 HTTPS 所需证书文件的完整路径。
- 所有 [egg-cluster](https://github.com/eggjs/egg-cluster) 的 Options 都支持透传，如 `--port` 等。



配置ssl证书，设置https请求

```json
"scripts": {
    "start": "egg-scripts start --sticky --daemon --port=443 --title=egg-server-egg --https.key=/root/eggjs/Nginx/***.key --https.cert=/root/eggjs/Nginx/***.crt"
  },
```



#### 启动配置项

你也可以在 `config.{env}.js` 中配置指定启动配置。

```js
// config/config.default.js

exports.cluster = {
  listen: {
    port: 7001,
    hostname: '127.0.0.1', // 不建议设置 hostname 为 '0.0.0.0'，它将允许来自外部网络和来源的连接，请在知晓风险的情况下使用
    // path: '/var/run/egg.sock',
  }
}
```

`path`，`port`，`hostname` 均为 [server.listen](https://nodejs.org/api/http.html#http_server_listen_port_hostname_backlog_callback) 的参数，`egg-scripts` 和 `egg.startCluster` 方法传入的 port 优先级高于此配置。

### 停止命令

```shell
$ egg-scripts stop [--title=egg-server]
```

该命令将杀死 master 进程，并通知 worker 和 agent 优雅退出。

支持以下参数：

- `--title=egg-server` 用于杀死指定的 egg 应用，未传递则会终止所有的 Egg 应用。

你也可以直接通过 `ps -eo "pid,command" | grep -- "--title=egg-server"` 来找到 master 进程，并 `kill` 掉，无需 `kill -9`。