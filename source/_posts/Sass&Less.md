title: Sass、Less使用教程
category: 技能
tags: sass,less
date: 2019-08-14
index_img: 

---

Sass、Less使用教程

<!--more-->

<!--
 * @Author: 柯军
 * @Date: 2019-08-14 12:30:52
 * @Description: 
 -->
# Sass、Less使用教程

### 一、下载安装方法

> Less官网 [Less](http://lesscss.cn/ "less")  
> Sass官网  [Sass](https://www.w3cplus.com/sassguide/ "Sass")  
> ​    
> 这两者安装都需要先[Ruby](http://www.runoob.com/ruby/ruby-installation-windows.html "ruby")才行。

```
gem install sass //安装sass
gem update sass  //升级sass
sass -v  //查看版本
sass -h  //查看帮助


//将ruby的源更换为国内的taobao源，下载速度回更快
$ gem sources --remove https://rubygems.org/
$ gem sources -a https://ruby.taobao.org/ 【如果你系统不支持https，请将淘宝源更换成：gem sources -a http://gems.ruby-china.org】
$ gem sources -l
*** CURRENT SOURCES ***

https://ruby.taobao.org
# 请确保只有 ruby.taobao.org
$ gem install sass
```



### 二、编译方法

> 编译方法有以下三种  
> 1.通过命令行编译，这种方法需要记的命令行较多，可以使用脚本简化过程。

```
$ sass --watch sassFileDirectory:cssFileDirectory  //文件夹监听命令
 
$ lessc styles.less styles.css  //Less命令
```

2.通过sublime的擦肩完成，操作简单，但是只适用于sublime。  
3.通过koala软件进行编译，这种方法最方便，只需要将要编译的文件打开即可，他会自动生成相应文件

### 三、自适应移动端

> 方法就是通过rem这种单位的特殊性实现网站响应式布局
> rem相对于html标签的font-size自适应，不同手机屏幕的
> 分辨率不同，根标签的font-size也不同，所以只需要根据不同屏幕的font-size来改变即可。

  

> 在html文件中加入一下代码
>
> ```
> <script>
> 	var pixelRatio  = 1 / window.devicePixelRatio;
> 	document.write('<meta name="viewport" content = "width=device-width,initial-scale='+pixelRatio+',minimum-scale='+pixelRatio+',maximum-scale='+pixelRatio+'"/>');
> 	var html=document.getElementsByTagName('html')[0];
> 	var pageWidth=html.getBoundingClientRect().width;
> 	html.style.fontSize=pageWidth/10+"px";
> </script> 
> ```
>
> > 在Less文件头部加入一下代码
>
> ```
> @charset "UTF-8";
> @rem:75rem;
> ```
>
> > 其中75rem是iphone6屏幕的font-size值，iphone5为60rem，根据你开发的机型相对应即可