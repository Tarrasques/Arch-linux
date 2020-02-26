### ArchLinux常用命令
```
pacman -S                        #安装程序包
pacman -Ss                       #搜索包
pacman -Scc                      #清理已下载的包和数据库
pacman -Syu                      #同步源并更新系统
pacman -Sy                       #同步源
pacman -Syy                      #同步软件包数据库
pacman -R                        #卸载包
pacman -Rsun                     #卸载包组
pacman -Rscn                     #删除包和包的依赖
pacman -Q                        #列出已安装的包
systemctl enable                 #设置开机启动
passwd                           #设置root密码
passwd 用户名                     #设置用户密码
useradd                          #添加普通用户
userdel                          #删除普通用户
cat                              #查看文件
rm                               #删除文件     
rm -f                            #强制性删除
rm -f                            #强制性删除
rmdir                            #删除文件夹
rmdir -rf                        #删除文件夹及目录以下文件
cd                               #进入某个目录
cd ..                            #返回上一级目录
cd ../..                         #返回上两级目录
cd /                             #返回根目录
ls                               #查看目录中文件
ls -F                            #查看目录中的文件 
ls -l                            #显示文件和目录的详细资料 
ls -a                            #显示隐藏文件 
tree                             #显示文件和目录由根目录开始的树形结构
lstree                           #显示文件和目录由根目录开始的树形结构
mkdir [文件夹名] [文件夹名]         #创建一个或多个文件夹  
mkdir -p /[文件夹名]/[文件夹名]     #创建文件目录树
mv                               #重命名/移动 文件/目录
cp                               #复制文件/目录
find [path] [file]               #再path目录中查找file文件
```
