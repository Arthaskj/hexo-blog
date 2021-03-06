title: Nginx搭建图片服务器
category: 技能
tags: 
  - nginx
  - oss
date: 2021-04-09
index_img: /images/nginx_images.assets/image-20210409103128411.png

---

Nginx搭建图片服务器


<!--more-->

# Nginx搭建图片服务器

关键词
- 腾讯云服务器
- 树莓派
- 内网映射
- nginx

仅仅是搭建的话还是很简单得，难点在于后期得优化。

首先，建议存储图片时根据时间生成对应的目录存放不同时间上传的图片，因为时间越久的图片访问量越小，时间越靠近的图片访问量越大。这样，我们可以方便的使用nginx的代理功能将时间较久的图片请求转发到低性能的服务器上，较新的图片请求转发到当前性能强劲的服务器上以达到性能的优化。

我得初步想法是利用树莓派挂载硬盘搭建服务器，然后使用内网映射暴露出来，这样就可以将时间较久的图片都存放在家里的树莓派上，优点就是节省腾讯云服务器资源，同时硬盘空间理论上可以随意扩容。

树莓派内网映射相关已经完成，可以看看以往的文章，接下来有时间就编写一下图片存储和图片同步这块的逻辑。

## 配置nginx文件



![image-20210409103128411](/images/nginx_images.assets/image-20210409103128411.png)



## 服务器上建立存储得文件夹目录

![image-20210409103231455](/images/nginx_images.assets/image-20210409103231455.png)



## 使用链接即可访问存储得图片

![image-20210409103201668](/images/nginx_images.assets/image-20210409103201668.png)