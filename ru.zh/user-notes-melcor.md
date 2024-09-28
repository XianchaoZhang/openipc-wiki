# OpenIPC Wiki 
[内容](../README.zh.md)

固件 gk7205v300 + IMX335 + XM_XT25F128B 
--------------------------------------------------------

带密码的引导加载程序，闪存被锁定。

1. 下载并安装 [tftpd сервер](https://bitbucket.org/phjounin/tftpd64/wiki/Download%20Tftpd64.md)。
2. 下载并解压[固件](https://github.com/OpenIPC/firmware/releases/download/latest/openipc.gk7205v300-br.tgz)到一个单独的文件夹中，在tftpd设置中指定路径与固件的文件夹。
3. 在 Telegram 频道 [OpenIPC Users (Ru)][t1] 中找到带有 #GkTool 标签的消息，在计算机上安装 ToolPlatform-1.0.0-win32-x86_64.zip
4. 从 Telegram 频道 [OpenIPC Users (Ru)][t1] 下载 u-boot.bin.img_cut。
5. 将相机和电脑通过以太网连接到同一路由器，使电脑和相机处于同一子网。
6. 将 USB-TTL 3.3V 适配器连接到计算机，推荐使用 FTDI232。它将连接到某个COM端口，在任务管理器中查看COM端口号。
7. 将相机上的 RX/TX/GND 连接到 USB-TTL 3.3V 适配器上的 TX/RX/GND。
8. 下载并安装Putty。将COM端口模式设置为速度115200。通过Putty，通过COM端口登录相机。确保电线连接正确、摄像机日志可见并且从键盘输入字符。

__请务必保存相机的 MAC 地址！在固件安装过程中它将被删除！__

### Putty

通过从 Cooper 修改的操作系统复制到 NFS 服务器来创建备份：

```
mkdir /mnt/Public
mount -o nolock 192.168.1.15(тут указывается IP компьютера):/home/pi/nfs_share /mnt/Public
cat /dev/mtdblock0 > /mnt/Public/mtd0
cat /dev/mtdblock1 > /mnt/Public/mtd1
cat /dev/mtdblock2 > /mnt/Public/mtd2
cat /dev/mtdblock3 > /mnt/Public/mtd3
cat /dev/mtdblock4 > /mnt/Public/mtd4
```

退出 Putty 并将其关闭。

### 通过 #gktool (工具平台) 闪存 xm boot u-boot.bin.img_cut

1. 在 ToolPlatform 中，选择安装适配器的 COM 端口、传输模式 - 串行。
2. 在"Burn Fastboot"选项卡中，选择"Flash type：spinor"、"File：file u-boot.bin.img_cut"。
3. 关闭相机，按刻录按钮，等待 5 秒，打开相机。固件将开始。
4. 重新启动相机电源。
5. 在相机打开时按几次 CTRL-C（快速按），通过 PUTTY 登录 U-boot。输入命令 如果重新启动没有帮助，并且控制台中有空格，则重复步骤 2。之后相机就可以工作了。
输入命令
```
sf probe 0
sf lock 0
sf erase 0 1000000

setenv soc gk7205v300
setenv osmem 32M
setenv totalmem 128M
saveenv

setenv gatewayip 192.168.1.1      // IP адрес вашего шлюза/роутера
setenv ipaddr 192.168.1.14        // IP адрес камеры
setenv netmask 255.255.255.0      // маска подсети
setenv serverip 192.168.1.15      // IP адрес компьютера на котором запущен TFTP сервер
setenv ethaddr 05:68:31:be:da:38  // MAC адрес IP камеры (обязательно!)
saveenv

setenv bootargs 'mem=${osmem:-32M} console=ttyAMA0,115200 panic=20 root=/dev/mtdblock3 rootfstype=squashfs init=/init mtdparts=sfc:256k(boot),64k(env),2048k(kernel),5120k(rootfs),-(rootfs_data)'
setenv bootcmd 'setenv setargs setenv bootargs ${bootargs}; run setargs; sf probe 0; sf read 0x42000000 0x50000 0x200000; bootm 0x42000000'
setenv uk 'mw.b 0x42000000 ff 1000000; tftp 0x42000000 uImage.${soc} && sf probe 0; sf erase 0x50000 0x200000; sf write 0x42000000 0x50000 ${filesize}'
setenv ur 'mw.b 0x42000000 ff 1000000; tftp 0x42000000 rootfs.squashfs.${soc} && sf probe 0; sf erase 0x250000 0x500000; sf write 0x42000000 0x250000 ${filesize}'
saveenv

run uk
run ur
reset
```

如果重新启动没有帮助，并且控制台中有空格，则重复步骤 2。
之后相机就可以工作了。

启动后，在 Putty 控制台中运行"firstboot"。

[t1]: https://t.me/openipc_modding
