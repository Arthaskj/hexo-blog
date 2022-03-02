title: 使用Docker部署前端项目
category: 技能
date: <文章日期> [YYYY-MM-DD]

---

使用Docker部署前端项目

<!--more-->

# 使用Docker部署前端项目



![image-20210409172519539](/images/docker_web.assets/image-20210409172519539.png)

docker-compose.yml

```yml
version: '2'
services:
  my-hy:
    image: my-hy
    ports:
      - 8333:8001
    volumes:
      - ~/Nginx/test:/etc/nginx/conf.d

```



Dockerfile

```dockerfile
FROM nginx:latest
COPY . /usr/trade
COPY ./trade.conf /etc/nginx/conf.d

```



原理就是先创建`Nginx`容器，然后拷贝`nginx.conf`文件到指定位置，然后将静态文件拷贝到目标目录，最后进行容器和外部端口的映射并启动nginx服务即可。

> 将目录下的`Dockerfile`和`trade.conf`复制到`dist`目录下，执行

```shell script
docker image build -t my-hy .
```

```shell script
docker run --rm -p 8333:8001 -v ~/Nginx/test:/etc/nginx/conf.d -d  my-hy   # 配置外部配置文件
```