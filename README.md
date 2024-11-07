# 简介
本教程适用于两个或以上网卡N5105主机配置PVE服务器，并在服务器中配置iKuai主路由，Openwrt旁路由，win11虚拟主机以及基于LXC容器的Docker服务。
# 网络拓扑结构
# PVE下载与安装
PVE下载官网：https://www.proxmox.com/en/downloads  
写盘工具：https://rufus.ie/downloads/  
用rufus写入PVE的ISO到U盘中，使用DD模式写入，修改主机Bios从U盘启动  
## PVE标准版本安装系统到EMMC磁盘方法
参考文档地址：https://ibug.io/blog/2022/03/install-proxmox-ve-emmc/  
这个方法实测在PVE 7.X和PVE 8.X版本都可以用  
官方原版ISO直接安装的报错  
Unable to get device for partition 1 on device /dev/mmcblk0  
### 安装到EMMC的命令
1. 安装的时候选择Advanced Option——Install Proxmox VE(Debug Mode)
2. 然后在启动过程第一次被打断的时候，按Ctrl+D 继续。
3. 在第二次被打断的时候，输入，大家对照着自己的PVE版本输入命令
  PVE 7.X版本
```ruby
vi /usr/bin/proxinstall
```
  PVE 8.X版本（注意区分大小写）
```ruby
vi /usr/share/perl5/Proxmox/Sys/Block.pm
```
4. 输入  
```ruby
/unable to get device  
```
  定位到对应位置，键入i键，进行修改
```ruby
} elsif ($dev =~ m|^/dev/[^/]+/hd[a-z]$|) {
    return "${dev}$partnum";
} elsif ($dev =~ m|^/dev/nvme\d+n\d+$|) {
    return "${dev}p$partnum";
} else {
    die "unable to get device for partition $partnum on device $dev\n";
}
```
  修改第6行代码
```ruby
} elsif ($dev =~ m|^/dev/[^/]+/hd[a-z]$|) {
     return "${dev}$partnum";
} elsif ($dev =~ m|^/dev/nvme\d+n\d+$|) {
     return "${dev}p$partnum";
} else {
     return "${dev}p$partnum";
} 
```
  修改完成后按键盘ESC键后输入
```ruby
:wq
```
  保存并退出。  
5. 保存退出后按Ctrl+D进图形化界面即可识别到内置的 EMMC 并安装。  
6. 安装完成后按Ctrl+D退出安装，拔出U盘并重新启动，系统安装完成。  
### 通过关闭swap来延长EMMC寿命  
前提条件：内存足够大，类似windows的虚拟内存，不需要通过EMMC来做swap交换数据，如果你需要虚拟内存交换数据建议把PVE安装在固态硬盘里面，不要安装在EMMC里面。  
1. 通过SSH连接到PVE的后台  
2. 输入命令后回车
```ruby
nano /etc/fstab
```
3. 用#号注释掉/dev/pve/swap none swap sw 0 0
4. Ctrl+X，Y确认，回车退出
## 安装步骤
按照提示操作，设置管理网口（默认插网线的那个口），管理密码，邮箱（随意），IP，网关，DNS，安装后自动重启  
### PVE换源  
```ruby
wget https://mirrors.ustc.edu.cn/proxmox/debian/proxmox-release-bullseye.gpg -O /etc/apt/trusted.gpg.d/proxmox-release-bullseye.gpg
echo "#deb https://enterprise.proxmox.com/debian/pve bullseye pve-enterprise" > /etc/apt/sources.list.d/pve-enterprise.list
echo "deb https://mirrors.ustc.edu.cn/proxmox/debian/pve bullseye pve-no-subscription" > /etc/apt/sources.list.d/pve-no-subscription.list
```
### Debian换源  
1. 备份并创建新的源文件
```ruby
mv /etc/apt/sources.list /etc/apt/sources.list.bk
nano /etc/apt/sources.list
```
2. Sources.list加入源  
```ruby
deb http://mirrors.ustc.edu.cn/debian stable main contrib non-free
# deb-src http://mirrors.ustc.edu.cn/debian stable main contrib non-free
deb http://mirrors.ustc.edu.cn/debian stable-updates main contrib non-free
# deb-src http://mirrors.ustc.edu.cn/debian stable-updates main contrib non-free
# deb http://mirrors.ustc.edu.cn/debian stable-proposed-updates main contrib non-free
# deb-src http://mirrors.ustc.edu.cn/debian stable-proposed-updates main contrib non-free
```
3. Ctrl+X，Y确认，回车退出
4. 更新&安装ethtool
```ruby
apt update
apt upgrade -y
```
