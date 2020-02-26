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

### 四、建立硬盘分区与格式化
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
### 五、下载安装必要的包
```
输入指令 pacstrap /mnt base base-devel linux linux-lts linux-lts-header linux-firmware wqy-zenhei ttf-dejavu wqy-microhei adobe-source-code-pro-fonts -y
```
### 六、生成fstab分区表
```
输入指令 genfstab -U /mnt >> /mnt/etc/fstab
```
### 七、进入半成品系统进行配置
```
*不要重启！不要重启！不要重启！现在还是半成品，重启电脑会前功尽废
输入指令 arch-chroot /mnt 进入半成品系统

1、设置系统时间地区和语言
ln -sf /usr/share/zoneinfo/Asia/Shanghai /etc/localtime
hwclock --systohc --utc
mkinitcpio -p linux
输入 nano /etc/locale.gen 编辑
在文头加入 en_US.UTF-8 UTF-8
换行再加入 zh_CN.UTF-8 UTF-8
保存后编辑 /etc/locale.conf 加入  LANG=en_US.UTF-8
执行 locale-gen

2、安装驱动程序
输入 arch-chroot /mnt 进入半成品系统
执行以下代码
pacman -S xorg-server xorg-twm xorg-xclock -y
pacman -S bumblebee -y
systemctl enable bumblebeed
pacman -S nvidia-dkms nvidia-utils nvidia-settings xf86-input-synaptics -y
pacman -S intel-ucode -y

3、安装网络通讯管理程序
执行一下代码
pacman -S --noconfirm iw wireless_tools wpa_supplicant dialog netctl networkmanager networkmanager-openconnect rp-pppoe network-manager-applet net-tools ntfs-3g -y
systemctl enable NetworkManager.service

4、设置主机名
编辑 /etc/hostname 在第一行输入自己设定的hostname
nano /etc/hostname
编辑 /etc/hosts 
nano /etc/hosts
添加以下代码（自行把hostname改为自己设定的主机名）
127.0.0.1     localhost
::1           localhost
127.0.0.1     hostname.localdomain   hostname
保存并退出

5、设置普通用户密码以及root用户密码
I、root用户密码
执行 passwd 按照提示输入两次密码（输入时密码为隐藏的）
II、添加普通用户及设置密码
添加用户
useradd -m -g users -G wheel -s /bin/bash 用户名
设置密码
passwd 用户名
修改 /etc/sudoers
nano /etc/sudoers
将开头为%wheel前面的#删除并保存
```
### 八、安装配置引导程序
```
验证自己是BIOS还是EFI：
执行 ls /sys/firmware/efi/efivars
提示 ls: cannot access '/sys/firmware/efi/efivars': 
No such file or directory 为BIOS，反则为EFI

BIOS系统执行以下命令：
pacman -S grub -y
grub-install --target=i386-pc /dev/sda
grub-mkconfig -o /boot/grub/grub.cfg

EFI系统执行以下命令：
pacman -S grub efibootmgr -y
grub-install && grub-mkconfig -o /boot/grub/grub.cfg
```
### 九、卸载被挂载的分区重启进入ArchLinux
```
输入 eixt 退出arch-chroot /mnt
输入以下指令：
umount -R /mnt
reboot
```
