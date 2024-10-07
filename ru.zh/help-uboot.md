# OpenIPC Wiki 
[内容](../README.zh.md)

帮助：U-boot 
------------

### 环境变量

如果您在尝试保存变量时收到"Too much args"错误，请尝试在 Linux 环境中重复该操作，将"setenv"替换为"fw_setenv"。

__U-boot console:__
```
hisilicon # setenv uk 'mw.b 0x82000000 ff 1000000; tftp 0x82000000 uImage.${soc}; sf probe 0; sf erase 0x50000 0x200000; sf write 0x82000000 0x50000 ${filesize}'
** Too many args (max. 16) **
```

__OpenIPC Linux:__
```
root@openipc-hi3518ev100:~# fw_setenv uk 'mw.b 0x82000000 ff 1000000; tftp 0x82000000 uImage.${soc}; sf probe 0; sf erase 0x50000 0x200000; sf write 0x82000000 0x50000 ${filesize}'
root@openipc-hi3518ev100:~#
```

### 无需 tftp 即可保存出厂固件。

在用于连接UART端口的终端程序中，设置要保存的会话日志。我们以终端程序"screen"为例。在这种情况下，连接到 UART 适配器并将会话日志保存到 _fulldump.log_ 文件的命令将如下所示：

```
$ screen -L -Logfile fulldump.log /dev/ttyUSB0 115200
```

然后，在引导加载程序控制台中，运行一系列命令，首先将闪存的全部内容读取到相机的 RAM 中，然后在屏幕上显示 RAM 中该数据的十六进制表示形式。

使用十六进制表示法表示内存地址。因此，十六进制表示形式的 0 看起来像 0x0、8 兆字节 (8 * 1024 * 1024 = 8,388,608 字节) - 如 0x800000、16 兆字节 (16 * 1024 * 1024 = 或 16,777,216 字节) - 如 0x1000000。

读取8MB闪存：

```
mw.b 0x82000000 ff 0x800000
sf probe 0
sf read 0x82000000 0x0 0x800000
md.b 0x82000000 0x800000
```

读取16MB闪存：

```
mw.b 0x82000000 ff 0x1000000
sf probe 0
sf read 0x82000000 0x0 0x1000000
md.b 0x82000000 0x1000000
```

运行命令读取内存内容后，您可以分离会话，以便意外的击键不会乱七八糟地记录日志。在"screen"中，这是通过连续按下"Ctrl-a"键和"d"（分离）键的组合来完成的。要随后加入正在运行的会话，请使用"screen -r"命令。

通过串行连接传输数据的过程可能需要几个小时，请做好准备。但结果，您将拥有原始固件的完整副本，可以将其转换为二进制文件并用于进一步研究或将相机恢复到原始形式。

```
cat fulldump.log | sed -E "s/^[0-9a-f]{8}\b: //i" | sed -E "s/ {4}.{16}\r?$//" > fulldump.hex
xxd -revert -plain fulldump.hex fulldump.bin
```

使用 [binwalk](https://github.com/ReFirmLabs/binwalk) 访问二进制文件的内容。

#### 通过 SD 卡保存固件。

碰巧相机只有无线访问权限，不能直接从引导加载程序运行。通常，此类相机都有一个用于 microSD 格式的外部存储卡的插槽。在这种情况下，您可以尝试使用该卡作为中间载体来获取原始固件的副本。您需要保存的数据量不超过 16 MB，因此任何可用的卡（即使是最小的卡）都可以。

将卡插入相机上相应的插槽，将串行适配器连接到 UART 端口，为相机供电并停止启动过程，以便进入引导加载程序控制台。

初始化对卡的访问，并清理空间以保存固件副本。向卡写入数据以 512 字节块为单位进行。要擦除 8 MB，您需要擦除这些块中的 16384 个，对于 16 MB，您需要擦除 32768 个块，这在十六进制中分别是 0x4000 和 0x8000。

请注意，我们使用直接访问映射寄存器，绕过分区表。为了避免访问卡数据时发生冲突，我们将从开头跳过 8 KB 写入数据（8 * 1024 = 8192 字节或 16 个 512 字节的块，或十六进制表示的 0x10 块）

```
mmc dev 0
mmc erase 0x10 0x8000
```

现在您需要将固件内容从闪存芯片复制到相机的 RAM。为此，请清除一部分 RAM（8 MB 芯片为 0x800000 字节，16 MB 芯片为 0x1000000 字节），访问闪存芯片，并将全部闪存复制到已清除的 RAM 空间中。然后将复制的数据从 RAM 保存到卡中。

注意！在示例中我们使用起始地址0x2000000，但对于不同的相机型号，该参数可能会有所不同。

```
mw.b 0x2000000 ff 0x1000000
sf probe 0
sf read 0x2000000 0x0 0x1000000
mmc write 0x2000000 0x10 0x8000
```

从相机中取出卡并将其插入 Linux 计算机。使用"dd"命令，将数据从卡复制到计算机磁盘上的二进制文件。

```
dd bs=512 skip=16 count=32768 if=/dev/sdc of=./fulldump.bin
```


### 绕过密码保护的引导加载程序。

更改引导加载程序是一项危险的操作。如果出现问题，相机变成镇纸的可能性太高了。因此，在烧录新的引导加载程序之前，您需要权衡所有风险和收益。

在大多数情况下，原始引导加载程序加上新内核和新操作系统是完全可行的选择。但也有例外。最近，越来越多的相机使用密码锁定对引导加载程序控制台的访问。也就是说，即使您通过 UART 端口连接到相机，在中断标准启动周期后您所能看到的只是提示输入密码。在这种情况下，一个相对安全的解决方案是将本机固件降级到尚不需要密码的版本。例如，对于雄迈相机，引导加载程序中的密码出现在 2021 年 7 月左右，因此，您将需要一个包含较早日期的相机专有固件的文件。成功降级到无密码引导加载程序后，您将能够使用标准方式安装 OpenIPC 固件。
