# 简介
本教程适用于两个或以上网卡N5105主机配置PVE服务器，并在服务器中配置iKuai主路由，Openwrt旁路由，win11虚拟主机以及基于LXC容器的Docker服务。
# 网络拓扑结构
# PVE下载与安装
PVE下载官网：https://www.proxmox.com/en/downloads  
写盘工具：https://rufus.ie/downloads/  
用rufus写入PVE的ISO到U盘中，使用DD模式写入  
# PVE标准版本安装系统到EMMC磁盘方法
参考文档地址：https://ibug.io/blog/2022/03/install-proxmox-ve-emmc/  
这个方法实测在PVE 7.X和PVE 8.X版本都可以用  
官方原版ISO直接安装的报错  
Unable to get device for partition 1 on device /dev/mmcblk0  
## 安装到EMMC的命令
1. 安装的时候选择Advanced Option——Install Proxmox VE(Debug Mode)
2. 然后在启动过程第一次被打断的时候，按Ctrl+D 继续。
3. 在第二次被打断的时候，输入，大家对照着自己的PVE版本输入命令  
  PVE 7.X版本  
    vi /usr/bin/proxinstall  
  PVE 8.X版本（注意区分大小写）  
    vi /usr/share/perl5/Proxmox/Sys/Block.pm  
4. 输入
    /unable to get device
   定位到对应位置，键入i键，进行修改
   } elsif ($dev =~ m|^/dev/[^/]+/hd[a-z]$|) {
    return "${dev}$partnum";
} elsif ($dev =~ m|^/dev/nvme\d+n\d+$|) {
    return "${dev}p$partnum";
} else {
    die "unable to get device for partition $partnum on device $dev\n";
}  
    修改第6行代码
   } elsif ($dev =~ m|^/dev/[^/]+/hd[a-z]$|) {
     return "${dev}$partnum";
 } elsif ($dev =~ m|^/dev/nvme\d+n\d+$|) {
     return "${dev}p$partnum";
 } else {
     return "${dev}p$partnum";
 }
