# OpenIPC Wiki [目录](../README.md)

仅适用于带有 Hi35{16Ev200,16Ev300,18Ev300} SoC 的 XM 板!!! ------------------------------------------------------------------------

其他主板可能有不同的内存布局，例如 HI3518Ev200 使用 0x82000000 而不是 0x42000000

### 支持的传感器

请在[支持的设备列表][1]中查找您的传感器。

### 初始设备固件更新

```
setenv bootargs 'mem=${osmem:-32M} console=ttyAMA0,115200 panic=20 root=/dev/mtdblock3 rootfstype=squashfs init=/init mtdparts=hi_sfc:256k(boot),64k(env),2048k(kernel),5120k(rootfs),-(rootfs_data)'
setenv bootcmd 'setenv setargs setenv bootargs ${bootargs}; run setargs; sf probe 0; sf read 0x42000000 0x50000 0x200000; bootm 0x42000000'
setenv uk 'mw.b 0x42000000 ff 1000000; tftp 0x42000000 uImage.${soc} && sf probe 0; sf erase 0x50000 0x200000; sf write 0x42000000 0x50000 ${filesize}'
setenv ur 'mw.b 0x42000000 ff 1000000; tftp 0x42000000 rootfs.squashfs.${soc} && sf probe 0; sf erase 0x250000 0x500000; sf write 0x42000000 0x250000 ${filesize}'
saveenv

setenv soc hi351xxxxxxx          # Set your SoC. hi3516ev200, hi3516ev300, or hi3518ev300.
setenv osmem 32M
setenv totalmem 64M              # 64M for hi3516ev200, hi3518ev300, 128M for hi3516ev300.
setenv ipaddr 192.168.1.10
setenv serverip 192.168.1.254    # Your TFTP server IP address.
saveenv

run uk; run ur; reset            # Flash kernel, rootfs and reboot device
```

### 后续快速更新

```
run uk; run ur; reset
```

### 第一次运行后执行命令

For a Hi3516Ev300 board:
```
set_allocator cma
firstboot
```

For a Hi3516Ev200 or Hi3518Ev300 board:
```
set_allocator hisi
firstboot
```

对于 Hi3516Ev300 板：对于 Hi3516Ev200 或 Hi3518Ev300 板：
### 危险区域

您始终可以选择更新引导加载程序。但是，您需要了解自己要做什么。

注意！将引导加载程序文件名替换为与您的 SoC 匹配的文件名。完整列表位于[此处](https://github.com/OpenIPC/firmware/releases/tag/latest)。

```
mw.b 0x42000000 ff 1000000
tftp 0x42000000 u-boot-hi3516xxxxx-beta.bin
sf probe 0
sf erase 0x0 0x50000
sf write 0x42000000 0x0 ${filesize}
reset
```

[1]: guide-supported-devices.md

### 无 tftp 命令的 Uboot

```
setenv uk 'mw.b 0x42000000 ff 1000000; setenv bootfile uImage.${soc} && tftpboot && sf probe 0; sf erase 0x50000 0x200000; sf write 0x40080000 0x50000 ${filesize}'
setenv ur 'mw.b 0x42000000 ff 1000000; setenv bootfile rootfs.squashfs.${soc} && tftpboot && sf probe 0; sf erase 0x250000 0x500000; sf write 0x40080000 0x250000 ${filesize}'
```


### 备份设备

```
setenv ipaddr 192.168.1.10
setenv serverip 192.168.1.254    # Your TFTP server IP address.

sf read 0x42000000 0x0 0x800000
tftpput 0x42000000 0x800000 backup.img
```


### 恢复设备

如果出现问题，uboot 可能会变砖！

```
setenv bootfile backup.img
tftpboot
sf probe 0
sf erase 0x0000 0x800000
sf write 0x40080000 0x0 ${filesize}
reset
```
