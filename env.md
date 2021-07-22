#### VirtualBox 环境设置
1、Virtualbox创建的虚机默认情况下 VT-X/AMD-V选项不能选，需要在创建虚机后使用如下命令进行设置
'''
C:\Program Files\Oracle\VirtualBox>VBoxManage.exe modifyvm "dev-desk" --nested-hw-virt on
'''
其中dev-desk为需要设置的虚机名
