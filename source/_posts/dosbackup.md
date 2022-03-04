title: Dos脚本自动备份文件
category: 技能
tags: dos,backup
date: 2019-08-14
index_img: 

---

Dos脚本自动备份文件

<!--more-->

<!--
 * @Author: 柯军
 * @Date: 2019-08-14 19:04:17
 * @Description: 
 -->
### Dos脚本自动备份文件
```
@echo off
Set nowdate=%date:~,10%
Md F:\BackUp\"%nowdate:/=-%"
xcopy F:\Project\XPWeb\02.Service F:\BackUp\"%nowdate:/=-%" /e /s
xcopy F:\Project\XPWeb\03.Presentation F:\BackUp\"%nowdate:/=-%" /e /s
pause
```
