# OpenIPC Wiki
[Table of Content](../README.md)

仅适用于带有 SSC335 Soc 的 Anjoy/Brovotech/Gospell/Uniview 板!!! 
------------------------------------------------------------------

### 初始设备固件更新

```
setenv bootargs 'mem=${osmem:-32M} console=ttyS0,115200 panic=20 root=/dev/mtdblock3 rootfstype=squashfs init=/init LX_MEM=0x3fe0000 mma_heap=mma_heap_name0,miu=0,sz=0x1C00000 mma_memblock_remove=1 mtdparts=NOR_FLASH:256k(boot),64k(tech),2048k(kernel),5120k(rootfs),-(rootfs_data)'
setenv bootcmd 'setenv setargs setenv bootargs ${bootargs}; run setargs; sf probe 0; sf read 0x21000000 0x50000 0x200000; bootm 0x21000000'
setenv uk 'mw.b 0x21000000 ff 1000000; tftpboot 0x21000000 uImage.${soc}; sf probe 0; sf erase 0x50000 0x200000; sf write 0x21000000 0x50000 ${filesize}'
setenv ur 'mw.b 0x21000000 ff 1000000; tftpboot 0x21000000 rootfs.squashfs.${soc}; sf probe 0; sf erase 0x250000 0x500000; sf write 0x21000000 0x250000 ${filesize}'
saveenv

setenv soc ssc335                # Your SoC. ssc325, ssc335, or ssc337.
setenv sensor none               # Your sensor. gc2053, imx307, or sc3335.
setenv osmem 32M
setenv totalmem 64M              # 64M for ssc335.
setenv ipaddr 192.168.1.10       # Your camera IP address.
setenv serverip 192.168.1.254    # Your TFTP server IP address.
saveenv

run uk; run ur; reset            # Flash kernel, rootfs and reboot device
```

### 后续快速更新

```
run uk; run ur; reset
```

### Notes

完成新固件刷新后，请运行“firstboot”命令来格式化用于存储设置的“jffs2”分区。


### 其他信息

#### SigmaStar 设备的人类可读处理器名称。
数据来自原始固件。


| Engraving | /sys/class/mstar/msys/CHIP_ID | /sys/devices/soc0/soc_id | /sys/devices/soc0/machine        |
|-----------|-------------------------------|--------------------------|----------------------------------|
| SSC325DE  | not found                     | 239                      | INFINITY6 SSC009B-S01A QFN128    |
|           |                               |                          |                                  |
| SSC335    | 0xF2                          | 242                      | INFINITY6B0 SSC009A-S01A QFN88   |
| SSC337    | 0xF2                          | 242                      | INFINITY6B0 SSC009A-S01A QFN88   |
| SSC337DE  | 0xF2                          | 242                      | INFINITY6B0 SSC009B-S01A QFN128  |
| SSC338Q   | 0xF1                          | 241                      | INFINITY6E SSC012B-S01A          |