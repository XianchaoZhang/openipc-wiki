# OpenIPC Wiki
[目录](../README.zh.md)

向您的固件添加 wifi 驱动程序 
--- 
由于大多数相机的闪存非常小，OpenIPC 固件镜像不包含许多 wifi 驱动程序，因为每个驱动程序很容易就超过 1.5MB。这意味着在许多情况下，您必须向您的固件镜像添加适当的 wifi 驱动程序。

### 步骤 1：准备构建环境 
您需要一个 Linux 环境。首先下载 OpenIPC 固件存储库：

```
git clone https://github.com/OpenIPC/firmware.git openipc-firmware
cd openipc-firmware
```

安装构建所需的包：

```
sudo make deps
```

### 第二步：确定驱动包
下面是一些最常见的wifi驱动包：

#### AIC:
```
BR2_PACKAGE_AIC8800_OPENIPC
```

#### Altobeam:
*1. Select general ATBM driver:*
```
BR2_PACKAGE_ATBM60XX
```
*2. Enable the driver for your specific card:*
```
BR2_PACKAGE_ATBM60XX_MODEL_601X
BR2_PACKAGE_ATBM60XX_MODEL_602X
BR2_PACKAGE_ATBM60XX_MODEL_603X
BR2_PACKAGE_ATBM60XX_MODEL_6041
```

*3. Set usb or sdio:*
```
BR2_PACKAGE_ATBM60XX_INTERFACE_USB
BR2_PACKAGE_ATBM60XX_INTERFACE_SDIO
```

*Example: to build atbm603x_wifi_usb:*
```
BR2_PACKAGE_ATBM60XX=y
BR2_PACKAGE_ATBM60XX_MODEL_603X=y
BR2_PACKAGE_ATBM60XX_INTERFACE_USB=y
```

#### iComm:
*SSV615X/SSV625X, USB ID 0x6000:*

```
BR2_PACKAGE_SSV615X_OPENIPC
```

*SSV635X, USB ID 0x6011:*

```
BR2_PACKAGE_SSV635X_OPENIPC
```

#### MediaTek:
```
BR2_PACKAGE_MT7601U_OPENIPC
```

#### SigmaStar:
```
BR2_PACKAGE_SSW101B
```

#### Realtek:
```
BR2_PACKAGE_RTL8188EUS_OPENIPC
BR2_PACKAGE_RTL8188FU_OPENIPC
BR2_PACKAGE_RTL8189ES_OPENIPC
BR2_PACKAGE_RTL8189FS_OPENIPC
BR2_PACKAGE_RTL8192EU_OPENIPC
BR2_PACKAGE_RTL8733BU_OPENIPC
BR2_PACKAGE_RTL8812AU_OPENIPC
```

记下您所需驱动程序的 `BR2_PACKAGE` 变量。观察原始固件的启动消息可能有助于确定网络设备和接口类型，因为从主板上看可能不明显。在启动消息中看到 `atbm603x_wifi_usb` 表明此相机有一个通过 USB 内部连接的 `atbm603x` wifi 设备。

### 步骤 3：将 BR2_PACKAGE 变量添加到您的固件配置中 
固件配置文件按芯片组排序在 `br-ext-chip-*` 目录中。导航到您要构建的芯片组的目录，然后导航到 `/configs/` 目录。

例如：你有一个海思芯片组：

`cd br-ext-chip-hisilicon/configs/`

您将在里面看到许多 `_defconfig` 文件。在文本编辑器中打开所需芯片和固件版本的文件。将适当的 `BR2_PACKAGE` 变量添加到此文件，并在变量末尾添加 `=y`。

例如：您想添加RTL8188EUS驱动程序：

`BR2_PACKAGE_RTL8188EUS_OPENIPC=y`

### 步骤 4：构建你的固件
返回 openipc 固件目录 `openipc-firmware/` 的根目录。运行 `make` 并选择你在上一步中编辑的配置。

或者，您可以运行 `make BOARD=<your_config>`，其中 `<your_config>` 是您刚刚编辑的配置文件的名称，减去 `_defconfig`

例如：你想为 `hi3516ev200` 构建  `ultimate`：

`make BOARD=hi3516ev200_ultimate`

构建完成后，您将在 `output/images/` 目录中找到输出：

```
./rootfs.hi3516ev200.cpio
./openipc.hi3516ev200-nor-ultimate.tgz
./rootfs.squashfs.hi3516ev200
./rootfs.hi3516ev200.tar
./uImage.hi3516ev200
```

您现在可以将 `rootfs.squashfs.*` 和 `uImage.*` 与 [sysupgrade](./sysupgrade.md) 或您喜欢的更新机制一起使用。

*更多 wifi 配置请参见[无线设置](./wireless-settings.md)。*

*有关从源代码构建 OpenIPC 的更多信息，请参阅[源代码](./source-code.md)。*

