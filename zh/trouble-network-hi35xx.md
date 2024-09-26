# OpenIPC Wiki
[Table of Content](../README.md)

## 故障排除：网络在 hi35xx 上不工作 
如果在安装 OpenIPC（无链接）后网络在您的 hi35xx 设备上不工作，则可能必须调整 MII（媒体独立接口）设置。对于 U-Boot，这可以通过设置“phyaddru”、“phyaddrd”和“mdio_intf”的值来完成。“phyaddru”和“phyaddrd”的可能值为：“0-3”，“mdio_intf”的可能值为：“rmii”、“rgmii”、“gmii”。对于 Linux 内核/操作系统，可以通过“extras”引导变量“hieth.phyaddru”、“hieth.phyaddrd”、“hieth.mdioifu”和“hieth.mdioifd”设置值。

通常可以在您的库存固件中找到正确的值。查看启动日志或在库存固件上运行 [ipctool](https://github.com/OpenIPC/ipctool) 可能会提供线索。

以下是您可以尝试的一些组合：

### 对于 Linux 中的以太网：

在 Linux 控制台中运行此命令：
```
fw_setenv extras 'hieth.phyaddru=0 hieth.phyaddrd=1' && reboot
```
如果上述设置不起作用，请尝试以下操作：
```
fw_setenv extras 'hieth.phyaddru=1 hieth.phyaddrd=0' && reboot
```
Or:
```
fw_setenv extras 'hieth.mdioifu=0 hieth.mdioifd=0' && reboot
```
Or:
```
fw_setenv extras 'hieth.mdioifu=1 hieth.mdioifd=1' && reboot
```
Or:
```
fw_setenv extras hieth.mdioifu=0 hieth.mdioifd=0 hieth.phyaddru=1 hieth.phyaddrd=2 && reboot
```
Or:
```
fw_setenv extras hieth.phyaddru=3 hieth.mdioifu=0 && reboot
```

*注意：如果某种组合导致您的设备无法启动，您可以通过调用“setenv <variable>”来清除 U-Boot 提示符中的变量，即使用空参数设置变量，然后调用“saveenv”。*

### 对于 U-boot 中的以太网：

从 U-Boot 控制台设置“phyaddru”和“phyaddrd”变量：
```
setenv phyaddru 0; setenv phyaddrd 1; saveenv; reset
```
如果上述设置不起作用，请尝试这个
```
setenv phyaddru 1; setenv phyaddrd 0; saveenv; reset
```
Or:
```
setenv phyaddru 0; setenv phyaddrd 0; saveenv; reset
```
Or:
```
setenv phyaddru 1; setenv phyaddrd 1; saveenv; reset
```

*注意：要在 U-Boot 中初始化和测试网络连接，可以使用“ping”命令。*

特定板的一些已知组合：

### XiongMai, HI3518EV100
*For U-boot network:*
```
    setenv phyaddru 1
    setenv phyaddrd 2
    setenv mdio_intf rmii
    saveenv
```
*For Linux network:*
```
    setenv extras 'hieth.phyaddru=1 hieth.phyaddrd=2'
    saveenv
```

### XiongMai IPG-53H20AF, HI3516CV100

*For Linux network:*
```
   setenv hieth.mdioifu=0 hieth.mdioifd=0 hieth.phyaddru=1 hieth.phyaddrd=2
   saveenv
```

### CamHi/HiChip/Xin, HI3518EV200

*For U-boot network:*
```
    setenv phyaddru 0
    setenv phyaddrd 1
    saveenv
```

### HiWatch, HI3518CV100

*For U-boot network:*
```
    setenv phyaddru 3
    saveenv
```

*For Linux network:*
```
    setenv extras 'hieth.phyaddru=3 hieth.mdioifu=0'
    saveenv
```

### Harex (5013A-CF/5020A-FF), HI3516CV100

*For U-boot network:*
```
    setenv extras 'hieth.phyaddru=1 hieth.mdioifu=0'
    saveenv
```

*如果以上方法均不适合您，请在[我们的 Telegram 频道](https://t.me/openipc) 中询问。

