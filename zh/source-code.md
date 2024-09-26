# OpenIPC 固件开发指南

## 目录
- 简介
- 从源代码构建
- 安装固件
- 项目剖析
- 修改和添加软件包
- 构建固件的自定义版本
- 统计数据

## 介绍

本文档为希望为 OpenIPC 固件做出贡献的开发人员提供了全面的指南，包括如何从源代码构建、了解项目的结构、修改和添加新软件包，以及在设备上安装固件的说明。

我们一直致力于创建高质量的源代码存储库。我们努力提供完全完善且随时可用的项目，感谢您的耐心等待，欢迎您通过 [OpenIPC 电报频道](https://t.me/openipc/117235) 提供您的想法和反馈

## 从源代码构建

在开始构建自己的固件之前，必须对系统进行一些更改并了解一般过程。

### 克隆 OpenIPC 固件 Git 存储库 
第一步是制作 OpenIPC 固件源代码的本地副本。我们在下面的脚本中使用"mylocalOpenIPC/src"，但您可以将其更改为您喜欢的任何位置，例如 ~/myprojects/myOpenIPC

```cd
mkdir -p mylocalOpenIPC/src
cd mylocalOpenIPC/src
git clone https://github.com/OpenIPC/firmware.git openipc-firmware
cd openipc-firmware
```
我们现在有固件存储库源代码的克隆。

### 安装所需的软件包 
为确保您的系统具有成功构建所需的依赖项，您可以使用预先构建的 **make deps** 命令（位于您刚刚创建的 openipc-firmware 目录的根目录中），或者在终端窗口中手动输入命令。

要运行 make 脚本（推荐的方式，因为这将在源代码 git 存储库本身内维护），请执行以下操作或手动输入命令：
```bash
sudo make deps
```

or to manually enter the commands yourself do the following:
```sudo apt-get update -y
sudo apt-get install -y automake autotools-dev bc build-essential cpio \
 curl file fzf git libncurses-dev libtool lzop make rsync unzip wget libssl-dev
```


### 为下载的软件包创建永久存储 
[Buildroot](https://buildroot.org/) 是用于 OpenIPC 的 Linux 发行版。它在许多嵌入式系统中使用，因为它占用空间很小，并且可以轻松定制以包含或排除特定功能（有关 OpenIPC 构建中包含的内容，请参阅[本文](https://github.com/OpenIPC/wiki/blob/master/en/dev-buildroot-packages.md)）。

默认情况下，每次构建固件时，OpenIPC 构建脚本都会创建一个新的 buildroot 源文件树，这会导致不必要的文件下载或复制。为避免这种情况，您可以创建一个永久位置并设置 BR2_DL_DIR 环境变量以告诉构建脚本每次都使用它。

使用您最喜欢的文本编辑器（例如 nano ~/.profile）将以下代码片段添加到主目录中的 `.profile` 文件中，
```bash
#Buildroot directory for OpenIPC build
BR2_DL_DIR="${HOME}/buildroot_dl"
[ ! -d "$BR2_DL_DIR" ] && mkdir -p $BR2_DL_DIR
export BR2_DL_DIR
```

然后获取更改以使其立即生效。

```bash
source ~/.profile
```

### 构建固件。
如果您已遵循上述步骤，那么您现在就可以为您的特定相机型号构建固件了。

固件目录中的 Makefile 包含每个受支持的相机型号的构建脚本。

您只需确保您位于固件目录中，例如 ~/mylocalOpenIPC/src/openipc-firmware，然后运行 ​​make。

```bash
cd ~/mylocalOpenIPC/src/openipc-firmware
make
```

您将看到一个可用目标的列表。

![image](https://github.com/user-attachments/assets/4e3c87e7-560a-45bb-89e5-2259282e8f2a)

每个目标的名称由供应商名称、SoC 模型（片上系统，IP 摄像机的核心，具有额外功能的中央处理器）和表示不同用途的版本的版本组成 - **Lite**，仅用于 8MB ROM 的摄像机的紧凑版本；**Ultimate**，用于 16MB+ ROM 的摄像机的扩展版本，**FPV**，专为无人机使用而设计的版本，或**Mini**，带有替代开源流媒体的完全解放版本的固件。

选择所需目标并按回车键。构建将开始。

_如果您收到错误"tar：这看起来不像 tar 存档"，请参阅本节底部的注释_

构建固件二进制文件的过程需要 15-20 分钟到几个小时，具体取决于您的计算机性能和所选目标。如果您想加快此过程，请确保使用带有 SSD 而不是 HDD 的计算机，因为编译过程涉及大量读写。更快的 CPU 也有好处，而且 RAM 也不嫌多。您甚至可以花几美分租用在线虚拟服务器，利用云计算的力量编译固件。

第一次运行耗时最长，因为脚本将下载成功编译所需的每个源包。后续运行将花费更少的时间。

编译完成后，您将在 `output/images/` 目录中找到最终的二进制内核 **uImage** 和 **rootfs** 图像。

```
~/mylocalOpenIPC/src/openipc-firmware/output/images$ ls -la
total 39352
-rw-rw-r-- 1 chrisdev chrisdev  6515434 Sep  5 14:52 openipc.v83x-nor-lite.tgz
-rw-r--r-- 1 chrisdev chrisdev 12971008 Sep  5 14:52 rootfs.cpio
-rw-r--r-- 1 chrisdev chrisdev  4464640 Sep  5 14:52 rootfs.squashfs.v83x
-rw-r--r-- 1 chrisdev chrisdev 14274560 Sep  5 14:52 rootfs.v83x.tar
-rw-r--r-- 1 chrisdev chrisdev  2058032 Sep  5 14:50 uImage.v83x
```


** **注意："tar：这看起来不像 tar 存档"错误** \ 如果您使用的是 Ubuntu，则在 make 脚本中使用 wget 时可能会遇到问题，导致构建失败，并向控制台报告消息"tar：这看起来不像 tar 存档"。这是因为脚本中使用的 wget 命令无法正确验证，因此结果是一个空文件。

The workaround for this is to ensure the Makefile in the firmware directory is updated with the addition of  '--ca-directory=/etc/ssl/certs' so the prepare section will now read  
```
prepare:
	@if test ! -e $(TARGET)/buildroot-$(BR_VER); then \
		wget -c -q --ca-directory=/etc/ssl/certs $(BR_LINK)/$(BR_VER).tar.gz -O $(BR_FILE); \
		mkdir -p $(TARGET); tar -xf $(BR_FILE) -C $(TARGET); fi
```
and the general/external.mk file is also updated to include:
```
export WGET := wget --ca-directory=/etc/ssl/certs --show-progress --passive-ftp -nd -t5 -T10
```


解决方法是确保固件目录中的 Makefile 已更新，并添加了"--ca-directory=/etc/ssl/certs"，因此现在将读取准备部分，并且 general/external.mk 文件也已更新为包含：
## 安装固件

构建固件后，您需要将其安装到相机上。

您可以通过多种方式执行此操作：
1) 如果您拥有完全支持的相机板，则在将新的 uImage 和 rootfs.squashfs 文件从 output/images 目录复制到 tftp 服务器后，使用您最初使用的生成指南中的高级安装说明。如果您没有此信息，则[只需再次生成](https://openipc.org/supported-hardware/featured)。

2) 按照 wiki 文档 [升级固件](https://github.com/OpenIPC/wiki/blob/master/en/sysupgrade.md) 进行操作，当然要使用你自己生成的文件。

3) 手动安装：启动你的相机，将其连接到你的本地网络，然后使用 scp 将两个文件 (rootfs 和 uImage) 复制到你的相机 /tmp 文件夹 (/tmp 文件夹是临时存储，大小与相机可用 RAM 一样大)。然后，运行以下命令：

```
sysupgrade --kernel=/tmp/uImage.... --rootfs=/tmp/rootfs.... -z
```
将 uImage... 和 rootfs... 替换为您在构建过程中产生的实际文件名。如果您需要在更新后清除覆盖（将所有设置重置为默认值），可以添加 -n 键。安装完成后，相机将自动重启。再次连接到相机并运行此命令（与上一个命令中的 -n 相同）：

```
firstboot
```

记住！大多数情况下，用户和密码将被重置为默认值（默认值通常为 root/12345）

## 项目剖析

OpenIPC 固件 v2 使用 [Buildroot][1] 来构建其固件二进制文件。因此，如果您不仅想编译源代码，还想对固件进行自己的修改，那么您应该熟悉 [Buildroot 文档][2]。

您可以添加我们官方版本中未包含的驱动程序或软件，也可以删除您不会使用的不需要的驱动程序或软件，以释放固件中的空间。您可以更改默认设置以更好地满足您的需求。开源的美妙之处在于任何人都可以随时改进它。只是不要忘记将您的更改贡献回上游存储库，以便每个人都能从中受益。

请注意，OpenIPC 使用的是 Buildroot 的一个略微过时的版本。截至今天，它是 Buildroot 2024.02.1，因此您可能需要查看该特定版本的文档，因为后续版本可能有不兼容的更改。

OpenIPC 固件源由 IP 摄像机 SoC 供应商在目录中组织为 Buildroot 外部树，即"br-ext-chip-<供应商名称>"。

![image](https://github.com/user-attachments/assets/bd060676-7008-41ae-9ec6-f0ed18f6f48e)

每个目录都有多个子目录："board/"、"configs/"、"linux/"和"packages/"，以及一些配置文件，所有这些都与特定供应商的带有芯片的不同主板相关。

"board/" 目录包含按处理器组（称为系列）命名的子目录。每个系列目录中都包含该系列中各个处理器的内核配置文件、通用补丁和其他系列特定文件。

"configs/" 目录包含来自给定供应商的处理器的各种主板的默认配置文件 (defconfig)。这些配置文件还可能因硬件设置、所含软件包集、不同的默认设置、品牌等而不同。每个 defconfig 文件都是一个单独的软件包，从而产生一个单独的固件二进制文件。

"linux/"目录包含用于修补内核的配置文件，以使其与供应商提供的二进制 blob（如果有）一起工作。

"package/" 目录包含用于构建结果固件的包的符号链接。

`Config.in` 是一个配置文件，整合了所有提供的包的配置文件。

`external.mk` 是一个引用所有提供的包的 makefile 的 makefile。

`external.desc` 是一个包含外部树描述的文件。

### 进行更改并添加包

您可以修改现有包或添加新包以满足项目需求。本节提供有关如何有效进行这些更改的指南。

Once you start tinkering with the packages you'll realize you need a way to
rebuild only one particular package, without rebuilding the whole project.
Is it even possible? Fortunately, yes. All you have to do after making changes
to the package configs is to run a couple of commands:
```
make br-<package>-dirclean
make br-<package>-rebuild
```
一旦你开始修改软件包，你就会意识到你需要一种方法来只重建一个特定的软件包，而不需要重建整个项目。这可能吗？幸运的是，是的。在对软件包配置进行更改后，你所要做的就是运行几个命令：其中 _\<package>_ 是要重新编译的软件包的名称。尽管正如 Buildroot 手册所述，

> 虽然 `br-<package>-rebuild` 意味着 `br-<package>-reinstall`，而 `br-<package>-reconfigure` 意味着 `br-<package>-rebuild`，但这些目标以及 `<package>` 仅对所述包起作用，并且不会触发重新创建根文件系统映像。如果需要重新创建根文件系统，还应运行 `make br-all`。

运行"make br-linux-rebuild br-all"重建 Linux 内核映像，运行"make br-busybox-rebuild br-all"重建 busybox 并将其打包成 rootfs 映像。

记住！上面两个命令中的包名是你的包的文件夹名，而不是你在Config.in文件中设置的包名

如果您想向特定项目添加新软件包，则需要进行以下更改（我们以 goke 板、fpv 类型固件为例；这些步骤可应用于任何项目或所有项目）：
  * 在 [root]/general/package/ 文件夹中添加新软件包（其中 [root] 是您克隆固件存储库的本地文件夹）；
  * 将新软件包 Config.in 文件添加到此文件中的源软件包列表中：[root]/general/package/Config.in
  * 修改目标项目配置（即 goke 板、fpv 固件）以包含并构建新软件包，将您的软件包添加到此文件：[root]/br-ext-chip-qoke/configs/gk7205v200_fpv_def_config
  * 构建固件。

构建完成后，您的包（如果它安装了任何文件）应该是生成的图像和文件系统的一部分。


### 构建自定义版本的固件

有时您需要向固件添加驱动程序或软件包。如何使用提供的 OpenIPC 固件源来实现这一点？这真的很容易。在本地克隆固件存储库并为您的特定硬件编译二进制文件。

编译过程很大程度上取决于您的计算机性能。您获得的 CPU 线程和内存越多，编译过程就越快。无论如何，您可以预期初始编译将持续大约半小时，或多或少。生成的二进制文件将驻留在"output/images"目录中。如果您没有对源进行任何更改，那么这些文件应该与 [可从 GitHub 获得的文件] [4] 相同。

编译过程还构建了一个适用于编译您固件版本的软件包的工具链。该工具链位于"output/host"目录中。

要自定义固件、添加或删除软件包，请运行"make br-menuconfig"。这将加载 buildroot 配置菜单，您可以按照 [Buildroot 用户手册][5] 进行更改。进行更改并在退出时保存修改后的配置。然后运行"make clean all"。

__请注意，直接使用 buildroot 构建固件不会重命名生成的映像文件并为其添加 soc 后缀。您可以自行操作，也可以调整固件并相应地更新命令。__


[1]: https://buildroot.org/
[2]: https://buildroot.org/docs.html
[3]: https://github.com/OpenIPC/firmware/blob/96b2a0ed2f5457fda5b903ab67146f30b5062147/Makefile#L25
[4]: https://github.com/OpenIPC/firmware/releases/tag/latest
[5]: https://buildroot.org/downloads/manual/manual.html
