title: 利用Gitee的webhook搭配自己的服务器实现自动化部署
category: 技能
tags: 
tags: Gitee,webhook,ci
date: 2019-03-17
index_img: /images/assets/image-20210224103102204.png

---

利用Gitee的webhook搭配自己的服务器实现自动化部署

<!--more-->

## 利用Gitee的webhook搭配自己的服务器实现自动化部署


在gitee仓库的设置中选择webhooks，配置被访问的接口和密码以及触发请求接口的事件（一般是push）就好了。关于gitee的webhooks详细文档可以看[这里](https://gitee.com/help/categories/40)。

![image-20210224103102204](/images/assets/image-20210224103102204.png)



使用自己会的语言写好被调用的接口，这里我是用Eggjs编写，并部署到自己的服务器上，原理就是仓库代码push时添加一些关键字，比如这里的`--kci--`，这样在接口里处理，发现关键字就走自己的自动化程序，这里我使用shell脚本作为自动化脚本，接口里只要负责运行shell脚本就好了。并做好脚本执行失败或成功的邮件提醒。

![image-20210224111948551](/images/assets/image-20210224111948551.png)



如下在服务器上针对每个仓库编写各自的自动化部署脚本，如果你的服务器性能足够，就可以执行更多命令来优化部署过程，但是我的服务器是低配，对于一些消耗内存的操作往往会失败，例如一些`npm`命令。所以也可以简化服务器上的操作，在本地就打包好文件然后进行上传。

```javascript
npm run build && git add ./ && git commit -m 'feature: 自动化部署 --kci--' && git push
```



![image-20210224104628390](/images/assets/image-20210224104628390.png)



![image-20210224104910026](/images/assets/image-20210224104910026.png)



对于网站可以使用nginx进行站点配置，只要更新网站静态文件就可以。对于接口服务可能就需要写一些更复杂的shell脚本。

![image-20210224111127938](/images/assets/image-20210224111127938.png)



commit时的关键字

![image-20210224110734744](/images/assets/image-20210224110734744.png)



部署成功或失败的提醒

![image-20210224110142194](/images/assets/image-20210224110142194.png)



这样，在提交代码时就可以控制站点或者接口服务自动部署了。

本博客站点也是这样进行部署的～

![image-20210224110321484](/images/assets/image-20210224110321484.png)