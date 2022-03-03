title: CentOS7安装Redis
category: 技能
date: 2021-04-20
index_img: /images/centos_redis.assets/image-20210420103232390.png

---

CentOS7安装Redis
<!--more-->

# CentOS7安装Redis

## 下载Redis包

```shell
wget http://download.redis.io/releases/redis-6.0.3.tar.gz
```



![image-20210420103232390](/images/centos_redis.assets/image-20210420103232390.png)



## 解压Redis包

```shell
tar -zxvf redis-6.0.3.tar.gz
```

![image-20210420103322101](/images/centos_redis.assets/image-20210420103322101.png)



## 编译Redis

进入redis包执行`make`命令

如果发生报错请检查是否安装`gcc`，没有安装先安装

安装完gcc如果出现以下报错

![image-20210420103542783](/images/centos_redis.assets/image-20210420103542783.png)

那么你需要更新gcc的版本，因为默认版本是4.x.x

```shell
 yum -y install centos-release-scl
 yum -y install devtoolset-9-gcc devtoolset-9-gcc-c++ devtoolset-9-binutils
```

完成之后再执行`make install`

按照以上要求基本上就能安装成功



## 修改Redis配置文件

安装完成后最好进行默认端口等配置修改，以防被攻击。文件地址` /etc/redis.conf `

1、更改默认端口

由port 6379 改为 你自己定义的端口

2、以后台进程方式启动

修改daemonize no 为daemonize yes

3、设置redis远程连接

（1）、先放行你设置的端口号

（2）、注释掉bind 127.0.0.1

（3）、设置redis连接密码：在requirepass foobard改为requirepass mzy1314520

mzy1314520为设置的密码，这个密码尽量设置复杂些



## 启停Redis

```shell
systemctl start redis # 启动
systemctl restart redis # 重启
systemctl stop redis # 停止
systemctl enable redis # 添加进系统启动项
```





