title: 树莓派下安装node和npm
category: 技能
date: <文章日期> [YYYY-MM-DD]

---

网上找了很多树莓派安装node和npm的方法，大多数跟杂乱，设置也非常麻烦，最后找到一个最简洁的方法。

<!--more-->
网上找了很多树莓派安装node和npm的方法，大多数跟杂乱，设置也非常麻烦，最后找到一个最简洁的方法。

通过nvm来安装

执行`curl -o- https://raw.githubusercontent.com/creationix/nvm/v0.33.11/install.sh | bash`

然后执行`source ~/.bashrc`

通过命令 `nvm --version` 可查看版本，

说明安装成功到nodejs.org找到最新推荐的版本。

目前是。10.15.1

执行`nvm install v10.15.1`

安装后可通过`node -v ,npm -v` 查看版本，说明node和npm安装成功
