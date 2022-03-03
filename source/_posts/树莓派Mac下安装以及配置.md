title: 树莓派Mac下安装以及配置
category: 技能
date: <文章日期> [YYYY-MM-DD]

---

树莓派Mac下安装以及配置

<!--more-->

# 一、抹掉sd卡内容

### 1、获取一张sd卡

1、干净的初始卡

如果是没有装过树莓派的sd卡直接用mac系统自带的磁盘工具抹掉就行，注意使用`MS-DOS(FAT)`格式

2、以前装过树莓派的sd卡

1）  首先打卡“Terminal”，用df命令查看当前已挂载的卷

df –h

![](/images/assets/pi/14360535-90030caaa119f1bc.png) 

这时候可以找到对应的boot盘（我的电脑中是/dev/disk2s1）

（2） 使用diskutil unmount将这些分区卸载：

diskutil unmount/dev/disk2s1

（3）  通过diskutil list来查看设备（这里可以看到磁盘真正的大小，图示是我的16G的SD卡）：

diskutil list



![img](/images/assets/pi/14360535-7fffdd44d4c3b89f.png)





（4） 使用指令dd覆盖磁盘的第一个扇区512个字节：

sudo dd bs=512 count=1if=/dev/zero of=/dev/rdisk2

注意！这里是“rdisk2”不是“disk2”，也不用管s1、s2什么的。上面的sudo是系统提示“Permission

denied”，所以用了sudo权限，但是会要求输入密码，输入你的用户密码即可。

（5）  这时候就已经完工啦，只需要把SD卡的读卡器重新拔插一下，Mac上面个就会自动出现“此电脑不能读取您插入的磁盘”如下图

![](/images/assets/pi/14360535-6402680142acd53e.png)

这时候选择“初始化”。然后选中对应SD卡的磁盘，将磁盘格式设置成FAT，名称起成你想要的名称即可啦~最后点击“抹掉”，整个磁盘就会被格式化，一个空的磁盘就恢复啦！

![](/images/assets/pi/14360535-66a6956de6cec5e0.png)



### 2、写入系统镜像

* Mac命令行输入`df -h`，得到挂载的SD卡位置（卷），比如`/dev/disk4s1`。

* 然后使用 `diskutil unmount /dev/disk4s1` 卸载这个分区
   最好，使用 `diskutil list` 这个命令，获得我们要安装系统的设备。
   注意：此处我们看到的是 `/dev/disk4`，但是写核心指令的时候要加一个字母r，即`/dev/rdisk4`。

*  最终得到的就是
   `sudo dd bs=4m if=2017-11-29-raspbian-stretch.img of=/dev/rdisk4`

  这个过程可能会很漫长，耐心等待，别着急的把命令行关掉

* 最后安全退出sd卡
   `diskutil unmountDisk /dev/disk4`

###  3、配置自动连接无限网络以及开启ssh

* 方法非常简单，首先在SD卡的根目录/boot下添加一个名为 `wpa_supplicant.conf`的文件，然后在该文件内添加以下的内容

* ```shell
  ctrl_interface=DIR=/var/run/wpa_supplicant GROUP=netdev
  network={
    ssid="你的WIFI名"
    psk="WIFI密码"
  }
  ```

  插入SD卡启动树莓派就能直接连接到你的WIFI网络了（切记树莓派现时只支持802.11.n的WIFI标准所以只能连接2.4G网络，所以你需要确保你所连接的是2.4G的通道而不是5G的。

* 但是，Raspbian 默认情况下是将SSH服务关闭的，那现在即使连上网又得到IP没有SSH服务怎末能登到上面去啊？

  方法同样很简单在树莓的官网上也有介绍，只要在/boot下新建一个名为`ssh`文件到SD的根目录就OK了。