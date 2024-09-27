# OpenIPC Wiki
[目录](../README.zh.md)

使用 NFS 启动设备 
--------------------

HI3516EV200 设备示例

```
bootargsnfs=mem=${osmem:-32M} console=ttyAMA0,115200 panic=20 root=/dev/nfs rootfstype=nfs ip=dhcp nfsroot=192.168.1.254:/media/nfs/hi3516ev200,v3,nolock rw ip=192.168.1.55:192.168.1.254:192.168.1.254:255.255.255.0::eth0

nfsboot=tftp 0x42000000 uImage;setenv setargs setenv bootargs ${bootargsnfs};run setargs;bootm 0x42000000

run nfsboot
```

如何在不替换库存固件的情况下使用 NFS 和 TFTP 启动 OpenIPC(russian post):
https://habr.com/ru/companies/ruvds/articles/774482/

