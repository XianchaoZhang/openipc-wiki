# OpenIPC Wiki
[Table of Content](../README.zh.md)

升级固件 
------------------

### 从 GitHub 升级 
对于旧固件，运行不带参数的 `sysupgrade` 就足够了。对于较新的固件，运行 `sysupgrade -k -r` 来更新内核和 rootfs。

__注意！升级固件可能会导致相机"变砖"。确保你在道德和技能方面都做好了准备。准备好救援 SD 卡和/或 UART 适配器。准备好作为最后的手段来拆除和重新编程闪存芯片。除非真的必须，否则不要升级生产相机！__

### 从 TFTP 服务器升级

[设置 TFTP 服务器](installation-tftpd.md)。

前往 <https://github.com/OpenIPC/firmware/releases/tag/latest> 并下载适用于您的 SoC 的最新固件包。将包的内容提取到 TFTP 服务器的根目录中。

在相机运行中：

#### Github：来自 Linux

```bash
soc=$(fw_printenv -n soc)
serverip=$(fw_printenv -n serverip)
busybox tftp -r rootfs.squashfs.${soc} -g ${serverip}
busybox tftp -r uImage.${soc} -g ${serverip}
```

#### Github: Alternatively, from U-Boot

for 8MB image

```bash
tftp ${baseaddr} uImage.${soc}
sf probe 0; sf erase 0x50000 0x200000; sf write ${baseaddr} 0x50000 ${filesize}

tftp ${baseaddr} rootfs.squashfs.${soc}
sf probe 0; sf erase 0x250000 0x500000; sf write ${baseaddr} 0x250000 ${filesize}
```

16MB image

```bash
tftp ${baseaddr} uImage.${soc}
sf probe 0; sf erase 0x50000 0x200000; sf write ${baseaddr} 0x50000 ${filesize}

tftp ${baseaddr} rootfs.squashfs.${soc}
sf probe 0; sf erase 0x250000 0xA00000; sf write ${baseaddr} 0x250000 ${filesize}
```

### 从本地文件升级

转到 <https://github.com/OpenIPC/firmware/releases/tag/latest> 并下载适用于您的 SoC 的最新固件包。解压包并使用 `scp` 将其内容上传到相机：

```bash
tar xvf <firmware.tgz>
scp uImage* rootfs* root@<yourcameraip>:/tmp/
```

在相机运行中：

```bash
soc=$(fw_printenv -n soc)
sysupgrade --kernel=/tmp/uImage.${soc} --rootfs=/tmp/rootfs.squashfs.${soc} -z
```

### 从 SD 卡升级

#### SD 卡：来自 Linux

转到 <https://github.com/OpenIPC/firmware/releases/tag/latest> 并下载适用于您的 SoC 的最新固件包。将 SD 卡插入您的台式电脑。解压包并将其内容复制到卡中：

```bash
tar xvf <firmware.tgz>
cp uImage* rootfs* /media/<username>/<card-id>/
```

将 SD 卡插入相机。在相机上运行：

```bash
soc=$(fw_printenv -n soc)
sysupgrade --kernel=/mnt/mmcblk0p1/uImage.${soc} --rootfs=/mnt/mmcblk0p1/rootfs.squashfs.${soc} --force_ver -z
```

#### SD 卡：或者，从 U-Boot

8MB image

```bash
mw.b ${baseaddr} 0xff 0x200000
fatload mmc 0:1 ${baseaddr} uImage.${soc}
sf probe 0; sf erase 0x50000 0x200000; sf write ${baseaddr} 0x50000 ${filesize}

mw.b ${baseaddr} 0xff 0x500000
fatload mmc 0:1 ${baseaddr} rootfs.squashfs.${soc}
sf probe 0; sf erase 0x250000 0x500000; sf write ${baseaddr} 0x250000 ${filesize}
```

16MB image

```bash
mw.b ${baseaddr} 0xff 0x300000
fatload mmc 0:1 ${baseaddr} uImage.${soc}
sf probe 0; sf erase 0x50000 0x300000; sf write ${baseaddr} 0x50000 ${filesize}

mw.b ${baseaddr} 0xff 0x500000
fatload mmc 0:1 ${baseaddr} rootfs.squashfs.${soc}
sf probe 0; sf erase 0x350000 0xa00000; sf write ${baseaddr} 0x350000 ${filesize}
```

### 通过 ymodem 刷新 U-Boot

清理 320K RAM 并将引导加载程序文件加载到其中：

```bash
mw.b ${baseaddr} 0xff 0x50000
loady
```

> _（按"Ctrl-a"，然后按":"，然后输入）_

```bash
exec !! sz --ymodem u-boot.bin
```

文件上传完成后，写入ROM：

```bash
sf probe 0
sf erase 0x0 0x50000
sf write ${baseaddr} 0x0 ${filesize}
```

### 故障排除

如果你收到此错误：

```console
losetup: /tmp/rootfs.squashfs.${soc}: No such file or directory
Rootfs: Unable to get hostname, execution was interrupted...
```

然后尝试先仅更新内核：`sysupgrade -k`

如果没有帮助，请使用 `--force` 选项：`sysupgrade -r --force`

如果发现故障，请检索该实用程序的最新版本：

```bash
curl -k -L -o /usr/sbin/sysupgrade "https://raw.githubusercontent.com/OpenIPC/firmware/master/general/overlay/usr/sbin/sysupgrade"
```
