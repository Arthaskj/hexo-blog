title: Git常用命令
category: 技能
date: 2019-08-14
index_img: 

---

Git常用命令

<!--more-->

### Git操作

```shell
git status 
git checkout -- */filename
git branch
git branch -a
git checkout -b localname onlinename

//git本地创建分支
git checkout -b dbg_lichen_star  //本地创建新分支
git push origin dbg_lichen_star:dbg_lichen_star  //推送到远程分支 
git push origin :dbg_lichen_star   //删除远程分支
git push origin --delete release-0.1.0   //删除远程分支

//git创建tag
git tag
git tag -a tagname -m 'tagDec'
git push origin --tags    ||   git push origin tagname

#--no-ff：不使用fast-forward方式合并，保留分支的commit历史
#--squash：使用squash方式合并，把多次分支commit历史压缩为一次
git merge --no-ff feature

git rebase -i <commit版本号>
# 将pick改为s，只留一个pick
# 精简注释

// 拉取指定分支代码
git fetch origin dev:dev
```



## Git Alisa 设置别名

```shell
# 获得非常漂亮的日志。
git config --global alias.plg "log --graph --pretty=format:'%C(yellow)%h%Creset -%Cred%d%Creset %s %Cgreen| %cr %C(bold blue)| %an%Creset' --abbrev-commit --date=relative"

# 使用--no-edit标志进行修改，可以在最近一次提交时在仓库中提交新的更改，你不需要再次重复提交消息。
git config --global alias.commend 'commit --amend --no-edit'

# 带有--orphan 标志的git checkout允许您创建一个分支，而没有来自父分支的任何历史记录。
git config --global alias.newbie 'checkout --orphan'

# 带有--single-branch --branch标志的git clone允许你从存储库中clone特定分支，当然是减少clone时间
git config --global alias.clonely 'clone --single-branch --branch'

[alias]
        co = checkout
        st = status
        ca = add
        cc = commit
        brv = branch --v
        geturl = config --get remote.origin.url
        cbuild = "git add . && git commit -m '--kci--' && git push"
        recommit = commit --amend -m
        commend = commit --amend --no-edit
        here = !git init && git add . && git commit -m \"Initialized a new repository\"
        search = grep
        who = blame
        zip = archive --format=tar.gz -o ../repo.tar.gz
        lonely = clone --single-branch --branch
        plg = log --graph --pretty=format:'%C(yellow)%h%Creset -%Cred%d%Creset %s %Cgreen| %cr %C(bold blue)| %an%Creset' --abbrev-commit --date=relative
        fresh = filter-branch --prune-empty --subdirectory-filter

```





### 带历史记录的迁移git仓库

**1. git push --mirror**

--mirror模式会把本地的分支都克隆

```
// 先用--bare克隆裸仓库
git clone git@gitee.com:zhangamie/testApp.git --bare
// 进入testApp.git，这样就可以把所有分支都克隆到新仓库了
git push --mirror git@gitee.com:zhangamie/testApp2.git
```

 或

```
// 普通模式克隆仓库
git clone git@gitee.com:zhangamie/testApp.git
// push所有本地已存在的分支（不管是否有对应的远程分支）
git push --mirror git@gitee.com:zhangamie/testApp2.git
```

 

**2. 加远程源**

```
git remote add origin2 git@gitee.com:zhangamie/testApp2.git
git push origin2
git checkout dev
git push origin2

git remote set-url --add origin <远程仓库地扯>
...
```

这种方式的缺点是只能一次push一个分支，如果要克隆所有分支，需要checkout到各个分支然后push

 

**3. git subtree**

推荐阅读：[git subtree操作](https://segmentfault.com/a/1190000012002151)

上面2种都是整个仓库的迁移，而git subtree可以把某个子目录拆出去

例如想把仓库的目录src/apps/testApp拆出去一个仓库，并且带上这个目录的所有提交历史记录

首先我们得创建一个空的仓库（记得别带任何初始化文件，例如README.md）

例如：git@github.com:xxxx/testApp.git master

然后到主仓库执行这段git命令，就可以到testApp看到代码啦

```
git subtree push --prefix=src/apps/testApp git@github.com:xxxx/testApp.git master
```



# Git tag相关命令

常见命令如下：

```
// 查看标签,可加上参数-l(列表形式列出） -n(附加说明)
git tag [-l -n]
// 查看符合检索条件的标签 
git tag -l 1.*.* 
// 查看对应标签状态 
git checkout 1.0.0 
// 创建标签(本地)
git tag 1.0.0-light 
// 创建带备注标签(推荐) 
git tag -a 1.0.0 -m "这是备注信息" 
// 针对特定commit版本SHA创建标签 
git tag -a 1.0.0 0c3b62d -m "这是备注信息" 
// 删除标签(本地) 
git tag -d 1.0.0 
// 将本地所有标签发布到远程仓库
git push origin --tags 
// 指定版本发送 
git push origin 1.0.0 
// 删除远程仓库对应标签（Git版本 > V1.7.0）
git push origin --delete 1.0.0 
// 旧版本Git 
git push origin :refs/tags/1.0.0
// 获取远程标签
git fetch origin tag "标签名称"
```