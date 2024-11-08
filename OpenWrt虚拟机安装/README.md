# 简介  
创建Openwrt虚拟机并设置为旁路由  
# 创建Openwrt虚拟机  
固件下载，这里推荐ImmortalWrt，镜像比较干净，后续需要自己安装插件    
https://firmware-selector.immortalwrt.org  
可以根据自己的主机型号进行选择，一般选择ext4.img格式的固件，方便扩容  
新手也可以选择iStoreOS  
## 安装步骤
1. 选择local>>ISO镜像,上传OpenWrt.img镜像文件，注意任务完成后，复制镜像文件地址，放在记事本备用
2. 创建Openwrt虚拟机，设置VMID和虚拟机名称
3. 操作系统选择不使用任何介质
4. 系统设置默认
5. 磁盘可以删除，安装完成后再添加扩容
6. CPU核心按需分配，一般全部核心都可以分给虚拟机，类别选择host
7. 内存按需设置，一般1G就绰绰有余了
8. 网络类型推荐VirtIO，虚拟机创建完成，先不要启动，接下来配置虚拟机。
9. 选择PVE节点的shell（注意不是虚拟机的），输入下面的代码
```ruby
qm importdisk 101 /var/lib/vz//template/iso/OpenWrt.img local-lvm #代码的格式为qm importdisk “虚拟机代码” “刚刚复制到记事本的镜像文件路径” local-lvm
```
11. 选择虚拟机>>硬件，双击未使用的硬盘，格式改为SATA
12. 选择虚拟机>>选项，将开机自启动更改为是，引导顺序将SATA调整至第一项并打勾
13. 开启虚拟机，自动安装，界面不动后按回车进入系统
14. 输入下面代码修改ip
```ruby
vi /etc/config/network
```

    按“i”进入编辑模式，修改OpenWrt的ip地址即路由器的登陆地址  
    修改完成后按esc，输入
```ruby
:wq
``` 
15. 修改登录密码，输入
```ruby
passwd
```

    将你想要的登陆密码输入2遍  
16. 重启路由
```ruby
reboot
```

