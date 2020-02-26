# ArchLinux+Windows双系统教程

### 安装准备

1、U盘>=16G，ArchLinux官方镜像，镜像写入工具

2、镜像写入工具推荐：balenaEtcher-Portable，优启通，deepin官方写入工具

3、利用镜像写入工具把ArchLinux镜像写入U盘中

4、在windows中利用系统自带的硬盘工具从系统盘中分出20G~30G内存
```
分出的内存新建分区但是不要给分区盘符和格式化（分区和格式和会在Arch的live中解决）
```
5、启动到ArchLinux的live环境设置
```
写入U盘中后重启电脑，设置启动顺序，大家自行搜索自己电脑品牌+启动顺序这个关键词来进行设置。
一般来说现在的主板都可以不用进入BIOS而快速地切换启动顺序，只要找到相应的快捷键就可以了。
```

### 安装过程

#### 一、连接网络
```
电脑连接有线网络并支持DHCP：执行dhcpcd获取ip地址
电脑没网可以连接无线网络：执行wifi-menu进入字符图形界面连接网络

连接网络后通过 ping www.baidu.com 看是否有网络跳动，判断网络连接是否正常
```

#### 二、同步系统时间
```
timedatectl set-ntp true
```

### 三、替换仓库列表
```
使用nano编辑器编辑 /etc/pacman.d/mirrorlist文件按F6搜索自己想设置的源的关键字

清华源关键字：tuna
网易源关键字：163
重大源关键词：cqu
中科大源关键词：ustc

输入后回车，光标所在的位置就是源链接位置，
通过ctrl+k剪切，把光标移动到最上面ctrl+u粘贴
ctrl+o保存，回车确定，ctrl+x退出（后面只要利用到nano都是这样保存）
执行pancman -Syy更新源数据
```

### 四、检查分区，分区&&格式化
```
通过指令 fdisk -l 或者 lsblk 查看电脑硬盘和分区
双系统的话，ArchLinux和Windows都在一个硬盘里（类如都在 /dev/sda）
执行 lsblk /dev/sda 查看分区详情
*例如我这儿是 /dev/nvme0n1   以下以这个为基础，按照自己的硬盘编号修改
-------------------------------------
jack@jack ~> lsblk /dev/nvme0n1
NAME        MAJ:MIN RM   SIZE RO TYPE MOUNTPOINT
nvme0n1     259:0    0 238.5G  0 disk 
├─nvme0n1p1 259:1    0   529M  0 part 
├─nvme0n1p2 259:2    0   100M  0 part
├─nvme0n1p3 259:3    0    16M  0 part 
├─nvme0n1p4 259:4    0 157.9G  0 part 
├─nvme0n1p5 259:5    0    30G  0 part 
-------------------------------------
其中nvme0n1p5就是我们想要装Arch的分区编号

执行 cfdisk /dev/nvme0n1 进入字符界面分区 主要分两个分区：swap交换分区，系统主分区

Swap交换分区：500M~2G         /dev/nvme0n1p5    Linux swap
Linux系统主分区：剩下所有大小   /dev/nvme0n1p6    Linux filesystem

通过delete删掉你选择的硬盘所有分区
用new来分出各个分区
使用type来设置各个分区的类型
设置完后选择write输入yes回车 选择quite退出

下面格式化分区：
mkswap /dev/nvme0n1p5
mkfs.ext4 /dev/nvme0n1p6

挂载分区
mount /dev/nvme0n1p6 /mnt
swapon /dev/nvme0n1p5
mkdir /mnt/boot
mkdir /mnt/boot/EFI
mount /dev/nvme0n1p2 /mnt/boot
输入lsblk查看是否挂载成功