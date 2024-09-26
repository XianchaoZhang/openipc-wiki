# OpenIPC Wiki
[Table of Content](../README.md)

仅适用于带有 SoC GK7202V300、GK7205V200、GK7205V300 的 XM 板!!! 
-----------------------------------------------------------------

### 支持的传感器

请在[支持的设备列表][1]中查找您的传感器。

### 初始设备固件更新

```
setenv bootargs 'mem=${osmem:-32M} console=ttyAMA0,115200 panic=20 root=/dev/mtdblock3 rootfstype=squashfs init=/init mtdparts=sfc:256k(boot),64k(env),2048k(kernel),5120k(rootfs),-(rootfs_data)'
setenv bootcmd 'setenv setargs setenv bootargs ${bootargs}; run setargs; sf probe 0; sf read 0x42000000 0x50000 0x200000; bootm 0x42000000'
setenv uk 'mw.b 0x42000000 ff 1000000; tftp 0x42000000 uImage.${soc} && sf probe 0; sf erase 0x50000 0x200000; sf write 0x42000000 0x50000 ${filesize}'
setenv ur 'mw.b 0x42000000 ff 1000000; tftp 0x42000000 rootfs.squashfs.${soc} && sf probe 0; sf erase 0x250000 0x500000; sf write 0x42000000 0x250000 ${filesize}'
saveenv

setenv soc gk7xxxxxxx            # Set your SoC. gk7202v300, gk7205v200, or gk7205v300.
setenv osmem 32M
setenv totalmem 64M              # 64M for gk7202v300, gk7205v200, 128M for gk7205v300.
setenv ipaddr 192.168.1.10
setenv serverip 192.168.1.254    # Your TFTP server IP address.
saveenv

run uk; run ur; reset            # Flash kernel, rootfs and reboot device
```

### 后续快速更新

```
run uk; run ur; reset
```

### 针对 GK7205V300+IMX335 用户的技巧

```
echo -e "!/bin/sh\n\ndevmem 0x120100f0 32 0x19\n" >/etc/init.d/S96trick
chmod +x /etc/init.d/S96trick
```

替代方法[这里]（https://github.com/OpenIPC/firmware/pull/117/files）

### 危险区域

您始终可以选择更新引导加载程序。但是，您需要了解自己要做什么。

注意！将引导加载程序文件名替换为与您的 SoC 匹配的文件名。完整列表位于[此处](https://github.com/OpenIPC/firmware/releases/tag/latest)。

```
mw.b 0x42000000 ff 1000000
tftp 0x42000000 u-boot-gk7xxxxxxxx-beta.bin
sf probe 0
sf erase 0x0 0x50000
sf write 0x42000000 0x0 ${filesize}
reset
```

[1]: guide-supported-devices.md
