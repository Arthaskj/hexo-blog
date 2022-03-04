title: NPM发布自己的包
category: 技能
tags: npm
date: 2021-04-28
index_img: /images/npm.assets/image-20210428090528794.png

---



<!--more-->

# NPM

## 发布自己的包

```bash
npm install [name]   //检查npm 是否存在Copy to clipboardErrorCopied
```

如果没有 新建一个 [name] 的文件夹

```bash
cd  [name]
npm init  -yCopy to clipboardErrorCopied
```

进入 package.json 文件

```json
 "name": "name", //必填项目名称
 "version": "0.0.1" //必填版本信息
 "repository": { //关联github 的库
    "type": "git",
    "url": "git@github.com:****/**.git"
  },Copy to clipboardErrorCopied
```

登录 npm

```bash
 npm login //输入用户名、密码和邮箱Copy to clipboardErrorCopied
```

更新 npm 包 修改 package.json 文件夹中的 version 版本信息 执行以下命令

```bash
 npm publishCopy to clipboardErrorCopied
```

利用 npm 撤销发布包

```bash
 npm unpublish 包名
```



## nvm

nvm 是一个可以让你在同一台机器上安装和切换不同版本的 node 管理工具

nvm 不支持 Windows 环境

```bash
npm  install   -g   nvm //安装nvm
nvm install ##  //安装指定node版本，可模糊安装，如：安装v6.2.0，既可nvm install v6.2.0，又可nvm install 6.2
nvm uninstall ## //删除已安装的指定版本，语法与install类似
nvm use ## //切换使用指定的版本node
nvm ls ## //列出所有安装的版本
nvm ls-remote ## //列出所以远程服务器的版本（官方node version list）
nvm current ## //显示当前的版本
nvm alias ## //给不同的版本号添加别名
nvm unalias ## //删除已定义的别名
nvm alias default ## //全局设置默认版本
nvm reinstall-packages ## //在当前版本node环境下，重新全局安装指定版本号的npm包
```

![image-20210428090528794](/images/npm.assets/image-20210428090528794.png)



## nrm 自由切换 npm 源

nrm(npm registry manager )是 npm 的镜像源管理工具,允许你在不同的 npm 源自由切换

```bash
nrm help　 查看nrm帮助
nrm ls  列出可用的源
nrm current 查看当前使用的源
nrm use [name] 选择国内淘宝的源
nrm home [name]　　跳转到指定源的官网
nrm test [name] 测试速度
nrm add [name]  [url]  添加源
nrm del  [name] 删除对应的源
```



![image-20210428090608010](/images/npm.assets/image-20210428090608010.png)

