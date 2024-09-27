# OpenIPC Wiki
[目录](../README.zh.md)

仅适用于带有 NT98562 和 NT98566 SoC 的 XM 供应商主板!!! 
---------------------------------------------------------

### 初始设备固件更新

> **此部分将在研究结束时完成**

```
run uk; run ur; reset            # Flash kernel, rootfs and reboot device
```

### 后续快速更新

```
run uk; run ur; reset
```

### 注释

完成新固件烧录后，请运行"firstboot"命令来格式化用于存储设置的"jffs2"分区。

### 已知问题

更改一些测试的内存地址。

```
setenv bootcmd 'setenv setargs setenv bootargs ${bootargs};run setargs;sf probe 0;sf read 0x03100000 0x50000 0x200000;nvt_boot'
saveenv
```
