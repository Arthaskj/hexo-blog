
title: SVN仓库无损迁移到GIT仓库
category: 技能
date: 2021-04-27
index_img: /images/svn2git.assets/image-20210427112121754.png

---

公司之前的项目都是放在svn上，现在要求将代码迁移到Git仓库，并且保留svn提交记录，经过查询，发现可以使用`git-svn`来解决这个问题。

<!--more-->


# SVN仓库无损迁移到GIT仓库
公司之前的项目都是放在svn上，现在要求将代码迁移到Git仓库，并且保留svn提交记录，经过查询，发现可以使用`git-svn`来解决这个问题。



## 拉取svn代码

```shell
git svn clone -r svn地址 --no-metadata

# 如果仓库历史很长，全部迁移会耗费很长时间，可以使用 版本号:HEAD 参数只拉取版本号之后的提交历史
git svn clone -r 35125:HEAD svn地址 --no-metadata

```

如果旧的项目很庞大的话迁移时间就会很长，我们的项目有85000+次提交记录，耗时大概18h左右。所以，如果更久以前的提交记录已经没有参考意义的话，可以从最近的提交开始拉取代码。



## 设置git remote

在拉取后的svn文件夹中添加remote

```shell
git remote add git仓库地址

git push 
```



## git-svn常用命令

```shell
git add  # 提交到暂存区
git commit # 暂存区提交到本地仓库
git push  # 提交到远程仓库
git svn dcommit # 提交到远程仓库
git svn fetch # 取回远程仓库所有分支的变化
git svn rebase # 取回远程仓库当前分支的变化，并与本地分支变基合并
```

![image-20210427112121754](/images/svn2git.assets/image-20210427112121754.png)

![image-20210427112323551](/images/svn2git.assets/image-20210427112323551.png)



## 后续提交处理

如果后续在svn仓库上又提交了代码，可以直接使用`merge`或者`cherry-pick`进行代码同步。此时的svn仓库就是git仓库中的一个分支，分支名称`git-svn`。

![image-20210427135404526](/images/svn2git.assets/image-20210427135404526.png)

![image-20210427135246729](/images/svn2git.assets/image-20210427135246729.png)

![image-20210427135317485](/images/svn2git.assets/image-20210427135317485.png)