title: win10_linux_wsl子系统开机启动ssh服务
category: 技能
tags: windows,linux,wsl,ssh
date: <文章日期> [YYYY-MM-DD]
index_img: /images/assets/1994177-20200717173138560-522656065.png

---

1、打开开机后进入wsl

2、输入su root并输入密码 进入root权限

3、输入 sodo vim /etc.init.wsl 创建文件并且输入下列代码，保存退出
...

<!--more-->

前提：已经安装好ssh服务

 

1、打开开机后进入wsl

2、输入su root并输入密码 进入root权限

3、输入 sodo vim /etc.init.wsl 创建文件并且输入下列代码，保存退出

![img](/images/assets/1994177-20200717173138560-522656065.png)

4、输入 sudo chmod +x /etc/init.wsl 添加为root可执行

5、添加root免密执行（可选）

sudo vim /etc/sudoers

添加一行 %sudo ALL=NOPASSWD: /etc/init.wsl

保存即可

 

在linux上的操作就告一段落了

 

接下来就开始在win10上进行操作了

1、在win10 上打开powershell，输入 C:\Windows\System32\bash.exe -c 'sudo /etc/init.wsl'，如果没问题，就可以尝试创建启动脚本啦！

2、创建一个名为[startservice.vbs]的文件，编辑，输入并保存

```vbscript
Set ws = WScript.CreateObject("WScript.Shell")
ws.run "C:\Windows\System32\bash.exe -c 'sudo /etc/init.wsl'",0
```

3、Win+R呼出运行，并输入：shell:startup打开开机启动项，把startservice.vbs复制进去，重启电脑即可