# OpenIPC Wiki
[Table of Content](../README.md)

闪存芯片接口 
--------------------

这是关于通过使用编程器直接与闪存芯片交互来识别、读取、写入、验证和擦除闪存芯片的高级主题。这不是刷新固件的首选方法，但当其他方法失败时，它肯定很有用。

如果您刚刚开始，那么您可能来错地方了：请查看 [安装](installation.md) 和 [刻录示例](burn-example.md) 以获得更好的起点。


SOIC8 芯片 
-------------------- 
SOIC8 是闪存芯片的常见封装。它是一种小型封装，有 8 个引脚，引脚间距为 1.27mm。引脚从左上角开始按逆时针方向编号，用圆点标记。左上角的引脚为引脚 1，右上角的引脚为引脚 2，

正如 [帮助：U-Boot](help-uboot.md) 中提到的，SOIC8 闪存芯片有时会被诱骗直接进入 u-boot 模式，如下图所示： 
![](../images/flash-pins.webp) 
![](../images/flash-pins-2.webp)


SOIC8 夹子 
--------------------

SOIC8 夹子是连接 SOIC8 芯片的便捷方式。它们价格便宜，使用起来也相当方便。不过请记住，夹子需要编程器作为夹子和计算机之间的接口。

查看夹子的带状电缆时可能会令人困惑，因为两排针脚——它是如何映射的？查看下面的带状电缆图像，有一根红线表示针脚 1，针脚是相对于电缆键槽进行编号的。不要误以为红色应该是电压，在这种情况下，它应该映射到 SOIC8 芯片和针脚电缆适配器的针脚 1。这样您就可以看到适配器的针脚与 SOIC8 闪存芯片规格的针脚相同。

![](../images/ribbon-cable.jpg)

以下是连接到 Raspberry Pi Pico 的 SOIC8 夹子的示例：

![](../images/soic8-clip-programmer-example.png)

**警告：** 通常，摄像头板除了闪存芯片外，还配有其他 SOIC8 类型的芯片，因此您需要确保在开始工作之前识别正确的芯片。


选择编程器
--------------------


### CH341A 
市面上有许多编程器，它们各有优缺点。最受欢迎的编程器是利用 CH341A 的电路板，这是一种便宜且易于使用的芯片组。有关 CH341A 的更多详细信息，请参阅 [CH341A 硬件编程器](hardware-programmer.md)。


