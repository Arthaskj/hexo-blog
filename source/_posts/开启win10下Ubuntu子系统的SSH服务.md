title: 开启win10下Ubuntu子系统的SSH服务
category: 技能
date: <文章日期> [YYYY-MM-DD]
index_img: /images/assets/70-20200730114753599.png

---

Win10中安装Ubuntu子系统后默认是没有开启SSH服务的，需要手动配置开启

<!--more-->

Win10中安装Ubuntu子系统后默认是没有开启SSH服务的，需要手动配置开启，

> 如何安装win10的linux子系统可以参考这篇文章：https://blog.csdn.net/zhouzme/article/details/78780479

先通过 bash 进入子系统修改配置

```bash
vi /etc/ssh/sshd_config1
```

如果文件不存在说明尚未安装，则执行安装

```bash
apt-get install openssh-server1
```

继续修改配置，下面以密码登录的配置作说明：

```bash
Port = 22 # 默认是22端口，如果和windows端口冲突或你想换成其他的否则不用动
#ListenAddress 0.0.0.0 # 如果需要指定监听的IP则去除最左侧的井号，并配置对应IP，默认即监听PC所有IP
PermitRootLogin no # 如果你需要用 root 直接登录系统则此处改为 yes
PasswordAuthentication no # 将 no 改为 yes 表示使用帐号密码方式登录1234
```

主要配置以上几项即可
然后启动 ssh 服务

```bash
service ssh start1
```

如果提示 `sshd error: could not load host key` 则需要重新生成 key

```bash
dpkg-reconfigure openssh-server1
```

查看服务状态

```bash
service ssh status
# * sshd is running  显示此内容则表示启动正常12
passwd root # 设置下密码1
```

不要忘了，还要配置防火墙开启端口才能被其他PC访问

打开 Win10 防火墙设置，（可以通过右下角有个向上的箭头点击盾牌快速进入面板）Windows Defender 安全中心，点击下方的高级设置

![这里写图片描述](/images/assets/70-20200730114753599.png)

选择左侧`入站规则` 然后点击右侧 `新建规则`
![这里写图片描述](/images/assets/70-20200730114753431.png)

选择 `端口` -> `tcp` -> `特定端口` -> 填入刚刚设置的 Port 默认 `22` -> 允许连接 -> 提交

连接的网络根据你本地的网络连接情况选择，搞不清楚就公用、专用全选好了，端口添加后即可正常访问了，现在可以通过XShell 或其他终端连接试试了

![这里写图片描述](/images/assets/70-20200730114753445.png)

使用 localhost，127.0.0.1，192.168.x.x 等服务器的IP都可以连接

如果使用Android手机，推荐看看这篇文章安装APP 即可操作 https://blog.csdn.net/zhouzme/article/details/81084661

如果需要限制服务器的访问权限，如指定局域网或外网可连接的客户端IP，则看下面：

双击刚才添加的入站规则名称进行配置

选择`作用域`选项卡，填写允许或拒绝的IP即可

![这里写图片描述](/images/assets/70.png)

这样 SSH 服务就配置好了，不过注意WIN10重启后，需要重新bash进入子系统手动启动 ssh 服务才可以使用，以后再试试下配置自动启动吧