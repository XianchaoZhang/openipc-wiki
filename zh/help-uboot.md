# OpenIPC Wiki
[Table of Content](../README.md)

帮助：U-Boot 
------------

### 准备环境 
在 booloader shell 中，检查"baseaddr"变量是否已定义。

```bash
printenv baseaddr
```

如果没有，请自行设置。

```bash
# Look up address for your SoC at https://openipc.org/supported-hardware/
setenv baseaddr 0x80600000
```

将闪存芯片的十六进制大小分配给名为"flashsize"的变量。

```bash
# Use 0x800000 for an 8MB flash chip, 0x1000000 for 16MB.
setenv flashsize 0x800000
```

然后将这些值保存到环境中。

```bash
saveenv
```

### 不使用 TFTP 保存原始固件。

在开始之前，[准备环境](#prepare-the-environment)。

在用于连接 UART 端口的终端程序中，启用保存会话日志文件。我喜欢为此使用"screen"，用于连接 UART 适配器并将活动会话记录到文件的命令如下所示：

```bash
screen -L -Logfile fulldump.log /dev/ttyUSB0 115200
```

连接到引导加载程序控制台后，运行一组命令，将全部数据从闪存芯片读取到 RAM 中，然后将其作为十六进制值转储到终端窗口中。

```shell
mw.b ${baseaddr} 0xff ${flashsize}
sf probe 0
sf read ${baseaddr} 0x0 ${flashsize}
md.b ${baseaddr} ${flashsize}
```

由于读取过程将花费相当长的时间（实际上是几个小时），您可能需要断开与终端会话的连接，以避免意外的按键污染输出。按 `Ctrl-a`，然后按 `d`，将会话与活动终端分离。稍后需要重新连接时，运行 `screen -r`，此时日志文件的大小将停止增长。读取 8 MB 闪存应该会产生约 40 MB 的日志文件，而对于 16 MB 芯片，文件大小应该是这个的两倍。

将十六进制转储转换为二进制固件文件，并将其用于进一步研究或将相机恢复到原始状态。

```bash
cat fulldump.log | sed -E "s/^[0-9a-f]{8}\b: //i" | sed -E "s/ {4}.{16}\r?$//" > fulldump.hex
xxd -revert -plain fulldump.hex fulldump.bin
```

使用 [binwalk](https://github.com/ReFirmLabs/binwalk) 解压二进制文件。

### 通过 SD 卡保存固件。

在开始之前，[准备环境](#prepare-the-environment)。

有时您的相机只有无线连接，无法直接从引导加载程序工作。此类相机通常有一个 microSD 卡插槽。在这种情况下，您可以尝试使用 SD 卡作为中间介质来保存原始固件的副本。

由于您要以二进制形式保存固件，因此数据量将是 8 MB 或 16 MB，具体取决于相机闪存芯片的大小。因此任何 SD 卡都可以，即使是最小的。

将卡插入相机上的卡槽，将串行适配器连接到 UART 端口，为相机供电并停止启动过程以进入引导加载程序控制台。

初始化对卡的访问，并清除一些空间以保存固件。数据以 512 字节的块写入卡中。您需要擦除 16384 个块才能清除 8 MB，32768 个块才能清除 16 MB，分别为 0x4000 和 0x8000 十六进制。

请注意，我们将直接写入卡寄存器，绕过分区表。为了避免以后从 PC 访问卡数据时发生冲突，请从卡的开头偏移 8 千字节（8 * 1024 = 8192 字节或 16 个 512 字节块，或十六进制表示的 0x10 个块）。

以 8MB 为例：

```shell
mmc dev 0
mmc erase 0x10 0x4000
```

以 16MB 为例：

```shell
mmc dev 0
mmc erase 0x10 0x8000
```

现在您需要将固件内容从闪存芯片复制到相机的 RAM。为此，请清除 RAM 的一部分（8MB 芯片为 0x800000 字节，16MB 芯片为 0x1000000 字节），读取闪存并将所有内容复制到 RAM 中准备好的空间。然后将复制的数据从 RAM 导出到卡中。

以 8MB 为例：

```shell
mw.b ${baseaddr} ff ${flashsize}
sf probe 0
sf read ${baseaddr} 0x0 ${flashsize}

mmc write ${baseaddr} 0x10 0x4000
```

另一个例子，对于 16MB：

```shell
mw.b ${baseaddr} ff ${flashsize}
sf probe 0
sf read ${baseaddr} 0x0 ${flashsize}

mmc write ${baseaddr} 0x10 0x8000
```

从相机中取出卡并将其插入运行 Linux 的计算机。使用"dd"命令将数据从卡复制到计算机上的二进制文件。

以 8MB 为例：

```bash
sudo dd bs=512 skip=16 count=16384 if=/dev/sdc of=./fulldump.bin
```

以 16MB 为例：

```bash
sudo dd bs=512 skip=16 count=32768 if=/dev/sdc of=./fulldump.bin
```

### 通过串行连接上传二进制图像。

有些相机只有无线连接，无法直接从引导加载程序获得。大多数此类相机也有 SD 卡插槽，但有些没有，或者由于某种原因无法使用，或者您没有卡，或者其他原因。无论如何，您仍然可以将二进制图像上传到相机并运行它，或将其保存到闪存中。方法如下。

首先，您需要在台式计算机上安装"lrzsz"包。我假设它运行 Linux，最好是 Debian 家族，这样示例会更容易。因此，运行此命令以满足先决条件：

```bash
apt install lrzsz
```

现在您已经准备好了。

将要上传的二进制文件放入您将从中启动相机"屏幕"会话的同一目录中。启动会话并使用组合键中断启动例程，启动引导加载程序控制台。

现在您可以运行"help"并检查您的 bootloader 版本支持哪些数据传输协议。如果您在命令列表中看到"loady"，则可以使用 ymodem 协议。在您的相机上运行"loady"，然后按"Ctrl-a"并按":"（分号）。它会将您切换到屏幕底部的命令行。

输入 `exec !! sz --ymodem filename.bin`，其中 _filename.bin_ 并查看通过串行连接上传的文件。速度为 115200 bps。很慢，非常慢。

文件上传后，你就可以做通常的魔术了。要么立即使用"bootm"从内存映像启动，要么将其写入闪存。

### 通过串行连接刷新完整图像

在开始之前，[准备环境](#prepare-the-environment)。

提交设置表单并点击"替代方法"按钮下隐藏的链接后，从 [OpenIPC 网站](https://openipc.org/supported-hardware/) 下载适用于您的 SoC 和闪存芯片的完整固件二进制文件。

![](../images/firmware-full-binary-link.webp)

打开 `screen` 并连接到 UART 端口。

```bash
screen /dev/ttyUSB0 115200
```

登录引导加载程序 shell 并运行：

```shell
mw.b ${baseaddr} 0xff ${flashsize}
loady ${baseaddr}
```

按"Ctrl-a"然后按":"，然后输入

```bash
exec !! sz --ymodem fullimage.bin
```

图片加载完成后，继续

```shell
sf probe 0
sf erase 0x0 ${flashsize}
sf write ${baseaddr} 0x0 ${filesize}
```

### 从 TFTP 刷写完整镜像

在开始之前，[准备环境](#prepare-the-environment)。

下载 [适用于您的 SoC 的完整图像二进制文件](https://openipc.org/supported-hardware/) 并将其放在本地 TFTP 服务器的根目录中。

启动会话并启动到引导加载程序控制台，使用组合键中断启动例程。在控制台中，设置本地网络的参数（如果需要）。

```bash
setenv ipaddr 192.168.1.10
setenv netmask 255.255.255.0
setenv gatewayip 192.168.1.1
setenv serverip 192.168.1.254
```

使用以下命令将完整图像重新刷新到相机：

以 8MB 为例：

```shell
mw.b ${baseaddr} 0xff ${flashsize}
tftpboot ${baseaddr} openipc-${soc}-lite-8mb.bin
sf probe 0; sf erase 0x0 ${flashsize}; sf write ${baseaddr} 0x0 ${filesize}
reset
```

以 16MB 为例：

```shell
mw.b ${baseaddr} 0xff ${flashsize}
tftpboot ${baseaddr} openipc-${soc}-ultimate-16mb.bin
sf probe 0; sf erase 0x0 ${flashsize}; sf write ${baseaddr} 0x0 ${filesize}
reset
```

首次启动时，再次登录引导加载程序 shell 并运行"run setnor16m"命令重新映射分区。

### 从 SD 卡读取二进制图像。

在开始之前，[准备环境](#prepare-the-environment)。

如果您的相机支持 SD 卡并且您在引导加载程序中有"fatload"命令，那么您可以从 SD 卡读取固件二进制文件。

首先，准备卡：将其格式化为 FAT 文件系统，并将引导加载程序、内核和 rootfs 二进制文件放在那里。将卡插入相机并启动引导加载程序控制台。

检查您是否有权使用该卡。

```bash
mmc rescan
```

然后解锁对闪存的访问并开始将卡中的文件内容写入闪存。

注意！请注意，本示例中使用的加载地址和文件名称不一定与您的特定相机相匹配。请参阅文档，或在 [我们的 Telegram 频道][telegram] 上寻求帮助。

闪存引导加载程序。

```shell
mw.b ${baseaddr} 0xff 0x50000
sf probe 0
sf erase 0x0 0x50000
fatload mmc 0:1 ${baseaddr} u-boot-with-spl.bin
sf write ${baseaddr} 0x0 ${filesize}
```

Flash kernel.

```shell
mw.b ${baseaddr} 0xff 0x200000
sf probe 0
sf erase 0x50000 0x200000
fatload mmc 0:1 ${baseaddr} uImage.${soc}
sf write ${baseaddr} 0x50000 ${filesize}
```

Flash root filesystem.

```shell
mw.b ${baseaddr} 0xff 0x500000
sf probe 0
sf erase 0x250000 0x500000
fatload mmc 0:1 ${baseaddr} rootfs.squashfs.${soc}
sf write ${baseaddr} 0x250000 ${filesize}
```

### 绕过受密码保护的引导加载程序。

更改引导加载程序是一项有风险的操作。如果出现问题，很有可能会让您的相机变成镇纸。因此，在刷新新的引导加载程序之前，您必须权衡所有风险和收益。在大多数情况下，原始引导加载程序加上新内核和新操作系统应该可以正常工作。但也有例外。

#### 短接闪存芯片上的引脚

如果您无法使用组合键中断启动序列，或者您的相机需要您不知道的引导加载程序密码，您仍然可以让它停止 Linux 内核启动并将您抛入 shell。

首先要做的是找到相机电路板上的闪存芯片。通常，这是一个方形芯片，有 8 个引脚，标记为 25Q64 或 25Q128，很少标记为 25L64 或 25L128。如果您找不到芯片，请尝试从两侧拍摄电路板的照片。然后 [在我们的 Telegram 频道](https://t.me/openipc) 中寻求帮助。__不要尝试短路任何随机芯片！这很可能会烧毁您的相机电路。__

在引导加载程序启动之后但在调用 Linux 内核之前，用小金属物体、螺丝刀或镊子短路闪存芯片的引脚 5 和 6。

SOIC8 芯片的引脚 5 和 6 位于引脚 1 的对角，由旁边的浮雕或绘制的点表示。

![](../images/flash-pins.webp)
![](../images/flash-pins-2.webp)

[详细破解](https://cybercx.co.nz/bypassing-bios-password/) 或 [存档](https://github.com/OpenIPC/wiki/blob/master/en/help-uboot.md#bypassing-password-protected-bootloader) 版本的文章

#### 降级库存固件。

如今，我们看到越来越多的相机使用密码保护对引导加载程序控制台的访问。因此，即使您连接到相机的 UART 端口，在中断标准启动周期后，您看到的也只是提示输入密码。在这种情况下，一个相对安全的解决方案是将固件降级到尚未实​​现密码保护的版本。例如，对于雄迈相机，引导加载程序密码保护在 2021 年 7 月左右开始出现，因此您需要从更早的日期为您的相机安装固件。成功将相机降级为无密码引导加载程序后，您可以以常规方式安装 OpenIPC 固件。

#### 侧载解锁的引导加载程序。

许多现代相机都使用 fastboot 协议，该协议允许相机将引导加载程序二进制代码直接加载到内存中，然后从那里运行。检查我们的 [burn utility][burn] 是否支持您的相机的 SoC。

#### 修改库存固件。

绕过引导加载程序保护的一种方法是转储原始固件，并用未锁定的替代方案替换那里的引导加载程序。或者，您可以刷新整个 OpenIPC 固件，因为无论如何，编程器中都有芯片。

__不要忘记备份您的原始固件！__

故障排除

在开始之前，[准备环境](#prepare-the-environment)。

如果在尝试设置环境变量时出现"参数过多"错误，请尝试在 Linux 内部使用"fw_setenv"而不是 U-boot 中的"setenv"执行此操作。

__U-boot 控制台：__

```shell
hisilicon # setenv uk 'mw.b ${baseaddr} 0xff ${flashsize}; tftp ${baseaddr} uImage.${soc}; sf probe 0; sf erase 0x50000 0x200000; sf write ${baseaddr} 0x50000 ${filesize}'
** Too many args (max. 16) **
```

__OpenIPC Linux：__

```shell
root@openipc-hi3518ev100:~# fw_setenv uk 'mw.b ${baseaddr} 0xff ${flashsize}; tftp ${baseaddr} uImage.${soc}; sf probe 0; sf erase 0x50000 0x200000; sf write ${baseaddr} 0x50000 ${filesize}'
```

[burn]: https://github.com/OpenIPC/burn
[telegram]: https://t.me/OpenIPC
