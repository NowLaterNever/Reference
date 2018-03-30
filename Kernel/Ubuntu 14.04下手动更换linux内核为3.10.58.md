# ubuntu14.04下手动更换linux内核为3.10.58

## 1. 下载并解压内核linux3.10.58

   内核下载官网：https://www.kernel.org. 解压内核(任意文件夹位置，10G左右空闲磁盘空间)：  
   - xz -d Linux-3.10.58.tar.xz
   - tar -xvf linux-3.10.58.tar

## 2. 安装执行sodu make menuconfig命令时依赖的ncurses工具

   sudo apt-get install libncurses5-dev
   
## 3. 清除生成文件及配置文件(内核第一次编译跳过此步骤，多次进行内核编译才使用)

   sudo make mrproper
   
## 4. 定制内核

   sudo make menuconfig
   
   进入界面需要将Device Drivers ---> Generic Driver Options ---> Automount devtmpfs at /dev, after the kernel mounted the rootfs 设置为N (不然系统启动不了，原因待学)。
   
## 5. 编译内核和模块

   sudo make(如果自己的机器处理器还可以的话，想加快速度的话，可以选在多线程:sudo make -j4)
   
## 6. 模块安装

   sudo make modules_install
   
## 7. 内核安装

   sudo make install
   
## 8. 重启并进入grub选择界面选择linux-3.10.58内核版本启动

   - sudo reboot
   - 选择内核：启动ubuntu时想进入grub选择界面，当进入VMware启动界面时，长按shift键
   
## 9. 重启成功后查看内核版本

   sudo uname -a 
