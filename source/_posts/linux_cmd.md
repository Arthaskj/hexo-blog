title: Linux硬件命令查询
category: 技能
date: 2021-05-07

---

linux查看系统的硬件信息，并不像windows那么直观，这里我罗列了查看系统信息的实用命令，并做了分类，实例解说。

<!--more-->

# Linux硬件命令查询



linux查看系统的硬件信息，并不像windows那么直观，这里我罗列了查看系统信息的实用命令，并做了分类，实例解说。

## **cpu**

lscpu命令，查看的是cpu的统计信息.

```shell
blue@blue-pc:~$ lscpu
Architecture:          i686            #cpu架构
CPU op-mode(s):        32-bit, 64-bit
Byte Order:            Little Endian   #小尾序
CPU(s):                4               #总共有4核
On-line CPU(s) list:   0-3
Thread(s) per core:    1               #每个cpu核，只能支持一个线程，即不支持超线程
Core(s) per socket:    4               #每个cpu，有4个核
Socket(s):             1               #总共有1一个cpu
Vendor ID:             GenuineIntel    #cpu产商 intel
CPU family:            6
Model:                 42
Stepping:              7
CPU MHz:               1600.000
BogoMIPS:              5986.12
Virtualization:        VT-x            #支持cpu虚拟化技术
L1d cache:             32K
L1i cache:             32K
L2 cache:              256K
L3 cache:              6144K
```

 

查看/proc/cpuinfo,可以知道每个cpu信息，如每个CPU的型号，主频等。

```
#cat /proc/cpuinfo
processor    : 0
vendor_id    : GenuineIntel
cpu family    : 6
model        : 42
model name    : Intel(R) Core(TM) i5-2320 CPU @ 3.00GHz
.....
```

上面输出的是第一个cpu部分信息，还有3个cpu信息省略了。

 

## **内存**

概要查看内存情况

```
free -m
             total       used       free     shared    buffers     cached
Mem:          3926       3651        274          0         12        404
-/+ buffers/cache:       3235        691
Swap:         9536         31       9505
```

这里的单位是MB，总共的内存是3926MB。

## **磁盘**

查看硬盘和分区分布

```
# lsblk
NAME   MAJ:MIN RM   SIZE RO TYPE MOUNTPOINT
sda      8:0    0 465.8G  0 disk 
├─sda1   8:1    0     1G  0 part /boot
├─sda2   8:2    0   9.3G  0 part [SWAP]
├─sda3   8:3    0  74.5G  0 part /
├─sda4   8:4    0     1K  0 part 
├─sda5   8:5    0 111.8G  0 part /home
└─sda6   8:6    0 269.2G  0 part 
```

显示很直观

 

如果要看硬盘和分区的详细信息

```
# fdisk -l

Disk /dev/sda: 500.1 GB, 500107862016 bytes
255 heads, 63 sectors/track, 60801 cylinders, total 976773168 sectors
Units = sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 4096 bytes
I/O size (minimum/optimal): 4096 bytes / 4096 bytes
Disk identifier: 0x00023728

   Device Boot      Start         End      Blocks   Id  System
/dev/sda1   *        2048     2148351     1073152   83  Linux
/dev/sda2         2148352    21680127     9765888   82  Linux swap / Solaris
/dev/sda3        21680128   177930239    78125056   83  Linux
/dev/sda4       177932286   976771071   399419393    5  Extended/dev/sda5       177932288   412305407   117186560   83  Linux
/dev/sda6       412307456   976771071   282231808   83  Linux
```

 

## lspci

lspci是另一个命令行工具，可以用来列出所有的 PCI 总线，还有与 PCI 总线相连的设备的详细信息，比如 VGA 适配器、显卡、网络适配器、usb 端口、SATA 控制器等。

```
lspci
```



## df

df命令能够列出不同分区的概要信息、挂载点、已用的和可用的空间。

可以在使用df命令的时候加上-H参数。

```
df -H
```