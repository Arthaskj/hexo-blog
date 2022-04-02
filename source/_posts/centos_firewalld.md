title: CentOS7防火墙操作
category: 技能
tags: 
  - centos
  - 防火墙
date: 2021-05-14

---

CentOS7防火墙操作

<!--more-->

# CentOS7防火墙操作

```shell
netstat -anp  # 查询已开放的端口
netstat -tnlp # 查询已开放的端口
firewall-cmd --query-port=666/tcp # 查询指定端口是否已开  提示 yes，表示开启；no表示未开启。

firewall-cmd --add-port=123/tcp --permanent # 添加指定需要开放的端口
firewall-cmd --reload # 重载入添加的端口
firewall-cmd --query-port=666/tcp # 查询指定端口是否已开
firewall-cmd --permanent --remove-port=123/tcp # 移除指定端口

systemctl status firewalld # 查看防火墙状态
systemctl start firewalld # 开启防火墙
systemctl restart firewalld # 重启防火墙
systemctl stop firewalld # 停止防火墙

```



