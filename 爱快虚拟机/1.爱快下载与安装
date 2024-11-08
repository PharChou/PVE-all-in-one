# 简介  
创建爱快主路由虚拟机并设置网卡直通  
# 准备工作  
## 识别网口（一般设备是正常顺序，此步骤可忽略）  
1. 安装ethtool
```ruby
apt install ethtool -y
```
2. 打开端口自动启动&重启系统
3. 确认所有网卡设备位置
```ruby
lspci | grep -i 'eth'
```
4. 通过ethtool识别网口对应设备位置以及系统设备名
```ruby
ethtool -i [设备名称]  #查看设备名对应设备位置
ethtool [设备名称]  #通过查看是否连接确认设备名对应实际网口，如果硬件支持可以使用ethtool --identify [设备名] 命令确认）
```
5. 关闭端口自动启动&重启系统
## 设置网卡直通
BIOS中打开硬件直通相关选项（VT-d & VMX）  
编辑Grub
```ruby
nano /etc/default/grub
```
注释原条目，并增加开启参数
```ruby
GRUB_CMDLINE_LINUX_DEFAULT="quiet intel_iommu=on"
```
如果你的pcie设备分组有问题也可以换成这一行对分组拆分（直通遇到问题都可以尝试这个）
```ruby
GRUB_CMDLINE_LINUX_DEFAULT="quiet intel_iommu=on pcie_acs_override=downstream"
```
更新grub
```ruby
update-grub
```
# 创建爱快虚拟机  
爱快固件下载地址：https://www.ikuai8.com/component/download  
推荐下载ISO固件，64位需要至少1G硬盘和4G内存  
1. 选择local>>ISO镜像,上传爱快镜像文件
2. 创建ikuai虚拟机，设置VMID和虚拟机名称
3. 操作系统选择使用CD/DVD镜像文件，选择刚刚上传的爱快镜像文件，类型选最新的linux
4. 系统设置默认
5. 磁盘类型更改为SATA，大小至少1G
6. CPU核心按需分配，一般全部核心都可以分给虚拟机，类别选择host
7. 内存至少设置为4096M
8. 网络类型推荐VirtIO，虚拟机创建完成，先不要启动，接下来配置虚拟机。
9. 选择刚创建的虚拟机>>硬件>>添加PCI设备，依次添加想要直通的网卡
10. 选择虚拟机>>选项，将开机自启动更改为是，引导顺序将SATA调整至第一项并打勾
11. 开启虚拟机，开始安装爱快系统
12. 按1选择将系统安装到硬盘1 sda
13. 安装完成后按R重启，打开控制台，首先需要更改ikuai后台ip地址，跟pve同网段，后续登陆浏览器操作即可
