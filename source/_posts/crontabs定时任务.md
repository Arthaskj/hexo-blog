title: centos上设置定时任务
category: 技能
tags: 
  - centos
  - crontabs
date: 2020-10-22
index_img: 

---

centos上设置定时任务

<!--more-->

<!--
 * @Author: 柯军
 * @Date: 2019-08-14 12:34:01
 * @Description: 
 -->
### 一、centos上设置定时任务

#### 1、安装 crontabs服务并设置开机自启：

```shell
yum install crontabs
systemctl enable crond
systemctl start crond

cron是一个linux下 的定时执行工具，可以在无需人工干预的情况下运行作业。
service crond start    //启动服务
service crond stop     //关闭服务
service crond restart  //重启服务
service crond reload   //重新载入配置
service crond status   //查看服务状态 
```

#### 2、设置用户自定义定时任务：

```
vi /etc/crontab

# Example of job definition:
# .---------------- minute (0 - 59)
# |  .------------- hour (0 - 23)
# |  |  .---------- day of month (1 - 31)
# |  |  |  .------- month (1 - 12) OR jan,feb,mar,apr ...
# |  |  |  |  .---- day of week (0 - 6) (Sunday=0 or 7) OR sun,mon,tue,wed,thu,fri,sat
# |  |  |  |  |
# *  *  *  *  * user-name  command to be executed
```

> 分钟(0-59) 小时(0-23) 日(1-31) 月(11-12) 星期(0-6,0表示周日) 用户名 要执行的命令

> 每隔30分钟root执行一次updatedb命令：

```
*/30 * * * * root updatedb
```

> 每天早上5点定时重启系统：

```
0 5 * * * root reboot
```

> 每隔三秒执行一次/home/somedir目录下的scripts.sh脚本：

```shell
## For excuting scripts.sh every 3 seconds##on 2014-10-15
*/1 * * * *  /home/somedir/scripts.sh
*/1 * * * * sleep 3 &&  /home/somedir/scripts.sh
*/1 * * * * sleep 6 &&  /home/somedir/scripts.sh
*/1 * * * * sleep 9 &&  /home/somedir/scripts.sh
*/1 * * * * sleep 12 &&  /home/somedir/scripts.sh
*/1 * * * * sleep 15 &&  /home/somedir/scripts.sh
*/1 * * * * sleep 18 &&  /home/somedir/scripts.sh
*/1 * * * * sleep 21 &&  /home/somedir/scripts.sh
*/1 * * * * sleep 24 &&  /home/somedir/scripts.sh
*/1 * * * * sleep 27 &&  /home/somedir/scripts.sh
*/1 * * * * sleep 30 &&  /home/somedir/scripts.sh
*/1 * * * * sleep 33 &&  /home/somedir/scripts.sh
*/1 * * * * sleep 36 &&  /home/somedir/scripts.sh
*/1 * * * * sleep 39 &&  /home/somedir/scripts.sh
*/1 * * * * sleep 42 &&  /home/somedir/scripts.sh
*/1 * * * * sleep 45 &&  /home/somedir/scripts.sh
*/1 * * * * sleep 48 &&  /home/somedir/scripts.sh
*/1 * * * * sleep 51 &&  /home/somedir/scripts.sh
*/1 * * * * sleep 54 &&  /home/somedir/scripts.sh
*/1 * * * * sleep 57 &&  /home/somedir/scripts.sh
```

> 例如

```shell
0 8 * * * py /root/py/demo.py
0 6 * * * py /root/py/Article.py
0 9 * * * py /root/py/ArticlePush.py
0 12 * * * py /root/py/ArticlePush.py
0 16 * * * py /root/py/ArticlePush.py
0 20 * * * py /root/py/ArticlePush.py
0 18 * * * py /root/py/demo.py

```

#### 3、保存生效：

```shell
#加载任务,使之生效
crontab /etc/crontab

#查看任务
crontab -l
```