### Raspberry Pi Pico 
Raspberry Pi Pico 是一块带有 USB 端口和多个 GPIO 引脚的微控制器板。它价格便宜且易于使用，也许您已经有一个了。无论它是否是无线版本，它们的工作原理都相同。为了将 Pico 用作编程器，您需要在将 pico 插入计算机时按住 BOOTSEL 按钮，使其进入引导加载程序模式。Pico 将显示为 USB 驱动器，您可以将 uf2 文件拖放到驱动器上。目前，最好的 pico-serprog 库是 [pico-serprog](https://github.com/opensensor/pico-serprog) 的一个分支，它修复了原始版本中遇到的许多问题。

pico-serprog Github 上有更多说明，但是 pico-serprog 固件将为您提供一个额外的 USB COM 端口，该端口映射到以下 GPIO。

| Pin | Function |
|-----|----------|
| 7   | CS       |
| 6   | MISO     |
| 5   | MOSI     |
| 4   | SCK      |

由于大多数 SOIC8 闪存芯片都是 3.3V，因此您需要连接到 PICO 的 3.3 V 导轨（引脚 36），并且记得同时连接 GND 引脚，Pico 上有许多 GND 引脚，例如引脚 38。
* 注意：如果由于某种原因您的芯片需要 5V，您可以使用 VSYS（引脚 40）而不是 3.3V，但请务必阅读特定闪存芯片的规格。



Flashrom 程序 
-------------------- 
Flashrom 是一个可用于读取、写入、擦除和验证闪存芯片的程序。它适用于 Linux、Windows 和 Mac。目前，通常您需要针对要使用的平台对其进行编译。它是一个命令行程序，有许多可用选项。以下是一些如何使用 flashrom 的示例。

本指南重点介绍如何使用 flashrom，目前不解释如何构建 flashrom，但您可以在 [flashrom 网站](https://flashrom.org/) 上找到说明。

### 确定你的 COM 端口

对于 Windows，您可以使用设备管理器来确定编程器所连接的 COM 端口。

对于 Linux，您可以使用“dmesg”命令来确定编程器所连接的 COM 端口。

### 读取闪存芯片

要读取闪存芯片，有时您需要知道正在读取的闪存芯片的类型。

尝试运行简单的探测来验证您是否与编程器建立了连接。pico-serprog 是一个 serprog（或串行编程器），需要将其指定给 flashrom。在此示例中，编程器连接到 COM23，波特率为 2000000，已知该波特率运行良好，可以在 2-3 分钟内读取 16MB 闪存芯片。以下是读取闪存芯片的示例。在此示例中，flashrom 告诉我们必须在三种不同的芯片之间进行选择，我们选择了“GD25B128B/GD25Q128B”。


```bash
./flashrom.exe -p serprog:dev=COM23:2000000 -V
```

Here is an example of reading a flash chip.   In this example, flashrom had told us we had to pick between three different chips, and we picked the "GD25B128B/GD25Q128B".
```bash
# ./flashrom.exe -p serprog:dev=COM23:2000000 -c "GD25B128B/GD25Q128B" -r gokev300-camera-12242023.bin -VV --force
flashrom 1.4.0-devel (git:v1.2-1386-g5106287e) on Windows 10.0 (x86_64)
flashrom is free software, get the source code at https://flashrom.org

Using clock_gettime for delay loops (clk_id: 1, resolution: 100ns).
flashrom was built with GCC 13.2.0, little endian
Command line (8 args): C:\msys64\home\matte\flashrom\flashrom.exe -p serprog:dev=COM23:2000000 -c GD25B128B/GD25Q128B -r gokev300-camera-12242023.bin -VV --force
Initializing serprog programmer
Baud rate is 2000000.
serprog: connected - attempting to synchronize
.
serprog: Synchronized
serprog: Interface version ok.
serprog: Bus support: parallel=off, LPC=off, FWH=off, SPI=on
serprog: Maximum write-n length is 32
serprog: Maximum read-n length is 32
serprog: Programmer name is "pico-serprog"
serprog: Serial buffer size is 64
Warning: Automatic command availability check failed for cmd 0x07 - won't execute cmd
Warning: NAK to query operation buffer size
serprog: operation buffer size is 300
serprog: Warning: Programmer does not support toggling its output drivers
The following protocols are supported: SPI.
Probing for GigaDevice GD25B128B/GD25Q128B, 16384 kB: compare_id: id1 0xc8, id2 0x4018
Added layout entry 00000000 - 00ffffff named complete flash
Found GigaDevice flash chip "GD25B128B/GD25Q128B" (16384 kB, SPI) on serprog.
Chip status register is 0x00.
Chip status register: Status Register Write Disable (SRWD, SRP, ...) is not set
Chip status register: Block Protect 4 (BP4) is not set
Chip status register: Block Protect 3 (BP3) is not set
Chip status register: Block Protect 2 (BP2) is not set
Chip status register: Block Protect 1 (BP1) is not set
Chip status register: Block Protect 0 (BP0) is not set
Chip status register: Write Enable Latch (WEL) is not set
Chip status register: Write In Progress (WIP/BUSY) is not set
This chip may contain one-time programmable memory. flashrom cannot read
and may never be able to write it, hence it may not be able to completely
clone the contents of this chip (see man page for details).
===
This flash part has status UNTESTED for operations: WP
The test status of this chip may have been updated in the latest development
version of flashrom. If you are running the latest development version,
please email a report to flashrom@flashrom.org if any of the above operations
work correctly for you with this flash chip. Please include the flashrom log
file for all operations you tested (see the man page for details), and mention
which mainboard or programmer you tested in the subject line.
Thanks for your help!
serprog_delay used, but programmer doesn't support delays natively - emulating
Block protection is disabled.
Reading flash... read_flash:  region (00000000..0xffffff) is readable, reading range (00000000..0xffffff).
done.
```

### 写入闪存芯片

写入闪存芯片与读取闪存芯片非常相似。您需要指定 COM 端口、波特率，可能还需要指定芯片类型。只需将 flashrom 的参数更改为包含 -w 选项和要写入闪存芯片的文件即可。

```bash
./flashrom.exe -p serprog:dev=COM23:2000000 -c "GD25B128B/GD25Q128B" -w openipc-hi3516ev300-ultimate-16mb.bin -VV --force
```

在写入操作期间，flashrom 将首先读取整个芯片，然后擦除并写入芯片，然后重新读取以进行验证。在尝试写入芯片之前，请务必确保成功备份并完成读取周期。


### 结论 
本指南并不是一份关于使用 flashrom 的完整指南，而只是为那些有兴趣使用它的人提供一个起点，同时也为那些拥有 raspberry picos 并想用它们做一些有用的事情的人提供灵感。

如果您喜欢 pico-serprog 示例，那么您可能也会非常喜欢 [pico-uart-bridge](https://github.com/Noltari/pico-uart-bridge)，它通过单个 USB 连接提供多个 COM 端口，用于连接 UART 终端。

