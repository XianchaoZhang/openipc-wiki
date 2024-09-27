# OpenIPC Wiki
[目录](../README.zh.md)

仅适用于带有 XM530/XM550 SoC 的 XM 供应商主板!!! 
--------------------------------------------------

### 初始设备固件更新

```
setenv bootargs 'mem=35M console=ttyAMA0,115200 panic=20 root=/dev/mtdblock3 rootfstype=squashfs init=/init mtdparts=xm_sfc:256k(boot),64k(env),2048k(kernel),5120k(rootfs),-(rootfs_data)'
setenv bootcmd 'sf probe 0; sf read 0x80007fc0 0x50000 0x200000; bootm 0x80007fc0'
setenv uk1 'mw.b 0x80007fc0 ff 1000000; tftp 0x80007fc0 uImage.${soc}'
setenv uk2 'sf probe 0; sf erase 0x50000 0x200000; sf write 0x80007fc0 0x50000 ${filesize}'
setenv uk 'run uk1 ; run uk2'
setenv ur1 'mw.b 0x80007fc0 ff 1000000; tftp 0x80007fc0 rootfs.squashfs.${soc}'
setenv ur2 'sf probe 0; sf erase 0x250000 0x500000; sf write 0x80007fc0 0x250000 ${filesize}'
setenv ur 'run ur1 ; run ur2'
saveenv

setenv soc xm530                 # Your SoC. xm530 for both xm530 and xm550.
setenv osmem 35M                 # 35M for xm530, 64M for xm550.
setenv totalmem 64M              # 64M for xm530, 128M for xm550.
setenv ipaddr 192.168.1.10       # Your camera IP address.
setenv serverip 192.168.1.254    # Your TFTP server IP address.
saveenv

run uk; run ur; reset            # Flash kernel, rootfs and reboot device
```

### 后续快速更新

```
run uk; run ur; reset
```

### 注释

完成新固件烧录后，请运行"firstboot"命令来格式化用于存储设置的 jffs2 分区。

### 已知问题


### 不使用 tftp 命令的 Uboot

```
setenv uk1 'mw.b 0x81000000 ff 1000000; setenv bootfile uImage.${soc}; tftpboot'
setenv uk2 'sf probe 0; sf erase 0x50000 0x200000; sf write 0x81000000 0x50000 ${filesize}'
setenv uk 'run uk1 ; run uk2'
setenv ur1 'mw.b 0x81000000 ff 1000000; setenv bootfile rootfs.squashfs.${soc}; tftpboot'
setenv ur2 'sf probe 0; sf erase 0x250000 0x500000; sf write 0x81000000 0x250000 ${filesize}'
setenv ur 'run ur1 ; run ur2'
saveenv

run uk; run ur; reset
```

### 备份设备（无 tftpput）

使用日志文件打开串行控制台注意：通过串行转储需要很长时间

```
sf probe 0
sf read 0x81000000 0x0 0x800000
md.b 0x81000000 0x800000
```

use `cut -b 11-57 | xxd -r -p` to reconstruct the binary from `md.b` output

