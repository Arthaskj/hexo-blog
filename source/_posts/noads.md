
title: 通过脚本给网页去广告
category: 技能
tags: 
	- js
	- 浏览器
	- 脚本
date: 2019-08-14
index_img: https://arthaskj.github.io/Photo/20190218/3.png

---

今天查资料的时候被网页上各种广告弄得很烦，不仅分散注意力，也很容易造成误操作，于是想了个办法把他给干掉了。以下内容可能需要你具备一定的代码基础，而且是基于Google Chrome的，如果在其他浏览器上不能使用，请联系我。

<!--more-->

<!--
 * @Author: 柯军
 * @Date: 2019-08-14 18:43:25
 * @Description: 
 -->
### 通过脚本给网页去广告
> 今天查资料的时候被网页上各种广告弄得很烦，不仅分散注意力，也很容易造成误操作，于是想了个办法把他给干掉了。以下内容可能需要你具备一定的代码基础，而且是基于Google Chrome的，如果在其他浏览器上不能使用，请联系我。

![网页广告](https://arthaskj.github.io/Photo/20190218/3.png)

### 效果演示
![创建标签](https://arthaskj.github.io/Photo/20190218/g1.gif)

#### 1.创建收藏标签
> 先在浏览器收藏栏上右键 => 添加网页

![创建标签](https://arthaskj.github.io/Photo/20190218/1.png)

#### 2.输入代码
> 在打开的弹出框上按要求输入内容(复制的时候把注释内容删除)

```
javascript:(function(){
	var href = window.location.href;	//	获取地址栏判断是哪个网页
	if(href.indexOf(".csdn.")){		//这里判断为CSDN，如果有其他网址需要去广告可以进行相应的代码更改
	//根据不同网页的dom元素进行相应的隐藏以及样式更改就可以实现对其他网页的去广告效果
		$("#asideFooter").hide();
		$(".recommend-right iframe").hide();
		$(".indexSuperise").hide();
		$(".fourth_column").hide();
		$(".pulllog-box").hide();
		$("aside > .csdn-tracking-statistics").hide();
		$(".blog-content-box  article>.article_content").css({"height":"auto"}).find(".article_content").css({"height":"auto"});
		$(".hide-article-box").hide();
	}  
})()

//无注释版本
javascript:(function(){
	var href = window.location.href;
	if(href.indexOf(".csdn.")){		
		$("#asideFooter").hide();
		$(".recommend-right iframe").hide();
		$(".indexSuperise").hide();
		$(".fourth_column").hide();
		$(".pulllog-box").hide();
		$("aside > .csdn-tracking-statistics").hide();
		$(".blog-content-box  article>.article_content").css({"height":"auto"}).find(".article_content").css({"height":"auto"});
		$(".hide-article-box").hide();
	}  
})()
```

![输入代码](https://arthaskj.github.io/Photo/20190218/2.png)

#### 3.开始使用
> 在浏览网页时，单击一下上方的收藏标签即可

![开始使用](https://arthaskj.github.io/Photo/20190218/4.png)

如果遇到问题可以在下方评论或者添加作者微信询问。
![开始使用](https://arthaskj.github.io/Photo/wechatcode.png)