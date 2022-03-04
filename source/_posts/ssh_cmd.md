
title: SSH、SCP执行远程服务器命令
category: 技能
tags: ssh,scp,服务器命令
date: 2021-05-12

---

最近接触了一个没有ci的项目，部署的时候只能本地打包，然后通过ftp放到服务器上，一来一回很费时间，于是打算自己写一个脚本来实现快速部署。

<!--more-->

# SSH、SCP执行远程服务器命令

最近接触了一个没有ci的项目，部署的时候只能本地打包，然后通过ftp放到服务器上，一来一回很费时间，于是打算自己写一个脚本来实现快速部署。

```shell
# 客户端脚本
# cd到目标目录
cd /h/workspace/pms-front-node/WebSource/
# 防止存在旧的包文件，先删除包文件
rm -rf Common.tar
# 对代码进行压缩
tar -cvf Common.tar Common/
# 将代码包通过scp命令上传到服务器上
scp -r Common.tar root@116.196.93.53:/export/k20/static/pms-front-node/WebSource
# 清理压缩的代码包
rm -rf Common.tar
# 远程执行服务端脚本
ssh root@116.196.93.53 "/export/k20/shell/pms-ci.sh"
```



```shell
# 服务端脚本
# cd到目标目录
cd /export/k20/static/pms-front-node/WebSource
# 解压代码包
tar -xvf Common.tar
# 删除代码包
rm -rf Common.tar
```

使用的时候只需要在本地执行脚本就可以更新代码到服务器上，还是很方便的。

缺点就是，由于我使用的是windows系统，使用的是bash终端，还没有想到如何进行密码自动填充，所以需要填写两次服务器密码。如果你使用的是linux，可以使用`expect`工具来实现自动填充密码。

过段时间会尝试使用windows下的wsl来实现自动填充密码。