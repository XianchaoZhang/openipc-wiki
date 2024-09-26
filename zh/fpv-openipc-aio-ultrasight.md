# OpenIPC Wiki
[Table of Content](../README.md)

OpenIPC AIO "UltraSight"
---
<a href="https://store.openipc.org"><img src="../images/fpv-openipc-aio.webp"></a>

## 盒子里有什么 
<a href="https://raw.githubusercontent.com/OpenIPC/wiki/master/images/fpv-openipc-aio-content-1.webp"><img src="../images/fpv-openipc-aio-content-1.webp" width="60%"></a> 
<a href="https://raw.githubusercontent.com/OpenIPC/wiki/master/images/fpv-openipc-aio-content-2.webp"><img src="../images/fpv-openipc-aio-content-2.webp" width="60%"></a>

## 第一步概述
- 连接无线天线并安装散热器（参见散热器部分）。
- 连接调试 PCB 并连接网线或 USB-C 线。
- 确保适当冷却，气流对于防止电路板过热是必要的。
- 为电路板供电（参见电源部分）并检查 DHCP 服务器（通常是路由器）是否有新设备及其 IP 地址，电路板将尝试通过 DHCP 获取分配的 IP。
- 您可以使用用户名 **admin** 和密码 **12345** 登录 OpenIPC 的 WebUI 来检查连接情况。

## 散热器
- 包装内附有散热器和导热垫。导热垫两侧均贴有 3M 双面胶带。套件内含两颗螺丝，可用于安装散热器。
- 散热器的作用是散发 PCB 上无线组件的热量。因此，导热垫和散热器需要放置在有无线芯片组的一侧（例如天线连接器、Realtek 芯片和功率放大器）。

## PCB 概述 
<a href="https://raw.githubusercontent.com/OpenIPC/wiki/master/images/fpv-openipc-aio-manual.webp"><img src="../images/fpv-openipc-aio-manual.webp" width="80%"></a>

### 为电路板通电
- 在为 AIO 电路板通电之前，必须将电源焊盘焊接或连接到可靠的 **3A @ 5V DC** 电源或 BEC。如果选择更高的 RF 功率水平，则此安培数会更高。
- 确保电路板通电时冷却充分。风扇必不可少，如果可能，也建议使用散热器。如果无法为电路板提供足够的冷却，很可能会导致电路板损坏。
- 当 BEC 上的负载电容不足时，建议在 BEC 和 AIO 电路板之间添加一个 470uF 电解电容器（包含在套件中），以保护 AIO 电路板免受浪涌损坏并提供去耦。将电容器安装在尽可能靠近 AIO-PCB 的位置。
- 为了提供额外的保护，可以在电容器之前将容量为 1W、最大电压为 5.1V 的齐纳二极管 (ZMY5V1、BZX55C5V1、BZT52C5V1、1N5338B) 焊接到 BEC 的输出端，下图显示了如何焊接组件： ![zener_diode_diagram](https://github.com/OneManChop/OpenIPCwiki/assets/33513057/f0ad9f0b-6146-45af-81b2-4bc530880370)

### 调试/以太网
- 包含以太网/调试适配器。扁平柔性电缆用于将其连接到 AIO-PCB。连接器引脚位于连接器的 PCB 侧，注意将扁平柔性电缆与裸露的连接件一起连接到 PCB。扁平柔性电缆上的“手柄”条将指向 AIO-PCB 上的散热器侧，远离调试器 PCB 上的 RJ45 连接器。

### 摄像头
- MIPI 连接器可以垂直推入 AIO-PCB 上的相应插座。无需很大的力气，也无需移动杠杆或松开夹子。为了拆卸，连接器两侧有两个小插脚，有助于抓住它。

## 软件

### 自动系统升级
- 使用调试以太网板将您的设备连接到网络。
- 登录系统（root：12345）。
- 运行以下命令：

```
fw_setenv upgrade https://github.com/OpenIPC/builder/releases/download/latest/ssc338q_fpv_openipc-urllc-aio-nor.tgz
sysupgrade -k -r -n
```

### 手动系统更新
- 准备一个 1GB 大小的 FAT32 格式的 SD 卡。
- 下载并解压 [此包](https://github.com/openipc/builder/releases/download/latest/ssc338q_fpv_openipc-urllc-aio-nor.tgz)。
- 将 uImage.ssc338q 和 rootfs.squashfs.ssc338q 复制到 SD 卡。
- 按住 Enter 键中断 uboot。
- 运行以下命令：

```
run setsdcard
run uknor
run urnor
```



### 连接到无线路由器
- 将固件升级到最新版本。
- 登录系统（root：12345）。
- 运行以下命令：

```
fw_setenv wlandev rtl8812au-generic
fw_setenv wlanssid Router
fw_setenv wlanpass Password
network restart
```

### 更新引导程序
- 登录系统（root：12345）。
- 运行以下命令：
```
curl -L -o /tmp/uboot.bin https://github.com/openipc/firmware/releases/download/latest/u-boot-ssc338q-nor.bin
flashcp -v /tmp/uboot.bin /dev/mtd0
```

### 自定义 GPIO
- 按钮输入：

```
echo 107 > /sys/class/gpio/export
echo in > /sys/class/gpio/gpio107/direction
cat /sys/class/gpio/gpio107/value
```
- LED 控制：
```
gpio clear 108
gpio set 108
```



### 第三方电缆兼容性
- DJI Pocket2 电缆 - 与 OpenIPC AIO 兼容
- DJI O3 mipi 电缆 - 不适用于 OpenIPC AIO
- RunCam mipi 电缆 - 不适用于 OpenIPC AIO

