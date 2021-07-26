#### 基本环境设置
----
1、本次实验使用的Host OS为：**ubuntu-20.04.2.0-desktop-amd64.iso** 
Host OS的安装过程为标准virtualbox虚机安装，本次实验希望能使用virtualbox的共享目录作为与外部交换的空间，所以在安装完ubuntu虚机后需要安装virtualbox扩展增强包，本次实验的外部共享目录如下：
```
ls /mnt/extstore -la
total 4
drwxrwx--- 1 root vboxsf    0 7月  24 10:59 .
drwxr-xr-x 3 root root   4096 7月  21 18:44 ..
drwxrwx--- 1 root vboxsf    0 7月  24 10:39 iso
-rwxrwx--- 1 root vboxsf    0 7月  24 10:42 spice-env.md
```

2、本次实验是基于virtualbox虚机来完成，virtualbox虚机作为HOST，需要启用virtualbox的VT-X/AMD-T硬件虚拟化功能，基本设置如下：
```
C:\Program Files\Oracle\VirtualBox>VBoxManage.exe modifyvm "dev-desk" --nested-hw-virt on
```
其中**dev-desk**为需要设置硬件虚拟化的virtualbox虚机名，设置完成后需要重启虚机生效；检查生效的命令如下：
```
#lsmod|grep kvm
kvm_intel             286720  0
kvm                   712704  1 kvm_intel
#s /dev/|grep kvm
kvm
```

3、Host安装KVM及虚拟化管理软件包
```
sudo apt-get install qemu-kvm libvirt-daemon-system libvirt-clients bridge-utils virtinst virt-manager
```
* qemu-kvm 为KVM管理程序提供硬件模拟的软件程序
* libvirt-daemon-system 将libvirt守护程序作为系统服务运行
* libvirt-clients 用来管理虚拟化平台的软件
* birdge-utils 用来配置网络桥接的命令行工具
* virtinst 用来创建虚拟机的命令行工具
* virt-manager 提供一个易用的图形界面，通过libvirt管理虚拟机
软件安装完毕后，检查libvirt守护进程是否正常：
```
# sudo systemctl is-active libvirtd
active
```
添加用户权限到libvirt、kvm用户组，使对应用户可以创建和管理虚拟机：
```
sudo usermod -aG libvirt alan
sudo usermod -aG kvm alan
```

4、virtualbox共享目录使用限制
* 在共享目录下无法使用chown/chgrp/chmod修改文件或目录的用户/组归属及访问权限
* 共享目录下缺省无法设置软链接
可以通过如下方法设置(未尝试)
```
VBoxManage setextradata *vm-name* VBoxInternal2/SharedFoldersEnableSymlinksCreate/*share-fold-name* 1
```
通过如下方法查看设置：
```
VBoxManage getextradata *vm-name* enumerate
```

#### 网络设置
在libvirt安装过程中，会默认创建一个**virbr0**的桥接设备：
```
# brctl show
bridge name	bridge id		STP enabled	interfaces
virbr0		8000.525400801bb2	yes		virbr0-nic
```

