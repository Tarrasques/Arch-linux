# ArchLinux单系统安装

### 安装准备

1、U盘>=16G，ArchLinux官方镜像，镜像写入工具

2、镜像写入工具推荐：balenaEtcher-Portable，优启通，deepin官方写入工具

3、利用镜像写入工具把ArchLinux镜像写入U盘中

4、启动到ArchLinux的live环境设置
```
写入U盘中后重启电脑，设置启动顺序，大家自行搜索自己电脑品牌+启动顺序这个关键词来进行设置。
一般来说现在的主板都可以不用进入BIOS而快速地切换启动顺序，只要找到相应的快捷键就可以了。
```

### 安装过程

#### 1、连接网络
```
电脑连接有线网络并支持DHCP：执行dhcpcd获取ip地址
电脑没网可以连接无线网络：执行wifi-menu进入字符图形界面连接网络

连接网络后通过 ping www.baidu.com 看是否有网络跳动，判断网络连接是否正常
```

#### 2、同步系统时间
```
timedatectl set-ntp true
```

### 3、替换仓库列表
```
使用nano编辑器编辑 /etc/pacman.d/mirrorlist文件按F6搜索自己想设置的源的关键字

清华源关键字：tuna
网易源关键字：163
重大源关键词：cqu
中科大源关键词：ustc

输入后回车，光标所在的位置就是源链接位置，
通过ctrl+k剪切，把光标移动到最上面ctrl+u粘贴
ctrl+o保存，回车确定，ctrl+x退出
执行pancman -Syy更新源数据
```

### 4、建立硬盘分区与格式化
```
通过指令 fdisk -l 或者 lsblk查看自己电脑的分区
找到自己想要安装的硬盘并记住硬盘分区的编号（比如/dev/sda，下面以这个为基础）

执行 cfdisk /dev/sda 进入字符界面分区 主要分三个分区：EFI分区，swap交换分区，Linux System date系统主分区

*分出来的区编号不一样，我这儿举个例子作为参考
EFI分区大小：100M~512M         /dev/sda1
Swap交换分区：500M~2G          /dev/sda2
Linux系统主分区：剩下所有大小   /dev/sda3

通过delete删掉你选择的硬盘所有分区
用new来分出各个分区
使用type来设置各个分区的类型
设置完后选择write输入yes回车 选择quite退出

下面格式化分区：
mkfs.fat /dev/sda1
mkswap /dev/sda2
mkfs.ext4 /dev/sda3

挂载分区
mount /dev/sda3 /mnt
swapon /dev/sda2
mkdir /mnt/boot
mount /dev/sda1 /mnt/boot
输入lsblk查看是否挂载成功
```
### 5、下载安装必要的包
```
输入指令 pacstrap /mnt base base-devel linux linux-lts linux-lts-header linux-firmware wqy-zenhei ttf-dejavu wqy-microhei adobe-source-code-pro-fonts -y
```
### 6、生成fstab分区表
```
输入指令 genfstab -U /mnt >> /mnt/etc/fstab
```
### 7、进入半成品系统进行配置
```
*不要重启！不要重启！不要重启！现在还是半成品，重启电脑会前功尽废
输入指令 arch-chroot /mnt 进入半成品系统
```
