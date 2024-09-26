# OpenIPC Wiki 
[内容](../README.zh.md)

Rostelecom HiWatch DS-I122 
--------------

**有充分理由相信这些说明适用于同一平台上构建的其他型号，即 DS-I120、DS-I114、DS-2CD2VC、DS-2CD3VC 等**

## 平台
- 处理器：hi3518cv100
- 传感器：ar0130
- ROM：16Mb
- 内存：128Mb

## 准备工作 
访问 [openipc.org](https://openipc.org/) 并单击 [Precompiled binary files](https://openipc.org/supported-hardware/featured) 按钮 - 我们进入[Supported] 部分 Hardware by SoC](https://openipc.org/supported-hardware/featured)，然后选择 **HiSilicon** 部分。接下来，我们寻找我们的处理器**hi3518cv100**，点击图标！[](https://mixatronik.ru/wp-content/uploads/2023/05/2023-05-03_16-11-18.png ）并查看页面指令生成器。

![](https://mixatronik.ru/wp-content/uploads/2023/05/2023-05-03_17-20-50.png)

在**相机MAC地址**字段中，最好输入相机贴纸上的地址，这样一切都符合风水，但您也可以单击链接生成随机地址。

**相机 IP 地址**字段应包含用于固件的相机地址。它必须与 **TFTP 服务器 IP 地址** 字段中的地址位于同一子网中，该地址通常是执行操作的计算机的地址。 Hicks 的默认服务器地址是 **192.168.1.128** – 这就是我们设置的服务器地址。

我们将内存大小设置为**NOR16M**，不要修改固件类型并将其保留为**Lite**，并将网络接口保留为**相机仅具有以太网网络**，关于存储卡插槽我们表明它存在 – **相机有一个 SD 卡插槽**，然后按 **生成安装指南** 按钮。

![](https://mixatronik.ru/wp-content/uploads/2023/05/2023-05-03_17-21-19.png)

从链接[下载 OpenIPC 固件（精简版）映像](https://openipc.org/cameras/vendors/hisilicon/socs/hi3518cv100/download_full_image?flash_size=16&flash_type=nor&fw_release=lite)下载转储。这是引导加载程序、内核和文件系统的完整转储。只需刷新它，在引导加载程序中执行几个命令，系统就可以运行了。

## 正确的引导加载程序 如果向下滚动上一页，将会出现另外两个链接 - 分别指向引导加载程序和 Linux。您需要刷新引导加载程序并从中刷新 Linux，但本机 Hick 引导加载程序中没有用于此目的的工具。让我们将 OpenIPC 引导加载程序加载到 RAM 中，将控制权转移给它，并从中刷新完整映像。

**超级终端**程序可以使用**Kermit**协议通过**UART**传输数据。为此，您需要在控制台中运行**loadb**命令，并从程序菜单中选择**传输->发送文件**。在出现的窗口中，选择引导加载程序映像文件**OpenIPC**、协议**Kermit**，然后按**发送**按钮 - 我们将看到一个数据传输进度窗口。传输完成后，图像将位于RAM中的**0x81000000**地址。

我们发出命令 **go 0x81000000** 将控制权转移给新的引导加载程序，并准备好按键以停止启动。如果一切按预期进行，我们就进入了 **OpenIPC** 引导加载程序，但问题是其中没有网络。要使其出现，您需要执行命令 **setenv phyaddru 3** 和 **setenv phyaddrd 1** 来初始化界面。

![](https://mixatronik.ru/wp-content/uploads/2023/07/2023-04-29_16-55-24.png)

## 备份 
现在网络已启动，必要的命令也已准备就绪。如果您想保护自己并轻松返回到当前固件，那么进行备份是有意义的。这对于尝试不同的固件也很有用，因为......转储包括 ROM 的全部内容，即不仅是引导加载程序和系统，还包括所有设置、脚本等。

```
setenv ipaddr 192.168.1.10
setenv serverip 192.168.1.128
mw.b 0x82000000 0xff 0x1000000
sf probe 0
sf read 0x82000000 0x0 0x1000000
tftp 0x82000000 backup.bin 0x1000000
```

## 固件 
将下载的 **OpenIPC** 转储上传到相机中。如果进行了备份，则已经设置了正确的地址，并且无需重复此操作，尽管这是可能的。
```
setenv ipaddr 192.168.1.10; setenv serverip 192.168.1.128
saveenv
mw.b 0x82000000 0xff 0x1000000
tftpboot 0x82000000 openipc-hi3518cv100-lite-16mb.bin
sf probe 0
sf erase 0x0 0x1000000
sf write 0x82000000 0x0 0x1000000
reset
```
再次停止启动，进入启动进行配置。您需要设置 **MAC 地址**，配置正确的内存布局，并确保网络在启动和系统中均已启动并运行。引导加载程序在种族上是正确的，因此它具有必要的宏，并且标记是用一个命令配置的，而网络则用更​​多命令配置。

```
run setnor16m
setenv ethaddr 56:8c:6c:c8:14:56
setenv phyaddru 3
setenv extras hieth.phyaddru=3 hieth.mdioifu=0
saveenv
reset
```



### 首次启动
固件和初始配置已完成，您可以登录系统。视频立即生效，可以在 **预览** 或 **VLC** 中观看。

![](https://mixatronik.ru/wp-content/uploads/2023/07/2023-04-24_20-03-55.png)

但日/夜切换不起作用。这里有一件奇怪的事情 - 相机上似乎有一个光传感器，但由于某种原因，即使在库存固件上，当它被阻挡时也没有任何反应，并且只有在镜头本身关闭时才会发生切换。在库存或 RT 固件中都没有发现 **GPIO** 传感器的痕迹，因此它要么是其他传感器，要么是假的。因此，您需要使用脚本来切换模式。对于手动和自动切换，您都需要配置IR滤镜和背光的**GPIO**。这可以在控制台或 Web 界面中完成。

```
cli -s .nightMode.enabled true
cli -s .nightMode.irCutPin1 6
cli -s .nightMode.irCutPin2 5
cli -s .nightMode.backlightPin 42
```


### Cloud IPEye 
现在固件不同了，无法访问原生云，但我想要它。为此，您可以使用 IPEye 云，其支持已集成到固件中。你可以录进去，但是要花钱，而且广播是免费的。 **IPEye** 支持在流媒体设置中启用，即 **Majestic->IPEYE 协议** 部分。接下来，您需要访问该网站，在那里注册并使用其 **MAC 地址** 添加摄像机。

