title: 为知笔记私有化部署
category: 笔记软件
date: <文章日期> [YYYY-MM-DD]

---

为知笔记私有化部署

<!--more-->

# 为知笔记私有化部署

> 参考文档：[为知笔记私有化部署](https://www.wiz.cn/zh-cn/docker)

## 一、创建本地文档

```shell
cd ~
mkdir wizdata
```



## 二、下载docker镜像

```shell
docker image pull wiznote/wizserver
```



## 三、启动容器

```shell
docker run --name wiz --restart=always -it -d -v  ~/wizdata:/wiz/storage -v  /etc/localtime:/etc/localtime -p 80:80 -p 9269:9269/udp  wiznote/wizserver		
```

