# OpenIPC Wiki 
[内容](../README.md)

软件从 openipc-1.0 (OpenWrt) 过渡到 openipc-2.x (Buildroot) 👻 
------------------------------------------ ------------------------------------------

我们使用旧的 openipc-1.0 设备并以任何方式停止尽可能多的服务（除了 dropbear）。我们以 snmp 为例，停止那些再次“复活”的服务。

```
/etc/init.d/snmpd stop; /etc/init.d/snmpd disable
```

使用“fw_setenv”命令，我们更改“bootargs”变量，依次添加“init=/init”变量。对于我的主板，该行看起来像这样，但您的可能有所不同：

```
fw_setenv bootargs 'console=ttyAMA 0,115200 root=/dev/mtdblock3 init=/init rootfstype=squashfs,jffs2 panic=20 mtdparts=hi_sfc:256k(boot),64k(env),2048k(kernel),5120k(rootfs),-(rootfs_data)'
```

使用“fw_setenv”命令添加新的 soc 变量，指定您的处理器：

```
fw_setenv soc hi3516ev100
```

我们使用“flashcp”命令刷新文件系统，该命令之前是从 GitHub OpenIPC 帐户下载的。就我而言，这是“/dev/mtd3”分区，但在某些较旧的硬件上可能存在差异：

```
flashcp -v rootfs.squashfs.hi3516ev100 /dev/mtd3
```

我们对主板进行硬重启：

```
reboot -f
```

在**-openipc-2.x 下加载并通过 DHCP 获取地址。之后，我们执行命令进行全局漂亮的更新：

```
sysupgrade -k -r -n
```

利润！

