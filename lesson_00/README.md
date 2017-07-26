# 1 编译

> arm-hisiv300-linux-gcc demo.c -o demo

# 2 打包

## 2.1 拷贝
> cp demo cp demo ../../rootfs_uclibc/usr/bin


## 2.2 制作固件镜像
> mksquashfs rootfs_uclibc rootfs.bin -b 256k -comp xz


## 2.3 把rootfs.bin&kernel.bin放到tftpd-hpa的目录下
> cp rootfs.bin kernel.bin /tftpboot


# 3 烧写
## 3.1 准备设备烧写环境
设置uboot下的环境参数：
	
	#设置下载服务器的IP地址
	setenv serverip 192.168.41.67 
	
	#设置设备本地的IP地址
	setenv ipaddr 192.168.41.65 
	
	#设置bootargs参数
	setenv bootargs 'mem=128M console=ttyAMA0,115200 root=/dev/mtdblock1 rootfstype=squashfs mtdparts=hi_sfc:5M(boot),3M(rootfs)'

	#设置启动命令
	setenv bootcmd 'sf probe 0;sf read 0x82000000 0x100000 0x500000;bootm 0x82000000'
	
	#保存参数
	saveenv


## 3.2 准备电脑侧的环境
* tftp服务
* 电脑的网络和设备的网络在一个局域网中

## 3.3 通过串口命令烧写
	#内核烧写
	sf probe 0;sf erase 100000 0x400000;mw.b 82000000 ff 400000;tftp 82000000 uImage.bin;sf write 82000000 100000 $(filesize)

	#文件系统烧写
	sf probe 0;sf erase 500000 0x300000;mw.b 82000000 ff 300000;tftp 82000000 rootfs.bin;sf write 82000000 500000 $(filesize)

## 3.4 重启设备
	#uboot下输入重启
	reset
	
	#直接使用bootcmd命令
	sf probe 0;sf read 0x82000000 0x100000 0x500000;bootm 0x82000000
	
	
# 4 调试
## 4.1 电脑侧环境
* nfs服务
* tftp服务

## 4.2 设备侧的环境
* 网络配置
> ifconfig eth0 192.168.41.65

* 挂载服务
> mount -t nfs -o nolock 192.168.41.67:/tftpboot /mnt