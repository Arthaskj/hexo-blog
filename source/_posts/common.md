title: windows系统下解决bash中文乱码问题
category: 技能
tags: windows,bash
date: 2019-03-17

---

windows系统下解决bash中文乱码问题

<!--more-->

## windows系统下解决bash中文乱码问题


找到git bash安装目录下的bash.bashrc配置文件，末尾添加


```bash
export LANG="zh_CN.UTF-8"
export LC_ALL="zh_CN.UTF-8"
```



![img](https://img-blog.csdnimg.cn/20201007144937889.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0hlbGxvX015c21hbGx3b3JsZA==,size_16,color_FFFFFF,t_70)