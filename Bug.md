# 执行 archlinuxcn-keyring 更新密匙出现无法部署错误
```
rm -rf /etc/pacman.d/gunpg
pacman-key --init
pacman-key archlinux
pacman -S archlinuxcn-keyring
pacman-key archlinuxcn
```
# 关机出现a stop job is running for session c1 of user root 1min30s
```
sudo vim /etc/systemd/system.conf
将以下语句前面的#删除并修改数值
DefaultTimeoutStartSec=10s
DefaultTimeoutStopSec=10s
```