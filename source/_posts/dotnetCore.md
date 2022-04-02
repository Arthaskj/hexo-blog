title: .Net Core MVC环境安装开发
category: 技能
tags: 
    - dotNet
    - mvc
date: 2020-10-22
index_img: /images/dotnetCore.assets/8704278-bac0af6d40475408.png

---

最近在学习 .net core mvc框架下的开发，因此有些学习体验可以提供给大家，全部都是自己在学习过程中总结的干货。包含Windows、Mac、CentOS等环境下遇到的问题以及解决方法。

<!--more-->


### .Net Core MVC环境安装开发

[TOC]

> 最近在学习 .net core mvc框架下的开发，因此有些学习体验可以提供给大家，全部都是自己在学习过程中总结的干货。包含Windows、Mac、CentOS等环境下遇到的问题以及解决方法。
>
> 本人也是小白，只是在学习过程中发现这一块儿遇到的问题网上资料太少，所以就将自己的学习经历记录下来，希望对后来者学习有所帮助

### 一、dotnet SDK 和Runtime的下载和安装

对于windows用户和Mac用户这一步就很简单了。直接在[官网上下载](http://www.microsoft.com/net/download/windows)对应的安装包进行安装就行了。但是一定要记住一点，那就是版本一定要一致，否则你会在后面踏进很多坑，特别是对于多平台开发的同学。
![Window或Mac安装](/images/dotnetCore.assets/8704278-bac0af6d40475408.png)


对于Linux平台的用户安装起来也很方便在[这个网址](https://www.microsoft.com/net/download/linux-package-manager/rhel/sdk-current)下选择好对应的操作平台就可以按照提示进行安装，同样的，不建议通过百度的方法进行安装，因为百度上每个人发表的文章时间都不固定，文章里附带的下载链接可能并不是最新的安装包，这对以后开发的版本统一很不利，因此建议到官网上下载统一的安装包进行下载。
![Linux安装](/images/dotnetCore.assets/8704278-82bd46eb90595d41.png)

### 二、创建MVC项目
> 编辑器推荐VSCode

在要创建项目的地址创建一个文件夹，例如**Demo**，然后在命令行模式下进入到当前的目录，例如我这里是**D:\Demo**

> 以下代码都可以在VSCode中直接输入，操作步骤是 **"查看 => 集成终端"**

![VScode](/images/dotnetCore.assets/8704278-c7746bb3104e4559.png)

![创建MVC项目](/images/dotnetCore.assets/8704278-ebe06e05bd97bb67.png)

新建项目之后可以看到文件夹下已经生成了对应的文件
![MVC项目文件夹](/images/dotnetCore.assets/8704278-dc65daa743127cb5.png)

这个时候我们执行 `dotnet restore`命令，可以安装项目的依赖文件。执行`dotnet run`命令后就可以在本地`localhost:5000`的地址上访问到界面。
![运行项目](/images/dotnetCore.assets/8704278-68aa8c185df723cf.png)
![项目运行截图](/images/dotnetCore.assets/8704278-f5e59abe21639dda.png)

最终，我们需要发布项目，执行`dotnet publish -c release`命令就可以将项目发布到当前文件夹的`bin/release/项目名称/publish`下，需要部署时只要将publish文件夹复制到服务器上进行部署即可。
![项目发布](/images/dotnetCore.assets/8704278-c98d6935d18fa8e9.png)
![发布文件内容](/images/dotnetCore.assets/8704278-bd9bdcd1da7620a9.png)

### 三、项目部署到服务器
> 部署之前一定要确定项目编译通过无错误，并且已经安装dotnet-sdk 以及 dotnet-runtime，**两者版本要一致！两者版本要一致！两者版本要一致！**

#### 在IIS服务器上部署：
> 在IIS上部署相对要简单很多，毕竟都是自家玩意儿，只需要在IIS上新建一个网站，然后选择**基本设置**，将**物理路径**选择自己项目的路径即可。
> ![IIS服务器发布](/images/dotnetCore.assets/8704278-9a8adef502e19f82.png)

#### 在CentOS服务器上部署：
> 在Linux服务器上部署的步骤就多了些。首先，依然是要确定项目编译通过无错误，并且已经安装dotnet-sdk 以及 dotnet-runtime，**两者版本要一致！两者版本要一致！两者版本要一致！**
>
> 然后,我们需要安装Jexus,通常情况下，对外提供的 Web 服务都在 80或者443 端口，而我们的 Web 应用程序默认运行在5000端口，在 Windows 上可以使用 IIS 来宿主 ASP.NET Core，在 Linux 下可以用 Nginx，Apache，Jexus 等来作为宿主。本实验向大家推荐使用国内社区自行开发的 Jexus。


***以下内容假设你的项目名称是aspetcoreapp，并且publish文件放置在/var/www/aspnetcore下***

执行命令安装独立版的 Jexus：
```
curl https://jexus.org/release/x64/install.sh|sh
```

执行命令将 asp.net core 应用部署到 Jexus
```
mkdir -p /var/www/aspnetcore
dotnet publish -o /var/www/aspnetcore
```

执行命令启动服务监听端口
```
dotnet /var/www/aspnetcore/aspnetcoreapp.dll
```

创建 aspnetcore 配置文件
```
cd /usr/jexus/siteconf
cp default aspnetcore
```

修改 aspnetcore 配置文件为如下内容
```
 port=80
    root=/ /var/www/aspnetcore
    hosts= *    #OR your.com,*.your.com

    AppHost={
        cmd=dotnet /var/www/aspnetcore/aspnetcoreapp.dll;
        root=/var/www/aspnetcore;
        port=0;
    }
```

执行命令删除原有默认配置文件
```
rm -rf /usr/jexus/siteconf/default
```

执行命令启动 Jexus
```
sh /usr/jexus/jws restart
sh /usr/jexus/jws start
```

恭喜，您的 ASP.NET Core Web 应用运行成功，使用浏览器直接访问 你的IP地址 即可看到 项目的 效果。

![成功运行](/images/dotnetCore.assets/8704278-e6599b1e4ed4581b.png)



### 四、学习过程中遇到的问题和解决方法
   1、网站部署之后输入网址出现`502 Bad GateWay`的问题
   2、使用`dotnet run` 和 `dotnet publish`命令时出现如下错误
   ```
   具体错误代码忘记了，内容大概是dotnet版本不一样，通过升级dotnet版本或者修改配置文件来解决问题
   ```
   3、网站部署后访问地址出现拒绝访问或者找不到页面的问题


  ##### 解决
  出现以上三个问题请：
  - 检查自己的dotnet版本是否统一(跨平台开发一定要注意这一点，否则无法编译、发布)
  - dotnet-SDK 和 dotnet-runtime 是不是都安装了，特别是dotnet-runtime
  - linux服务器下第一次安装或配置Jexus建议重启一下服务器再启动Jexus服务
  - 在项目文件.csproj文件中添加如下内容
    ```
    <PropertyGroup>
        <PublishWithAspNetCoreTargetManifest>false</PublishWithAspNetCoreTargetManifest>
    </PropertyGroup>
    ```
    


### 五、总结
以上就是我在.net core项目开发和部署中遇到的坑，希望能给后来学习的朋友一点帮助，有什么疑问的可以留言。