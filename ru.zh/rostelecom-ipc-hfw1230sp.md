# OpenIPC Wiki 
[内容](../README.md)

Rostelecom IPC-HFW1230SP/IPC-HDW1230SP 
--------------

**注意，目前这些只是工作笔记，而不是完整的操作说明！**

我并不是说该相机是大华DH-IPC1230SP，因为这并不完全正确。当然，它是由大华生产的，但模型显然是另一回事。事实证明，原厂相应大华型号的固件不适合该尸体。幸运的是，该硬件平台得到了**OpenIPC**项目的支持。

## 目前情况
- OpenIPC 加载程序。
- 基于光传感器的日/夜模式切换。
- 有一个图像，但图像还有很多不足之处，目前还没有解决方案。

## 平台
- 处理器 hi3516cv300
- NAND内存
- 传感器ov2735

## 固件 从 GitHub 下载存档，其中包含现有处理器的引导加载程序以及内核和文件系统映像。
- [u-boot-hi3516cv300-universal.bin](https://github.com/OpenIPC/firmware/releases/download/latest/u-boot-hi3516cv300-universal.bin)
- [openipc.hi3516cv300-nand-ultimate.tgz](https://github.com/OpenIPC/firmware/releases/download/latest/openipc.hi3516cv300-nand-ultimate.tgz)

我们将引导加载程序按原样放入 tftp 服务器文件夹中，并且必须首先解压内核和文件系统映像。  要安装固件，您需要通过 UART 连接，相应地，这将需要 USB-TTL 适配器、连接器和终端程序。

### 联系

- 将连接器连接至相机和适配器。
- 将适配器连接到计算机 - 将出现一个虚拟 COM 端口。我假设驱动程序已经安装。 
- 启动终端。 
- 我们为相机供电，一旦启动日志开始运行，按任意键停止启动并进入引导加载程序。

### Bootloader 
对于现有平台有一个通用的OpenIPC bootloader，它大大简化了固件过程。  
```
mw.b 0x82000000 0xff 0x100000
tftp 0x82000000 u-boot-hi3516cv300-universal.bin
nand erase 0x0 0x100000
nand write.i 0x82000000 0x0 0x100000

reset
```
如果一切顺利，重新启动系统后，您将需要再次按任意键停止并进入 OpenIPC 引导加载程序。引导加载程序包含必要的宏，因此一切都变得更加简单。首先，使用 setnand 命令将引导加载程序切换到 NAND 内存类型： 

```
run setnand
```
重新启动后，环境变量和标记将与 NAND 内存相对应。
```
setargs=setenv bootargs mem=${osmem} console=ttyAMA0,115200 panic=20 init=/init root=ubi0:rootfs rootfstype=ubifs ubi.mtd=3,2048 mtdparts=${mtdparts}
bootargs=mem=32M console=ttyAMA0,115200 panic=20 init=/init root=ubi0:rootfs rootfstype=ubifs ubi.mtd=3,2048 mtdparts=hinand:256k(boot),768k(wtf),3072k(kernel),-(ubi)
```
将 serverip 更改为您计算机的地址，设置您的传感器、MAC 地址并保存：
```
setenv serverip 192.168.1.128
setenv sensor ov2735_i2c
setenv ethaddr 00:12:34:56:78:90    //MAC-адрес с наклейки камеры

saveenv
```
### 内核和文件系统映像 
由于种族正确的引导加载程序，固件并不容易，但非常简单：
```
run uknand
run urnand

reset
```
### 首次启动
我们不中断启动的加载，观察系统启动日志。如果一切按预期进行，并且所使用的程序集中没有任何损坏，那么几秒钟后我们将看到登录提示。以 root 身份登录（无需密码）并输入 ifconfig eth0 命令以查看生成的 IP 地址。默认 Web 界面在端口 85 上可用。登录名：admin，密码：12345。首次登录时，系统会要求您设置新的复杂密码，该密码也将成为通过 UART 或登录控制台时的 root 密码。 SSH。广播第一个流立即生效，无需额外设置。

预览只有幻灯片放映，但如果您想要视频流，查看它的最简单方法是在 VLC 中，从菜单中选择"打开 URL"并输入以下行之一：

- rtsp://admin:password@ip-address:554/stream=0 - 第一个流
- rtsp://admin:password@ip-address:554/stream=1 - 第二个流，其中：password - 您的密码，ip-address - 摄像机地址。

## 夜间模式 
当天黑或光源关闭时，摄像机通常会切换到夜间模式。图像切换为黑白模式，红外滤镜关闭，红外照明打开。在相反的情况下，执行相反的操作。由于有光传感器，因此您只需在 Majestic -> 夜间模式部分的相应字段中输入正确的 GPIO：

- 启用夜间模式
- 设置IR传感器信号的GPIO引脚：64
- 设置 IRcut 滤波器信号的 GPIO pin1：59
- 设置 IRcut 滤波器信号的 GPIO pin2：58
- 设置 GPIO 引脚以打开夜间模式照明：53

![夜间模式](https://mixatronik.ru/wp-content/uploads/2023/02/2023-02-25_15-47-50.png)

