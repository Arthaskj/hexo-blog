<!--
 * @Author: 柯军
 * @Date: 2019-08-14 18:56:04
 * @Description: 
 -->

### 4、CentOS 7.x开启防火墙端口

```sh
 #开启端口
 firewall-cmd --zone=public --add-port=8001/tcp --permanent
 #查看所有端口情况
 netstat -lntp
 #重启防火墙
 systemctl [restart][start][stop] firewalld
 #杀死端口进程
 lsof -i :8001

```

