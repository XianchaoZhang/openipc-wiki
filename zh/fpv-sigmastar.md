# OpenIPC Wiki
[Table of Content](../README.zh.md)


在 SigmaStar 设备上安装 OpenIPC 固件的说明 
---

<p align="center">
  <img src="https://github.com/OpenIPC/wiki/blob/master/images/fpv-logo.jpg?raw=true" alt="Logo"/>
</p>

---

### SSC338Q + IMX415 + NAND 闪存，来自 CamHi 供应商的主板
#### 关于实验的简短说明，将会修订和更新

#### 摘要 
Sigmastar IPL（预引导加载程序）允许从 SD 卡启动自定义 U-Boot。使用此临时 OpenIPC 固件，您可以创建 nand 的备份，然后刷新永久固件。

#### 准备
- 将 SD 卡连接到计算机，创建一个 1 GB 分区并将其格式化为 FAT32 / VFAT。
- [下载 ssc338q-initramfs.zip][1]
- 将所有文件复制到 SD 卡的根目录，在 autostart.sh 上更新您的无线凭据：

```diff
#!/bin/sh
+WLAN_SSID="Router"
+WLAN_PASS="12345678"
```
- 将 SD 卡放入摄像头模块，启动它并等待它连接到您的路由器。
- 使用 ssh 连接到摄像头：

```
ssh root@192.168.1.100
12345
```

#### 备份
- /dev/mtd0 使用 nand 闪存的完整分区大小。
- 这可能需要一些时间，备份到 SD 卡相当慢。

```
nanddump -f /mnt/mmcblk0p1/backup-nand.bin /dev/mtd0
```

- 或者，可以通过 tftp 上传闪存内容。
```
cd /dev
tftp 192.168.1.10 -pr mtd0
```

#### Install
```
flash_eraseall /dev/mtd0
nandwrite -k /dev/mtd0 /mnt/mmcblk0p1/ssc338q-fpv.bin
rm /mnt/mmcblk0p1/UBOOT
reboot -f
```

#### 购买设备（CamHi 供应商）
- https://aliexpress.com/item/1005002879158570.html
- https://aliexpress.com/item/1005005750013595.html

---

### SSC338Q + IMX415 + NOR 闪存，来自 Anjoy 供应商的主板
#### 信息收集继续 
信息收集继续

#### 购买设备（Anjoy 供应商）
- https://aliexpress.com/item/1005003738087454.html

---

### 其他说明 
如需测试，请使用 [MPV](https://mpv.io/) 播放器，其中 Shift+I 组合键可用于获取调试信息。

[1]: https://github.com/OpenIPC/wiki/files/13382282/ssc338q-initramfs.zip
