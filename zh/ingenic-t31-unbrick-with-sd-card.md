[Table of Content](../README.md)

使用 SD 卡解开 Ingenic T31 
---

#### 注意： 
```
在某些设备上，例如许多 Wyze 和 Atom 型号，SD 卡通过 GPIO（通用输入/输出）连接供电。这意味着您必须在 U-Boot 或 Linux 系统中激活特定的 GPIO 才能为使用 SD 卡供电。如果您的设备以这种方式设置，则您无法使用此方法，除非对硬件进行物理更改。

```


### Ingenic T31 启动序列

![](../images/t31_boot_sequence.png)

如果无法从闪存上的 uboot 启动，则无论设置了什么 bootsel 引脚，T31 都会尝试从 SD 卡启动。因此，如果闪存芯片上的 uboot 因某种原因损坏，我们可以将 uboot 刻录到 SD 卡并从中启动。uboot 文件应专门为 SD 卡启动而编译，您不能使用用于普通闪存启动的文件。

### 编译uboot用于SD卡启动

```
mkdir /opt/openipc
cd /opt/openipc
git clone https://github.com/Dafang-Hacks/mips-gcc472-glibc216-64bit.git
git clone https://github.com/OpenIPC/u-boot-ingenic.git
export PATH="$PATH:/opt/openipc/mips-gcc472-glibc216-64bit/bin"
cd u-boot-ingenic
make distclean
```
现在根据您的 T31 芯片类型选择最终的"make"命令

SoC  | Command
---- | ---------------------------
T31N | make isvp_t31_msc0 
T31L | make isvp_t31_msc0_lite
T31X | make isvp_t31_msc0_ddr128M
T31A | make isvp_t31a_msc0_ddr128M

现在你将得到编译好的uboot文件"u-boot-with-spl.bin"

### 将 uboot 刻录到 SD 卡

将 SD 卡插入您的电脑，运行"fdisk -l"进行检查，您应该看到类似我的情况的设备"磁盘 /dev/sdb：29.72 GiB，31914983424 字节，62333952 扇区"。

**注意！** 仔细检查 `/dev` 设备名称是否确实是您的 SD 卡，否则您可能会丢失其他驱动器上的数据

```
dd if=./u-boot-with-spl.bin of=/dev/sdb bs=512 seek=34
```
这会将 uboot 文件刻录到 SD 卡上距 0x0 偏移 17KBytes 的位置

### 从 SD 卡启动

如果原始闪存芯片上的 uboot 已损坏或为空，它将自动选择从 SD 卡启动，但如果您只是想在闪存芯片上有可用的 uboot 且相机 PCB 板的 `bootsel` 引脚设置为 1 时侧载自己的 uboot，它仍将从闪存芯片上的 uboot 启动。要强制从 SD 卡启动，您可以在启动相机时短路 SOIC8 闪存芯片的引脚 5 和 6，以阻止读取闪存，详情见[此处](https://github.com/gitgayhub/wiki/blob/master/en/help-uboot.md#shorting-pins-on-flash-chip)。

#### OpenIPC uboot 自动重置问题

如果无法从默认地址加载内核，OpenIPC 的 uboot 将自动重置，如果您尝试从 SD 卡侧载 uboot，而闪存芯片上有一个有效的 uboot，这将导致相机再次启动到原始 uboot。要禁用自动重置功能，请编辑"include/configs/isvp_common.h"，从"bootcmd"行末尾删除"; reset"

### 适用于其他 Ingenic SoC T10 T20 T21 和 T30 的 uboot

可以为这些 SoC 构建 uboot 用于 SD 卡启动，但尚未在真实设备上验证

T10 和 T20

SoC | Command
--- | --------------------
T10	| `make isvp_t10_msc0`
T20	| `make isvp_t20_msc0`

#### T21 & T30

编辑 `/opt/openipc/u-boot-ingenic/boards.cfg` 添加以下行

```
isvp_t21_msc0                mips        xburst      isvp_t21            ingenic        t21         isvp_t21:SPL_MMC_SUPPORT,ENV_IS_IN_MMC,JZ_MMC_MSC0,SFC_COMMOND
isvp_t30_msc0                mips        xburst      isvp_t30            ingenic        t30        isvp_t30:SPL_MMC_SUPPORT,ENV_IS_IN_MMC,GPT_CREATOR,JZ_MMC_MSC0,SFC_COMMOND
isvp_t30_msc0_lite           mips        xburst      isvp_t30            ingenic        t30        isvp_t30:SPL_MMC_SUPPORT,ENV_IS_IN_MMC,GPT_CREATOR,JZ_MMC_MSC0,SFC_COMMOND,LITE_VERSION
isvp_t30_msc0_ddr128M        mips        xburst      isvp_t30            ingenic        t30        isvp_t30:SPL_MMC_SUPPORT,ENV_IS_IN_MMC,GPT_CREATOR,JZ_MMC_MSC0,SFC_COMMOND,DDR2_128M
isvp_t30a_msc0_ddr128M       mips        xburst      isvp_t30            ingenic        t30        isvp_t30:SPL_MMC_SUPPORT,ENV_IS_IN_MMC,GPT_CREATOR,JZ_MMC_MSC0,SFC_COMMOND,DDR2_128M,T30A
```

根据您的芯片类型选择"make"命令

SoC  | Command
-----| -----------------------------
T21  | `make isvp_t21_msc0`
T30N | `make isvp_t30_msc0`
T30L | `make isvp_t30_msc0_lite`
T30X | `make isvp_t30_msc0_ddr128M`
T30A | `make isvp_t30a_msc0_ddr128M`

### 在 uboot 中从 SD 卡安装 OpenIPC

例如，使用带有 16MB NOR 闪存的 T31ZX，下载 [16MB 全尺寸图像]（https://openipc.org/cameras/vendors/ingenic/socs/t31x/download_full_image?flash_size=16&flash_type=nor&fw_release=ultimate）

#### 方法 1

如果 uboot 中有 mmc 但没有 fatload 命令，我们可以将固件刻录到 SD 卡，而无需任何文件系统

**注意！** 仔细检查 `/dev` 设备名称是否确实是您的 SD 卡，否则您可能会丢失其他驱动器上的数据

```bash
dd if=./openipc-t31x-ultimate-16mb.bin of=/dev/[sd-card-device] seek=20480
```

当卡块大小为 512 字节时，这将把 OpenIPC 映像刻录到 SD 卡的 10MB 偏移处

在 uboot 中运行

```
mw.b 0x80600000 0xff 0x1000000
mmc read 0x80600000 0x5000 0x8000
sf probe 0
sf erase 0x0 0x1000000
sf write 0x80600000 0x0 0x1000000
```

#### 方法 2

如果 uboot 中有"fatload"命令，则用于直接从 FAT 文件系统加载文件

将 SD 卡的 FAT 文件系统挂载到 PC 上，将 OpenIPC 固件映像复制到其中。在 uboot 中，运行"fatls mmc 0"列出 SD 卡中的文件，然后

```
mw.b 0x80600000 0xff 0x1000000
fatload mmc 0 0x80600000 openipc-t31zx-ultimate-16mb.bin
sf probe 0
sf erase 0x0 0x1000000
sf write 0x80600000 0x0 0x1000000
```
