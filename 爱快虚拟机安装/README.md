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
