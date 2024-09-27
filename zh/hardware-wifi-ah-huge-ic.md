# OpenIPC Wiki
[目录](../README.zh.md)

为 Ingenic T31 添加 Huge-IC AH WiFi HaLow 驱动程序 
---------------------------------------------------- 
对于像我这样的基础用户，高级用户可以完全忽略本文档。

本文档介绍了如何将 Huge-IC 的 AH [WiFi HaLow](https://iot4beginners.com/wi-fi-halow/) 驱动程序添加到 OpenIPC 固件。

### 获取驱动源并编辑 Makefile

获取驱动程序的源文件，以便您可以编译这些驱动程序并上传到您的相机系统。这样当硬件连接时，相机（主机）就可以识别硬件并与其连接。这就像当您将 USB 加密狗连接到计算机时，它会加载驱动程序。您的微处理器不会像您的 PC 那样加载驱动程序，因此您需要自己动手。

首先，找到 Makefile。该文件包含编译说明，如果操作不正确，则意味着您的相机板无法使用 wifi。在 Makefile 中，编辑 ARCH、COMPILER 和 LINUX_KERNEL_PATH 的值。

Ingenic 使用 MIPS 架构，因此将其用于 ARCH ‘ARCH := mips’

对于编译器和内核路径，它将是一个交叉编译——有点依赖于你在 OpenIPC 中获得的内容。要进一步了解，请阅读：https://blukat.me/2017/12/cross-compile-arm-kernel-module/

我们现在可以继续下载并提取 OpenIPC 固件，其提取的文件将为我们提供编译器和内核的路径。

### 下载 OpenIPC 固件

```
cd
git clone git@github.com:OpenIPC/firmware.git
cd firmware
./building.sh t31_ultimate
```
（t31_ultimate 因为 ultimate 附带对无线设备的支持。lite 删除了这些设备以节省空间。）

现在您可以在驱动程序源 Makefile 中更改编译器和内核路径：

```
#Driver Compilation for Ingenic T31
ARCH := mips
COMPILER := ~/firmware/output/host/bin/mipsel-linux-
LINUX_KERNEL_PATH := ~/firmware/output/build/linux-3.10.14
```

### 现在运行 ‘make fmac’

（此处为 FMAC 或任何其他与您的驱动程序相关的名称）

如果一切按计划进行，您应该有一个脚本文件和一个或多个 .ko 扩展文件。这些 .ko 扩展文件是您的驱动程序，脚本文件包含主机微控制器查找和激活驱动程序的指令。

### 是时候将驱动程序上传到相机系统了

如果文件很小并且 Ingenic 有额外的可用空间，则可以侧载驱动程序。

将 .ko（驱动程序）和 `fmac`（您的脚本文件可以有不同的名称）脚本文件上传到您选择的文件夹。如果需要，请确保在 fmac 脚本文件中编辑相应的路径。

### 测试

使用你的 WiFi 硬件进行测试以确保其正常工作。

### 接下来，创建一个包

一旦您学会了如何创建包，请与 OpenIPC 管理团队成员分享。他们可能会同意将您的驱动程序包包含在 repo 中。一旦作为包包含，下次您只需在 OpenIPC 配置文件中取消注释并激活包即可使用它。

