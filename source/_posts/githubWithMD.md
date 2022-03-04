title: GitHub怎么用？？？
category: 技能
tags: github
date: 2019-08-14
index_img: /images/githubWithMD.assets/1.png

---

> gitHub是一个面向开源及私有软件项目的托管平台，因为只支持git 作为唯一的版本库格式进行托管，故名gitHub。
> gitHub于2008年4月10日正式上线，除了git代码仓库托管及基本的 Web管理界面以外，还提供了订阅、讨论组、文本渲染、在线文件编辑器、协作图谱（报表）、代码片段分享（Gist）等功能。目前，其注册用户已经超过350万，托管版本数量也是非常之多，其中不乏知名开源项目 Ruby on Rails、jQuery、python 等。

<!--more-->

<!--
 * @Author: 柯军
 * @Date: 2019-08-14 12:14:43
 * @Description: 
 -->
### GitHub怎么用？？？

> gitHub是一个面向开源及私有软件项目的托管平台，因为只支持git 作为唯一的版本库格式进行托管，故名gitHub。
> gitHub于2008年4月10日正式上线，除了git代码仓库托管及基本的 Web管理界面以外，还提供了订阅、讨论组、文本渲染、在线文件编辑器、协作图谱（报表）、代码片段分享（Gist）等功能。目前，其注册用户已经超过350万，托管版本数量也是非常之多，其中不乏知名开源项目 Ruby on Rails、jQuery、python 等。
> **2018年6月4日，微软宣布，通过75亿美元的股票交易收购代码托管平台GitHub。**

### 一、安装Git

Windows平台用户直接搜索[Git下载地址](https://git-scm.com/downloads)进行下载即可.安装也是傻瓜式安装，就安装在默认目录就行，如果不想安装在默认路径，就要记住自己安装的路径，安装完成后将路径添加到环境变量**Path**中。(安装过程很简单就不多说)

Linux或MAC下直接执行

```
sudo apt-get install git  //Ubuntu

sudo yum install git  //CentOS
```

### 二、设置用户名和用户邮件地址

在git中利用如下两行代码设置用户名和邮箱：

```
git config --global user.name "用户名"
git config --global user.email "邮箱地址"
```

### 三、获取SSH密钥

这一步会在相应文件夹下生成密钥文件，具体路径生成完成之后可以看得到

```
ssh-keygen -t rsa -C "your_email@example.com"
//执行命令之后一路回车即可
```

Windows用户生成路径一般在

```
/c/Users/chenjs/.ssh/id_rsa.pub
```

Linux或Mac用户生成密钥路径一般在

```
~/.ssh/id_rsa_.pub
```

### 四、添加Git的SSH密钥

登录自己的github账户，按照如下顺序操作
![登录github账户选择设置](/images/githubWithMD.assets/1.png)

![选择SSH和GPG密钥进行添加](/images/githubWithMD.assets/2.png)

这里标题可以随便填，下面的Key则是刚刚我们生成的**id_rsa.pub**里的所有内容
![命名密钥并填写密钥](/images/githubWithMD.assets/3.png)

### 五、GitHub操作

### 1、下载操作

> 下载操作只需要执行如下命令即可

```
git clone https://github.com/用户名/仓库名.git
例:git clone https://github.com/Arthas/kj.git
```

### 2、上传操作

> 上传操作需要先在网页版上新建一个库并得到库的地址才可以进行。

![新建一个库](/images/githubWithMD.assets/4.png)
![新建一个库](/images/githubWithMD.assets/5.png)

> 如下图，新的库建立之后我们就可以获取到他的地址,并且官方也给出了链接的使用方法

![新建一个库](/images/githubWithMD.assets/6.png)

新建好远程仓库后我们就可以在电脑上创建一个对应的文件夹，在当前文件夹下试着添加一个文件，例如**demo.txt**,然后执行如下代码

```
git init    //初始化当前文件夹
git add .   //提交当前文件夹下所有文件
git commit -m “first commit”    //提交时的备注信息
git remote add origin https://github.com/用户名/demo2.git
 git push -u origin master   //开始提交
```

![新建一个库](/images/githubWithMD.assets/7.png)

![新建一个库](https://arthaskj.github.io/Photo/github/8.png)

> 这样，本地的文件就可以同步到github上，我们可以在任何一台装有git的软件上将文件再次下载下来，比如我们在公司做的项目没有做完，就可以将项目同步到github上，回到家再将项目下载下来继续工作。**不过要记住，一个项目如果在两台设备上都clone过，那么其中一台提交更新之后，另一台设备想要上传更新必须先UpDate项目之后在更新。**

```
git pull    //更新本地仓库的数据
```

### 六、使用GitHub注意事项

- 不要把链接数据或者其他的密码文件直接上传到github，因为github仓库是对所有可见，别人可以随便下载你的代码，如果保存有真实的密码信息，就会发生危险。当然，github也有付费的账户，可以将仓库设为私有，这样别人就无法访问，怎么抉择取决于你的使用方式。
- 如果在进行push操作的时候报错，不需要太紧张，多数原因是因为你没有先pull的原因，所以在对仓库里的项目进行编辑之前先**git pull**一下，确保版本不会错乱，因为一旦同一个文件在不同的设备被提交，github会将两者都保存下来，并在相应的地方进行版本标注，这样以来里的代码就很可能编译不通过或者执行失败。
- github每个项目都是项目主人辛苦的结晶，虽然github奉行开源的理念，但是大家在引用别人的项目时最好还是声明一下，这也是对别人的尊重。同时项目主人也可以选择生成声明文件来表示该项目是一个开源项目。只要用户在项目副本中包含了版权声明和许可声明，他们就可以拿你的代码做任何想做的事情，你也无需承担任何责任。

![新建一个库](https://arthaskj.github.io/Photo/github/9.png)

### 七、我用GitHUb来做什么

除了用来管理代码之外，我还会将GitHub用做网页展示，以及网络硬盘。

通过设置，可以将GitHub仓库设置成一个服务器，但是功能有限，而且国内访问会非常慢。因此仅仅作为一个展示即可。

![新建一个库](https://arthaskj.github.io/Photo/github/10.png)

![新建一个库](https://arthaskj.github.io/Photo/github/11.png)

但是我们可以把这个服务器作为一个网络图床，知道MarkDown的同学应该都知道MarkDown并不支持图片上传，他是通过链接来显示图片的，所以与其把图片放在自己的服务器上浪费空间，不如直接放在GitHub仓库里，并将这个仓库设置成服务器，那么我们就可以通过上面的链接加上图片名称就可以链接或者下载到图片。就像这篇文章里的所有照片都是放在GitHub上通过如下方式链接到Markdown文章里的

```
![新建一个库](https://用户名.github.io/Photo/github/11.png)

```

![新建一个库](https://arthaskj.github.io/Photo/github/12.png)