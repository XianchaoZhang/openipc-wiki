# OpenIPC Wiki
[目录](../README.zh.md)

Introduction
------------

本页介绍基于 OpenWRT 的固件变体。

### 固件功能

* RTSP、ONVIF、NETIP
* 原生 ipeye 服务支持
* 支持 squashfs、jffs2、overlayfs、vfat
* Vlan 和网桥支持
* 标准 OPKG 软件包系统
* 微型 SNMP 守护进程
* 使用 SSL 的 Curl 上传/下载文件
* 从 u-boot ENV (linux_cmd=) 运行任意命令
* 具有流量整形和压缩功能的简单 L2/L3 VPN (vtun)
* 简单发送者 Telegram 机器人 (estgb)
* 在 hilink 模式下支持低成本 3G/4G USB 调制解调器
* µVPN 隧道服务
* 等等...


### 支持的设备

我们的目标是开发**通用**、便携式固件，支持广泛的制造商并提供供应商通常无法提供的更新和修复。

该列表不断更新，请经常访问和/或关注我们的 Telegram 群组以获取新发布通知。

有关传感器的更多信息：[https://cctvsp.ru][8]（使用谷歌翻译从俄语翻译而来）。


### 网页界面

* <http://192.168.1.10/> - 基于OpenWrt Luci的标准系统接口


### Majestic streamer

Majestic 是一款视频流应用程序，是我们固件的核心（与摄像头/视频监控功能相关）。它可通过文件 `/etc/majestic.yaml` 进行配置，默认情况下启用了许多功能/服务。可以关闭不需要的选项以获得更好的安全性和性能。

要在调试模式下运行"majestic"：

```bash
killall -sigint majestic; export SENSOR=$(ipctool --sensor_id); majestic
```

要在生产模式下运行"majestic"，请重新启动相机或运行命令：

```bash
killall -sigint majestic; export SENSOR=$(ipctool --sensor_id); majestic 2>&1 | logger -p daemon.info -t majestic &
```


### 固件中相机相关的 URL

有关流媒体 URL 及其描述的信息可以在 [Wiki][9] 中找到。


### 统计数据

软件可能会收集产品使用数据，包括 SoC 和传感器型号名称，以收集 QA 流程中使用的统计数据。

我们保证数据完全匿名，并且不包含任何可以被视为个人数据、可以被视为最终用户数据或对用户来说敏感或机密的数据。


获取固件 
----------------

### 下载（最新开发版本）


| Building status            | SoC         | U-Boot      | Kernel        | Rootfs        |
|----------------------------|-------------|-------------|---------------|---------------|
| ![Hi3516Cv100 images][b1]  | Hi3516CV100 | [uboot][u1] | [kernel][k1]  | [rootfs][r1]  |
| ![Hi3516Cv200 images][b2]  | Hi3516CV200 | [uboot][u2] | [kernel][k2]  | [rootfs][r2]  |
| ![Hi3516Cv300 images][b3]  | Hi3516CV300 | [uboot][u3] | [kernel][k3]  | [rootfs][r3]  |
| ![Hi3516Ev100 images][b4]  | Hi3516EV100 | [uboot][u4] | [kernel][k4]  | [rootfs][r4]  |
| ![Hi3518Av100 images][b5]  | Hi3518AV100 | [uboot][u5] | [kernel][k5]  | [rootfs][r5]  |
| ![Hi3518Cv100 images][b6]  | Hi3518CV100 | [uboot][u6] | [kernel][k6]  | [rootfs][r6]  |
| ![Hi3518Ev100 images][b7]  | Hi3518EV100 | [uboot][u7] | [kernel][k7]  | [rootfs][r7]  |
| ![Hi3518Ev200 images][b8]  | Hi3518EV200 | [uboot][u8] | [kernel][k8]  | [rootfs][r8]  |
| ![Hi3518Ev201 images][b9]  | Hi3518EV201 | [uboot][u9] | [kernel][k9]  | [rootfs][r9]  |
| ![Hi3520Dv100 images][b10] | Hi3520DV100 | !           | [kernel][k10] | [rootfs][r10] |
| ![Hi3520Dv200 images][b11] | Hi3520DV200 | !           | [kernel][k11] | [rootfs][r11] |


### 发布

OpenIPC 固件的 **发布** 托管在 <https://github.com/OpenIPC/chaos_calmer/releases>。


### 源代码

OpenIPC 固件的**源代码**托管在<https://github.com/openipc/chaos_calmer>。


从源代码构建 
--------------------

### 在 Linux 机器上构建

Debian 8/9 的使用示例

```bash
git clone --depth=1 https://github.com/OpenIPC/chaos_calmer.git OpenIPC
cd OpenIPC
./Project_OpenIPC.sh update
./Project_OpenIPC.sh 16cv300_DEFAULT
```


### 使用 Docker 进行构建

> **默认 Dockerfile.openipc**

```docker
FROM debian:stretch

RUN DEBIAN_FRONTEND=noninteractive apt-get update \
    && apt-get --no-install-recommends -y install bc bison build-essential \
    ca-certificates cmake cpio curl dos2unix file flex gawk gcc-multilib \
    gettext gettext-base git intltool libc6-dev liblocale-gettext-perl \
    libncurses-dev libssl-dev locales mc openssl python rsync subversion \
    time tofrodos unzip upx wget zlib1g-dev \
    && localedef -i en_US -c -f UTF-8 -A /usr/share/locale/locale.alias \
    en_US.UTF-8 && rm -rf /var/lib/apt/lists/*

ENV LANG en_US.utf8

WORKDIR /src/openipc

RUN git clone --depth=1 https://github.com/OpenIPC/chaos_calmer.git /src/openipc
RUN ./Project_OpenIPC.sh update
RUN ./Project_OpenIPC.sh 18ev200_DEFAULT  # <= Change this ID to you profile
```

> **开始构建**

```bash
#!/bin/bash

docker build -t openipc -f Dockerfile.openipc .
```


准备安装 
--------------------

### 访问 U-boot

需要与您的相机设备建立串行（UART）连接。

* CamHi | 在 U-boot 启动时按 **Ctrl+C** 并获取密码 - HI2105CHIP
* Dahua | 在 U-boot 启动时按 **Shift 8**
* JVT | 在 U-boot 启动时按 **Ctrl+Q**
* XM | 在 U-boot 启动时按 **Ctrl+C**
* SigmaStar | 在 U-boot 启动时按 **Ctrl+B** (UNIV) 或 **Enter** (Anjvision)


### 备份原有 MAC

您一定要在 eth0 端口上写入设备的原始 MAC。

这非常重要并且是设备配置的最后阶段所必需的。


### 备份原版固件

#### 8M 闪存

```txt
setenv ipaddr 192.168.1.10
setenv serverip 192.168.1.254
sf probe 0
mw.b 0x82000000 ff 1000000
sf read 0x82000000 0x0 0x800000
tftp 0x82000000 fullflash.img 0x800000
```

16M 闪存

```txt
setenv ipaddr 192.168.1.10
setenv serverip 192.168.1.254
sf probe 0
mw.b 0x82000000 ff 1000000
sf read 0x82000000 0x0 0x1000000
tftp 0x82000000 fullflash.img 0x1000000
```

32M闪存

```txt
setenv ipaddr 192.168.1.10
setenv serverip 192.168.1.254
sf probe 0
mw.b 0x82000000 ff 2000000
sf read 0x82000000 0x0 0x2000000
tftp 0x82000000 fullflash.img 0x2000000
```


### Flash 和内存布局

我们为相机闪存芯片开发了一个通用分区系统，现在它已成为所有类型设备的标准配置。（请注意，这意味着它可能与供应商闪存布局不匹配。）

#### OpenIPC 闪存布局

```txt
0x000000000000-0x000000040000 : "boot"
0x000000040000-0x000000050000 : "env"
0x000000050000-0x000000250000 : "kernel"
0x000000250000-0x000000750000 : "rootfs"
0x000000750000-0x000001000000 : "rootfs_data"
```

#### 内核内存加载地址

```txt
loadaddr-$(CONFIG_TARGET_hi35xx_16cv100) := 0x80008000
loadaddr-$(CONFIG_TARGET_hi35xx_16cv200) := 0x80008000
loadaddr-$(CONFIG_TARGET_hi35xx_16cv300) := 0x80008000
loadaddr-$(CONFIG_TARGET_hi35xx_16dv100) := 0x80008000
loadaddr-$(CONFIG_TARGET_hi35xx_16ev100) := 0x80008000
loadaddr-$(CONFIG_TARGET_hi35xx_16ev200) := 0x40008000
loadaddr-$(CONFIG_TARGET_hi35xx_16ev300) := 0x40008000
loadaddr-$(CONFIG_TARGET_hi35xx_18cv100) := 0x80008000
loadaddr-$(CONFIG_TARGET_hi35xx_18ev100) := 0x80008000
loadaddr-$(CONFIG_TARGET_hi35xx_18ev200) := 0x80008000
loadaddr-$(CONFIG_TARGET_hi35xx_18ev201) := 0x80008000
loadaddr-$(CONFIG_TARGET_hi35xx_18ev300) := 0x40008000
loadaddr-$(CONFIG_TARGET_hi35xx_20dv100) := 0x80008000
loadaddr-$(CONFIG_TARGET_hi35xx_20dv200) := 0x80008000
```


烧录新固件 
---------------------

**注意力！**

所有示例均表示通过 TFTP 服务器下载固件组件。如果您的设备没有以太网端口，请将所有"tftp"命令替换为"fatload mmc 0:1"。例如：

```txt
tftp 0x82000000 openwrt-hi35xx-XXXXX-u-boot.bin
#
fatload mmc 0:1 0x82000000 openwrt-hi35xx-XXXXX-u-boot.bin
```

### Hi3516Cv100

**此类板卡通过 GPIO 和寄存器具有附加以太网控制系统。请咨询专家！**

**实验设备：**

* 00:12:16:FA:F3:52 * 00:12:12:10:31:54 - BLK18C_0222_38x38_S_v1.03

```txt
setenv ipaddr 192.168.1.10
setenv serverip 192.168.1.254
sf probe 0; sf lock 0

mw.b 0x82000000 ff 1000000
tftp 0x82000000 openwrt-hi35xx-16cv100-u-boot.bin
sf erase 0x0 0x50000
sf write 0x82000000 0x0 ${filesize}

mw.b 0x82000000 ff 1000000
tftp 0x82000000 openwrt-hi35xx-16cv100-default-uImage
sf erase 0x50000 0x200000
sf write 0x82000000 0x50000 ${filesize}

mw.b 0x82000000 ff 1000000
tftp 0x82000000 openwrt-hi35xx-16cv100-default-root.squashfs
sf erase 0x250000 0x500000
sf write 0x82000000 0x250000 ${filesize}
```

### Hi3516Cv200

```txt
setenv ipaddr 192.168.1.10
setenv serverip 192.168.1.254
sf probe 0; sf lock 0

mw.b 0x82000000 ff 1000000
tftp 0x82000000 openwrt-hi35xx-16cv200-u-boot.bin
sf erase 0x0 0x50000
sf write 0x82000000 0x0 ${filesize}

mw.b 0x82000000 ff 1000000
tftp 0x82000000 openwrt-hi35xx-16cv200-default-uImage
sf erase 0x50000 0x200000
sf write 0x82000000 0x50000 ${filesize}

mw.b 0x82000000 ff 1000000
tftp 0x82000000 openwrt-hi35xx-16cv200-default-root.squashfs
sf erase 0x250000 0x500000
sf write 0x82000000 0x250000 ${filesize}
```

### Hi3516Cv300

```txt
setenv ipaddr 192.168.1.10
setenv serverip 192.168.1.254
sf probe 0; sf lock 0

mw.b 0x82000000 ff 1000000
tftp 0x82000000 openwrt-hi35xx-16cv300-u-boot.bin
sf erase 0x0 0x50000
sf write 0x82000000 0x0 ${filesize}

mw.b 0x82000000 ff 1000000
tftp 0x82000000 openwrt-hi35xx-16cv300-default-uImage
sf erase 0x50000 0x200000
sf write 0x82000000 0x50000 ${filesize}

mw.b 0x82000000 ff 1000000
tftp 0x82000000 openwrt-hi35xx-16cv300-default-root.squashfs
sf erase 0x250000 0x500000
sf write 0x82000000 0x250000 ${filesize}
```

### Hi3516Ev100

**实验设备：**

* 00:12:13:02:d7:2c

```txt
setenv ipaddr 192.168.1.10
setenv serverip 192.168.1.254
sf probe 0; sf lock 0

mw.b 0x82000000 ff 1000000
tftp 0x82000000 openwrt-hi35xx-16ev100-u-boot.bin
sf erase 0x0 0x50000
sf write 0x82000000 0x0 ${filesize}

mw.b 0x82000000 ff 1000000
tftp 0x82000000 openwrt-hi35xx-16cv300-default-uImage
sf erase 0x50000 0x200000
sf write 0x82000000 0x50000 ${filesize}

mw.b 0x82000000 ff 1000000
tftp 0x82000000 openwrt-hi35xx-16cv300-default-root.squashfs
sf erase 0x250000 0x500000
sf write 0x82000000 0x250000 ${filesize}
```

### Hi3518Cv100

**此类板卡通过 GPIO 和寄存器具有附加以太网控制系统。请咨询专家！**

```txt
setenv ipaddr 192.168.1.10
setenv serverip 192.168.1.254
sf probe 0; sf lock 0

mw.b 0x82000000 ff 1000000
tftp 0x82000000 openwrt-hi35xx-18cv100-u-boot.bin
sf erase 0x0 0x50000
sf write 0x82000000 0x0 ${filesize}

mw.b 0x82000000 ff 1000000
tftp 0x82000000 openwrt-hi35xx-16cv100-default-uImage
sf erase 0x50000 0x200000
sf write 0x82000000 0x50000 ${filesize}

mw.b 0x82000000 ff 1000000
tftp 0x82000000 openwrt-hi35xx-16cv100-default-root.squashfs
sf erase 0x250000 0x500000
sf write 0x82000000 0x250000 ${filesize}
```

### Hi3518Ev100

**此类板卡通过 GPIO 和寄存器具有附加以太网控制系统。请咨询专家！**

```txt
setenv ipaddr 192.168.1.10
setenv serverip 192.168.1.254
sf probe 0; sf lock 0

mw.b 0x82000000 ff 1000000
tftp 0x82000000 openwrt-hi35xx-18ev100-u-boot.bin
sf erase 0x0 0x50000
sf write 0x82000000 0x0 ${filesize}

mw.b 0x82000000 ff 1000000
tftp 0x82000000 openwrt-hi35xx-18ev100-default-uImage
sf erase 0x50000 0x200000
sf write 0x82000000 0x50000 ${filesize}

mw.b 0x82000000 ff 1000000
tftp 0x82000000 openwrt-hi35xx-18ev100-default-root.squashfs
sf erase 0x250000 0x500000
sf write 0x82000000 0x250000 ${filesize}
```

### Hi3518Ev200

```txt
setenv ipaddr 192.168.1.10
setenv serverip 192.168.1.254
sf probe 0; sf lock 0

mw.b 0x82000000 ff 1000000
tftp 0x82000000 openwrt-hi35xx-18ev200-u-boot.bin
sf erase 0x0 0x50000
sf write 0x82000000 0x0 ${filesize}

mw.b 0x82000000 ff 1000000
tftp 0x82000000 openwrt-hi35xx-18ev200-default-uImage
sf erase 0x50000 0x200000
sf write 0x82000000 0x50000 ${filesize}

mw.b 0x82000000 ff 1000000
tftp 0x82000000 openwrt-hi35xx-18ev200-default-root.squashfs
sf erase 0x250000 0x500000
sf write 0x82000000 0x250000 ${filesize}
```

### Hi3520Dv100

```txt
setenv ipaddr 192.168.1.10
setenv serverip 192.168.1.254
sf probe 0; sf lock 0

mw.b 0x82000000 ff 1000000
tftp 0x82000000 openwrt-hi35xx-20dv100-experimental-u-boot.bin
sf erase 0x0 0x50000
sf write 0x82000000 0x0 ${filesize}

mw.b 0x82000000 ff 1000000
tftp 0x82000000 openwrt-hi35xx-20dv100-default-uImage
sf erase 0x50000 0x200000
sf write 0x82000000 0x50000 ${filesize}

mw.b 0x82000000 ff 1000000
tftp 0x82000000 openwrt-hi35xx-20dv100-default-root.squashfs
sf erase 0x250000 0x500000
sf write 0x82000000 0x250000 ${filesize}
```

### Hi3520Dv200

```txt
setenv ipaddr 192.168.1.10
setenv serverip 192.168.1.254
sf probe 0; sf lock 0

mw.b 0x82000000 ff 1000000
tftp 0x82000000 openwrt-hi35xx-20dv200-experimental-u-boot.bin
sf erase 0x0 0x50000
sf write 0x82000000 0x0 ${filesize}

mw.b 0x82000000 ff 1000000
tftp 0x82000000 openwrt-hi35xx-20dv200-default-uImage
sf erase 0x50000 0x200000
sf write 0x82000000 0x50000 ${filesize}

mw.b 0x82000000 ff 1000000
tftp 0x82000000 openwrt-hi35xx-20dv200-default-root.squashfs
sf erase 0x250000 0x500000
sf write 0x82000000 0x250000 ${filesize}
```


更新固件部分 
------------------------------

如果你已经安装了 OpenIPC 固件，你可以从 shell 命令行更新单个闪存分区：


### 更新 u-boot

```bash
flashcp -v openwrt-hi35xx-XXXXX-u-boot.bin boot
```

> **或**

```bash
flashcp -v openwrt-hi35xx-XXXXX-u-boot.bin /dev/mtd0
```


### 更新内核

```bash
flashcp -v openwrt-hi35xx-XXXXX-default-uImage kernel
```


### 更新 rootfs

```bash
flashcp -v openwrt-hi35xx-XXXXX-default-root.squashfs rootfs
```


安装后配置系统 
--------------------------------------

### 格式化 overlayfs 分区

**必须在第一次运行时执行！**

```txt
flash_eraseall -j /dev/$(awk -F ':' '/rootfs_data/ {print $1}' /proc/mtd)
reboot
```


### 安装原版 MAC

> **U-boot ENV 和 Linux UCI**

```txt
fw_setenv ethaddr 00:01:02:03:04:05
uci set network.lan.macaddr=00:01:02:03:04:05
uci commit
```


### 安装正确的传感器

> **指定正确的传感器、控制类型和数据总线**

```txt
fw_setenv sensor imx291_i2c_lvds
```


重置配置 
-----------------------

如果出现问题，您可以将配置重置为默认值。


### 清理 overlayfs（重置）

> **恢复为默认 Linux 设置**

```txt
firstboot
reboot
```


### 清理 u-boot 环境

> **恢复默认的 u-boot 环境**

```txt
flash_eraseall -j /dev/$(awk -F ':' '/env/ {print $1}' /proc/mtd)
reboot
```


### 恢复备份固件

如果出现严重问题，而你又想恢复备份固件

> **通过串口恢复备份固件**

使用 [此说明](https://glasstty.com/?p=662) 或类似说明安装 kermit。以下是 8MB Flash 的示例命令。

```
kermit
Linux Kermit> CONNECT
Connecting to /dev/ttyUSB0, speed 115200
 Escape character: Ctrl-\ (ASCII 28, FS): enabled
Type the escape character followed by C to get back, 
or followed by ? to see other options.
----------------------------------------------------
## Total Size      = 0x002fb3f1 = 3126257 Bytes
## Start Addr      = 0x82000000
OpenIPC # sf probe 0
8192 KiB hi_sfc at 0:0 is now current device
OpenIPC # mw.b 0x82000000 ff 1000000
OpenIPC # loadb 0x82000000
## Ready for binary (kermit) download to 0x82000000 at 115200 bps...

(Back at alex-B85M-D3H)
----------------------------------------------------
Linux Kermit> SEND /srv/tftp/fullflash.img
Linux Kermit> CONNECT
Connecting to /dev/ttyUSB0, speed 115200
 Escape character: Ctrl-\ (ASCII 28, FS): enabled
Type the escape character followed by C to get back,
or followed by ? to see other options.
----------------------------------------------------
## Total Size      = 0x00800000 = 8388608 Bytes
## Start Addr      = 0x82000000
OpenIPC # sf erase 0x0 0x00800000
Erasing at 0x800000 -- 100% complete.
OpenIPC # sf write 0x82000000 0x0 ${filesize}
Writing at 0x800000 -- 100% complete.
OpenIPC # 
```
> **通过 TFTP 恢复备份固件**

以下是针对 8MB Flash 的命令。

```shell
setenv ipaddr 192.168.1.10
setenv serverip 192.168.1.254
sf probe 0; sf lock 0

mw.b 0x82000000 ff 1000000
tftp 0x82000000 fullflash.img
sf erase 0x0 0x00800000
sf write 0x82000000 0x0 ${filesize}
```


## 参考书

待寫...

[1]: https://aliexpress.com/item/32493067946.html
[2]: https://aliexpress.com/item/32851596596.html
[3]: https://aliexpress.com/item/1005002315913099.html
[4]: https://aliexpress.com/item/1005002298832047.html
[5]: https://aliexpress.com/item/4000119561119.html
[6]: https://aliexpress.com/item/4000054902736.html
[7]: https://aliexpress.com/item/1005001933429701.html
[8]: https://translate.google.com/translate?sl=ru&tl=en&u=https://www.cctvsp.ru/articles/obzor-i-sravnenie-matrits-dlya-kamer-videonablyudeniya
[9]: majestic-streamer.md

[b1]: https://github.com/openipc/chaos_calmer/actions/workflows/hi3516cv100_images.yml/badge.svg?branch=master
[b2]: https://github.com/openipc/chaos_calmer/actions/workflows/hi3516cv200_images.yml/badge.svg?branch=master
[b3]: https://github.com/openipc/chaos_calmer/actions/workflows/hi3516cv300_images.yml/badge.svg?branch=master
[b4]: https://github.com/openipc/chaos_calmer/actions/workflows/hi3516cv300_images.yml/badge.svg?branch=master
[b5]: https://github.com/openipc/chaos_calmer/actions/workflows/hi3516cv100_images.yml/badge.svg?branch=master
[b6]: https://github.com/openipc/chaos_calmer/actions/workflows/hi3516cv100_images.yml/badge.svg?branch=master
[b7]: https://github.com/openipc/chaos_calmer/actions/workflows/hi3516cv100_images.yml/badge.svg?branch=master
[b8]: https://github.com/openipc/chaos_calmer/actions/workflows/hi3516cv200_images.yml/badge.svg?branch=master
[b9]: https://github.com/openipc/chaos_calmer/actions/workflows/hi3516cv200_images.yml/badge.svg?branch=master
[b10]: https://github.com/openipc/chaos_calmer/actions/workflows/hi3520dv200_images.yml/badge.svg?branch=master
[b11]: https://github.com/openipc/chaos_calmer/actions/workflows/hi3520dv200_images.yml/badge.svg?branch=master

[u1]: https://github.com/OpenIPC/chaos_calmer/releases/download/latest/openwrt-hi35xx-16cv100-u-boot.bin
[u2]: https://github.com/OpenIPC/chaos_calmer/releases/download/latest/openwrt-hi35xx-16cv200-u-boot.bin
[u3]: https://github.com/OpenIPC/chaos_calmer/releases/download/latest/openwrt-hi35xx-16cv300-u-boot.bin
[u4]: https://github.com/OpenIPC/chaos_calmer/releases/download/latest/openwrt-hi35xx-16ev100-u-boot.bin
[u5]: https://github.com/OpenIPC/chaos_calmer/releases/download/latest/openwrt-hi35xx-18av100-u-boot.bin
[u6]: https://github.com/OpenIPC/chaos_calmer/releases/download/latest/openwrt-hi35xx-18cv100-u-boot.bin
[u7]: https://github.com/OpenIPC/chaos_calmer/releases/download/latest/openwrt-hi35xx-18ev100-u-boot.bin
[u8]: https://github.com/OpenIPC/chaos_calmer/releases/download/latest/openwrt-hi35xx-18ev200-u-boot.bin
[u9]: https://github.com/OpenIPC/chaos_calmer/releases/download/latest/openwrt-hi35xx-18ev201-u-boot.bin

[k1]: https://github.com/OpenIPC/chaos_calmer/releases/download/latest/openwrt-hi35xx-16cv100-default-uImage
[k2]: https://github.com/OpenIPC/chaos_calmer/releases/download/latest/openwrt-hi35xx-16cv200-default-uImage
[k3]: https://github.com/OpenIPC/chaos_calmer/releases/download/latest/openwrt-hi35xx-16cv300-default-uImage
[k4]: https://github.com/OpenIPC/chaos_calmer/releases/download/latest/openwrt-hi35xx-16ev100-default-uImage
[k5]: https://github.com/OpenIPC/chaos_calmer/releases/download/latest/openwrt-hi35xx-18av100-default-uImage
[k6]: https://github.com/OpenIPC/chaos_calmer/releases/download/latest/openwrt-hi35xx-18cv100-default-uImage
[k7]: https://github.com/OpenIPC/chaos_calmer/releases/download/latest/openwrt-hi35xx-18ev100-default-uImage
[k8]: https://github.com/OpenIPC/chaos_calmer/releases/download/latest/openwrt-hi35xx-18ev200-default-uImage
[k9]: https://github.com/OpenIPC/chaos_calmer/releases/download/latest/openwrt-hi35xx-18ev201-default-uImage
[k10]: https://github.com/OpenIPC/chaos_calmer/releases/download/latest/openwrt-hi35xx-20dv100-default-uImage
[k11]: https://github.com/OpenIPC/chaos_calmer/releases/download/latest/openwrt-hi35xx-20dv200-default-uImage

[r1]: https://github.com/OpenIPC/chaos_calmer/releases/download/latest/openwrt-hi35xx-16cv100-default-root.squashfs
[r2]: https://github.com/OpenIPC/chaos_calmer/releases/download/latest/openwrt-hi35xx-16cv200-default-root.squashfs
[r3]: https://github.com/OpenIPC/chaos_calmer/releases/download/latest/openwrt-hi35xx-16cv300-default-root.squashfs
[r4]: https://github.com/OpenIPC/chaos_calmer/releases/download/latest/openwrt-hi35xx-16ev100-default-root.squashfs
[r5]: https://github.com/OpenIPC/chaos_calmer/releases/download/latest/openwrt-hi35xx-18av100-default-root.squashfs
[r6]: https://github.com/OpenIPC/chaos_calmer/releases/download/latest/openwrt-hi35xx-18cv100-default-root.squashfs
[r7]: https://github.com/OpenIPC/chaos_calmer/releases/download/latest/openwrt-hi35xx-18ev100-default-root.squashfs
[r8]: https://github.com/OpenIPC/chaos_calmer/releases/download/latest/openwrt-hi35xx-18ev200-default-root.squashfs
[r9]: https://github.com/OpenIPC/chaos_calmer/releases/download/latest/openwrt-hi35xx-18ev201-default-root.squashfs
[r10]: https://github.com/OpenIPC/chaos_calmer/releases/download/latest/openwrt-hi35xx-20dv100-default-root.squashfs
[r11]: https://github.com/OpenIPC/chaos_calmer/releases/download/latest/openwrt-hi35xx-20dv200-default-root.squashfs
