title: MySQL
category: 技能
date: <文章日期> [YYYY-MM-DD]

---


<!--more-->

# MYSQL



## Docker启动Mysql

```docker run -itd --name mysql-test -p 3306:3306 -e MYSQL_ROOT_PASSWORD=123456 mysql```



## 允许远程访问

```mysql
USE mysql; 

ALTER USER 'root'@'%' IDENTIFIED WITH mysql_native_password BY '<password>';

FLUSH PRIVILEGES;
```



## 给指定用户赋权

```grant all privileges on 想授权的数据库.* to 'user1'@'%';```



